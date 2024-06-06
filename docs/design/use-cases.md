# Use Cases

A global overview of the use cases for the MLOps components from a user point of view.

!!! note
    Based on [Design > Requirements](./requirements.md).

## Diagram

<figure markdown="span">
  ![Use cases diagram](./diagrams/use-cases.drawio.png)
  <figcaption>Use Case diagram of MLOps BB</figcaption>
</figure>

## Definitions

### Train model

- **Actors:** ML Developer, Model Trainer.
- **Description:** As an _ML developer_, I want to be able to train my machine learning model with the _Model Trainer_.

#### Use popular ML frameworks

- **Actors:** ML Developer, Model Trainer.
- **Description:** As an _ML Developer_, I want to use popular machine learning frameworks for training models with the _Model Trainer_.

#### Use ONNX model representation format

- **Actors:** ML Developer, Model Trainer.
- **Description:** As an _ML Developer_, I want to use the ONNX model representation format for training models with the _Model Trainer_.

#### Log model training with metrics

- **Actors:** ML Developer, Model Trainer.
- **Description:** As an _ML Developer_, I want to logs metrics from my model training runs with the _Model Trainer_.

### Publish assets in platform storage

- **Actors:** ML Developer, Model Trainer.
- **Description:** As an _ML Developer_, I want to publish my model assets in the platform storage with the _Model Trainer_.

### Model Release (versioning)

- **Actors:** ML Developer, Model Trainer.
- **Description:** As an _ML Developer_, I want to create releases/versions of my model with the _Model Trainer_.

### Register models in Resource Discovery BB

- **Actors:** ML Developer, Model Trainer.
- **Description:** As an _ML Developer_, I want to register my models with the _Model Trainer_ in the **Resource Discovery Building Block** for easy discovery and reuse.

### Use Workspace for assets

- **Actors:** ML Developer, Model Trainer.
- **Description:** As an _ML Developer_, I want to use my workspace (**Workspace Building Block**) with the _Model Trainer_ for my assets.

### View model training history

- **Actors:** ML Developer, MLOps UI.
- **Description:** As an _ML Developer_, I want to view the history of my model training runs with the _MLOps UI_.

### View model training run metrics

- **Actors:** ML Developer, MLOps UI.
- **Description:** As an _ML Developer_, I want to view metrics from my model training runs with the _MLOps UI_.

### Assess/evaluate model training performance

- **Actors:** ML Developer, MLOps UI.
- **Description:** As an _ML Developer_, I want to evaluate the performance of my trained model with the _MLOps UI_ by viewing the model training runs metrics.

### Manage model training history

- **Actors:** ML Developer, Model Trainer, MLOps UI.
- **Description:** As an _ML Developer_, I want to manage the history of my models training runs, including renaming, editing metadata, or deleting/archiving past runs, with the _MLOps UI_.

### Access datasets

- **Actors:** ML Developer, Data Scientist, MLOps UI.
- **Description:** As an _ML Developer_ or _Data scientist_, I want to access my available datasets, with the _MLOps UI_.

### Manage datasets

- **Actors:** Data Scientist, Training Data Manager.
- **Description:** As a _Data scientist_, I want to manage datasets, including creating, updating, and deleting, with the _Training Data Manager_.

### Datasets versioning

- **Actors:** Data Scientist, Training Data Manager.
- **Description:** As a _Data scientist_, I want to version datasets to keep track of changes and maintain different versions over time with the _Training Data Manager_.

### Register datasets in Resource Discovery BB

- **Actors:** Data Scientist, Training Data Manager.
- **Description:**  As a _Data scientist_, I want to register datasets in the **Resource Discovery Building Block** for easy discovery and reuse with the _Training Data Manager_.
