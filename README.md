# External NFS client provisioner

It will deploy nfs-client provisioner and create a storage class name **nfs-client**. You need [kustomize](https://kustomize.io/) to deploy the resources. There should be a NFS server already configured. There are two ways to deploy the provisioner

## 1. Cloning The Repo

clone the repo and change the value of `nfsServer` and `nfsPath` in the [kustomization.yaml](resources/kustomization.yaml)

then `kustomize build . | kubectl apply -f -` in the root folder.

## 2. Kustomize patch

Make a place to work:

```console
DEMO_HOME=$(mktemp -d)
```

create a patch file

```console
cat <<EOF >$DEMO_HOME/patch.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: not-important
data:
  nfsServer: YOUR_NFS_SERVER_IP_OR_HOSTNAME
  nfsPath: NFS_PATH_THE_SERVER_ALREADY_DEFINE
EOF
```

define a kustomization file that specifies your patch.

```console
cat <<EOF >$DEMO_HOME/kustomization.yaml
resources:
- github.com/ffoysal/nfs-client-provisioner
patches:
- path: patch.yaml
  target:
    kind: ConfigMap
    name: nfs-provisioner-client-info
EOF
```

then run kustomize

```console
kustomize build $DEMO_HOME | kubectl apply -f -
```

### Reference

1. https://github.com/helm/charts/tree/master/stable/nfs-client-provisioner
