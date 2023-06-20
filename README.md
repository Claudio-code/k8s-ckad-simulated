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
