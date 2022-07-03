# Codefresh GitOps Certification examples

This repository contains examples for the ArgoCD/GitOps
certification workshops.

Take the certification yourself at https://codefresh.io/courses/get-gitops-certified/

---

### Notes 
#### 1. argocd-repo-server 
- Responsible for cloning Git repository, keeping it up to date and Generating Manifests using the appropriate tool

#### 2. argocd-application-controller
- uses `argocd-repo-server` to get generated Manifests and Kubernetes API Server to get actual cluster state
- Polls Git every `3 minutes` but can be changed by modifying `timeout.reconcilliation` in `argocd-cm`
- For HA in case there's too much memory being used, the number of replicas can be incremented and the environment variable `ARGOCD_CONTROLLER_REPLICAS` should match that number

---

### Sync Strategy parameters 
1. Manual or automatic sync 
2. Auto pruning of resources (Automatic Sync)
3. Self heal of cluster (Automatic Sync)

- If Sync is set to automatic then argo applies latest changes on GitHub 
- Auto-Pruning defines what should be done when files are removed from Git
  - If enabled ArogCD will remove respective resources in cluster as well 
  - If Disabled ArgoCD Will never remove anything from the cluster
- Self-heal defines the behavior of argo when changes are made directly on the cluster 
  - If enabled then ArgoCD Will revert the cluster changes and stick to the defined state in Git (Which is the best practice)

---

### Recommended approach 
- All ArgoCD related resources are deployed Via ArgoCD
- All ArgoCD related `Applications` or `ApplicationSet` Definition are allowed to only be deployed in the namespace where ArgoCD is deployed to
- Use Webhooks in tandem with ArgoCD Reconciliation
- This way should the webhooks fail or you are operating within an environment behind a firewall - the webhook that cannot reach a deployment will continue as expected
