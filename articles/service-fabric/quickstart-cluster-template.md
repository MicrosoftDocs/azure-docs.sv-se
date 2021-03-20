---
title: Skapa ett Service Fabric kluster med Azure Resource Manager-mall
description: I den här snabb starten skapar du ett Azure Service Fabric test kluster genom att använda Azure Resource Manager mall.
author: erikadoyle
ms.service: service-fabric
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: edoyle
ms.date: 07/29/2020
ms.openlocfilehash: 359b527733ee8eebf7e1e7d12c40a0c74ec1c9bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460311"
---
# <a name="quickstart-create-a-service-fabric-cluster-using-arm-template"></a>Snabb start: skapa ett Service Fabric kluster med ARM-mall

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster och containrar. Ett Service Fabric *kluster* är en nätverksansluten uppsättning virtuella datorer där dina mikrotjänster distribueras och hanteras. Den här artikeln beskriver hur du distribuerar ett Service Fabric test kluster i Azure med hjälp av en Azure Resource Manager-mall (ARM-mall).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Detta Windows-kluster med fem noder skyddas med ett självsignerat certifikat som därför endast är avsett för instruktions ändamål (i stället för produktions arbets belastningar). Vi använder Azure PowerShell för att distribuera mallen. Förutom Azure PowerShell kan du också använda Azure Portal, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-portal.md).

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-secure-cluster-5-node-1-nodetype%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

### <a name="install-service-fabric-sdk-and-powershell-modules"></a>Installera Service Fabric SDK och PowerShell-moduler

För att slutföra den här snabb starten måste du:

* Installera [modulen Service Fabric SDK och PowerShell](service-fabric-get-started.md).

* Installera [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="download-the-sample-template-and-certificate-helper-script"></a>Ladda ned exempel mal len och certifikat hjälps skriptet

Klona eller hämta [Azure Resource Manager snabb starts mallar](https://github.com/Azure/azure-quickstart-templates) lagrings platsen. Du kan också kopiera nedåt lokalt följande filer som vi kommer att använda från mappen *Service-Fabric-Secure-Cluster-5-Node-1-NodeType* :

* [New-ServiceFabricClusterCertificate.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/scripts/New-ServiceFabricClusterCertificate.ps1)
* [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)
* [azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.parameters.json)

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure och ange den prenumeration som ska användas för att skapa ditt Service Fabric-kluster.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

### <a name="create-a-self-signed-certificate-stored-in-key-vault"></a>Skapa ett självsignerat certifikat som lagras i Key Vault

Service Fabric använder X. 509-certifikat för att [skydda ett kluster](./service-fabric-cluster-security.md) och tillhandahålla funktioner för program säkerhet och [Key Vault](../key-vault/general/overview.md) för att hantera dessa certifikat. För att kunna skapa kluster krävs ett kluster certifikat för att aktivera kommunikation mellan noder. För att kunna skapa det här snabb starts test klustret skapar vi ett självsignerat certifikat för klientautentisering. Produktions arbets belastningar kräver certifikat som skapats med en korrekt konfigurerad Windows Server-certifikatutfärdare eller en från en godkänd certifikat utfärdare (CA).

```powershell
# Designate unique (within cloudapp.azure.com) names for your resources
$resourceGroupName = "SFQuickstartRG"
$keyVaultName = "SFQuickstartKV"

# Create a new resource group for your Key Vault and Service Fabric cluster
New-AzResourceGroup -Name $resourceGroupName -Location SouthCentralUS

# Create a Key Vault enabled for deployment
New-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -Location SouthCentralUS -EnabledForDeployment

# Generate a certificate and upload it to Key Vault
.\scripts\New-ServiceFabricClusterCertificate.ps1
```

Skriptet kommer att uppmana dig att ange följande (se till att ändra *CertDNSName* och *KeyVaultName* från exempel värden nedan):

* **Lösen ord:** Lösen ord! 1
* **CertDNSName:** *sfquickstart*. southcentralus.cloudapp.Azure.com
* **KeyVaultName:** *SFQuickstartKV*
* **KeyVaultSecretName:** clustercert

Vid slut för ande tillhandahåller skriptet de parameter värden som krävs för mall distributionen. Se till att lagra dessa i följande variabler, eftersom de behövs för att distribuera kluster mal len:

```powershell
$sourceVaultId = "<Source Vault Resource Id>"
$certUrlValue = "<Certificate URL>"
$certThumbprint = "<Certificate Thumbprint>"
```

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/). Mallen för den här artikeln är för lång för att kunna visas här. Om du vill visa mallen går du till [azuredeploy.jspå](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json) filen.

Flera Azure-resurser har definierats i mallen:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Network/belastningsutjämnare](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)
* [Microsoft. ServiceFabric/kluster](/azure/templates/microsoft.servicefabric/clusters)

Du hittar fler mallar som är relaterade till Azure Service Fabric i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?sort=Popular&term=service+fabric).

### <a name="customize-the-parameters-file"></a>Anpassa parameter filen

Öppna *azuredeploy.parameters.jspå* och redigera parameter värden så att:

* **kluster** namn matchar värdet du angav för *CertDNSName* när du skapade ditt kluster certifikat
* **adminUserName** är ett annat värde än standardvärdet *generation-Unique*
* **adminPassword** är ett annat värde än standard-token för *GENERATIONs lösen ord*
* **certificateThumbprint**, **sourceVaultResourceId** och **certificateUrlValue** är en tom sträng ( `""` )

Exempel:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "sfquickstart"
    },
    "adminUsername": {
      "value": "testadm"
    },
    "adminPassword": {
      "value": "Password#1234"
    },
    "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultResourceId": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    }
  }
}
```

## <a name="deploy-the-template"></a>Distribuera mallen

Lagra Sök vägarna för ARM-mallen och-parametervärdena i variabler och distribuera sedan mallen.

```powershell
$templateFilePath = "<full path to azuredeploy.json>"
$parameterFilePath = "<full path to azuredeploy.parameters.json>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $certThumbprint `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultResourceId $sourceVaultId `
    -Verbose
```

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När distributionen är klar söker du efter `managementEndpoint` värdet i utdata och öppnar adressen i en webbläsare för att Visa klustret i [Service Fabric Explorer](./service-fabric-visualizing-your-cluster.md).

![Service Fabric Explorer visar nya kluster](./media/quickstart-cluster-template/service-fabric-explorer.png)

Du kan också hitta Service Fabric Explorer slut punkten från resurs bladet för service Explorer i Azure Portal.

![Service Fabric resurs bladet som visar Service Fabric Explorer slut punkten](./media/quickstart-cluster-template/service-fabric-explorer-endpoint-azure-portal.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, som tar bort resurserna i resurs gruppen.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Ta sedan bort kluster certifikatet från det lokala arkivet. Lista installerade certifikat för att hitta tumavtrycket för klustret:

```powershell
Get-ChildItem Cert:\CurrentUser\My\
```

Ta sedan bort certifikatet:

```powershell
Get-ChildItem Cert:\CurrentUser\My\{THUMBPRINT} | Remove-Item
```

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar en anpassad Azure Service Fabric Cluster-mall finns i:

> [!div class="nextstepaction"]
> [Skapa en Service Fabric Cluster Resource Manager-mall](service-fabric-cluster-creation-create-template.md)
