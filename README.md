# k8s-ckad-simulated

### Change k8s editor to nano


```bash

echo "source <(kubectl completion bash)" >> ~/.bashrc

autoload -U +X bashcompinit && bashcompinit
autoload -U +X compinit && compinit

export KUBE_EDITOR=nano

alias la='ls -A'
alias ll='ls -alF'
alias l='ls -CF'

alias k=kubectl
complete -F __start_kubectl k
alias kgp='kubectl get pod'
alias kd='kubectl describe'
export d="--dry-run=client -o yaml"

nano .nanorc

set tabsize 2
set tabstospaces

```

### Force delete/replace pod

```bash

## to delete
k delete po pod-name --force --grace-period 0

## to replace
k replace -f ./manifest.yaml --force --grace-period 0

```

### Create pod and generate manifest

```bash

k run nginx --image nginx --port 80 --dry-run=client -o yaml > manifest-pod.yaml

```

### Get info of pod and print manifest in terminal

```bash

k get po pod-name -o yaml

```

### Enter in pod with two containers

```bash

k exec -it nginx -c nginx -- bash

```

### Get all info of specific pod

```bash

k describe po nginx

```

### Find how make configs in manifest with terminal doc

```bash

### Complete info
k explain pod.spec.containers

### Just examples
k explain pod.spec.containers --recursive

```

### Force delete pod

```bash
k delete po my-pod nginx sleep --force --grace-period 0
```

### Create deployment more fast

```bash
k create deployment nginx --image nginx -o yaml --dry-run=client > replica-set-two.yaml
```

### How edit deployment when it is running

```bash
k edit deployments.app first-deploy
## OR
k edit deploy first-deploy
```

### How set scale in deployment running

```bash
k scale deployment --replica 6 first-deploy
## OR
k scale deploy --replica 1 first-deploy 
```
### How see all changes in deployment

```bash
k rollout history deployment first-deploy
## OR
k rollout history deploy first-deploy
```

### Make rollback to previous version

```bash
k rollout undo --to-revision 1 deploy first-deploy
```

---

## PersistentVolume

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: firstPV
spec:
  storageClassName: ""
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/home"
```

### PersistentVolume to test

> spec

#### Capacity:
Specific storage size of persistence volume ( Gi Mi )

```yaml
capacity:
  storage: 1Gi
```

#### VolumeMode:

```yaml
volumeMode: [Filesystem | Block]
```

#### AccessMode:

```yaml
accessMode:
  - [ReadWriteOnce | ReadOnlyMany | ReadWriteMany]
```

- ReadWriteOnce: The volume could it be mounted with read and write to one node.
- ReadOnlyMany: The volume could it be mounted with only read to many nodes.
- ReadWriteMany: The volume could it be mounted with read and write to many nodes.

#### Reclaim Policy
> It option has the objective the action that the cluster will make after exclude PVC.
```yaml
persistentVolumeReclaimPolicy: [Retain | Recycle | Delete]
```
- Retain: Need one manual action.
- Recycle: basic (rm -rf /volumeDir/*).
- Delete: Using AWS EBS, GCE PD, Azure Disk or OpenStack Cinder the volume as deleted. 

#### Class
> Determine which will be the storageClass to PV will use.

```yaml
storageClassName: "Slow"
```
#### HostPath:
> One volume hostPath mount one file or directory of filesystem of node of host in your pod.
```yaml
hostPath:
  path:
  type: [FileOrCreate | DirectoryOrCreate]
```

- FileOrCreate: If nothing exist in path provided, one file empty will be created there as need with permission like 0644, having the same group and properties of kubelet. 
- DirectoryOrCreate: If no exist in path provided, one directory empty will be created there as need with defined permission like 0755, having the same group and property of kubelet.
