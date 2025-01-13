# Example: Flood Model Training

**Practical Case**: Training a flood detection model.

## Context

We want to be able to train our Flood Model using the MLOps platform.

The repositories are on the develop cluster [GitLab](https://gitlab.develop.eoepca.org/).
They are mirrored in the following repositories:

- [Flood Model](https://gitlab.develop.eoepca.org/sharinghub-test/flood-model): <https://github.com/EOEPCA/flood-model>
- [Sen1Floods11-Dataset](https://gitlab.develop.eoepca.org/sharinghub-test/sen1floods11-dataset): <https://github.com/EOEPCA/Sen1Floods11-Dataset>

## Scenario (ML Developer)

### Step 1: Browsing SharingHub for the model

- Navigate to <https://sharinghub.develop.eoepca.org>
- Click on "Models" category
- Filter "flood"
- Click on the model
- Open in GitLab

### Step 2: Clone the model

- Flood Model: <https://gitlab.develop.eoepca.org/sharinghub-test/flood-model>

### Step 3: Model setup

- Install poetry environment
- Setup .env file: credentials for mlflow

### Step 4: Dataset setup

- Follow the model README "Getting started"
    - Clone dataset: <https://gitlab.develop.eoepca.org/sharinghub-test/sen1floods11-dataset>
    - Setup credentials for dvc
- Run "dvc pull" in terminal

### Step 5: Train Model

- Run training session.
- Check metrics in mlflow UI.
- Show modes streaming and no cache.

### Step 6: Inference

- Run an inference with a tif file 512x512: `Pakistan_43105_S1Hand.tif` and import the model from .onnx file.
- Show the result stored in `predictions/prediction.tif` file.

### Step 7: Packaging

#### Docker for training

- Build the docker image for training.
- Run this image in a docker container.

#### Docker for inference

- Build the docker image for inference and specify the onnx file to use 1 time.
- Run this image with a tif file 512x512: `Pakistan_43105_S1Hand.tif` as an input.

---

## Scenario (ML User)

### CWL for inference

- Download the onnx from the sharinghub
- Run the cwl file with cwltool. Parameters are saved in `run_inference_input.yml` file.
