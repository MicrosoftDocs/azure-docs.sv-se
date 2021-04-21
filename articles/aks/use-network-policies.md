---
title: Skydda poddtrafik med nätverksprincip
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skyddar trafik som flödar in och ut från poddar med hjälp av Kubernetes-nätverksprinciper i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: b05c4add0a62f07b187376d670f23179ba97f3a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767447"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Skydda trafik mellan poddar med hjälp av nätverksprinciper i Azure Kubernetes Service (AKS)

När du kör moderna, mikrotjänstbaserade program i Kubernetes vill du ofta styra vilka komponenter som kan kommunicera med varandra. Principen om minsta behörighet ska tillämpas på hur trafik kan flöda mellan poddar i ett Azure Kubernetes Service-kluster (AKS). Anta att du förmodligen vill blockera trafik direkt till backend-program. Med *funktionen Nätverksprincip* i Kubernetes kan du definiera regler för in- och utgående trafik mellan poddar i ett kluster.

Den här artikeln visar hur du installerar nätverksprincipmotorn och skapar Kubernetes-nätverksprinciper för att styra trafikflödet mellan poddar i AKS. Nätverksprincipen bör endast användas för Linux-baserade noder och poddar i AKS.

## <a name="before-you-begin"></a>Innan du börjar

Azure CLI version 2.0.61 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

