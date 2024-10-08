# Introduction

The documentation for the MLOps building block is organized as follows...

* **Introduction** :material-map-marker-radius:<br>
  Introduction to the BB - including summary of purpose and capabilities.
* [**Design** :material-link:](./design/architecture.md)<br>
  Description of the BB design - including its subcomponent architecture and interfaces.
* [**Administration** :material-link:](./admin/deployment-guide/intro.md)<br>
  Deployment guide, configuration and maintenance of the BB.
* [**Usage** :material-link:](./usage/tutorials.md)<br>
  Tutorials, How-to guides, etc. to communicate usage of the BB.
* [**API** :material-link:](./api/endpoint-specification.md)<br>
  Details of APIs provided by the BB - including endpoints, usage descriptions and examples etc.

## About MLOps

The MLOps BB provides support services for training of machine learning models within the cloud
platform. It also integrates within the EOEPCA Building Block ecosystem, with the other Building
Blocks such as Processing, Workspace, and Resource Discovery.

This building block is implemented with **SharingHub**, a service that enable the discovery of hosted
repositories through an integrated STAC catalog answering STAC API. Each repository conforms to the
definition of a STAC item, with some STAC extensions (notably ML Model).
The content of this catalog is generated on the fly from the information stored in the _GitLab_
projects and is depending on the authenticated user and his/her access rights. It is also integrated
with an MLflow instance that can serve as a remote tracking server for model experiments, as well
as expose a model registry.

## Capabilities

The MLOps BB orchestrates the training of ML models in a variety of popular machine learning
frameworks, maintains a history of training runs with associated metrics used to assess model
performance, and maintains the associated training data.

The [Design](./design/architecture.md) section can give a good overview of the features
expected for this Building Block.
