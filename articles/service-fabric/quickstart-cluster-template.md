---
title: Skapa ett Service Fabric kluster med hjälp Azure Resource Manager mall
description: I den här snabbstarten skapar du ett Azure Service Fabric-testkluster med hjälp Azure Resource Manager mall.
author: erikadoyle
ms.author: edoyle
ms.date: 07/29/2020
ms.topic: quickstart
ms.service: service-fabric
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: cb3758ae837dc03789371d00f768b257a9ef295d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535318"
---
# <a name="quickstart-create-a-service-fabric-cluster-using-arm-template"></a>Snabbstart: Skapa ett Service Fabric med arm-mall

Azure Service Fabric är en distribuerad systemplattform som gör det enkelt att paketera, distribuera och hantera skalbara och tillförlitliga mikrotjänster och containrar. Ett Service Fabric *är* en nätverksansluten uppsättning virtuella datorer där dina mikrotjänster distribueras och hanteras. I den här artikeln beskrivs hur du distribuerar Service Fabric ett testkluster i Azure med en Azure Resource Manager mall (ARM-mall).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Det här Windows-klustret med fem noder skyddas med ett själv signerat certifikat och är därför endast avsett för instruktionsändamål (i stället för produktionsarbetsbelastningar). Vi använder Azure PowerShell för att distribuera mallen. Förutom att Azure PowerShell kan du även använda Azure Portal, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-portal.md)

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-secure-cluster-5-node-1-nodetype%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

### <a name="install-service-fabric-sdk-and-powershell-modules"></a>Installera Service Fabric SDK och PowerShell-moduler

För att slutföra den här snabbstarten måste du:

* Installera [Service Fabric SDK och PowerShell-modulen](service-fabric-get-started.md).

* Installera [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="download-the-sample-template-and-certificate-helper-script"></a>Ladda ned exempelmallen och hjälpskriptet för certifikat

Klona eller ladda ned [lagringsplatsen Azure Resource Manager snabbstartsmallar.](https://github.com/Azure/azure-quickstart-templates) Du kan också kopiera ned följande filer lokalt från mappen *service-fabric-secure-cluster-5-node-1-nodetype:*

* [New-ServiceFabricClusterCertificate.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/scripts/New-ServiceFabricClusterCertificate.ps1)
* [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)
* [azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.parameters.json)

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure och ange den prenumeration som ska användas för att skapa Service Fabric kluster.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

### <a name="create-a-self-signed-certificate-stored-in-key-vault"></a>Skapa ett själv signerat certifikat som lagras i Key Vault

Service Fabric använder X.509-certifikat [](./service-fabric-cluster-security.md) för att skydda ett kluster och tillhandahålla programsäkerhetsfunktioner, och Key Vault [för](../key-vault/general/overview.md) att hantera dessa certifikat. För att kluster ska kunna skapas krävs ett klustercertifikat för att aktivera nod-till-nod-kommunikation. För att skapa det här snabbstartstestklustret skapar vi ett själv signerat certifikat för klusterautentisering. Produktionsarbetsbelastningar kräver certifikat som skapats med en korrekt konfigurerad Windows Server-certifikattjänst eller ett från en godkänd certifikatutfärdare (CA).

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

Skriptet uppmanar dig att ange följande (se till att ändra *CertDNSName* och *KeyVaultName* från exempelvärdena nedan):

* **Lösenord:** Lösenord!1
* **CertDNSName:** *sfquickstart*.southcentralus.cloudapp.azure.com
* **KeyVaultName:** *SFQuickstartKV*
* **KeyVaultSecretName:** clustercert

När skriptet har slutförts anger det de parametervärden som behövs för malldistributionen. Se till att lagra dessa i följande variabler, eftersom de kommer att behövas för att distribuera din klustermall:

```powershell
$sourceVaultId = "<Source Vault Resource Id>"
$certUrlValue = "<Certificate URL>"
$certThumbprint = "<Certificate Thumbprint>"
```

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/). Mallen för den här artikeln är för lång för att visas här. Om du vill visa mallen går du [azuredeploy.jspå](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json) filen.

Flera Azure-resurser har definierats i mallen:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)
* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)

Du hittar fler mallar som är relaterade till Azure Service Fabric i [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/?sort=Popular&term=service+fabric).

### <a name="customize-the-parameters-file"></a>Anpassa parameterfilen

Öppna *azuredeploy.parameters.jspå* och redigera parametervärdena så att:

* **clusterName** matchar det värde som du angav för *CertDNSName när* du skapade klustercertifikatet
* **adminUserName är** ett annat värde än *standard-GEN-UNIQUE-token*
* **adminPassword** är ett annat värde än *standardtoken för GEN-PASSWORD*
* **certificateThumbprint,** **sourceVaultResourceId** och **certificateUrlValue** är alla tomma strängar ( `""` )

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

Lagra sökvägarna för ARM-mallen och parameterfilerna i variabler och distribuera sedan mallen.

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

När distributionen är klar hittar du värdet i utdata och öppnar adressen i en webbläsare för att visa klustret `managementEndpoint` i [Service Fabric Explorer](./service-fabric-visualizing-your-cluster.md).

![Service Fabric Explorer visar nytt kluster](./media/quickstart-cluster-template/service-fabric-explorer.png)

Du hittar även slutpunkten Service Fabric Explorer service Explorer-resursbladet i Azure Portal.

![Service Fabric resursblad som visar Service Fabric Explorer slutpunkt](./media/quickstart-cluster-template/service-fabric-explorer-endpoint-azure-portal.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen när den inte längre behövs, vilket tar bort resurserna i resursgruppen.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Ta sedan bort klustercertifikatet från det lokala arkivet. Visa en lista över installerade certifikat för att hitta tumavtrycket för klustret:

```powershell
Get-ChildItem Cert:\CurrentUser\My\
```

Ta sedan bort certifikatet:

```powershell
Get-ChildItem Cert:\CurrentUser\My\{THUMBPRINT} | Remove-Item
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skapar en anpassad azure Service Fabric klustermall finns i:

> [!div class="nextstepaction"]
> [Skapa en Service Fabric för Resource Manager kluster](service-fabric-cluster-creation-create-template.md)
