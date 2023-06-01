# gitops-for-organizations-aws


* Se logger sur RH ACM

* Installer OpenShift GitOps


* Configure groups and rbac

```shell
oc apply -k https://github.com/lcolagio/gitops-for-organizations-aws/clusters/acm-hub.redhat.com/groups
```

* Configure cluster
```shell
oc apply -k https://github.com/lcolagio/gitops-for-organizations-aws/clusters/acm-hub.redhat.com/applications
```
