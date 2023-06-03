# gitops-for-organizations-aws


* Se logger sur RH ACM

# Bootstrap Cluster
```shell
until oc apply -k https://github.com/lcolagio/gitops-for-organizations-aws/bootstrap/overlays/default/; do sleep 5; done
```

# Configure groups and rbac

<!-- ```shell
oc apply -k https://github.com/lcolagio/gitops-for-organizations-aws/clusters/acm-hub.redhat.com/groups
```

# Configure cluster
```shell
oc apply -k https://github.com/lcolagio/gitops-for-organizations-aws/clusters/acm-hub.redhat.com/applications
``` -->
