<img src="https://avatars.githubusercontent.com/u/40179672" width="75">

[![hack.d Lawrence McDaniel](https://img.shields.io/badge/hack.d-Lawrence%20McDaniel-orange.svg)](https://lawrencemcdaniel.com)
[![discuss.overhang.io](https://img.shields.io/static/v1?logo=discourse&label=Forums&style=flat-square&color=ff0080&message=discuss.overhang.io)](https://discuss.overhang.io)
[![docs.tutor.overhang.io](https://img.shields.io/static/v1?logo=readthedocs&label=Documentation&style=flat-square&color=blue&message=docs.tutor.overhang.io)](https://docs.tutor.overhang.io)<br/>
[![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/)
[![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)](https://kubernetes.io/)

# tutor-plugin-enable-ecommerce

Github Action to install and enable the Tutor plugin - Open edX Ecommerce service

This action is designed to work seamlessly with Kubernetes secrets created by the Terraform modules contained in [Cookiecutter Tutor Open edX Production Devops Tools](https://github.com/lpm0073/cookiecutter-openedx-devops).

**IMPORTANT SECURITY DISCLAIMER**: Sensitive data contained in Kubernetes secrets is masked in Github Actions logs and console output provided that the secret was created with the Terraform scripts provided in the Cookiecutter. If you are working a Kubernetes secret created outside of the Cookiecutter then **be aware that you run a non-zero risk of your sensitive data becoming exposed inside the Github Actions log data and/or console output**.

**ADDITIONAL CONFIGURATION REQUIRED**: An ecommerce payment processors configuration yaml file is expected to exist in the `secrets` S3 bucket for your platform environment. If it is not found then this action will upload a template with scaffolding to your `secrets` buckets. The template contains scaffolding for cybersource, paypal and stripe and can be viewed in the file `ecommerce-payment-processors.yml` located in the root of this repository.

## Usage:


```yaml
name: Example workflow

on: workflow_dispatch

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      # required antecedent
      - uses: actions/checkout@v3.0.2

      # required antecedent
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1.6.1
        with:
          aws-access-key-id: ${{ secrets.THE_NAME_OF_YOUR_AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.THE_NAME_OF_YOUR_AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-2

      # install and configure tutor & kubectl
      - name: Configure Github workflow environment
        uses: openedx-actions/tutor-k8s-init@v1.0.0
        with:
          namespace: openedx-prod

      #
      # ... steps to deploy your Open edX instance to k8s ...
      #

      # This action.
      - name: Enable tutor plugin - Ecommerce
        uses: openedx-actions/tutor-enable-plugin-ecommerce@v1.0.2
        with:
          namespace: openedx-prod
          secrets-s3-bucket-name: {global_platform_name}-{global_platform_region}-{environment_name}-secrets
          currency: USD
          enabled-payment-processors: '["stripe", "paypal"]'


      #
      # ... more steps to deploy your Open edX instance to k8s ...
      #

```
