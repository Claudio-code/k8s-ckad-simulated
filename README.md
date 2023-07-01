# k8s-ckad-simulated

### Change k8s editor to nano


```bash

autoload -U +X bashcompinit && bashcompinit
autoload -U +X compinit && compinit

echo "source <(kubectl completion bash)" >> ~/.bashrc

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
