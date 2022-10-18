# Development

## Development Workflow

**Change Helm Chart**

1.	Change chart in nheidloff/test
2.	Change version in nheidloff/test/Chart.yaml
3.	Change version in nheidloff/test-charts/stable/test/Chart.yaml
4.	Copy chart to nheidloff/test-charts/stable/test (Chart.yaml, values.yaml, templates)
5.	Run `helm package .` in test-charts/stable/test and move to test-charts/tgz folder
6.	Commit changed files to test repo
7.	Update the tgz link and version in nheidloff/test-charts/index.yaml
8.	Commit changed files to test-charts repo
9.	In test-charts, verify current tag with `git tag`
10.	Tag test in nheidloff/test-charts (`git tag stable/test/v0.0.n` and `git push origin stable/test/v0.0.n`)
11.	Via GitHub.com, create a release with the same name
12.	Add tgz file to cloud-native-toolkit/terraform-gitops-watson-nlp/charts/watson-nlp/charts
13.	Change helm version and dependencies helm version in terraform-gitops-watson-nlp/charts/watson-nlp/charts/Chart.yaml
14.	Commit changes to terraform-gitops-watson-nlp
15.	to be done: add manual testing step
16.	Continue with 'Change Terraform Module'

**Change Terraform Module**

1.	Change the Terraform module in cloud-native-toolkit/terraform-gitops-watson-nlp (optional), for example when input variables.main.tf need to be changed
2.	Verify current tag and tag the new Terraform module version in cloud-native-toolkit/terraform-gitops-watson-nlp (`git tag v0.0.n` and `git push origin v0.0.n`)
3.	Via GitHub.com, create a release in cloud-native-toolkit/terraform-gitops-watson-nlp
4.	Update the version in isv-assets/watson-embedded-catalog/catalog.yaml
5.	Commit changes to repo Watson-embedded-catalog
6.	Update version in isv-assets/watson-boms/roks-new-nlp/bom.xml
7.	Update version in isv-assets/watson-boms/roks-existing-nlp/bom.xml
8.	Commit changes to repo Watson-boms
9.	Continue with 'Test new Terraform Module Version'

**Test new Terraform Module Version**

1.	Remove module in isv-assets/watson-boms/roks-new-nlp/bom.xml
2.	Remove NLP files in GitOps repo: watson-demo/blob/main/argocd/2-services/cluster/default/base/watson-demo-watson-nlp.yaml, watson-demo/tree/main/payload/2-services/namespace/watson-demo/watson-nlp/* and change watson-demo/blob/main/argocd/2-services/cluster/default/kustomization.yaml
3.	Run `iascable build`, `sh launch.sh` and `sh apply.sh`
4.	Add module in isv-assets/watson-boms/roks-new-nlp/bom.xml
5.	Run `iascable build`, `sh launch.sh` and `sh apply.sh`

