# Introduction

The MLOps BB is made from these components:

- [SharingHub](https://github.com/csgroup-oss/sharinghub): web application offering collaborative services useful to developers or data scientists working in the fields of AI and earth observation. It offers tools for managing datasets, AI models and associated tools. Inspired by the HuggingFace platform, SharingHub brings the management dimension of geographic information, and is deployable on top of any GitLab instance.
- [MLflow SharingHub](https://github.com/csgroup-oss/mlflow-sharinghub/): custom SharingHub-integrated version of MLflow, a platform to streamline machine learning development, including tracking experiments, packaging code into reproducible runs, and sharing and deploying models.

And it needs these components:

- [GitLab](https://about.gitlab.com/): open source end-to-end software development platform with built-in version control, issue tracking, code review, CI/CD, and more.

Head over to [Design > Architecture > Implementation](../../design/architecture.md#implementation) to check-out for more details about the design of the solution.

For this deployment you will need a Gitlab instance, if you don't have one please start by deploying a [GitLab](./components/gitlab.md) instance, otherwise jump directly to [SharingHub](./components/sharinghub.md).
