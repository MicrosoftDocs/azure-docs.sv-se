---
title: API-server-auktoriserade IP-intervall i Azure Kubernetes Service (AKS)
description: Lär dig hur du skyddar klustret med ett IP-adressintervall för åtkomst till API-servern i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 8fca3fe61e26a031e6ea09692c9ba0781bfca21f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769651"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Säker åtkomst till API-servern med auktoriserade IP-adressintervall i Azure Kubernetes Service (AKS)

I Kubernetes tar API-servern emot begäranden om att utföra åtgärder i klustret, till exempel för att skapa resurser eller skala antalet noder. API-servern är det centrala sättet att interagera med och hantera ett kluster. För att förbättra klustersäkerheten och minimera attacker bör API-servern endast vara tillgänglig från en begränsad uppsättning IP-adressintervall.

Den här artikeln visar hur du använder API-serverns auktoriserade IP-adressintervall för att begränsa vilka IP-adresser och CIDR som kan komma åt kontrollplanet.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln visar hur du skapar ett AKS-kluster med hjälp av Azure CLI.

Azure CLI version 2.0.76 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

### <a name="limitations"></a>Begränsningar

Funktionen auktoriserat IP-intervall för API-servern har följande begränsningar:
- I kluster som skapades efter att API-serverns auktoriserade IP-adressintervall flyttades utanför förhandsversionen i oktober  2019 stöds endast API-serverns auktoriserade IP-adressintervall i standard-SKU-lastbalanseraren. Befintliga kluster med Basic SKU-lastbalanseraren och API-serverns auktoriserade IP-adressintervall som konfigurerats fortsätter att fungera som de är, men kan inte migreras till en  *standard-SKU-lastbalanserare.* Dessa befintliga kluster fortsätter också att fungera om deras Kubernetes-version eller kontrollplan uppgraderas. API-serverns auktoriserade IP-adressintervall stöds inte för privata kluster.
- Den här funktionen är inte kompatibel med kluster som använder [offentlig IP per nod.](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools)

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Översikt över API-server-auktoriserade IP-intervall

Kubernetes API-servern är hur de underliggande Kubernetes-API:erna exponeras. Den här komponenten ger interaktion för hanteringsverktyg, till exempel `kubectl` eller Kubernetes-instrumentpanelen. AKS tillhandahåller ett kontrollplan för en enda klient med en dedikerad API-server. Som standard tilldelas API-servern en offentlig IP-adress och du bör styra åtkomsten med hjälp av rollbaserad åtkomstkontroll (Kubernetes RBAC) eller Azure RBAC.

Om du vill skydda åtkomsten till den offentligt tillgängliga AKS-kontrollplanet/API-servern kan du aktivera och använda auktoriserade IP-intervall. Dessa auktoriserade IP-intervall tillåter endast att definierade IP-adressintervall kommunicerar med API-servern. En begäran som görs till API-servern från en IP-adress som inte ingår i dessa auktoriserade IP-intervall blockeras. Fortsätt att använda Kubernetes RBAC eller Azure RBAC för att auktorisera användare och de åtgärder som de begär.

Mer information om API-servern och andra klusterkomponenter finns i [Kubernetes core concepts for AKS (Kubernetes-kärnbegrepp för AKS).][concepts-clusters-workloads]

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Skapa ett AKS-kluster med API-serverns auktoriserade IP-intervall aktiverade

Skapa ett kluster med [az aks create och ange][az-aks-create] *`--api-server-authorized-ip-ranges`* parametern för att tillhandahålla en lista över auktoriserade IP-adressintervall. Dessa IP-adressintervall är vanligtvis adressintervall som används av dina lokala nätverk eller offentliga IP-adresser. När du anger ett CIDR-intervall börjar du med den första IP-adressen i intervallet. Till exempel är *137.117.106.90/29* ett giltigt intervall, men se till att du anger den första IP-adressen i intervallet, till exempel *137.117.106.88/29*.

> [!IMPORTANT]
> Som standard använder klustret [standard-SKU-lastbalanserare][standard-sku-lb] som du kan använda för att konfigurera den utgående gatewayen. När du aktiverar API-serverns auktoriserade IP-intervall när klustret skapas tillåts även den offentliga IP-adressen för klustret som standard utöver de intervall som du anger. Om du anger *"" eller* inget värde för *`--api-server-authorized-ip-ranges`* inaktiveras API-serverns auktoriserade IP-intervall. Observera att om du använder PowerShell kan du använda *`--api-server-authorized-ip-ranges=""`* (med likhetstecken) för att undvika parsningsproblem.

I följande exempel skapas ett kluster med en nod med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup* med API-serverns auktoriserade IP-intervall aktiverade. Ip-adressintervallen som *tillåts är 73.140.245.0/24:*

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Du bör lägga till dessa intervall i en lista över tillåtna:
> - Den offentliga IP-adressen för brandväggen
> - Alla intervall som representerar nätverk som du ska administrera klustret från
> - Om du använder Azure Dev Spaces i ditt AKS-kluster måste du tillåta [ytterligare intervall baserat på din region.][dev-spaces-ranges]
>
> Den övre gränsen för antalet IP-intervall som du kan ange är 200.
>
> Det kan ta upp till 2 min att sprida reglerna. Tillåt fram till den tiden när du testar anslutningen.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Ange utgående IP-adresser för standard-SKU-lastbalanserare

