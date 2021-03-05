---
title: Distribuera OpenShift container Platform 3,11 i Azure
description: Distribuera OpenShift container Platform 3,11 i Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: ce3f56530a7bad6eecd4c2edd3b21debe8c75a24
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214158"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Distribuera OpenShift container Platform 3,11 i Azure

Du kan använda en av flera metoder för att distribuera OpenShift container Platform 3,11 i Azure:

- Du kan distribuera de nödvändiga komponenterna i Azure-infrastrukturen manuellt och sedan följa [dokumentationen för OpenShift container Platform](https://docs.openshift.com/container-platform).
- Du kan också använda en befintlig [Resource Manager-mall](https://github.com/Microsoft/openshift-container-platform/) som fören klar distributionen av OpenShift container Platform-klustret.
- Ett annat alternativ är att använda [Azure Marketplace-erbjudandet](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy).

För alla alternativ krävs en Red Hat-prenumeration. Under distributionen registreras Red Hat Enterprise Linux-instansen till Red Hat-prenumerationen och kopplas till det pool-ID som innehåller rättigheterna för OpenShift container Platform.
Se till att du har ett giltigt användar namn, lösen ord och pool-ID för Red Hat Subscription Manager (RHSM). Du kan använda en aktiverings nyckel, ett org-ID och ett pool-ID. Du kan kontrol lera den här informationen genom att logga in på https://access.redhat.com .


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Distribuera med hjälp av mallen OpenShift container Platform Resource Manager 3,11

### <a name="private-clusters"></a>Privata kluster

Distribution av privata OpenShift-kluster kräver mer än att inte bara ha en offentlig IP-adress kopplad till huvud belastnings utjämningen (webb konsolen) eller till den infraröda belastningsutjämnaren (router).  Ett privat kluster använder vanligt vis en anpassad DNS-server (inte standard Azure DNS), ett anpassat domän namn (t. ex. contoso.com) och fördefinierade virtuella nätverk (n).  För privata kluster måste du konfigurera ditt virtuella nätverk med alla lämpliga undernät och DNS-serverinställningar i förväg.  Använd sedan **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference** och **existingNodeSubnetReference** för att ange det befintliga under nätet som ska användas av klustret.

Om du väljer privat huvud server (**masterClusterType**= Private) måste du ange en statisk privat IP-adress för **masterPrivateClusterIp**.  Den här IP-adressen tilldelas klient delen av huvud belastnings utjämningen.  IP-adressen måste ligga inom CIDR för huvud under nätet och den används inte.  **masterClusterDnsType** måste anges till Custom och Master-DNS-namnet måste anges för **masterClusterDns**.  DNS-namnet måste mappas till den statiska privata IP-adressen och kommer att användas för att få åtkomst till-konsolen på huvudnoderna.

Om du väljer privat router (**routerClusterType**= Private) måste du ange en statisk privat IP-adress för **routerPrivateClusterIp**.  Den här IP-adressen tilldelas klient delen av den infraröda belastningsutjämnaren.  IP-adressen måste vara i CIDR för att kunna använda under nätet och inte används.  **routingSubDomainType** måste anges till Custom och jokertecken för DNS-namn för routning måste anges för **routingSubDomain**.  

Om privata huvud servrar och privata routrar väljs måste det anpassade domän namnet anges för **domän** namn

När distributionen är klar är skydds-noden den enda noden med en offentlig IP-adress som du kan ssha till.  Även om huvudnoderna har kon figurer ATS för offentlig åtkomst visas de inte för SSH-åtkomst.

Om du vill distribuera med hjälp av Resource Manager-mallen använder du en parameter fil för att ange indataparametrarna. Om du vill anpassa distributionen ytterligare delar du GitHub-lagrings platsen och ändrar lämpliga objekt.

Några vanliga anpassnings alternativ är, men är inte begränsade till:

- Skydds VM-storlek (variabel i azuredeploy.jspå)
- Namngivnings konventioner (variabler i azuredeploy.jspå)
- OpenShift-kluster, information, modifierad via hosts-filen (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Konfigurera parameter filen

[Mallen OpenShift container Platform](https://github.com/Microsoft/openshift-container-platform) har flera grenar som är tillgängliga för olika versioner av OpenShift container Platform.  Utifrån dina behov kan du distribuera direkt från lagrings platsen eller så kan du använda lagrings platsen och göra anpassade ändringar i mallarna eller skripten innan du distribuerar.

Använd `appId` värdet från tjänstens huvud namn som du skapade tidigare för `aadClientId` parametern.

I följande exempel visas en parameter fil med namnet azuredeploy.parameters.jspå med alla nödvändiga indata.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Ersätt parametrarna med din egen information.

Olika versioner kan ha olika parametrar för att kontrol lera de nödvändiga parametrarna för den gren som du använder.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy.Parameters.jspå fil förklaring

| Egenskap | Beskrivning | Giltiga alternativ | Standardvärde |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL för artefakter (JSON, skript osv.) |  |  https: \/ /RAW.githubusercontent.com/Microsoft/OpenShift-container-Platform/Master  |
| `location` | Azure-region för att distribuera resurser till |  |  |
| `masterVmSize` | Storlek på den virtuella huvud datorn. Välj från en av de tillåtna VM-storlekarna som anges i azuredeploy.jspå filen |  | Standard_E2s_v3 |
| `infraVmSize` | Den virtuella datorns storlek. Välj från en av de tillåtna VM-storlekarna som anges i azuredeploy.jspå filen |  | Standard_D4s_v3 |
| `nodeVmSize` | Storlek på den virtuella app-noden. Välj från en av de tillåtna VM-storlekarna som anges i azuredeploy.jspå filen |  | Standard_D4s_v3 |
| `cnsVmSize` | Storleken på den virtuella CNS-noden (container Native Storage). Välj från en av de tillåtna VM-storlekarna som anges i azuredeploy.jspå filen |  | Standard_E4s_v3 |
| `osImageType` | RHEL-avbildningen som ska användas. defaultgallery: på begäran, Marketplace: avbildning från tredje part | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Om `osImageType` är Marketplace anger du sedan lämpliga värden för "utgivare", "erbjudande", "SKU", "version" av Marketplace-erbjudandet. Den här parametern är en objekt typ |  |  |
| `storageKind` | Typ av lagrings utrymme som ska användas  | leda<br> ohanterade | leda |
| `openshiftClusterPrefix` | Kluster prefix som används för att konfigurera värdnamn för alla noder.  Mellan 1 och 20 tecken |  | MyCluster |
| `minoVersion` | Den lägre versionen av OpenShift container Platform 3,11 för distribution |  | 69 |
| `masterInstanceCount` | Antal huvudnoder som ska distribueras | 1, 3, 5 | 3 |
| `infraInstanceCount` | Antal infraröda noder som ska distribueras | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Antal noder som ska distribueras | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 15, 16, 17, 21, 22, 23, 19, 28, 21, 22, 23, 24, 27 | 2 |
| `cnsInstanceCount` | Antal CNS-noder som ska distribueras | 3, 4 | 3 |
| `osDiskSize` | Storlek på operativ system disk för den virtuella datorn (i GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Storlek på datadisk som ska bifogas till noder för Docker-volym (i GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Storlek på datadisk som ska bifogas till CNS-noder för användning av glusterfs (i GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Admin-användarnamn för både OS (VM)-inloggning och inledande OpenShift-användare |  | ocpadmin |
| `enableMetrics` | Aktivera mått. Mått kräver mer resurser, så välj rätt storlek för den virtuella datorn som ska vara infraröd | true <br> falskt | falskt |
| `enableLogging` | Aktivera loggning. ElasticSearch-Pod kräver 8 GB RAM-minne så välj rätt storlek för den virtuella datorn som är virtuell | true <br> falskt | falskt |
| `enableCNS` | Aktivera intern lagring för behållare | true <br> falskt | falskt |
| `rhsmUsernameOrOrgId` | Användar namn för Red Hat-prenumerations hanteraren eller organisations-ID |  |  |
| `rhsmPoolId` | ID för den Red Hat Subscription Manager-poolen som innehåller dina OpenShift-rättigheter för Compute-noder |  |  |
| `rhsmBrokerPoolId` | ID för den Red Hat Subscription Manager-poolen som innehåller dina OpenShift-rättigheter för Master-och-hanterade noder. Om du inte har olika pool-ID anger du samma pool-ID som "rhsmPoolId" |  |
| `sshPublicKey` | Kopiera din offentliga SSH-nyckel här |  |  |
| `keyVaultSubscriptionId` | Prenumerations-ID för den prenumeration som innehåller Key Vault |  |  |
| `keyVaultResourceGroup` | Namnet på den resurs grupp som innehåller Key Vault |  |  |
| `keyVaultName` | Namnet på Key Vault som du har skapat |  |  |
| `enableAzure` | Aktivera Azure Cloud Provider | true <br> falskt | true |
| `aadClientId` | Azure Active Directory klient-ID även känt som program-ID för tjänstens huvud namn |  |  |
| `domainName` | Namnet på det anpassade domän namn som ska användas (om tillämpligt). Ange till "ingen" om du inte distribuerar fullständigt privat kluster |  | inget |
| `masterClusterDnsType` | Domän typ för webb konsolen OpenShift. default använder DNS-etiketten för den offentliga IP-adressen. med Custom kan du definiera ett eget namn | standard <br> anpassad | standard |
| `masterClusterDns` | Det anpassade DNS-namn som ska användas för åtkomst till OpenShift-webbkonsolen om du har valt anpassad för `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Om värdet är "Nipio" `routingSubDomain` används Nip.io.  Använd Custom om du har en egen domän som du vill använda för routning | nipio <br> anpassad | nipio |
| `routingSubDomain` | Det DNS-namn för jokertecken som du vill använda för routning om du har valt anpassad för `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Välj om du vill använda en befintlig Virtual Network eller skapa en ny Virtual Network | befintliga <br> ny | ny |
| `virtualNetworkResourceGroupName` | Namnet på resurs gruppen för den nya Virtual Network om du har valt "nytt" för `virtualNetworkNewOrExisting` |  | resourceGroup (). namn |
| `virtualNetworkName` | Namnet på den nya Virtual Network som ska skapas om du har valt "nytt" för `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Adressprefix för det nya virtuella nätverket |  | 10.0.0.0/14 |
| `masterSubnetName` | Namnet på huvud under nätet |  | mastersubnet |
| `masterSubnetPrefix` | CIDR som används för huvud under nätet – måste vara en delmängd av addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | Namnet på det infraröda under nätet |  | infrasubnet |
| `infraSubnetPrefix` | CIDR som används för fjärrundernät – måste vara en delmängd av addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | Namnet på nod-undernätet |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR som används för nod-undernät – måste vara en delmängd av addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Fullständig referens till befintligt undernät för överordnade noder. Behövs inte om du skapar ett nytt vNet/undernät |  |  |
| `existingInfraSubnetReference` | Fullständig referens till befintligt undernät för infraröda noder. Behövs inte om du skapar ett nytt vNet/undernät |  |  |
| `existingCnsSubnetReference` | Fullständig referens till befintligt undernät för CNS-noder. Behövs inte om du skapar ett nytt vNet/undernät |  |  |
| `existingNodeSubnetReference` | Fullständig referens till befintligt undernät för Compute-noder. Behövs inte om du skapar ett nytt vNet/undernät |  |  |
| `masterClusterType` | Ange om klustret ska använda privata eller offentliga huvud noder. Om du väljer privat, kommer huvudnoderna inte att exponeras för Internet via en offentlig IP-adress. I stället används den privata IP-adress som anges i `masterPrivateClusterIp` | folkhälsan <br> personligt | folkhälsan |
| `masterPrivateClusterIp` | Om du väljer privata huvudnoder måste du ange en privat IP-adress som ska användas av den interna belastningsutjämnaren för huvudnoder. Den här statiska IP-adressen måste vara i CIDR-blocket för huvud under nätet och används inte redan. Om de offentliga huvudnoderna väljs, används inte det här värdet, men det måste fortfarande anges |  | 10.1.0.200 |
| `routerClusterType` | Ange om klustret ska använda privata eller offentliga fjärrnoder. Om du väljer privat är de infraröda noderna inte tillgängliga för Internet via en offentlig IP-adress. I stället används den privata IP-adress som anges i `routerPrivateClusterIp` | folkhälsan <br> personligt | folkhälsan |
| `routerPrivateClusterIp` | Om du väljer privata fjärrnoder måste du ange en privat IP-adress som ska användas av den interna belastningsutjämnaren för infraröda noder. Den här statiska IP-adressen måste vara i CIDR-blocket för det infraröda under nätet och den används inte redan. Om du väljer offentliga fjärrnoder används inte det här värdet, men det måste fortfarande anges |  | 10.2.0.200 |
| `routingCertType` | Använd anpassat certifikat för routningsdomänen eller det självsignerade självsignerade certifikatet – Följ instruktionerna i avsnittet **anpassade certifikat** | selfsigned <br> anpassad | selfsigned |
| `masterCertType` | Använd anpassat certifikat för huvud domän eller standard självsignerade certifikat – Följ instruktionerna i avsnittet **anpassade certifikat** | selfsigned <br> anpassad | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Distribuera med Azure CLI

> [!NOTE] 
> Följande kommando kräver Azure CLI 2.0.8 eller senare. Du kan kontrol lera CLI-versionen med `az --version` kommandot. Information om hur du uppdaterar CLI-versionen finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

I följande exempel distribueras OpenShift-klustret och alla relaterade resurser till en resurs grupp med namnet openshiftrg, med ett distributions namn på myOpenShiftCluster. Mallen refereras direkt från GitHub-lagrings platsen och en lokal parameter fil med namnet azuredeploy.parameters.jsi filen används.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Distributionen tar minst 60 minuter att slutföra, baserat på det totala antalet distribuerade noder och alternativ som kon figurer ATS. Skydds DNS FQDN och URL för OpenShift-konsolen skrivs till terminalen när distributionen är klar.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Om du inte vill koppla upp kommando raden som väntar på att distributionen ska slutföras, lägger du till `--no-wait` som ett alternativ för grupp distributionen. Utdata från distributionen kan hämtas från Azure Portal i avsnittet distribution för resurs gruppen.

## <a name="connect-to-the-openshift-cluster"></a>Ansluta till OpenShift-klustret

När distributionen är klar hämtar du anslutningen från avsnittet utdata i distributionen. Anslut till OpenShift-konsolen med webbläsaren med hjälp av **URL: en för OpenShift-konsolen**. Du kan också SSH till skydds-värden. Följande är ett exempel där admin-användarnamnet är clusteradmin och skydds offentliga IP DNS FQDN är bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Rensa resurser

Använd kommandot [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen, OpenShift-klustret och alla relaterade resurser när de inte längre behövs.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Nästa steg

- [Uppgifter efter distribution](./openshift-container-platform-3x-post-deployment.md)
- [Felsöka OpenShift-distribution i Azure](./openshift-container-platform-3x-troubleshooting.md)
- [Kom igång med OpenShift container Platform](https://docs.openshift.com)