> [!TIP]
> Om du använde funktionen nätverksprincip i förhandsversionen rekommenderar vi att du [skapar ett nytt kluster.](#create-an-aks-cluster-and-enable-network-policy)
> 
> Om du vill fortsätta att använda befintliga testkluster som använde nätverksprinciper i förhandsversionen uppgraderar du klustret till en ny Kubernetes-version för den senaste ga-versionen och distribuerar sedan följande YAML-manifest för att åtgärda den kraschande måttservern och Kubernetes-instrumentpanelen. Den här korrigeringen krävs endast för kluster som använde Nätverksprincipmotorn FörCo.
>
> Som en säkerhetsmetod bör du granska innehållet i det här [YAML-manifestet][calico-aks-cleanup] för att förstå vad som distribueras i AKS-klustret.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Översikt över nätverksprincipen

Alla poddar i ett AKS-kluster kan som standard skicka och ta emot trafik utan begränsningar. För att förbättra säkerheten kan du definiera regler som styr trafikflödet. Backend-program exponeras ofta bara för nödvändiga frontend-tjänster, till exempel. Databaskomponenter är också bara tillgängliga för de programnivåer som ansluter till dem.

Nätverksprincip är en Kubernetes-specifikation som definierar åtkomstprinciper för kommunikation mellan poddar. Med hjälp av nätverksprinciper definierar du en ordnad uppsättning regler för att skicka och ta emot trafik och tillämpa dem på en samling poddar som matchar en eller flera etikettväljare.

Dessa nätverksprincipregler definieras som YAML-manifest. Nätverksprinciper kan ingå som en del av ett bredare manifest som även skapar en distribution eller tjänst.

### <a name="network-policy-options-in-aks"></a>Nätverksalternativ i AKS

Azure tillhandahåller två sätt att implementera nätverksprincipen. Du väljer ett alternativ för nätverksprincip när du skapar ett AKS-kluster. Principalternativet kan inte ändras när klustret har skapats:

* Azures egen implementering, Som kallas *Azure-nätverksprinciper.*
* *Nätverkspolicyer med öppen* källkod, en nätverks- och nätverkssäkerhetslösning med öppen källkod som bygger [på Etta.][tigera]

Båda implementeringarna använder Linux *IPTables för* att genomdriva de angivna principerna. Principer översätts till uppsättningar med tillåtna och otillåtna IP-par. Dessa par programmeras sedan som IPTable-filterregler.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Skillnader mellan Azure- och Kanco-principer och deras funktioner

| Funktion                               | Azure                      | Kalikå                      |
|------------------------------------------|----------------------------|-----------------------------|
| Plattformar som stöds                      | Linux                      | Linux, Windows Server 2019 (förhandsversion)  |
| Nätverksalternativ som stöds             | Azure CNI                  | Azure CNI (Windows Server 2019 och Linux) och kubenet (Linux)  |
| Efterlevnad med Kubernetes-specifikation | Alla principtyper som stöds |  Alla principtyper som stöds |
| Ytterligare funktioner                      | Inget                       | Utökad principmodell som består av global nätverksprincip, global nätverksuppsättning och värdslutpunkt. Mer information om hur du använder `calicoctl` CLI för att hantera dessa utökade funktioner finns i [referensen för användare][calicoctl]. |
| Support                                  | Stöds av Azures support- och teknikteam | Support från Communityn för Blico. Mer information om ytterligare betald support finns i [Supportalternativ för ProjectCo.][calico-support] |
| Loggning                                  | Regler som läggs till/tas bort i IPTables loggas på varje värd under */var/log/azure-npm.log* | Mer information finns i [Componentco-komponentloggar][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Skapa ett AKS-kluster och aktivera nätverksprincip

Om du vill se nätverksprinciper i praktiken ska vi skapa och sedan expandera en princip som definierar trafikflödet:

* Neka all trafik till podd.
* Tillåt trafik baserat på poddetiketter.
* Tillåt trafik baserat på namnområde.

Först ska vi skapa ett AKS-kluster som stöder nätverksprincip.

> [!IMPORTANT]
>
> Funktionen nätverksprincip kan bara aktiveras när klustret skapas. Du kan inte aktivera nätverksprincip i ett befintligt AKS-kluster.

Om du vill använda Azure-nätverksprincip [måste du Azure CNI plugin-programmet][azure-cni] och definiera egna virtuella nätverk och undernät. Mer detaljerad information om hur du planerar de nödvändiga undernätsintervallen finns i [Konfigurera avancerade nätverk.][use-advanced-networking] Princip för Nätverksnätverk kan användas med antingen Azure CNI plugin-programmet eller med CNI-plugin-programmet Kubenet.

Följande exempelskript:

* Skapar ett virtuellt nätverk och undernät.
* Skapar ett Azure Active Directory (Azure AD) för användning med AKS-klustret.
* Tilldelar *deltagarbehörigheter* för tjänstens huvudnamn för AKS-klustret i det virtuella nätverket.
* Skapar ett AKS-kluster i det definierade virtuella nätverket och aktiverar nätverksprincipen.
    * Alternativet _Azure-nätverksprincip_ används. Om du vill använda Alternativet För nätverkspolicy i stället använder du `--network-policy calico` parametern . Obs! Använd Så här kan Du använda Ettco med `--network-plugin azure` antingen eller `--network-plugin kubenet` .

Observera att du i stället för att använda ett huvudnamn för tjänsten kan använda en hanterad identitet för behörigheter. Mer information finns i Använda [hanterade identiteter.](use-managed-identity.md)

Ange din egen säkra *SP_PASSWORD*. Du kan ersätta *RESOURCE_GROUP_NAME* *och CLUSTER_NAME* variabler:

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

### <a name="create-an-aks-cluster-for-azure-network-policies"></a>Skapa ett AKS-kluster för Azure-nätverksprinciper

Skapa AKS-klustret och ange det virtuella nätverket, information om tjänstens huvudnamn och *azure för* nätverks-plugin-programmet och nätverksprincipen.

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

Det tar några minuter att skapa klustret. När klustret är klart konfigurerar du för att ansluta till `kubectl` ditt Kubernetes-kluster med hjälp av [kommandot az aks get-credentials.][az-aks-get-credentials] Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>Skapa ett AKS-kluster för Clusterco-nätverksprinciper

Skapa AKS-klustret och ange det virtuella nätverket, information om tjänstens huvudnamn, *azure* för nätverks-plugin-programmet *ochco* för nätverksprincipen. Med *hjälp avcoco* som nätverksprincip möjliggörs Java-nätverk i både Linux- och Windows-nodpooler.

Om du planerar att lägga till Windows-nodpooler i klustret ska du inkludera parametrarna och `windows-admin-username` `windows-admin-password` som uppfyller Windows [Server-lösenordskraven.][windows-server-password] Om du vill användaCo med Windows-nodpooler måste du också registrera `Microsoft.ContainerService/EnableAKSWindowsCalico` .

Registrera `EnableAKSWindowsCalico` funktionsflaggan med [kommandot az feature register][az-feature-register] enligt följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 Du kan kontrollera registreringsstatusen med kommandot [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *resursprovidern Microsoft.ContainerService* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> För närvarande är användning av Takco-nätverksprinciper med Windows-noder tillgängligt i nya kluster som använder Kubernetes version 1.20 eller senare med Hjälp av 3.17.2 Och kräver Azure CNI nätverk. Windows-noder i AKS-kluster med Alternativetco aktiverat har också [DSR (Direct Server Return)][dsr] aktiverat som standard.
>
> För kluster med endast Linux-nodpooler som kör Kubernetes 1.20 med tidigare versioner av Automatco uppgraderas Automatiskt Version Avco till 3.17.2.

Nätverkspolicyer med Windows-noder är för närvarande i förhandsversion.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Skapa ett användarnamn som ska användas som administratörsautentiseringsuppgifter för dina Windows Server-containrar i klustret. Följande kommandon uppmanar dig att ange ett användarnamn och ange WINDOWS_USERNAME för användning i ett senare kommando (kom ihåg att kommandona i den här artikeln anges i ett BASH-gränssnitt).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

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
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.2 \
    --network-plugin azure \
    --network-policy calico
```

Det tar några minuter att skapa klustret. Som standard skapas klustret med endast en Linux-nodpool. Om du vill använda Windows-nodpooler kan du lägga till en. Exempel:

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

När klustret är klart konfigurerar du för att ansluta till `kubectl` ditt Kubernetes-kluster med hjälp av [kommandot az aks get-credentials.][az-aks-get-credentials] Det här kommandot laddar ned autentiseringsuppgifter och konfigurerar Kubernetes CLI för att använda dem:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Neka all inkommande trafik till en podd

Innan du definierar regler för att tillåta specifik nätverkstrafik måste du först skapa en nätverksprincip för att neka all trafik. Den här principen ger dig en startpunkt för att börja skapa en lista över tillåtna endast för önskad trafik. Du kan också tydligt se att trafiken tas bort när nätverksprincipen tillämpas.

För exempelprogrammiljön och trafikreglerna ska vi först skapa ett namnområde som kallas *utveckling för* att köra exempelpoddarna:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Skapa ett exempel på en backend-podd som kör NGINX. Den här backend-podden kan användas för att simulera ett exempel på ett webbaserat backend-program. Skapa den här podden *i namnområdet* för utveckling och öppna port *80 för* att betjäna webbtrafik. Märk podden *med app=webapp,role=backend* så att vi kan rikta den mot en nätverksprincip i nästa avsnitt:

```console
kubectl run backend --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --labels app=webapp,role=backend --namespace development --expose --port 80
```

Skapa en annan podd och bifoga en terminalsession för att testa att du kan nå NGINX-standardwebbsidan:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

I shell-prompten använder du `wget` för att bekräfta att du kan komma åt NGINX-standardwebbsidan:

```console
wget -qO- http://backend
```

Följande exempelutdata visar att NGINX-standardwebbsidan returnerades:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den anslutna terminalsessionen. Testpodden tas bort automatiskt.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Skapa och tillämpa en nätverksprincip

Nu när du har bekräftat att du kan använda den grundläggande NGINX-webbsidan på exempel-backend-podden skapar du en nätverksprincip för att neka all trafik. Skapa en fil med namnet `backend-policy.yaml` och klistra in följande YAML-manifest. Det här manifestet använder *en podSelector* för att koppla principen till poddar som har *etiketten app:webapp,role:backend,* som nginx-exempelpodden. Inga regler definieras under *ingress,* så all inkommande trafik till podden nekas:

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

Gå till [https://shell.azure.com](https://shell.azure.com) för att Azure Cloud Shell i webbläsaren.

Tillämpa nätverksprincipen med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testa nätverksprincipen

Nu ska vi se om du kan använda NGINX-webbsidan på backend-podden igen. Skapa en till testpodd och koppla en terminalsession:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Använd i shell-prompten `wget` för att se om du kan komma åt NGINX-standardwebbsidan. Den här gången anger du ett timeout-värde *till 2* sekunder. Nätverksprincipen blockerar nu all inkommande trafik, så sidan kan inte läsas in, som du ser i följande exempel:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Avsluta den anslutna terminalsessionen. Testpodden tas bort automatiskt.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Tillåt inkommande trafik baserat på en poddetikett

I föregående avsnitt schemalagdes en NGINX-podd för backend och en nätverksprincip skapades för att neka all trafik. Nu ska vi skapa en frontend-podd och uppdatera nätverksprincipen för att tillåta trafik från frontend-poddar.

Uppdatera nätverksprincipen så att trafik från poddar tillåts med *etiketterna app:webapp, role:frontend* och i alla namnområden. Redigera den tidigare *backend-policy.yaml-filen* och lägg till *matchLabels* ingress-regler så att manifestet ser ut som i följande exempel:

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
> Den här nätverksprincipen *använder ett namnområdeSelector* och *ett podSelector-element* för ingressregeln. YAML-syntaxen är viktig för att ingressreglerna ska vara additiva. I det här exemplet måste båda elementen matcha för att ingressregeln ska tillämpas. Kubernetes-versioner före *1.12* kanske inte tolkar dessa element korrekt och begränsar nätverkstrafiken som förväntat. Mer information om det här beteendet finns [i Beteende för till och från väljare.][policy-rules]

Tillämpa den uppdaterade nätverksprincipen med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f backend-policy.yaml
```

Schemalägg en podd som är märkt *som app=webapp,role=frontend* och bifoga en terminalsession:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

I shell-prompten använder du `wget` för att se om du kan komma åt NGINX-standardwebbsidan:

```console
wget -qO- http://backend
```

Eftersom ingressregeln tillåter trafik med poddar som har appen *labels: webapp, role: frontend* tillåts trafiken från frontend-podden. Följande exempelutdata visar standardwebbsidan för NGINX som returnerades:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den anslutna terminalsessionen. Podden tas bort automatiskt.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testa en podd utan en matchande etikett

Nätverksprincipen tillåter trafik från poddar med etiketten *app: webapp, role: frontend*, men bör neka all annan trafik. Nu ska vi testa om en annan podd utan dessa etiketter kan komma åt NGINX-podden på backend-delen. Skapa en till testpodd och koppla en terminalsession:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Använd i shell-prompten `wget` för att se om du kan komma åt NGINX-standardwebbsidan. Nätverksprincipen blockerar den inkommande trafiken så att sidan inte kan läsas in, som du ser i följande exempel:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Avsluta den anslutna terminalsessionen. Testpodden tas bort automatiskt.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Tillåt endast trafik inifrån ett definierat namnområde

I föregående exempel skapade du en nätverksprincip som nekade all trafik och uppdaterade sedan principen för att tillåta trafik från poddar med en specifik etikett. Ett annat vanligt behov är att begränsa trafiken till endast inom ett visst namnområde. Om föregående exempel gäller trafik  i ett namnområde för utveckling skapar du en nätverksprincip som förhindrar att trafik från ett annat namnområde, till exempel produktion *,* når poddarna.

Skapa först ett nytt namnområde för att simulera ett produktionsnamnområde:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Schemalägg en testpodd *i produktionsnamnrymden* som är märkt som *app=webapp,role=frontend*. Koppla en terminalsession:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

I shell-prompten använder du `wget` för att bekräfta att du kan komma åt NGINX-standardwebbsidan:

```console
wget -qO- http://backend.development
```

Eftersom etiketterna för podden matchar det som för närvarande tillåts i nätverksprincipen tillåts trafiken. Nätverksprincipen tittar inte på namnrymderna, utan bara poddetiketterna. Följande exempelutdata visar standardwebbsidan för NGINX som returnerades:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den anslutna terminalsessionen. Testpodden tas bort automatiskt.

```console
exit
```

### <a name="update-the-network-policy"></a>Uppdatera nätverksprincipen

Nu ska vi uppdatera avsnittet namnområde för *ingressregelnSelector* så att endast trafik tillåts inifrån *namnområdet för* utveckling. Redigera *manifestfilen backend-policy.yaml* enligt följande exempel:

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

I mer komplexa exempel kan du definiera flera ingress-regler, till exempel ett *namnområdeVäljare* och sedan en *podSelector.*

Tillämpa den uppdaterade nätverksprincipen med kommandot [kubectl apply][kubectl-apply] och ange namnet på ditt YAML-manifest:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testa den uppdaterade nätverksprincipen

Schemalägg en annan podd *i produktionsnamnrymden* och bifoga en terminalsession:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

I shell-prompten använder `wget` du för att se att nätverksprincipen nu nekar trafik:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Avsluta testpodden:

```console
exit
```

Med trafik nekad från *produktionsnamnrymden* schemalägger du en testpodd i *namnområdet* för utveckling och kopplar en terminalsession:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

I shell-prompten använder `wget` du för att se att nätverksprincipen tillåter trafiken:

```console
wget -qO- http://backend
```

Trafik tillåts eftersom podden schemaläggs i namnområdet som matchar det som tillåts i nätverksprincipen. Följande exempelutdata visar den returnerade NGINX-standardwebbsidan:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Avsluta den anslutna terminalsessionen. Testpodden tas bort automatiskt.

```console
exit
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln skapade vi två namnrymder och tillämpade en nätverksprincip. Om du vill rensa resurserna använder du [kommandot kubectl delete][kubectl-delete] och anger resursnamnen:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Nästa steg

Mer information om nätverksresurser finns [i Nätverksbegrepp för program i Azure Kubernetes Service (AKS).][concepts-network]

Mer information om principer finns i [Kubernetes-nätverksprinciper.][kubernetes-network-policies]

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip