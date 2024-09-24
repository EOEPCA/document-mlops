# Configuration

The configuration is mainly that of SharingHub, which is the central component of te MLOps Building Block.
There is also the configuration of MLflow SharingHub, much lighter. We recommend checking directly the
documentation of these components in their own repositories, as they will always be the most up-to-date.

## SharingHub

As you may have noticed in the [SharingHub deployment configuration](./deployment-guide/components/sharinghub.md#deploy-sharinghub)
that most of the configuration is defined in a config field, with content in YAML format. Some values may also be
passed from the environment.

For the complete list of configuration available, check the file
[CONFIGURATION.md](https://github.com/csgroup-oss/sharinghub-server/blob/main/CONFIGURATION.md) in the server repository.

Let's break down the main configuration options.

### Server global settings

#### Debug & log level

Debug can be activated by setting `DEBUG` env variable to `true`, or in the config:

```yaml
server:
    debug: true
```

The log level is `DEBUG` if debug is true, else `INFO`.
You can also set it with `LOG_LEVEL` or:

```yaml
server:
    log-level: WARNING
```

Possible values: `CRITICAL`, `WARNING`, `INFO`, `DEBUG`

#### Security

The server have CORS policy enabled. If you want to allow requests from other origins, add them:

```yaml
server:
  allowed-origins:
    - https://eoepca.readthedocs.io
```

#### Session

A session secret key should have been create in ["Create a secret key"](./deployment-guide/components/sharinghub.md#create-a-secret-key).

You can customize the session cookie like this:

```yaml
server:
  session:
    cookie: sharinghub-session
    domain: develop.eoepca.org
    max-age: 14400.0
```

#### Cache

Enable the caching system. We recommend you to let this setting default value, `true`, because
it is important for performance issues. However, sometimes for debugging purposes it may be
practical to disable it:

```yaml
server:
  cache: true
```

The different cache "timeout" can be configured like so:

```yaml
checker:
  cache-timeout: 30.0 # Check API
s3:
  check-access:
    cache-timeout: 30.0 # S3 store permission check
stac:
  projects:
    cache-timeout: 30.0 # STAC item for project
```

### GitLab

The GitLab configuration is important, as SharingHub is integrated with it.

Here's an overview:

```yaml
gitlab:
  url: https://gitlab.example.com
  allow-public: true
  ignore:
    topics:
      - "gitlab-ci"
```

Some details:

- `allow-public`: `true` if your GitLab allow Public visibility on projects, else `false`.
- `ignore.topics`: List of topics to filter-out from tag list.

#### OAuth client

See [here](./deployment-guide/components/sharinghub.md#configure-a-gitlab-application-for-authentication) how to configure the authentication.

#### Default token

The default token is a mechanism used to give the server a fallback token. If configured, users not authenticated will
have their request by default use this token, and browse the projects available through this token.
See [here](./deployment-guide/components/sharinghub.md#configure-a-gitlab-application-for-authentication) how to
configure the default token alongside the authentication.

### STAC

The main feature of SharingHub is the STAC API. The STAC structure is dynamically generated from the configuration,
and items are generated from GitLab projects. We follow "OGC API Features - Part 1: Core", the root is a STAC Catalog,
then collections, and finally items in each collection. The collections are mapped to GitLab topics, and defined in
the configuration. In SharingHub, we call them globally "categories".

Example from [Deployment Guide](./deployment-guide/components/sharinghub.md#deploy-sharinghub):

```yaml
stac:
  extensions:
    eo: https://stac-extensions.github.io/eo/v1.1.0/schema.json
    label: https://stac-extensions.github.io/label/v1.0.1/schema.json
    sci: https://stac-extensions.github.io/scientific/v1.0.0/schema.json
    ml-model: https://stac-extensions.github.io/ml-model/v1.0.0/schema.json
  root:
    id: gitlab-cs
    title: SharingHub brings your data and models closer.
    description: Your platform for collaborating on ML and NLP projects store in [GitLab](https://gitlab.com) instance STAC catalog.
    locales:
      fr:
        title: SharingHub rapproche vos données et vos modèles.
        description: Votre plateforme de collaboration sur les projets ML et NLP stockés dans le catalogue STAC de l'instance [GitLab](https://gitlab.com).
  categories:
    - ai-model:
        title: "AI Models"
        description: "AI models are the core of our platform, go and browse them to discover our models."
        gitlab_topic: sharinghub:aimodel
        logo: https://data.web.<domain_name>/sharinghub/ai-model.jpg
        icon: https://img.icons8.com/material/24/artificial-intelligence.png
        locales:
          fr:
            title: "Modèles IA"
            description: "Les modèles d'IA sont au cœur de notre plateforme, allez les parcourir pour découvrir nos modèles."
        features:
          map-viewer: enable
          store-s3: enable
          mlflow: disable
    - dataset:
        title: "Datasets"
        description: "Datasets are very important in the process of training an AI, discover those that we put at your disposal."
        gitlab_topic: sharinghub:dataset
        logo: https://data.web.<domain_name>/sharinghub/datasets.jpg
        icon: https://img.icons8.com/ios/50/data-backup.png
        locales:
          fr:
            title: "Jeux de données"
            description: "Les jeux de données sont très importants dans le processus de formation d'une IA, découvrez ceux que nous mettons à votre disposition."
        features:
          map-viewer: enable
          store-s3: enable
          mlflow: disable
```

The structure should be self-explanatory. Text fields can be localized through the `locales`.
The `extensions` allow you to declare STAC extensions, to help user that will be able to use them
without declaring them themselves. The root declares metadata for the root STAC catalog, and
categories the STAC collections. The `gitlab_topic` map the category/collection to a topic used in
GitLab for discovery of the projects. The `features` are toggled with `enable` of `disable` and
configured per-category.

List of features:

- `map-viewer`: display map, useful to display geo assets
- `store-s3`: enable store API, allow use of DVC.
- `mlflow`: enable MLflow SharingHub integration.

### MLflow

The MLflow configuration is as the following:

```yaml
mlflow:
  type: mlflow-sharinghub
  url: https://sharinghub.example.com/mlflow
```

The `mlflow.type` can be multiple values:

- **STRONGLY RECOMMENDED** `mlflow-sharinghub`: our custom mlflow that integrates with SharingHub
  and offers per-project tracking URI mapped from GitLab, as well as permission checking.
- `mlflow`: the URL is for a classic MLflow instance, per-project features is not available,
  and no authentication enabled.
- `gitlab`: use of GitLab for ML Tracking, compatible with MLflow. This feature is quite new,
  and not really polished for now. The interface is not up to par with what MLflow offers.

It is important to know that we maintain actively `mlflow-sharinghub` integration, as this is what
we recommend and use. The other are also much less integrated.

### S3 store

The S3 store is an optional API that can be enabled or disabled. When enabled, it allows user to
use DVC. It is disabled by default.

Example:

```yaml
s3:
  enable: true
  bucket: <bucket>
  region: <bucket-region>
  endpoint: https://<s3-endpoint>
```

If you use S3, the `bucket`, `region` and `endpoint` should be familiar. We are still missing an
access and secret key, that should be passed by secret.

```bash
kubectl create secret generic sharinghub-s3 --from-literal access-key="<access-key>" --from-literal secret-key="<secret-key>" --namespace sharinghub
```

### Tags

The tags are used by the web UI, to configure the left-side panel.

Example:

```yaml
tags:
  gitlab:
    minimum_count: 1
  sections:
    - name: "Computer Vision"
      enabled_for:
        - ai-model
        - dataset
        - processor
        - challenge
      keywords:
        - "Image qualification"
        - "Object detection"
        - "Image segmentation"
        - "Mask generation"
```

Each section list its related tags as `keywords` (topics in GitLab), and is enabled for (`enabled_for`)
the specified categories.

Keywords/tags not configured can still be used, they are available in the "Other" tab. It is possible
to specify the minimum count of project a GitLab topic point to in order to be listed. We recommend
at least `1`, to avoid collecting topics with zero projects.

### Alert message

An alert message can be configured to be displayed for new users (cookie-based detection).

```yaml
alerts:
  timeout: 3 # days unit
  type: info # color of alert | possibility (info, danger, success, warning,primary, dark, secondary)
  title: "Welcome to new SharingHub"
  message: "To see all projects and unlock all features, please login.." # Possible to render primitives html component in message ex: <a href='url'> text here <a/>
  locales:
    fr:
      title: "Bienvenue sur le nouveau sharing hub"
      message: "Pour voir tous les projets et débloquer toutes les fonctionnalités, veuillez vous connecter..." # Possible to render primitives html component in message ex: <a href='url'> text here <a/>
```

## MLflow SharingHub

### SharingHub integration

The basic deployment values are the following:

```yaml
sharinghubUrl: https://sharinghub.<domain-name>
sharinghubStacCollection: "ai-model"
sharinghubAuthDefaultToken: false
```

Let's break it down:

- `sharinghubUrl`: URL of the SharingHub instance, it will be used by MLflow SharingHub to request an API that check the permissions of the projects.
- `sharinghubStacCollection`: the stac collection for the models. The project URI enabled by MLflow SharingHub will be restricted to projects that are registered on this collection, to avoid people using MLflow on unrelated projects.
- `sharinghubAuthDefaultToken`: If your SharingHub have a [default token](#default-token) configured, use `sharinghubAuthDefaultToken: false`. It is useful to access projects (read-only) without being authenticated, if the project is available through the default token.

### Backend store

By default, our docker image uses an sqlite database (a single file) for the data, located at `/home/mlflow/data/mlflow.db`.

#### PostgreSQL

You can alternatively choose PostgreSQL as a database.

##### From the charts dependency

First, create the secret that will contain PostgreSQL passwords:

```bash
kubectl create secret generic mlflow-sharinghub-postgres --from-literal password="<mlflow-user-password>" --from-literal postgres-password="<root-user-password>" --namespace sharinghub
```

Then, configure the deployment values:

```yaml
postgresql:
  enabled: true
  auth:
    existingSecret: mlflow-sharinghub-postgres
```

##### For existing instance

Edit your `mlflow-sharinghub` secret that contains the key `secret-key`, and add a new key named `backend-store-uri` with the value `postgresql://<user>:<password>@<host>:5432/<database>`, filled with your PostgreSQL instance values.

Then, configure the deployment values:

```yaml
mlflowSharinghub:
  backendStoreUriSecret: true
```

### Artifacts store

By default, our docker image uses a directory for the artifacts, located at `/home/mlflow/data/mlartifacts`.

#### S3

You can alternatively choose to store your artifacts in an S3.

If you chose to use one, you need to create a s3 bucket in your provider and create the associated secret:

```bash
kubectl create secret generic mlflow-sharinghub-s3 --from-literal access-key-id="<access-key>" --from-literal secret-access-key="<secret-key>" --namespace sharinghub
```

Then, configure the deployment values:

```yaml
mlflowSharinghub:
  artifactsDestination: s3://<bucket>

s3:
  enabled: true
  endpointUrl: https://<s3-endpoint>
```
