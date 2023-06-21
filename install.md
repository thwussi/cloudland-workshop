# Flux Install

## Bootstrapping

Für das Bereitstellen gibt es verschiedene Möglichkeiten. Diese können je nach eigener Umgebung verwendet werden.

### Installation Flux CLI

Um das Setup zu machen muss zunächst die CLI installiert werden. Das kann per diverse Paketmanager erfolgen oder per Curl

Brew:  

```
brew install fluxcd/tap/flux
```
oder spezifische Version:
```
brew install fluxcd/tap/flux@0.41
```
Sollte eine version installiert sein dann switchen mit
```
brew unlink flux
brew link flux@0.41
```

Curl:  
```
sudo curl -s https://fluxcd.io/install.sh | sudo bash
```
Spezifische Version:  
```
sudo curl -s https://fluxcd.io/install.sh | sudo FLUX_VERSION=0.41.0 bash
```

### Check vor Installation

Ausführen eines Checks ob das System für Flux korrekt eingerichtet ist
```
flux check --pre
```

### Über CLI
Ausführen des Bootstrap über die Flux CLI. Ein GIT-Repository muss vorhanden sein
```
  flux bootstrap git --url=https://bitbucket.org/slixrepo/cloudland-workshop.git --password=<password> --components-extra="image-reflector-controller,image-automation-controller" --path=clusters/my-cluster --branch=<userbranch>

  flux bootstrap git --url=https://bitbucket.org/slixrepo/cloudland-workshop.git --components-extra="image-reflector-controller,image-automation-controller" --path=clusters/my-cluster --branch=test1 --token-auth=true --token ATCTT3xFfGN01M_vkcvvCTqoDbuAe0aNqrO_98xJtaMr6DXCz4d1_VE9sU4YNf_HDGdcpqLwUBJ1kvtq376pFPlHLpHxwA83QlctrFWGhVvqTqfkZTLN1Qo2WwTYVMFcaC_Foufwq4HAhrJEkfnyrIqtObxfkKFHICMydhI4pX03mllUMqceLFk=B5AF78D2

```
```
ATCTT3xFfGN01M_vkcvvCTqoDbuAe0aNqrO_98xJtaMr6DXCz4d1_VE9sU4YNf_HDGdcpqLwUBJ1kvtq376pFPlHLpHxwA83QlctrFWGhVvqTqfkZTLN1Qo2WwTYVMFcaC_Foufwq4HAhrJEkfnyrIqtObxfkKFHICMydhI4pX03mllUMqceLFk=B5AF78D2
```
### Manuell
Erstellen der Dateien mit der CLI
```
flux install --export --components-extra="image-reflector-controller,image-automation-controller" > gotk-components.yaml
```
Die Artefakte werden direkt aus der CLI heraus generiert. Um eine spezielle Version zu erhalten, muss die CLI in der entsprechenden Version installiert werden.

Ein Secret anlegen für Zugriff auf GitRepo
Eine Resource GitRepository anlegen für Zugriff auf das Reporitory wo die config des Clusters abliegt.
```
apiVersion: v1
kind: Secret
metadata:
  name: flux-system
  namespace: flux-system
type: Opaque
stringData:
  username: <username>
  password: <password>
---
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: flux-system
  namespace: flux-system
spec:
  interval: 1m0s
  ref:
    branch: master
  secretRef:
    name: flux-system
  url: <git-repo-url>/fluxClusterConfig.git
```

Eine Resource Kustomization anlegen die dafür sorgt das Flux aktuell bleibt.
```
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- gotk-components.yaml
- gotk-sync.yaml

namespace: flux-system
```


# Update von Flux 

Erstellen der Dateien über flux-CLI:
```
flux install --export --components-extra="image-reflector-controller,image-automation-controller" > gotk-components.yaml
```

Die aktualisierten Dateien einchecken und von Flux syncen lassen. Vorher prüfen ob es ggf Breaking-Changes gibt und diese in der ClusterConfig anpassen

# Anpassungen an den Artefakten
Es können per patch und andere Mittel auch Anpassungen an den Fluxartefakten durchgeführt werden.