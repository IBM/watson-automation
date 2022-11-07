# Development

## Development Workflow

### **Change Helm Chart**

1. Change chart in [_cloud-native-toolkit/terraform-gitops-watson-nlp_](https://github.com/cloud-native-toolkit/terraform-gitops-watson-nlp/tree/main/chart/watson-nlp)
2. Change "version" in `/terraform-gitops-watson-nlp/chart/watson-nlp/Chart.yaml`
3. Commit changes to _cloud-native-toolkit/terraform-gitops-watson-nlp_

### **Change Helm Chart Docs**

1. Update [`IBM/watson-automation/helm-nlp/values.yaml`](https://github.com/IBM/watson-automation/blob/main/helm-nlp/values.yaml) if new properties have been added, or existing default values need changing
2. Update [`IBM/watson-automation/documentation/NLPHelmChart.md`](https://github.com/IBM/watson-automation/blob/main/documentation/NLPHelmChart.md) if an explanation of new properties is required
3. Commit changes to _IBM/watson-automation_

### **Change Terraform Module**

1. Change the Terraform module in _cloud-native-toolkit/terraform-gitops-watson-nlp_ (optional), for example when input `variables.main.tf` need to be changed. Commit changes to _terraform-gitops-watson-nlp_.
2. Verify "current" tag and tag the new Terraform module version in _cloud-native-toolkit/terraform-gitops-watson-nlp_ (`git tag v0.0.n` and `git push origin v0.0.n`)
3. Via GitHub.com, create a release in *cloud-native-toolkit/terraform-gitops-watson-nl*p, with the same name as the tag "v0.0.n"

### **Change Example BOMs**

1. Update "version" in [`IBM/watson-automation/roks-new-nlp/bom.yaml`](https://github.com/IBM/watson-automation/blob/main/roks-new-nlp/bom.yaml)
2. Update "version" in [`IBM/watson-automation/roks-existing-nlp/bom.yaml`](https://github.com/IBM/watson-automation/blob/main/roks-existing-nlp/bom.yaml)
3. Commit changes to _IBM/watson-automation_

### **Test new Terraform Module Version**

1. Remove "terraform-gitops-watson-nlp" module in `IBM/watson-automation/roks-new-nlp/bom.xml`
2. Remove NLP files in GitOps repo. The easiest way to delete these files is to switch to the GitHub.com to GitHub.dev (or press '.'), to load the web IDE.
   - `watson-demo/blob/main/argocd/2-services/cluster/default/base/watson-demo-watson-nlp.yaml`
   - `watson-demo/tree/main/payload/2-services/namespace/watson-demo/watson-nlp/\*`
   - `watson-demo/blob/main/argocd/2-services/cluster/default/kustomization.yaml`
3. Run `iascable build`, `sh launch.sh` and `sh apply.sh`
4. Add "terraform-gitops-watson-nlp" module in `IBM/watson-automation/roks-new-nlp/bom.xml`
5. Run `iascable build`, `sh launch.sh` and `sh apply.sh`
