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


## Config nano
nano .nanorc

set tabsize 2
set tabstospaces

## Config vim

vim .vimrc

set expandtab
set tabstop=2
set shiftwidth=2
syntax on
```

### Command to create resource quota in kubectl

```bash
kubectl create quota quota2 --hard pod=2,cpu=2,memory=1Gi --dry-run=client -o yaml
```

### Command to found resource using api-resource in kubectl

```bash
kubectl api-resources -o wide | grep <resource-name>
```

### Command to verify if user was permission in many different resources in k8s

```bash
kubectl auth can-i get secrets --as system:serviceaccount:default:reader
```

### Create Role with command

```bash
kubectl create role leitor2 --verb get,watch, list --resource secrets --dry-run=client -o yaml
```

### Create ClusterRole with command

```bash
kubectl create clusterrole readerTwo --verb get, watch, list --resource secrets --dry-run=client -o yaml
```

### Create ServiceAccount

```bash
kubectl create sa reader --dry-run=client -o yaml
### Create Ingress in kubectl command line

```bash
## ingress by path
k create ingress ingress2 --class nginx --rule="/foo=foo-nginx:80" --dry-run=client -o yaml

## ingress by host
k create ingress ingress2 --class nginx --rule="foo.possuidao.xyy/foo=foo-nginx:80" --dry-run=client -o yaml
```

### Filter outputs in when get infos of kubectl

```bash

k get pods -o=name > /tmp/pods

```

### Force delete/replace pod

```bash

## to delete
k delete po pod-name --force --grace-period 0

## to replace
k replace -f ./manifest.yaml --force --grace-period 0

```

### Create deployment and generate manifest

```bash
k create deployment firstdeploy --image=nginx --dry-run=client -o yaml > firstdeploy.yaml
```

### Create pod and generate manifest

```bash

k run nginx --image nginx --port 80 --dry-run=client -o yaml > manifest-pod.yaml

```

### Create service in command line

```bash
k expose pod pod-name --target-port 80 --port 80
```

### Pass command to execute in pod

```bash
k exec -it second-pod -- curl 10.42.0.2
```

### Get info of pod and print manifest in terminal

```bash

k get po pod-name -o yaml

```

### Delete all pods

```bash

k delete --all pods

# OR

k delete --all po

# OR OF SPECIFIC NAMESPACE

k delete --all po --namespace=default

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

### Generate manifest of pod

```bash
k run nginx --image nginx --dry-run=client -o yaml > pod-pvc.yaml
```

---

## PersistentVolumeClaim

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-first
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

### PersistentVolumeClaim to test

> spec

#### accessModes

```yaml
accessModes:
  - [ReadWriteOnce | ReadOnlyMany | ReadWriteMany]
```

- ReadWriteOnce: The volume can mount with read and write by only node.
- ReadOnlyMany: The volume can mount only read to many nodes.
- ReadWriteMany: The volume can mount with read and write to many nodes.

#### class

> Determine which will be the storageClass to PV will use.

```yaml
storageClassName: "Slow"
```

#### resources

> Like pods can solicit specifics quantities of one resource. the solicitation is to storage.

```yaml
resources:
  requests:
    storage: 9Gi
```

#### volumeName

> Force the use specific PV

```yaml
volumeName: firstPV
```

#### selector

> The PVCS can specific one "label selector" to filter further process to one PV

```yaml
selector: # [matchLabels | matchExpressions]
  matchLabels:
    release: "dev"
  mathExpression:
    - { key: enviroment, operator: In, values: [dev] }
```

- matchLabels: The volume must have one label with it value.
- matchExpressions: One list of requirements made specific key, list of values and operator to relate key and operator. The operators valid include in, notIn, Exists and DoesNotExists.

#### Example in pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
  namespace: day1
spec:
  containers:
    - image: nginx
      name: nginx
      volumeMounts:
        - name: usingpvc
          mountPath: /data
  dnsPolicy: ClusterFirst
  restartPolicy: Always
  volumes:
    - name: usingpvc
      persistentVolumeClaim:
        claimName: pvc-first
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

#### Capacity

Specific storage size of persistence volume ( Gi Mi )

```yaml
capacity:
  storage: 1Gi
```

#### VolumeMode

```yaml
volumeMode: [Filesystem | Block]
```

#### AccessMode

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
- Recycle: basic (rm -rf /volumeDir/\*).
- Delete: Using AWS EBS, GCE PD, Azure Disk or OpenStack Cinder the volume as deleted.

#### Class

> Determine which will be the storageClass to PV will use.

```yaml
storageClassName: "Slow"
```

#### HostPath

> One volume hostPath mount one file or directory of filesystem of node of host in your pod.

```yaml
hostPath:
  path:
  type: [FileOrCreate | DirectoryOrCreate]
```

- FileOrCreate: If nothing exist in path provided, one file empty will be created there as need with permission like 0644, having the same group and properties of kubelet.
- DirectoryOrCreate: If no exist in path provided, one directory empty will be created there as need with defined permission like 0755, having the same group and property of kubelet.

## ConfigMap and Secrets

### How fast create configMap

````yaml
k create configmap second-cm --from-literal=ip=22.2.2.22 --from-literal=server=web --dry-run=client -o yaml
## Secret

Secret encode all values in base64 you can test it encode using it command:

Encode:

```bash
  echo -n "senha" | base64
````

Decode:

```bash
  echo -n "dXNlcg==" | base64 -d
```

### Create secret from file

```bash
k create secret generic my-secret --from-file=./secrets.txt --from-file=./another-secrets.txt
```

### Create secret literal

```bash
k create secret generic my-secret --from-literal=user=admin --from-literal=pass=mysecretpass
```

### Create secret to docker register

```bash
k create secret docker-registry my-docker \
  --docker-username=admin \
  --docker-password=pass123 \
  --docker-email=admin@soneca.corp.com
```