Om du anger utgående IP-adresser eller prefix för klustret när du skapar ett AKS-kluster tillåts även dessa adresser eller prefix. Exempel:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

I exemplet ovan tillåts alla IP-adresser som anges i *`--load-balancer-outbound-ip-prefixes`* parametern tillsammans med IP-adresser i *`--api-server-authorized-ip-ranges`* parametern .

I stället kan du ange parametern *`--load-balancer-outbound-ip-prefixes`* för att tillåta utgående IP-prefix för lastbalanseraren.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Tillåt endast den utgående offentliga IP-adressen för standard-SKU-lastbalanserare

När du aktiverar API-serverns auktoriserade IP-intervall när klustret skapas tillåts även den utgående offentliga IP-adressen för standard-SKU-lastbalanseraren för klustret som standard utöver de intervall som du anger. Om du bara vill tillåta den utgående offentliga IP-adressen för standard-SKU-lastbalanseraren använder du *0.0.0.0/32* när du anger *`--api-server-authorized-ip-ranges`* parametern.

I följande exempel tillåts endast den utgående offentliga IP-adressen för standard-SKU:ns lastbalanserare och du kan bara komma åt API-servern från noderna i klustret.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Uppdatera ett klusters API-server-auktoriserade IP-intervall

Om du vill uppdatera API-serverns auktoriserade IP-intervall i ett befintligt kluster använder du [kommandot az aks update][az-aks-update] och använder parametrarna *`--api-server-authorized-ip-ranges`* ,--load-balancer-outbound-ip-prefixes *, *`--load-balancer-outbound-ips`* eller--load-balancer-outbound-ip-prefixes.*

I följande exempel uppdateras API-serverns auktoriserade IP-intervall i klustret med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*. IP-adressintervallet som ska godkännas *är 73.140.245.0/24:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Du kan också använda *0.0.0.0/32* när du anger parametern så att endast den offentliga IP-adressen för *`--api-server-authorized-ip-ranges`* standard-SKU:ns lastbalanserare tillåts.

## <a name="disable-authorized-ip-ranges"></a>Inaktivera auktoriserade IP-intervall

Om du vill inaktivera auktoriserade IP-intervall använder du [az aks update][az-aks-update] och anger ett tomt intervall för att inaktivera API-serverns auktoriserade IP-intervall. Exempel:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>Hitta befintliga auktoriserade IP-intervall

Om du vill hitta IP-intervall som har auktoriserats använder du [az aks show][az-aks-show] och anger klustrets namn och resursgrupp. Exempel:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Uppdatera, inaktivera och hitta auktoriserade IP-intervall med hjälp av Azure Portal

Ovanstående åtgärder för att lägga till, uppdatera, hitta och inaktivera auktoriserade IP-intervall kan också utföras i Azure Portal. Om du vill komma åt går **du** till **Nätverk under** Inställningar på menybladet för klusterresursen.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="I en webbläsare visas klusterresursens nätverksinställningar på Azure Portal sidan. Alternativen &quot;Ange angivet IP-intervall&quot; och &quot;Angivna IP-intervall&quot; är markerade.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Hur hittar jag min IP-adress som ska inkluderas i `--api-server-authorized-ip-ranges` ?

Du måste lägga till dina utvecklingsdatorer, verktygs- eller automatiserings-IP-adresser i AKS-klusterlistan över godkända IP-intervall för att få åtkomst till API-servern därifrån. 

Ett annat alternativ är att konfigurera en jumpbox med nödvändiga verktyg i ett separat undernät i brandväggens virtuella nätverk. Detta förutsätter att din miljö har en brandvägg med respektive nätverk och att du har lagt till brandväggs-IP-adresserna i auktoriserade intervall. Om du har tvingad tunneling från AKS-undernätet till brandväggsundernätet går det också bra att ha jumpboxen i klustrets undernät.

Lägg till ytterligare en IP-adress i godkända intervall med följande kommando.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> Exemplet ovan lägger till API-serverns auktoriserade IP-intervall i klustret. Om du vill inaktivera auktoriserade IP-intervall använder du az aks update och anger ett tomt intervall "". 

Ett annat alternativ är att använda kommandot nedan på Windows-system för att hämta den offentliga IPv4-adressen, eller så kan du använda stegen i [Hitta din IP-adress.](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address)

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

Du kan också hitta den här adressen genom att söka efter "what is my IP address" (vad är min IP-adress) i en webbläsare.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du aktiverat API-serverns auktoriserade IP-intervall. Den här metoden är en del av hur du kan köra ett säkert AKS-kluster.

Mer information finns i [Säkerhetsbegrepp för program och][concepts-security] kluster i AKS och Metodtips för [klustersäkerhet och uppgraderingar i AKS.][operator-best-practices-cluster-security]

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
