# Configure IDP HTPasswd

## Create htpasswd file

```shell
echo "Creating htpasswd file"

htpasswd -c -b -B htpasswd opentlc-mgr yyy
htpasswd -b -B htpasswd admin yyy
htpasswd -b -B htpasswd admin1 yyy
htpasswd -b -B htpasswd admin2 yyy
htpasswd -b -B htpasswd admin3 yyy

htpasswd -b -B htpasswd developer xxx

htpasswd -b -B htpasswd john xxx
htpasswd -b -B htpasswd paul xxx
htpasswd -b -B htpasswd ringo xxx
htpasswd -b -B htpasswd george xxx
htpasswd -b -B htpasswd pete xxx

htpasswd -b -B htpasswd andrew xxx
htpasswd -b -B htpasswd karla xxx
htpasswd -b -B htpasswd marina xxx

htpasswd -b -B htpasswd user1 xxx
htpasswd -b -B htpasswd user2 xxx
htpasswd -b -B htpasswd user3 xxx

htpasswd -b -B htpasswd dev1 xxx
htpasswd -b -B htpasswd dev2 xxx
htpasswd -b -B htpasswd dev3 xxx

htpasswd -b -B htpasswd ops1 xxx
htpasswd -b -B htpasswd ops2 xxx
htpasswd -b -B htpasswd ops3 xxx

htpasswd -b -B htpasswd manager1 xxx
htpasswd -b -B htpasswd manager2 xxx
htpasswd -b -B htpasswd manager3 xxx

htpasswd -b -B htpasswd dev xxx
htpasswd -b -B htpasswd qua xxx
htpasswd -b -B htpasswd prd xxx

cat htpasswd | base64 -w 0

# Solution 1
oc delete secret  htpasswd-secret -n openshift-config
oc create secret generic htpasswd-secret --from-file htpasswd -n openshift-config

# Solution 2 with KubeSeal
cat << EOF >htpasswd-secret.yaml
kind: Secret
apiVersion: v1
metadata:
  name: htpasswd-secret
  namespace: openshift-config
data:
  htpasswd: >-
    $(cat htpasswd | base64 -w 0)
type: Opaque
EOF

oc delete secret  htpasswd-secret -n openshift-config
kubeseal --cert "./${PUBLICKEY}" --scope cluster-wide < htpasswd-secret.yaml | kubectl apply -f-


# Apply htpasswd-secret config
cat << EOF | oc apply -f -
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: htpasswd
    challenge: true
    login: true
    mappingMethod: claim
    type: HTPasswd
    htpasswd:
      fileData:
        name: htpasswd-secret
EOF
```

## Add Group with Users

```shell
cat << EOF | oc apply -f -
apiVersion: user.openshift.io/v1
kind: Group
metadata:
  name: cluster-admins
users:
- opentlc-mgr
- admin
- admin1
- admin2
- admin3
EOF

cat << EOF | oc apply -f -
apiVersion: user.openshift.io/v1
kind: Group
metadata:
  name: users
users:
- user0
- user1
- user2
- john
- paul
- ringo
- george
- pete
- andrew
- karla
- marina
EOF
```


## Add CLusterRole and Role

```shell
oc adm policy add-cluster-role-to-group cluster-admin cluster-admin
oc adm policy add-cluster-role-to-user cluster-admin admin
oc adm policy add-cluster-role-to-user cluster-admin opentlc-mgr
```
