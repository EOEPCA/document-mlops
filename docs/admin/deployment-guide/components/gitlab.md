# GitLab

!!! info
    While a GitLab instance is mandatory, this is an optional deployment, please deploy only if you don't already have a usable instance.

To deploy this application you need to:

- [Create two S3 bucket](#create-two-s3-bucket)
- *Optional* [Configure Git LFS](#configure-git-lfs)
- *Optional* [Configure OIDC](#configure-oidc)
- [Deploy the Gitlab ArgoCD Application](#deploy-the-gitlab-argocd-application)

This GitLab deployment comes in minimal mode, with Keycloak OIDC preconfigured.
If you want to customize your Gitlab deployment, you can check this [link](https://docs.gitlab.com/charts/charts/).

## Create two S3 bucket

Create two S3 bucket `gitlab-backup-storage` and `gitlab-tmp-storage` in your favorite S3 provider.
If you want to enable lfs, you will need a third bucket `gitlab-lfs-storage`

Then, create a S3 config file called `storage.config`:

```conf
[default]
access_key = <access_key>
bucket_location = <bucket_region>
host_base = <s3_endpoint>
secret_key = <secret_key>
use_https = True
```

Finally, create the s3 config secret:

```bash
kubectl create ns gitlab
kubectl create secret generic storage-config --from-file=config=storage.config -n gitlab
```

For more details about object storage configuration go [here](https://docs.gitlab.com/charts/advanced/external-object-storage/index.html).

## Configure Git LFS

If you want to enable lfs, you will need a third bucket `gitlab-lfs-storage`

Then, create a S3 config file called `lfs-s3.yaml`:

```yaml
provider: AWS
# Specify the region
region: eu
# Specify access/secret keys
aws_access_key_id: <access_key>
aws_secret_access_key: <secret_key>
aws_signature_version: 4
host: <s3_endpoint>
endpoint: "https://<s3_endpoint>"
path_style: true
```

Finally, create the s3 config secret:

```bash
kubectl create secret generic object-storage --from-file=connection=lfs-s3.yaml -n gitlab
```

For more details about lfs object storage configuration go [here](https://docs.gitlab.com/charts/advanced/external-object-storage/#lfs-artifacts-uploads-packages-external-diffs-terraform-state-dependency-proxy-secure-files).

## Configure OIDC

This step is optional, continue if you want to connect your Gitlab instance to your Keycloak realm.
Create a `gitlab` client in your Keycloak realm.

Them create a config file called `provider.yaml`:

```yaml
name: openid_connect
label: EOEPCA
icon: "https://eoepca.readthedocs.io/img/favicon.ico"
args:
  name: openid_connect
  scope: ["openid", "profile", "email"]
  response_type: "code"
  issuer: "https://<keycloka_domain>/realms/<your_realm>"
  client_auth_method: "query"
  discovery: true
  uid_field: "preferred_username"
  pkce: true
  client_options:
    identifier: "gitlab"
    secret:
    redirect_uri: "https://gitlab.<domain_name>/users/auth/openid_connect/callback"
```

Finally create the provider secret:

```bash
kubectl create secret generic -n gitlab openid-connect --from-file=provider=provider.yaml
```

For more details about Keycloak OIDC in Gitlab, check these links:

- [Use OpenID Connect as an authentication provider - Configure Keycloak](https://docs.gitlab.com/ee/administration/auth/oidc.html?tab=-1#configure-keycloak)
- [Configure charts using globals - OmniAuth](https://docs.gitlab.com/charts/charts/globals.html#omniauth)

## Deploy the Gitlab ArgoCD Application

Create a file called `gitlab.yaml`:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: gitlab
  namespace: argocd
spec:
  destination:
    namespace: gitlab
    server: https://kubernetes.default.svc
  project: default
  source:
    repoURL: https://charts.gitlab.io
    chart: gitlab
    targetRevision: 8.1.0
    helm:
      valuesObject:
        global:
          edition: ce
          hosts:
            domain: <domain_name>

          ingress:
            configureCertmanager: false
            class: nginx
            annotations:
              cert-manager.io/cluster-issuer: letsencrypt-prod

          registry:
            enabled: false

          minio:
            enabled: false

          appConfig:
            omniauth:
              enabled: false #Set to true and uncomment the part below if you want to use OIDC
              # allowSingleSignOn: ["saml", "openid_connect"]
              # providers:
              #   - secret: openid-connect

            lfs:
              enabled: false #Set to true and uncomment the part below if you want to use OIDC
              bucket: gitlab-lfs-storage
              connection:
                secret: object-storage
                key: connection
            artifacts:
              enabled: false
            uploads:
              enabled: false
            packages:
              enabled: false
            backups:
              bucket: gitlab-backup-storage
              tmpBucket: gitlab-tmp-storage

        upgradeCheck:
          enabled: false

        gitlab:
          webservice:
            ingress:
              tls:
                secretName: gitlab.<domain_name>-tls

          kas:
            ingress:
              tls:
                secretName: kas.<domain_name>-tls
          toolbox:
            backups:
              objectStorage:
                config:
                  secret: storage-config
                  key: config

          sidekiq:
            enabled: true

        registry:
          enabled: false
          ingress:
            tls:
              secretName: registry.<domain_name>-tls

        minio:
          ingress:
            tls:
              secretName: minio.<domain_name>-tls

        certmanager-issuer:
          email: me@example.com

        certmanager:
          install: false

        nginx-ingress:
          enabled: false

        nginx-ingress-geo:
          enabled: false

        prometheus:
          install: false

        gitlab-runner:
          install: false

        redis:
          metrics:
            enabled: false

        postgresql:
          metrics:
            enabled: false

  syncPolicy:
    syncOptions:
      - FailOnSharedResource=true
      - CreateNamespace=true
```

Before you deploy the `gitlab.yaml` application, you will need to fill some fields:

- `<domain_name>` with your domain name (ex: example.com).

Finally, apply the Application manifest:

```bash
kubectl apply -f gitlab.yaml
```

For more detail about Gitlab configuration check their [documentation](https://docs.gitlab.com/charts/).
