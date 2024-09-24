# MLflow SharingHub

!!! warning
    Depends on SharingHub.

The Helm charts are in the [Mlflow repository](https://github.com/csgroup-oss/mlflow-sharinghub), in the folder `deploy/helm/mlflow-sharinghub`.
A more complete Deployment Guide can be found in [MLflow SharingHub's README.md](https://github.com/csgroup-oss/mlflow-sharinghub/blob/main/README.md#deployment-guide).

## Preparation steps

### Create a secret key

The server needs a secret key for security purposes, create the secret:

```bash
kubectl create secret generic mlflow-sharinghub --from-literal secret-key="<random-secret-key>" --namespace sharinghub
```

### Create/configure artifacts S3

We recommend using an S3 storage for your artifacts. You will need to create a S3 bucket in your provider and create the associated secret:

```bash
kubectl create secret generic mlflow-sharinghub-s3 --from-literal access-key-id="<access-key>" --from-literal secret-access-key="<secret-key>" --namespace sharinghub
```

## Deploy mlflow-sharinghub

Next, create a file called `mlflow-sharinghub.yaml`:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: mlflow-sharinghub
  namespace: argocd
spec:
  destination:
    namespace: sharinghub
    server: https://kubernetes.default.svc
  project: default
  source:
    repoURL: https://github.com/csgroup-oss/mlflow-sharinghub.git
    path: deploy/helm/mlflow-sharinghub
    targetRevision: main
    helm:
      valuesObject:
        image:
          repository: eoepca/mlflow-sharinghub
          tag: latest

        mlflowSharinghub:
          artifactsDestination: s3://<bucket>
          sharinghubUrl: https://sharinghub.<domain_name>
          sharinghubStacCollection: ai-model
          sharinghubAuthDefaultToken: false

        s3:
          enabled: true
          endpointUrl: https://<s3_endpoint>

        persistence:
          enabled: false

        podSecurityContext:
          fsGroup: 999

        ingress:
          enabled: true
          className: nginx
          annotations:
            cert-manager.io/cluster-issuer: letsencrypt-prod
            nginx.ingress.kubernetes.io/ssl-redirect: "true"
            nginx.ingress.kubernetes.io/proxy-body-size: 10g
            nginx.ingress.kubernetes.io/configuration-snippet: |
              proxy_set_header X-Script-Name /mlflow;
              rewrite ^/mlflow/(.*)$ /$1 break;
          hosts:
            - host: sharinghub.<domain_name>
              paths:
                - path: /mlflow/
                  pathType: ImplementationSpecific
          tls:
            - secretName: sharinghub.<domain_name>-tls
              hosts:
                - sharinghub.<domain_name>
        postgresql:
          enabled: true
          auth:
            existingSecret: mlflow-sharinghub-postgres

  syncPolicy:
    syncOptions:
      - FailOnSharedResource=true
      - CreateNamespace=true
```

You will need to fill some fields:

- `<domain_name>` with your platform domain name (ex: example.com).
- `<bucket>`: name of the bucket used for artifacts storage.
- `<s3_endpoint>`: endpoint of S3 provider.

If you want to learn more about the configuration, go to [Administration > Configuration](../../configuration.md#mlflow-sharinghub).

Once the configuration for the server satisfies you, apply the Application manifest:

```bash
kubectl apply -f mlflow-sharinghub.yaml
```
