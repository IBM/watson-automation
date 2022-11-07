# Watson NLP Embed

Helm chart to deploy Watson NLP services to Kubernetes, for embedding into another application.

- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
    - [Dependencies](#dependencies)
    - [Configuration](#configuration)
    - [Deployment Example](#deployment-example)
  - [Usage](#usage)
  - [Cleaning Up](#cleaning-up)
- [Documentation](#documentation)

## Getting Started

This chart deploys the container images required to provide embedded Watson NLP libraries which can be called from another application.

At a minimum a Watson NLP runtime image is required. The NLP runtime container runs in the Watson NLP pod at runtime. Additional "model images" are necessary for different functionality provided by Watson NLP. There are two types of model images:

- Pretrained models provided by IBM
- Custom models provided by consumers

The model containers run as Kubernetes init containers. They are triggered when pods are created. Their purpose is to put the model artifacts onto pod storage so that the Watson NLP runtime container can access them. Once they have done this, these containers terminate.

This chart deploys and Watson NLP runtime and one pretrained model image, the Syntax model.

### Prerequisites

#### **Kubernetes**

A OpenShift or Kubernetes cluster is required.

#### **Helm**

Install instructions for Helm can be found [here](https://helm.sh/docs/intro/install/).

#### **IBM Entitlement**

Container images for both runtime and models are located the IBM Entitled Registry, which requires an entitlement key.

Instructions for obtaining your [IBM entitlement API key](https://www.ibm.com/docs/en/cloud-paks/cp-data/4.5.x?topic=information-obtaining-your-entitlement-api-key).

### Installation

#### Dependencies

The chart is dependent on a pull secret for the registry or registries containing the Watson Runtime and Model images.

#### Configuration

Explanation of the [`values.yaml`](../helm-nlp/values.yaml)

- "componentName" - The Deployment and Services will be named using a combination of the Helm release, and this property.

- "serviceType" - The type of Kubernetes Service used to expose the watson-runtime container. Valid values are according to the Kuberenetes specification.

- "registries" - A list of all registries assosiated with the Deployment. At a minimum, there will be a registry from which to pull the watson-runtime container and IBM provided pretrained models. Additionally, there could be a separate registry containing custom models.

- "imagePullSecrets" - A list of pull secret names that the Deployment will reference. At a minimum, the pull secret for the IBM entitled registry/Artifactory should be provided. Additional pull secrets can be specified if there is a separate registry for custom models.

- "runtime" - Specifies which of the defined registries should be used to pull the watson-runtime container, and its image name/tag.

- "models" - A list of models to include in the Deployment, each specifies which of the defined registries should be used and the image names/tags.

#### Deployment Example

1. Login and create namespace

   ```sh
   oc login --token=sha256~xxx --server=https://xxx
   oc new-project watson-demo
    ```

2. Create a Secret with credentials to the entitled registry.

   ```sh
   oc create secret docker-registry \
   --docker-server=cp.icr.io \
   --docker-username=cp \
   --docker-password=<your IBM Entitlement Key> \
   ibm-entitlement-key
   ```

3. Install the chart

   Clone the repo which contains the helm chart.

   ```sh
   git clone https://github.com/cloud-native-toolkit/terraform-gitops-watson-nlp
   ```

   Clone the watson-automation repo to use a provided sample [values.yaml](../helm-nlp/values.yaml).

   ```sh
   git clone https://github.ibm.com/isv-assets/watson-automation
   ```

   You must edit the 1 file to accept the Watson NLP license by setting the following property:

   ```sh
   acceptLicense: true
   ```

   Copy your sample `values.yaml` and install the chart.

   ```sh
   cp watson-automation/helm-nlp/values.yaml terraform-gitops-watson-nlp/chart/watson-nlp/values.yaml
   cd terraform-gitops-watson-nlp/chart/watson-nlp
   helm install -f values.yaml watson-embedded .
   ```

   Verify the following components were created:

   ```sh
   oc get deployment/watson-embedded-watson-nlp
   oc get svc/watson-embedded-watson-nlp
   ```

   Wait for the pod to become ready, this can take approximately 5 minutes.

### Usage

Follow these instructions for [usage testing](../README.md#usage)

### Cleaning Up

Delete all resources with:

```sh
helm delete watson-embedded
```

## Documentation

For further information about Watson NLP, refer to the [Watson Runtime documentation](https://pages.github.ibm.com/ai-foundation/watson-runtime/current/). **TO BE DONE**
