# gitops-for-organizations-aws
* Se logger sur RH ACM

# Bootstrap Cluster
```shell
until oc apply -k https://github.com/lcolagio/gitops-for-organizations-aws/bootstrap/overlays/default/; do sleep 5; done
```


