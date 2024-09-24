# Model Training

**Practical Case**: Developing an AI Model for training Wine model

## Context

We want to be able to train an AI model using the MLOps platform. The project team consists of
**ML Developers**, who will develop the model and train it.

## Scenario

### Step 1: Model Creation

- **Actors**: ML Developer, GitLab
- **Action**: The **ML Developer** starts a new project on the **GitLab** platform after login.

### Step 2: Editing Project to create metadata and reference in SharingHub

- **Actors**: ML Developer, GitLab
- **Action**: The **ML Developer** edit the **GitLab** project to add necessary metadata to the `README.md`,
  and add the AI model topic in the project settings, to be referenced in Sharinghub.

### Step 3: Training environment

- **Actors**: ML Developer, MLflow client, SharingHub
- **Action**: The **ML Developer** initialize his training environment with the **MLflow client** installed,
  as well as the model dependencies. It can be locally or remotely on a computing platform.

### Step 4: Prepare the dataset

- **Actors**: ML Developer, SharingHub
- **Action**: The **ML Developer** find the dataset on **SharingHub**, or another source, retrieve it,
  which may be with Git LFS or DVC, and prepare the dataset for the training.

### Step 5: Model Training

- **Actors**: ML Developer, MLflow client, SharingHub
- **Action**: The **ML Developer** configures with the parameters the model and begins training
  using the dataset. Metrics are logged with **MLflow client**, using the tracking URI given by **SharingHub**.

### Step 6: Evaluating Model Performance

- **Actors**: ML Developer, MLflow UI
- **Action**: After training, the **ML Developer** evaluates the model’s performance using the metrics
  on the **MLflow UI** `Experiments->runs`.

### Step 7: Versioning the Model

- **Actors**: ML Developer, MLflow UI, SharingHub
- **Action**: After achieving satisfactory results, the **ML Developer** save the final model via the
  **MLflow UI** in the registry. After that, the model version is automatically linked to the project
  STAC metadata in **SharingHub**. This ensures that other team members can test and use the model release.
