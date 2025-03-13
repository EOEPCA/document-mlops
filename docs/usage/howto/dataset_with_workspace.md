# Configure Dataset with Workspace BB

In the documentation of SharingHub you can find a tutorial named "Dataset with DVC" that
explains the usage of DVC to manage your data alongside a Git repository. In that tutorial
you can learn to configure DVC with an S3 bucket as remote, making it possible to use the
[Workspace BB](https://eoepca.readthedocs.io/projects/workspace/en/latest/) for the remote storage.

Example of configuration for a dataset with your Workspace bucket:

```bash
dvc remote add --default workspace s3://ws-bob/my-dataset
dvc remote modify workspace endpointurl https://minio.develop.eoepca.org
```

Setup your credentials:

```bash
dvc remote modify --local workspace access_key_id <access-key-id>
dvc remote modify --local workspace secret_access_key <secret-access-key>
```
