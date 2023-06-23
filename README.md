
## Install Managelment Hub Cluster via Red Hat Demo Platform
* https://demo.redhat.com/catalog?item=babylon-catalog-prod/sandboxes-gpte.sandbox-ocp.prod

## Copy your Master Key to encrypt Secret via SealedSecret

```shell
export BASTION=lab-user@bastion.grjm6.sandbox1844.opentlc.com
scp $HOME/.ssh/sealed-secrets-from-gitops.* $BASTION:/home/lab-user
```

## Connect to Bastion and generate SealedSecret for Cloud provider AWS

```shell
ssh $BASTION
```

## On Git update conf.yam and provision.yaml
* search and Repplace sandboxxxx by sandboxYYYY

## On Git update sealedsecret-aws.yaml

* Generate SealedSecret for aws credential
```shell

## Install tools and Cli

```shell
wget https://github.com/mikefarah/yq/releases/download/v4.34.1/yq_linux_amd64
mv yq_linux_amd64 yq
chmod +x yq
sudo mv yq /usr/bin/yq

wget https://mirror.openshift.com/pub/openshift-v4/clients/helm/latest/helm-linux-amd64
mv helm-linux-amd64 helm
chmod +x helm
sudo mv helm /usr/bin/helm
helm version

version=0.19.1
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v${version}/kubeseal-${version}-linux-amd64.tar.gz
tar xfz kubeseal-${version}-linux-amd64.tar.gz
sudo install -m 755 kubeseal /usr/local/bin/kubeseal
kubeseal --version
```

## Encrypt with sealed-Secret

```shell
SECRET=aws-creds-secret

cat << EOF >xxx-aws-creds-secret.yaml
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: xxx-aws-creds
  namespace: xxx
stringData:
  aws_access_key_id: xxxx
  aws_secret_access_key: xxxx
EOF

kubeseal --cert "./${PUBLICKEY}" --scope cluster-wide < xxx-aws-creds-secret.yaml >xxx-aws-creds-sealed-secret.json
yq  xxx-aws-creds-sealed-secret.json -oy > xxx-aws-creds-sealed-secret.yaml
cat xxx-aws-creds-sealed-secret.yaml
```

* Update encryptedData in sealedsecret-aws.yaml

```shell
  encryptedData:
    aws_access_key_id: xxx
    aws_secret_access_key: xxx
```

## Update sealed secrets key

```shell
export NAMESPACE="sealed-secrets"
export PRIVATEKEY="sealed-secrets-from-gitops.key"
export PUBLICKEY="sealed-secrets-from-gitops.crt"
export SECRETNAME="sealed-secrets-from-gitops"

oc new-project "$NAMESPACE"

kubectl -n "$NAMESPACE" create secret tls "$SECRETNAME" --cert="$PUBLICKEY" --key="$PRIVATEKEY"
kubectl -n "$NAMESPACE" label secret "$SECRETNAME" sealedsecrets.bitnami.com/sealed-secrets-key=active
```

## Bootstrap RH ACM Cluster

```shell
until oc apply -k https://github.com/lcolagio/gitops-for-organizations-aws/bootstrap/overlays/default/; do sleep 5; done
```


