---
title: Skapa ett Linux Service Fabric-kluster i Azure
description: Lär dig att distribuera ett Linux Service Fabric-kluster till ett befintligt virtuellt nätverk i Azure med Azure CLI.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: mvc
ms.openlocfilehash: c4b71328ce59284f8870407c9492d24afe9acd8a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586928"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Distribuera ett Service Fabric-kluster i Linux till ett virtuellt Azure-nätverk

I den här artikeln får du lära dig hur du distribuerar ett Linux Service Fabric-kluster till ett [virtuellt Azure-nätverk (VNet)](../virtual-network/virtual-networks-overview.md) med Azure CLI och en mall. När du är klar körs ett kluster i molnet som du kan distribuera program till. Om du vill skapa ett Windows-kluster med PowerShell läser du informationen om att [skapa ett säkert Windows-kluster i Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Krav

Innan du börjar:

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Service Fabric CLI](service-fabric-cli.md)
* Installera [Azure CLI](/cli/azure/install-azure-cli)
* Läs [Översikt över Azure-kluster](service-fabric-azure-clusters-overview.md) för att lära dig viktiga begrepp i kluster
* [Planera och förbereda](service-fabric-cluster-azure-deployment-preparation.md) för distribution av produktions kluster.

Följande procedurer skapar ett Service Fabric-kluster med sju noder. Du kan beräkna kostnaden för att köra ett Service Fabric-kluster i Azure med [Azures prissättningsberäknare](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Ladda ned och titta närmare på mallen

Ladda ned följande mallfiler för Resource Manager:

För Ubuntu 16,04-LTS:

* [AzureDeploy.jspå][template]
* [AzureDeploy.Parameters.jspå][parameters]

För Ubuntu 18,04-LTS:

* [AzureDeploy.jspå][template2]
* [AzureDeploy.Parameters.jspå][parameters2]

För Ubuntu 18,04 LTS skillnaden mellan de två mallarna 
* attributet **vmImageSku** anges till "18,04-LTS"
* varje nods **typeHandlerVersion** anges till 1,1
* Microsoft. ServiceFabric/Clusters-resurs
   - **API version** anges till "2019-03-01" eller högre
   - **vmImage** -egenskapen anges till "Ubuntu18_04"

Den här mallen distribuerar ett säkert kluster med sju virtuella datorer och tre nodtyper till ett virtuellt nätverk.  Andra exempelmallar finns på [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). [AzureDeploy.jspå][template] distribuerar en siffer resurs, inklusive följande.

### <a name="service-fabric-cluster"></a>Service Fabric-kluster

I resursen **Microsoft.ServiceFabric/clusters** distribueras ett Linux-kluster med följande egenskaper:

* tre nodtyper
* fem noder i den primära nodtypen (kan konfigureras i mallparametrar), en nod i var och en av de andra Node-typerna
* OS: (Ubuntu 16,04 LTS/Ubuntu 18,04 LTS) (kan konfigureras i mallparametrar)
* skyddat med certifikat (kan konfigureras i mallparametrarna)
* [DNS-tjänst](service-fabric-dnsservice.md) är aktiverad
* [Hållbarhetsnivå](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Brons (kan konfigureras i mallparametrarna)
* [Tillförlitlighetsnivå](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) Silver (kan konfigureras i mallparametrarna)
* slutpunkt för klientanslutning: 19000 (kan konfigureras i mallparametrarna)
* slutpunkt för HTTP-gateway: 19080 (kan konfigureras i mallparametrarna)

### <a name="azure-load-balancer"></a>Azure-lastbalanserare

I resursen **Microsoft.Network/loadBalancers** har en lastbalanserare konfigurerats och avsökningar samt regler har konfigurerats för följande portar:

* klientanslutningsslutpunkt: 19000
* HTTP-gatewayslutpunkt: 19080
* programport: 80
* programport: 443

### <a name="virtual-network-and-subnet"></a>Virtuellt nätverk och undernät

Namnen på det virtuella nätverket och undernätet deklareras också i mallparametrarna.  Adressutrymmen i det virtuella nätverket och undernätet deklareras också i mallparametrarna och konfigureras i resursen **Microsoft.Network/virtualNetworks**:

* det virtuella nätverkets adressutrymme: 10.0.0.0/16
* Service Fabric-undernätsadressutrymme: 10.0.2.0/24

Om du behöver andra programportar måste du justera resursen Microsoft.Network/loadBalancers för att låta trafiken komma in.

## <a name="set-template-parameters"></a>Ställa in mallparametrar

Filen **AzureDeploy. Parameters** deklarerar många värden som används för att distribuera klustret och associerade resurser. Här är några av parametrarna du kan behöva ändra för distributionen:

|Parameter|Exempelvärde|Kommentarer|
|---|---||
|adminUserName|vmadmin| Administratörsnamn för virtuella datorer i klustret. |
|adminPassword|Password#1234| Administratörslösenord för virtuella datorer i klustret.|
|clusterName|mysfcluster123| Namnet på klustret. |
|location|USA, södra centrala| Klustrets placering. |
|certificateThumbprint|| <p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets SHA1-tumavtrycksvärde. Till exempel ”6190390162C988701DB5676EB81083EA608DCCF3”. </p>|
|certificateUrlValue|| <p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i certifikatets webbadress. Till exempel "https: \/ /mykeyvault.Vault.Azure.net:443/Secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Värdet ska vara tomt om du skapar ett självsignerat certifikat eller tillhandahåller en certifikatfil.</p><p>Om du vill använda ett befintligt certifikat som tidigare har laddats upp till ett nyckelvalv fyller du i källans nyckelvärde. Till exempel ”/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”.</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Distribuera det virtuella nätverket och klustret

Konfigurera sedan nätverkstopologin och distribuera Service Fabric-klustret. Resource Manager-mallen **AzureDeploy.json** skapar ett virtuellt nätverk (VNET) och ett undernät för Service Fabric. Mallen distribuerar också ett kluster med certifikatsäkerhet aktiverad.  För produktionskluster ska du använda ett certifikat från en certifikatutfärdare som klustercertifikat. Ett självsignerat certifikat kan användas för att skydda testkluster.

Mallen i den här artikeln distribuerar ett kluster som använder certifikatets tumavtryck för att identifiera kluster certifikatet.  Två certifikat kan inte ha samma tumavtryck, vilket gör certifikathantering svårare. Om ett distribuerat kluster växlas från att använda certifikattumavtryck till att använda vanliga certifikatnamn blir certifikathanteringen mycket enklare.  Du kan lära dig att uppdatera klustret till att använda vanliga certifikatnamn för certifikathantering genom att läsa avsnittet om att [ändra kluster till vanligt certifikatnamn för hantering](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Skapa ett kluster med ett befintligt certifikat

Följande skript använder kommandot [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) och mallen för att distribuera ett nytt kluster som skyddas med ett befintligt certifikat. Kommandot skapar även ett nytt nyckelvalv i Azure och laddar upp certifikatet.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Skapa ett kluster med ett nytt, självsignerat certifikat

Följande skript använder kommandot [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) och en mall för att distribuera ett nytt kluster i Azure. Kommandot skapar även ett nytt nyckelvalv i Azure, lägger till ett nytt självsignerat certifikat i nyckelvalvet och laddar ned certifikatfilen lokalt.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json \
   --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password \
   --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Ansluta till det skyddade klustret

Anslut till klustret med Service Fabric CLI `sfctl cluster select`-kommandot med din nyckel.  Använd enbart alternativet **--no-verify** för ett självsignerat certifikat.

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Kontrollera att du är ansluten och att klustret är felfritt med kommandot `sfctl cluster health`.

```console
sfctl cluster health
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte genast fortsätter till nästa artikel kanske du vill [ta bort klustret](./service-fabric-tutorial-delete-cluster.md) för att undvika kostnader.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skalar ett kluster](service-fabric-tutorial-scale-cluster.md).

Mallen i den här artikeln distribuerar ett kluster som använder certifikatets tumavtryck för att identifiera kluster certifikatet.  Två certifikat kan inte ha samma tumavtryck, vilket gör certifikathantering svårare. Om ett distribuerat kluster växlas från att använda certifikattumavtryck till att använda vanliga certifikatnamn blir certifikathanteringen mycket enklare.  Du kan lära dig att uppdatera klustret till att använda vanliga certifikatnamn för certifikathantering genom att läsa avsnittet om att [ändra kluster till vanligt certifikatnamn för hantering](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
[template2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.json
[parameters2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.Parameters.json
