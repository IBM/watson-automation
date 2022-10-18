# Automation for IBM Watson Deployments - Advanced Documentation

This document describes in more detail how to use the toolkit and the Watson NLP module.

* [Watson NLP Images and Registries](#watson-nlp-images-and-registries)
* [Usage of existing Clusters](#usage-of-existing-clusters)
* [Docker Alternatives](#docker-alternatives)
* [Best Practices](#best-practices)


## Watson NLP Images and Registries

At a minimum you need the Watson NLP runtime image. The NLP runtime container runs in the Watson NLP pod at runtime.

Additionally you have to access **TO BE DONE** ['model images'](https://ibmdocs-test.mybluemix.net/docs/en/watson-libraries?topic=home-working-nlp-models) which are necessary for different functionality provided by Watson NLP. There are two types of model images:

* Stock models provided out of the box by Watson NLP
* Custom models provided by consumers

The model containers run as Kubernetes initContainers. They are triggered when pods are created. Their purpose is to put the model artifacts on disc so that the Watson NLP runtime container can access them. Once they have done this, these containers terminate.

Images reside in registries which are typically protected. Pull secrets need to be provided to access these registries. [Sealed Secrets for Kubernetes](https://github.com/bitnami-labs/sealed-secrets) is used to protect the secrets.

There can be multiple registries (N >= 1) and multiple secrets (M >= 0). Registries can use secrets, but don't have to (N > M). There needs to be one registry to access the NLP runtime image which is usually stored in a protected registry.

The configuration is done in two files:

1. bom.yaml
2. credentials.yaml

Pull secrets have to contain the following information: 

1. Secret name 
  * Defined in the 'imagePullSecrets' array in the BOM 
2. Registry URL
  * Defined in the 'registries' array in the BOM
3. Registry user name
  * Defined in the 'registryUserNames' array in the BOM
  * The 'registry' name needs to map to the same name under registries 
4. Registry password 
  * Defined in 'TF_VAR_terraform-gitops-watson-nlp_registry_credentials' in credentials.properties 
  * This variable can include a comma delimited list of registry passwords/tokens
  * For multiple secrets the order needs to be the same one as in the BOM for 'registryUserNames' 


## Usage of existing Clusters

Follow the instructions below to deploy the modules against an existing cluster.

```
cd roks-existing-nlp
cp output/credentials-template.properties output/credentials.properties
vi output/credentials.properties
```

Fill the values with the ones from your own OpenShift cluster.

```
export TF_VAR_gitops_repo_token=___your-github-token____
export TF_VAR_terraform_gitops_watson_nlp_registry_credentials=___your-registry-credentials___
export TF_VAR_server_url=https://cXXX-e.yy-zz.containers.cloud.ibm.com:30364
export TF_VAR_cluster_login_token=sha256~xxx
```

To obtain 'TF_VAR_server_url' and 'TF_VAR_cluster_login_token' open the OpenShift console, click on your user name in the upper right corner and choose 'copy login command'.

Follow the [instructions](../README.md#instructions) how to deploy Watson NLP to your existing cluster. There are only two differences:

1. The credentials.properties files as just described
2. Use the directory 'roks-existing-nlp' rather than 'roks-new-nlp'


## Docker Alternatives

If you cannot or don't want to use Docker, we have instructions how to use [Multipass](https://multipass.run/). See the [documentation](https://github.com/cloud-native-toolkit/automation-solutions/blob/main/common-files/RUNTIMES.md) for more details on Multipass.

**Instructions**

```sh
$ brew install --cask multipass
$ curl https://raw.githubusercontent.com/cloud-native-toolkit/sre-utilities/main/cloud-init/cli-tools.yaml --output cli-tools.yaml
$ multipass launch --name cli-tools --cloud-init ./cli-tools.yaml
$ multipass mount $PWD cli-tools:/automation
$ multipass shell cli-tools
```

In the VM invoke these commands:

```sh
$ cd ../../automation
$ source credentials.properties
$ echo $TF_VAR_ibmcloud_api_key
$ cd cluster-with-watson-nlp 
$ ./apply.sh
```

Here are some best practices how to run Multipass:

* The Multipass VM option will not currently work with Apple M1 based hardware.
* Due to a current bug in iascable, when performing command `source credentials.properties` (applies only to Multipass), you will encounter an error `not a valid identifier` for some variable names. You can safely ignore this as you will be prompted later to enter a value.
* When using Multipass, the launch script is not used.  Enter the VM and use apply.sh
* When creating the VM with Multipass, if you see the following error on MacOS: 'cli-tools: timed out waiting for response'. It is neccessary to disable the MacOS firewall. 


## Best Practices

Here are a couple of best practices and topics you should be aware of, especially when sharing the same IBM Cloud organizations.

* Specify your own resource group, rather than using the default
* Be aware that each IBM Cloud region has a VPC limit of ten. Ensure the region you select has sufficient capacity.
