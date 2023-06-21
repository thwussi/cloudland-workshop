# GitOps Workshop 

## Tools and Setup

https://medium.com/geekculture/50-kubernetes-tools-6acb01d0a876

### Local Kubernetes
Docker Desktop  
Rancher Desktop  
minikube  
colima (Mac)  

### Setup Colima (Mac)

```
colima start --edit
```
oder 
```
colima start --memory 12 --cpu 4 --disk 10 --network-address --kubernetes
```
Nützliche Kommandos
```
colima status
colima delete
```
### cmd-tools
kubectl  
https://medium.com/merapar/fixing-bash-autocompletion-on-macos-for-kubectl-and-kops-e87f019652e8

#### Autovervollständigung Mac
brew install bash-completion

Dann folgende Snippets in .bashrc oder .profile:  
```
if [ -f $(brew --prefix)/etc/bash_completion ]; then 
. $(brew --prefix)/etc/bash_completion
fi
```
Autocompletion kubectl with kubecolor
```
source <(k completion bash)
```
Autocompletion stern
```
source <(stern --completion=bash)
```
Autocompletion flux:  
```
command -v flux >/dev/null && . <(flux completion bash)
```

#### PS1 Prompt Decoration
Dazu mit brew (oder andere Möglichkeit) das kube-ps1.sh Skript installieren
```
brew install kube-ps1
```
Dann in .bashrc oder .profile folgendes Snippet einfügen (GitBranch ist optional):  
```
parse_git_branch() {
   git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/[\1]/'
}
source "/opt/homebrew/opt/kube-ps1/share/kube-ps1.sh"
PS1='[\u@\h \W $(kube_ps1)|\[\033[33m\]$(parse_git_branch)\[\033[00\]]$ '
```

kubecolor  
stern  
lens
k9s  
kube-ps1.sh  
GitOps Tools für VSCode von Weave

# Install
Verschiedene Varianten
direkt per flux bootstrap
Oder Sourcen generieren

flux install --namespace=flux-system --components-extra="image-reflector-controller,image-automation-controller" --export > 

## Themen
Install
Config
Version 2.0 Vorstellung
Sharding
Security
Mutli-Tenancy
Multi-Cluster

Lessons Learned:
healthchecks blockieren queue -> Sharding

# Flux Essentials

## Bootstraping

### Über CLI
Ausführen des Bootstrap das Repository muss vorhanden sein
```
  flux bootstrap git --url=https://example.com/repository.git --password=<password> --path=clusters/my-cluster
```


### Manuell
Erstellen der Dateien mit der CLI
```
flux install --export --components-extra="image-reflector-controller,image-automation-controller" > gotk-components.yaml
```

Ein Secret anlegen für Zugriff auf GitRepo
Eine Resource GitRepository anlegen für Zugriff auf das Reporitory wo die config des Clusters abliegt.

TODO Sourcecode

Eine Resource Kustomization anlegen die dafür sorgt das Flux aktuell bleibt.

TODO Sourcecode


# Update von Flux 

Erstellen der Dateien über flux-CLI:
```
flux install --export --components-extra="image-reflector-controller,image-automation-controller" > gotk-components.yaml
```
