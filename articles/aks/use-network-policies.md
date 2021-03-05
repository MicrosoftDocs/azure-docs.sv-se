---
title: Skydda Pod-trafik med nätverks princip
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skyddar trafik som flödar in och ut ur poddar med Kubernetes-nätverks principer i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 4b72c5551d6ed33deb4df40a60215aed8071141d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178906"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Skydda trafik mellan poddar med hjälp av nätverks principer i Azure Kubernetes service (AKS)

När du kör moderna, mikrotjänster-baserade program i Kubernetes vill du ofta styra vilka komponenter som kan kommunicera med varandra. Principen om minsta behörighet bör tillämpas på hur trafiken kan flöda mellan poddar i ett Azure Kubernetes service-kluster (AKS). Anta att du förmodligen vill blockera trafik direkt till Server dels program. Med funktionen *nätverks princip* i Kubernetes kan du definiera regler för inkommande och utgående trafik mellan poddar i ett kluster.

Den här artikeln visar hur du installerar nätverks princip motorn och skapar Kubernetes-nätverks principer för att styra trafik flödet mellan poddar i AKS. Nätverks principen bör endast användas för Linux-baserade noder och poddar i AKS.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.61 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

> [!TIP]
> Om du använde funktionen nätverks princip under för hands versionen av, rekommenderar vi att du [skapar ett nytt kluster](#create-an-aks-cluster-and-enable-network-policy).
> 
> Om du vill fortsätta att använda befintliga test kluster som använde nätverks principen under för hands versionen, uppgraderar du klustret till en ny Kubernetes-version för den senaste GA-versionen och distribuerar sedan följande YAML-manifest för att åtgärda krasching av mått servern och Kubernetes-instrumentpanelen. Den här korrigeringen krävs bara för kluster som använder Calico Network Policy Engine.
>
> Av säkerhets skäl bör du [läsa innehållet i det här yaml-manifestet][calico-aks-cleanup] för att förstå vad som distribueras till AKS-klustret.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Översikt över nätverks princip

Alla poddar i ett AKS-kluster kan skicka och ta emot trafik utan begränsningar som standard. För att förbättra säkerheten kan du definiera regler som styr flödet av trafik. Server dels program exponeras ofta enbart för de klient dels tjänster som krävs, till exempel. Eller, databas komponenter är bara tillgängliga för de program nivåer som ansluter till dem.

Nätverks principen är en Kubernetes-specifikation som definierar åtkomst principer för kommunikation mellan poddar. Med hjälp av nätverks principer definierar du en ordnad uppsättning regler för att skicka och ta emot trafik och tillämpa dem på en samling poddar som matchar en eller flera etikett väljare.

De här nätverks princip reglerna definieras som YAML-manifest. Nätverks principer kan inkluderas som en del av ett bredare manifest som även skapar en distribution eller tjänst.

### <a name="network-policy-options-in-aks"></a>Alternativ för nätverks princip i AKS

Azure erbjuder två sätt att implementera nätverks principer. Du väljer ett alternativ för nätverks princip när du skapar ett AKS-kluster. Det går inte att ändra princip alternativet när klustret har skapats:

* Azures egen implementering, som kallas *Azure-nätverks principer*.
* *Calico nätverks principer*, en nätverks-och nätverks säkerhets lösning med öppen källkod, som baseras på [Tiger][tigera],

Båda implementeringarna använder Linux- *program varan iptables* för att genomdriva de angivna principerna. Principer översätts till uppsättningar av tillåtna och otillåtna IP-par. Dessa par är sedan programmerade som IPTable filter regler.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Skillnader mellan Azure och Calico-principer och deras funktioner

| Funktion                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Plattformar som stöds                      | Linux                      | Linux, Windows Server 2019 (för hands version)  |
| Nätverks alternativ som stöds             | Azure-CNI                  | Azure-CNI (Windows Server 2019 och Linux) och Kubernetes (Linux)  |
| Efterlevnad med Kubernetes-specifikation | Alla princip typer som stöds |  Alla princip typer som stöds |
| Ytterligare funktioner                      | Inget                       | Utökad princip modell bestående av global nätverks princip, global nätverks uppsättning och värd slut punkt. Mer information om hur du använder `calicoctl` CLI för att hantera dessa utökade funktioner finns i [calicoctl User Reference][calicoctl]. |
| Support                                  | Stöds av support-och teknik teamet för Azure | Calico community-support. Mer information om ytterligare avgiftsbelagd support finns i [Support alternativ för Project Calico][calico-support]. |
| Loggning                                  | Regler som läggs till/tas bort i program varan iptables loggas på varje värd under */var/log/Azure-NPM.log* | Mer information finns i [Calico-komponent loggar][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Skapa ett AKS-kluster och aktivera nätverks princip

För att se nätverks principer i praktiken ska vi skapa och sedan expandera i en princip som definierar trafikflöde:

* Neka all trafik till pod.
* Tillåt trafik baserat på pod-etiketter.
* Tillåt trafik baserat på namn område.

Först ska vi skapa ett AKS-kluster som har stöd för nätverks principer.

> [!IMPORTANT]
>
> Funktionen nätverks princip kan bara aktive ras när klustret skapas. Du kan inte aktivera nätverks princip i ett befintligt AKS-kluster.

Om du vill använda Azure Network Policy måste du använda [Azure cni-plugin-programmet][azure-cni] och definiera ett eget virtuellt nätverk och undernät. Mer detaljerad information om hur du planerar ut de nödvändiga under näts intervallen finns i [Konfigurera avancerade nätverk][use-advanced-networking]. Calico nätverks princip kan användas med antingen samma Azure CNI-plugin-program eller med plugin-programmet Kubernetes CNI.

Följande exempel skript:

* Skapar ett virtuellt nätverk och undernät.
* Skapar ett tjänst huvud namn för Azure Active Directory (Azure AD) för användning med AKS-klustret.
* Tilldelar *deltagar* behörighet för AKS-kluster tjänstens huvud namn i det virtuella nätverket.
* Skapar ett AKS-kluster i det definierade virtuella nätverket och aktiverar nätverks principen.
    * Alternativet _Azure Network_ policy används. Använd parametern för att använda Calico som alternativet nätverks princip i stället `--network-policy calico` . Obs: Calico kan användas med antingen `--network-plugin azure` eller `--network-plugin kubenet` .

Observera att du kan använda en hanterad identitet för behörigheter i stället för att använda ett huvud namn för tjänsten. Mer information finns i [använda hanterade identiteter](use-managed-identity.md).

Ange dina egna säkra *SP_PASSWORD*. Du kan ersätta *RESOURCE_GROUP_NAME* och *CLUSTER_NAME* variabler:

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)
```

### <a name="create-an-aks-cluster-for-azure-network-policies"></a>Skapa ett AKS-kluster för Azure nätverks principer

Skapa AKS-klustret och ange det virtuella nätverket, tjänstens huvud namns information och *Azure* som nätverks-plugin och nätverks princip.

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-plugin azure \
    --network-policy azure
```

Det tar några minuter att skapa klustret. När klustret är klart konfigurerar `kubectl` du för att ansluta till ditt Kubernetes-kluster med hjälp av kommandot [AZ AKS get-credentials][az-aks-get-credentials] . Detta kommando hämtar autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>Skapa ett AKS-kluster för Calico-nätverks principer

Skapa AKS-klustret och ange det virtuella nätverket, tjänstens huvud namns information, *Azure* för nätverks-plugin-programmet och *Calico* för nätverks principen. Att använda *Calico* som nätverks princip aktiverar Calico-nätverk i både Linux-och Windows-adresspooler.

Om du planerar att lägga till Windows-nodkonfigurationer i klustret inkluderar du `windows-admin-username` parametrarna och `windows-admin-password` med som uppfyller [lösen ords kraven för Windows Server][windows-server-password]. Om du vill använda Calico med Windows Node-pooler måste du också registrera `Microsoft.ContainerService/EnableAKSWindowsCalico` .

Registrera `EnableAKSWindowsCalico` funktions flaggan med hjälp av kommandot [AZ Feature register][az-feature-register] som visas i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> För närvarande är det möjligt att använda Calico-nätverks principer med Windows-noder på nya kluster med Kubernetes version 1,20 eller senare med Calico 3.17.2 och kräver användning av Azure CNI-nätverk. Windows-noder i AKS-kluster med Calico aktiverat har också aktiverat [direkt Server retur (DSR)][dsr] som standard.
>
> För kluster med endast Linux-nodkonfigurationer som kör Kubernetes 1,20 med tidigare versioner av Calico uppgraderas Calico-versionen automatiskt till 3.17.2.

Calico nätverks principer med Windows-noder är för närvarande en för hands version.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

```azurecli
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.2 \
    --network-plugin azure \
    --network-policy calico
```

Det tar några minuter att skapa klustret. Som standard skapas klustret med bara en Linux-adresspool. Om du vill använda Windows-nodkonfigurationer kan du lägga till ett. Exempel:

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

När klustret är klart konfigurerar `kubectl` du för att ansluta till ditt Kubernetes-kluster med hjälp av kommandot [AZ AKS get-credentials][az-aks-get-credentials] . Detta kommando hämtar autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Neka all inkommande trafik till en POD

Innan du definierar regler för att tillåta speciell nätverks trafik måste du först skapa en nätverks princip för att neka all trafik. Den här principen ger dig en start punkt för att börja skapa en lista över tillåtna för den önskade trafiken. Du kan också tydligt se att trafiken bryts när nätverks principen tillämpas.

För exempel programmets miljö och trafik regler ska vi först skapa ett namn område som heter *utveckling* för att köra exemplet poddar:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Skapa en exempel Server dels Pod som kör NGINX. Den här backend-Pod kan användas för att simulera ett exempel på ett webbaserat program för Server delen. Skapa den här Pod i namn området för *utveckling* och öppna port *80* för att hantera webb trafik. Märk Pod med *app = webapp, Role = Server* del så att vi kan rikta den mot en nätverks princip i nästa avsnitt:

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80
```

Skapa en annan Pod och koppla en terminalsession för att testa att du kan nå standard webb sidan NGINX:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development
```

I Shell-prompten använder `wget` du för att bekräfta att du har åtkomst till standard webb sidan för nginx:

```console
wget -qO- http://backend
```

Följande exempel på utdata visar att standard webb sidan NGINX returnerades:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den anslutna Terminal-sessionen. Test Pod tas bort automatiskt.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Skapa och tillämpa en nätverks princip

Nu när du har bekräftat att du kan använda den grundläggande NGINX-webbsidan i exempel backend-pod, skapar du en nätverks princip som nekar all trafik. Skapa en fil med namnet `backend-policy.yaml` och klistra in följande yaml-manifest. I det här manifestet används en *podSelector* för att koppla principen till poddar som har *appen: webapp, roll: Server dels* etikett, t. ex. ditt exempel nginx pod. Inga regler har definierats i *ingress*, så all inkommande trafik till Pod nekas:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Gå till [https://shell.azure.com](https://shell.azure.com) för att öppna Azure Cloud Shell i webbläsaren.

Tillämpa nätverks principen med hjälp av kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testa nätverks principen

Låt oss se om du kan använda webb sidan NGINX på backend-Pod igen. Skapa en annan test-Pod och koppla en terminalsession:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development
```

I Shell-prompten använder `wget` du för att se om du kan komma åt standard webb sidan för nginx. Den här gången anger du ett timeout-värde till *2* sekunder. Nätverks principen blockerar nu all inkommande trafik, så det går inte att läsa in sidan, som du ser i följande exempel:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Avsluta den anslutna Terminal-sessionen. Test Pod tas bort automatiskt.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Tillåt inkommande trafik baserat på en POD-etikett

I det föregående avsnittet schemalades en backend-NGINX Pod och en nätverks princip har skapats för att neka all trafik. Nu ska vi skapa en frontend-Pod och uppdatera nätverks principen för att tillåta trafik från front-end-poddar.

Uppdatera nätverks principen så att den tillåter trafik från poddar med etiketten *app: webapp, Role: frontend* och i valfritt namn område. Redigera den tidigare *backend-yaml-* filen och Lägg till *matchLabels* ingress-regler så att ditt manifest ser ut som i följande exempel:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Den här nätverks principen använder ett *namespaceSelector* och ett *podSelector* -element för ingress-regeln. YAML-syntaxen är viktig för ingångs reglerna som ska användas. I det här exemplet måste båda elementen matcha för att ingångs regeln ska tillämpas. Kubernetes-versioner före *1,12* kanske inte tolkar dessa element korrekt och begränsar nätverks trafiken som du förväntar dig. Mer information om det här problemet finns i [beteende för till och från väljare][policy-rules].

Tillämpa den uppdaterade nätverks principen med hjälp av kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f backend-policy.yaml
```

Schemalägg en pod som är märkt som *app = webapp, Role = frontend* och koppla en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development
```

I Shell-prompten använder `wget` du för att se om du kan komma åt standard webb sidan för nginx:

```console
wget -qO- http://backend
```

Eftersom ingångs regeln tillåter trafik med poddar som har etiketten *app: webapp, roll: frontend*, trafiken från frontend-Pod tillåts. Följande exempel på utdata visar standard webb sidan NGINX som returneras:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den anslutna Terminal-sessionen. Pod tas bort automatiskt.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testa en POD utan en matchande etikett

Nätverks principen tillåter trafik från poddar-märkta *appar: webapp, Role: frontend*, men bör neka all annan trafik. Vi testar att se om en annan Pod utan dessa etiketter kan komma åt backend-NGINX pod. Skapa en annan test-Pod och koppla en terminalsession:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development
```

I Shell-prompten använder `wget` du för att se om du kan komma åt standard webb sidan för nginx. Nätverks principen blockerar inkommande trafik, så det går inte att läsa in sidan, som du ser i följande exempel:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Avsluta den anslutna Terminal-sessionen. Test Pod tas bort automatiskt.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Tillåt enbart trafik inom ett definierat namn område

I föregående exempel skapade du en nätverks princip som nekade all trafik och uppdaterade sedan principen för att tillåta trafik från poddar med en speciell etikett. Ett annat vanligt behov är att begränsa trafiken till endast inom ett angivet namn område. Om föregående exempel var för trafik i ett namn område för *utveckling* skapar du en nätverks princip som förhindrar trafik från en annan namnrymd, till exempel *produktion*, från att nå poddar.

Börja med att skapa ett nytt namn område för att simulera ett produktions namn område:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Schemalägg en test-Pod i namn området för *produktion* som är märkt som *app = webapp, Role = frontend*. Koppla en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production
```

I Shell-prompten använder `wget` du för att bekräfta att du har åtkomst till standard webb sidan för nginx:

```console
wget -qO- http://backend.development
```

Eftersom etiketterna för Pod matchar vad som för närvarande tillåts i nätverks principen, tillåts trafiken. Nätverks principen tittar inte på namn områdena, endast Pod-etiketter. Följande exempel på utdata visar standard webb sidan NGINX som returneras:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den anslutna Terminal-sessionen. Test Pod tas bort automatiskt.

```console
exit
```

### <a name="update-the-network-policy"></a>Uppdatera nätverks principen

Nu ska vi uppdatera avsnittet ingångs regel *namespaceSelector* så att trafik inifrån *utvecklings* namn området inte tillåts. Redigera manifest filen *backend-policy. yaml* så som visas i följande exempel:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

I mer komplexa exempel kan du definiera flera ingångs regler, t. ex. en *namespaceSelector* och sedan en *podSelector*.

Tillämpa den uppdaterade nätverks principen med hjälp av kommandot [kubectl Apply][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testa den uppdaterade nätverks principen

Schemalägg en annan Pod i *produktions* namn rymden och koppla en terminalserversession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production
```

I Shell-prompten använder `wget` du för att se att nätverks principen nu nekar trafik:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Avsluta testet pod:

```console
exit
```

När trafik nekas från namn området för *produktion* , Schemalägg en test-Pod tillbaka i *utvecklings* namn rymden och koppla en terminalsession:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development
```

I Shell-prompten använder `wget` du för att se att nätverks principen tillåter trafiken:

```console
wget -qO- http://backend
```

Trafik tillåts eftersom pod är schemalagd i namn området som matchar vad som tillåts i nätverks principen. Följande exempel på utdata visar standard webb sidan NGINX som returneras:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den anslutna Terminal-sessionen. Test Pod tas bort automatiskt.

```console
exit
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln skapade vi två namn rymder och tillämpade en nätverks princip. Om du vill rensa resurserna använder du kommandot [kubectl Delete][kubectl-delete] och anger resurs namnen:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Nästa steg

Mer information om nätverks resurser finns i [nätverks koncept för program i Azure Kubernetes service (AKS)][concepts-network].

Mer information om principer finns i [Kubernetes Network policies][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip