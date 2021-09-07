# ArgoCD Applications

Sensitive values are managed with the argocd-vault-plugin

The dev-prod split is managed using the dev/kustomize.yaml v.s. prod/kustomize.yaml files. These select different applications, and also modify the target-revision of the application CRs. (`dev` v.s. `master` respectively)

**NOTE:** Because the kustomization files require `../` you need to use `kustomize --load-restrictor LoadRestrictionsNone`.

## Use this to bootstrap AAW instances

Either deploy this manually, via terraform, or with whatever your "bootstrapping" process is for the cluster.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: aaw-manifests
  namespace: argocd
spec:
  destination:
    name: in-cluster
    namespace: daaas-system
  project: default
  source:
    repoURL: https://github.com/StatCan/aaw-argocd-applications
    # change this to `prod` to deploy prod instead 
    path: dev
    targetRevision: master
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

## Example

```yaml
# kustomize build prod --load-restrictor LoadRestrictionsNone
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: eck-daaas
  namespace: argocd
spec:
  destination:
    name: in-cluster
    namespace: daaas-system
  project: default
  source:
    path: daaas-system/eck/daaas
    repoURL: https://github.com/statcan/argocd-manifests
    targetRevision: master
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
---
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: minio-credential-injector
  namespace: daaas-system
spec:
  destination:
    name: in-cluster
    namespace: daaas-system
  project: platform
  source:
    path: daaas-system/minio-credential-injector
    repoURL: https://github.com/StatCan/argocd-manifests.git
    targetRevision: master
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```


```yaml
# kustomize build dev --load-restrictor LoadRestrictionsNone
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: eck-daaas
  namespace: argocd
spec:
  destination:
    name: in-cluster
    namespace: daaas-system
  project: default
  source:
    path: daaas-system/eck/daaas
    repoURL: https://github.com/statcan/argocd-manifests
    targetRevision: dev
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
---
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: minio-credential-injector
  namespace: daaas-system
spec:
  destination:
    name: in-cluster
    namespace: daaas-system
  project: platform
  source:
    path: daaas-system/minio-credential-injector
    repoURL: https://github.com/StatCan/argocd-manifests.git
    targetRevision: dev
  syncPolicy:
    automated:
      prune: true
      selfHeal: true

```
