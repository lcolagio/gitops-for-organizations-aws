# gitops-for-organizations-aws



oc apply -k https://github.com/lcolagio/gitops-for-organizations-aws/clusters/acm-hub.redhat.com/applications/app-argocd.yaml
# oc apply -f https://raw.githubusercontent.com/lcolagio/gitops-for-organizations-aws/main/clusters/acm-hub.redhat.com/applications/kustomization.yaml


* Se logger sur RH ACM

oc login

```shell
oc apply -k https://github.com/lcolagio/gitops-for-organizations-aws/clusters/acm-hub.redhat.com/applications
```
