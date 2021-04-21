---
title: Skapa ett privat Azure Kubernetes Service kluster
description: Lär dig hur du skapar ett Azure Kubernetes Service-kluster (AKS)
services: container-service
ms.topic: article
ms.date: 3/31/2021
ms.openlocfilehash: 76785caedb9ca97d947e83f5aa8ff5b32d827914
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772909"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Skapa ett privat Azure Kubernetes Service kluster

I ett privat kluster har kontrollplanet eller API-servern interna IP-adresser som definieras i [dokumentet RFC1918 – Adressallokering för privat Internet.](https://tools.ietf.org/html/rfc1918) Genom att använda ett privat kluster kan du se till att nätverkstrafiken mellan API-servern och dina nodpooler endast finns kvar i det privata nätverket.

Kontrollplanet eller API-servern finns i en AKS Azure Kubernetes Service prenumeration (AKS). En kunds kluster eller nodpool finns i kundens prenumeration. Servern och klustret eller nodpoolen kan kommunicera med varandra via [Azure Private Link-tjänsten][private-link-service] i det virtuella API-servernätverket och en privat slutpunkt som exponeras i undernätet i kundens AKS-kluster.

## <a name="region-availability"></a>Regional tillgänglighet

Privata kluster är tillgängligt i offentliga regioner, Azure Government och Azure China 21Vianet där [AKS stöds.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)

> [!NOTE]
> Azure Government-webbplatser stöds, men US Gov, Texas stöds inte för närvarande på grund av att Private Link stöds.

## <a name="prerequisites"></a>Förutsättningar

* Azure CLI version 2.2.0 eller senare
* Tjänsten Private Link stöds endast på Standard Azure Load Balancer. Grundläggande Azure Load Balancer stöds inte.  
* Om du vill använda en anpassad DNS-server lägger du till Azure DNS IP 168.63.129.16 som överordnad DNS-server i den anpassade DNS-servern.

## <a name="create-a-private-aks-cluster"></a>Skapa ett privat AKS-kluster

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp eller använd en befintlig resursgrupp för ditt AKS-kluster.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Grundläggande standardnätverk 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Där `--enable-private-cluster` är en obligatorisk flagga för ett privat kluster. 

### <a name="advanced-networking"></a>Avancerade nätverk  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Där `--enable-private-cluster` är en obligatorisk flagga för ett privat kluster. 

> [!NOTE]
> Om CIDR (172.17.0.1/16) hamnar i konflikt med undernätets CIDR ändrar du Docker-bryggadressen på rätt sätt.

## <a name="configure-private-dns-zone"></a>Konfigurera Privat DNS zon 

Följande parametrar kan användas för att konfigurera Privat DNS Zon.

- "System" är standardvärdet. Om argumentet --private-dns-zone utelämnas skapar AKS en Privat DNS zon i nodresursgruppen.
- "Ingen" innebär att AKS inte skapar en Privat DNS zon.  Detta kräver att du tar med din egen DNS-server och konfigurerar DNS-upplösningen för privat FQDN.  Om du inte konfigurerar DNS-matchning kan DNS bara matchas i agentnoderna och orsakar klusterproblem efter distributionen. 
- "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" kräver att du skapar en Privat DNS Zon i det här formatet för Azures globala moln: `privatelink.<region>.azmk8s.io` . Du behöver resurs-ID för den Privat DNS zonen framöver.  Dessutom behöver du en användar tilldelad identitet eller tjänstens huvudnamn med minst `private dns zone contributor`  rollerna `vnet contributor` och .
- "fqdn-subdomain" kan endast användas med "CUSTOM_PRIVATE_DNS_ZONE_RESOURCE_ID" för att tillhandahålla underdomänfunktioner för att `privatelink.<region>.azmk8s.io`

### <a name="prerequisites"></a>Förutsättningar

* Förhandsversionen av AKS version 0.5.7 eller senare
* API-versionen 2020-11-01 eller senare

### <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>Skapa ett privat AKS-kluster Privat DNS zon

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone [system|none]
```

### <a name="create-a-private-aks-cluster-with-a-custom-private-dns-zone"></a>Skapa ett privat AKS-kluster med en anpassad Privat DNS zon

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --enable-managed-identity --assign-identity <ResourceId> --private-dns-zone <custom private dns zone ResourceId> --fqdn-subdomain <subdomain-name>
```

## <a name="options-for-connecting-to-the-private-cluster"></a>Alternativ för att ansluta till det privata klustret

API-serverslutpunkten har ingen offentlig IP-adress. För att hantera API-servern måste du använda en virtuell dator som har åtkomst till AKS-klustrets Azure Virtual Network (VNet). Det finns flera alternativ för att upprätta nätverksanslutning till det privata klustret.

* Skapa en virtuell dator i samma Azure Virtual Network (VNet) som AKS-klustret.
* Använd en virtuell dator i ett separat nätverk och konfigurera [peering för virtuella nätverk.][virtual-network-peering]  Mer information om det här alternativet finns i avsnittet nedan.
* Använd en [Express Route- eller VPN-anslutning.][express-route-or-VPN]
* Använd [funktionen AKS Kör kommando](#aks-run-command-preview).

Det enklaste alternativet är att skapa en virtuell dator i samma virtuella nätverk som AKS-klustret.  Express Route och VPN:er lägger till kostnader och kräver ytterligare nätverkskomplexitet.  Peering för virtuella nätverk kräver att du planerar ditt nätverks CIDR-intervall för att säkerställa att det inte finns några överlappande intervall.

### <a name="aks-run-command-preview"></a>AKS Kör kommando (förhandsversion)

Idag när du behöver åtkomst till ett privat kluster måste du göra det i klustrets virtuella nätverk eller ett peer-peer-nätverk eller klientdator. Detta kräver vanligtvis att datorn är ansluten via VPN eller Express Route till klustrets virtuella nätverk eller en jumpbox som ska skapas i klustrets virtuella nätverk. Med AKS-körningskommandot kan du fjärranropa kommandon i ett AKS-kluster via AKS-API:et. Den här funktionen tillhandahåller ett API som gör att du till exempel kan köra just-in-time-kommandon från en fjärransluten bärbar dator för ett privat kluster. Detta kan ge snabb just-in-time-åtkomst till ett privat kluster när klientdatorn inte finns i klustrets privata nätverk samtidigt som samma RBAC-kontroller och privata API-server behålls och framtvings.

### <a name="register-the-runcommandpreview-preview-feature"></a>Registrera `RunCommandPreview` förhandsgranskningsfunktionen

Om du vill använda det Kör kommando API:et måste du aktivera `RunCommandPreview` funktionsflaggan för din prenumeration.

Registrera `RunCommandPreview` funktionsflaggan med kommandot [az feature register][az-feature-register] enligt följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "RunCommandPreview"
```

Det tar några minuter för statusen att visa *Registrerad*. Kontrollera registreringsstatusen med hjälp av [kommandot az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/RunCommandPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *resursprovidern Microsoft.ContainerService* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-run-command"></a>Använda AKS-Kör kommando

Enkelt kommando

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl get pods -n kube-system"
```

Distribuera ett manifest genom att bifoga den specifika filen

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f deployment.yaml
```

Distribuera ett manifest genom att koppla en hel mapp

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "kubectl apply -f deployment.yaml -n default" -f .
```

Utföra en Helm-installation och skicka manifestet för specifika värden

```azurecli-interactive
az aks command invoke -g <resourceGroup> -n <clusterName> -c "helm repo add bitnami https://charts.bitnami.com/bitnami && helm repo update && helm install my-release -f values.yaml bitnami/nginx" -f values.yaml
```

## <a name="virtual-network-peering"></a>Virtuell nätverkspeering

Som tidigare nämnts är peering för virtuella nätverk ett sätt att komma åt ditt privata kluster. Om du vill använda peering för virtuella nätverk måste du konfigurera en länk mellan det virtuella nätverket och den privata DNS-zonen.
    
1. Gå till nodresursgruppen i Azure Portal.  
2. Välj den privata DNS-zonen.   
3. I den vänstra rutan väljer du **länken Virtuellt** nätverk.  
4. Skapa en ny länk för att lägga till det virtuella nätverket för den virtuella datorn i den privata DNS-zonen. Det tar några minuter innan länken för DNS-zonen blir tillgänglig.  
5. I Azure Portal navigerar du till resursgruppen som innehåller klustrets virtuella nätverk.  
6. Välj det virtuella nätverket i den högra rutan. Namnet på det virtuella nätverket har formen *aks-vnet- \**.  
7. I den vänstra rutan väljer du **Peerings**.  
8. Välj **Lägg** till, lägg till det virtuella nätverket för den virtuella datorn och skapa sedan peering.  
9. Gå till det virtuella nätverket där du har den virtuella datorn, välj **Peerings**, välj det virtuella AKS-nätverket och skapa sedan peering. Om adressintervallen i det virtuella AKS-nätverket och den virtuella datorns virtuella nätverk krockar, misslyckas peering. Mer information finns i [Peering för virtuella nätverk.][virtual-network-peering]

## <a name="hub-and-spoke-with-custom-dns"></a>Hubb och eker med anpassad DNS

[Nav- och ekerarkitekturer](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) används ofta för att distribuera nätverk i Azure. I många av dessa distributioner är DNS-inställningarna i de virtuella ekernätverken konfigurerade att referera till en central DNS-vidarebefordrare för att möjliggöra lokal och Azure-baserad DNS-upplösning. När du distribuerar ett AKS-kluster till en sådan nätverksmiljö finns det några särskilda överväganden som måste beaktas.

![Privat klusterhubb och eker](media/private-clusters/aks-private-hub-spoke.png)

1. När ett privat kluster etableras skapas som standard en privat slutpunkt (1) och en privat DNS-zon (2) i den kluster hanterade resursgruppen. Klustret använder en A-post i den privata zonen för att matcha IP-adressen för den privata slutpunkten för kommunikation till API-servern.

2. Den privata DNS-zonen är endast länkad till det virtuella nätverk som klusternoderna är kopplade till (3). Det innebär att den privata slutpunkten endast kan matchas av värdar i det länkade virtuella nätverket. I scenarier där ingen anpassad DNS har konfigurerats på det virtuella nätverket (standard) fungerar detta utan problem som värdar pekar på 168.63.129.16 för DNS som kan matcha poster i den privata DNS-zonen på grund av länken.

3. I scenarier där det virtuella nätverk som innehåller klustret har anpassade DNS-inställningar (4), misslyckas klusterdistributionen om inte den privata DNS-zonen är länkad till det virtuella nätverk som innehåller de anpassade DNS-matcharna (5). Den här länken kan skapas manuellt när den privata zonen har skapats under klusteretablering eller via automatisering när du har upptäckt att zonen har skapats med hjälp av händelsebaserade distributionsmekanismer (till exempel Azure Event Grid och Azure Functions).

> [!NOTE]
> Om du använder Bring Your Own Route Table med [kubenet](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) och Bring Your Own DNS med privat kluster misslyckas klusterskapandet. Du måste associera [RouteTable](./configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet) i nodresursgruppen till undernätet när klustret har skapats, för att skapa klustret.

## <a name="limitations"></a>Begränsningar 
* IP-auktoriserade intervall kan inte tillämpas på den privata API-serverslutpunkten, de gäller bara för den offentliga API-servern
* [Azure Private Link tjänstbegränsningar][private-link-service] gäller för privata kluster.
* Inget stöd för Microsoft-värdbaserade agenter i Azure DevOps med privata kluster. Överväg att använda [agenter med egen värd.](/azure/devops/pipelines/agents/agents?tabs=browser) 
* För kunder som behöver aktivera Azure Container Registry att arbeta med privat AKS måste det virtuella Container Registry-nätverket peer-vara peer-ed med agentklustrets virtuella nätverk.
* Inget stöd för att konvertera befintliga AKS-kluster till privata kluster
* Om du tar bort eller ändrar den privata slutpunkten i kundens undernät slutar klustret att fungera. 
* När kunderna har uppdaterat A-posten på sina egna DNS-servrar skulle dessa poddar fortfarande matcha apiserver-FQDN till den äldre IP-adressen efter migreringen tills de startas om. Kunder måste starta om hostNetwork Pods och standard-DNSPolicy Pods efter kontrollplansmigrering.
* Vid underhåll på kontrollplanet kan [AKS IP-adressen](./limit-egress-traffic.md) ändras. I det här fallet måste du uppdatera A-posten som pekar på API-serverns privata IP-adress på din anpassade DNS-server och starta om eventuella anpassade poddar eller distributioner med hjälp av hostNetwork.

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents
[availability-zones]: availability-zones.md
