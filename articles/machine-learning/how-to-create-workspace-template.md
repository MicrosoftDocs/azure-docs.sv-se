---
title: Skapa en arbets yta med Azure Resource Manager mall
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder en Azure Resource Manager mall för att skapa en ny Azure Machine Learning arbets yta.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli, devx-track-azurepowershell
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.openlocfilehash: 9df8a67fd3dfbf23986f1cc5ed18392463fc7ecb
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522214"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Använd en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning


<br>

I den här artikeln får du lära dig flera sätt att skapa en Azure Machine Learning arbets yta med Azure Resource Manager-mallar. En Resource Manager-mall gör det enkelt att skapa resurser som en enda, koordinerad åtgärd. En mall är ett JSON-dokument som definierar de resurser som behövs för en distribution. Den kan också ange distributions parametrar. Parametrar används för att ange indatavärden när du använder mallen.

Mer information finns i [distribuera ett program med Azure Resource Manager-mall](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Om du inte har en sådan kan du prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* Om du vill använda en mall från en CLI behöver du antingen [Azure PowerShell](/powershell/azure/) eller [Azure CLI](/cli/azure/install-azure-cli).

* Vissa scenarier kräver att du öppnar ett support ärende. Du kan till exempel använda en privat länk aktive rad arbets yta med en kundhanterad nyckel. Mer information finns i [Hantera och öka kvoter](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Begränsningar

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="workspace-resource-manager-template"></a>Resource Manager-mall för arbets yta

Den Azure Resource Manager-mall som används i det här dokumentet finns i katalogen [201-Machine-Learning-Avancerat](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json) i GitHub-lagringsplatsen för Azure snabb starts mal len.

Den här mallen skapar följande Azure-tjänster:

* Azure Storage-konto
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-arbetsyta

Resurs gruppen är den behållare som innehåller tjänsterna. De olika tjänsterna krävs av Azure Machine Learning-arbetsytan.

Exempel mal len har två **obligatoriska** parametrar:

* Den **plats** där resurserna kommer att skapas.

    Mallen kommer att använda den plats du väljer för de flesta resurser. Undantaget är Application Insights tjänst, som inte är tillgänglig på alla platser som de andra tjänsterna är. Om du väljer en plats där den inte är tillgänglig kommer tjänsten att skapas på platsen södra centrala USA.

* **WorkspaceName**, som är det egna namnet på arbets ytan Azure Machine Learning.

    > [!NOTE]
    > Namnet på arbets ytan är Skift läges okänsligt.

    Namnen på de andra tjänsterna genereras slumpmässigt.

> [!TIP]
> Även om mallen som är associerad med det här dokumentet skapar en ny Azure Container Registry, kan du också skapa en ny arbets yta utan att skapa ett behållar register. En kommer att skapas när du utför en åtgärd som kräver ett behållar register. Till exempel utbildning eller distribution av en modell.
>
> Du kan också referera till ett befintligt behållar register eller lagrings konto i Azure Resource Manager mall, i stället för att skapa ett nytt. När du gör detta måste du antingen [använda en hanterad identitet](how-to-use-managed-identities.md) (förhands granskning) eller [Aktivera administratörs kontot](../container-registry/container-registry-authentication.md#admin-account) för behållar registret.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Mer information om mallar finns i följande artiklar:

* [Redigera Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md)
* [Distribuera ett program med Azure Resource Manager mallar](../azure-resource-manager/templates/deploy-powershell.md)
* [Resurs typer för Microsoft. MachineLearningServices](/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>Distribuera mallen

Om du vill distribuera mallen måste du skapa en resurs grupp.

Se avsnittet [Azure Portal](#use-the-azure-portal) om du föredrar att använda det grafiska användar gränssnittet.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

När resurs gruppen har skapats distribuerar du mallen med följande kommando:

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

Som standard är alla resurser som skapats som en del av mallen nya. Du kan dock också välja att använda befintliga resurser. Genom att ange ytterligare parametrar för mallen kan du använda befintliga resurser. Om du till exempel vill använda ett befintligt lagrings konto anger du **storageAccountOption** -värdet till **befintlig** och anger namnet på ditt lagrings konto i parametern **storageAccountName** .

> [!IMPORTANT]
> Om du vill använda ett befintligt Azure Storage-konto kan det inte vara ett Premium-konto (Premium_LRS och Premium_GRS). Det får inte heller ha ett hierarkiskt namn område (används med Azure Data Lake Storage Gen2). Varken Premium Storage eller hierarkiskt namn område stöds med arbets ytans standard lagrings konto. Varken Premium Storage eller hierarkiska namn områden stöds med arbets ytans _standard_ lagrings konto. Du kan använda Premium Storage eller hierarkiskt namnrymd med lagrings konton som _inte är standard_ .

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>Distribuera en krypterad arbets yta

Följande exempel-mall visar hur du skapar en arbets yta med tre inställningar:

* Aktivera inställningar för hög konfidentialitet för arbets ytan. Detta skapar en ny Cosmos DB-instans.
* Aktivera kryptering för arbets ytan.
* Använder en befintlig Azure Key Vault för att hämta Kundhanterade nycklar. Kundhanterade nycklar används för att skapa en ny Cosmos DB-instans för arbets ytan.

    [!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

> [!IMPORTANT]
> När en arbets yta har skapats kan du inte ändra inställningarna för konfidentiella data, kryptering, nyckel valv-ID eller nyckel identifierare. Om du vill ändra dessa värden måste du skapa en ny arbets yta med de nya värdena.

Mer information finns i avsnittet om [kryptering i vilo](concept-data-encryption.md#encryption-at-rest)läge.

> [!IMPORTANT]
> Det finns vissa särskilda krav som din prenumeration måste uppfylla innan du använder den här mallen:
> * Du måste ha en befintlig Azure Key Vault som innehåller en krypterings nyckel.
> * Azure Key Vault måste vara i samma region där du planerar att skapa Azure Machine Learning-arbetsytan.
> * Du måste ange ID: t för Azure Key Vault och URI för krypterings nyckeln.

Använd följande steg __för att hämta värdena__ för `cmk_keyvault` (ID för Key Vault) och `resource_cmk_uri` PARAMETRARNA (nyckel-URI) som behövs för den här mallen:    

1. Använd följande kommando för att hämta Key Vault-ID:  

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault show --name <keyvault-name> --query 'id' --output tsv   
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'    
    ``` 
    --- 

    Det här kommandot returnerar ett värde som liknar `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>` .  

1. Använd följande kommando för att hämta värdet för URI: n för den hanterade nyckeln för kunden:    

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)   

    ```azurecli 
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv  
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>' 
    ``` 
    --- 

    Det här kommandot returnerar ett värde som liknar `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` . 

> [!IMPORTANT]  
> När en arbets yta har skapats kan du inte ändra inställningarna för konfidentiella data, kryptering, nyckel valv-ID eller nyckel identifierare. Om du vill ändra dessa värden måste du skapa en ny arbets yta med de nya värdena.

Om du vill aktivera användning av kund hanterade nycklar anger du följande parametrar när du distribuerar mallen:

* **encryption_status** till **aktive rad**.
* **cmk_keyvault** till `cmk_keyvault` värdet som hämtades i föregående steg.
* **resource_cmk_uri** till `resource_cmk_uri` värdet som hämtades i föregående steg.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

När du använder en kundhanterad nyckel skapar Azure Machine Learning en sekundär resurs grupp som innehåller Cosmos DB-instansen. Mer information finns i avsnittet om [kryptering i rest-Cosmos DB](concept-data-encryption.md#encryption-at-rest).

En ytterligare konfiguration som du kan tillhandahålla för dina data är att ställa in **confidential_data** parametern på **True**. Gör så här:

* Startar kryptering av den lokala virtuella datorn för Azure Machine Learning beräknings kluster, förutsatt att du inte har skapat några tidigare kluster i din prenumeration. Om du tidigare har skapat ett kluster i prenumerationen öppnar du ett support ärende för att ha kryptering av den virtuella hård disken som är aktive rad för dina beräknings kluster.
* Rensar upp den lokala Scratch-disken mellan körningar.
* Skickar autentiseringsuppgifter på ett säkert sätt för lagrings kontot, behållar registret och SSH-kontot från körnings skiktet till dina beräknings kluster med hjälp av Key Vault.
* Aktiverar IP-filtrering för att säkerställa att underliggande batch-pooler inte kan anropas av andra externa tjänster än AzureMachineLearningService.

    > [!IMPORTANT]
    > När en arbets yta har skapats kan du inte ändra inställningarna för konfidentiella data, kryptering, nyckel valv-ID eller nyckel identifierare. Om du vill ändra dessa värden måste du skapa en ny arbets yta med de nya värdena.

  Mer information finns i avsnittet om [kryptering i vilo](concept-data-encryption.md#encryption-at-rest)läge.

## <a name="deploy-workspace-behind-a-virtual-network"></a>Distribuera arbets ytan bakom ett virtuellt nätverk

Genom att ange `vnetOption` parametervärdet till antingen `new` eller `existing` kan du skapa de resurser som används av en arbets yta bakom ett virtuellt nätverk.

> [!IMPORTANT]
> Endast "Premium"-SKU stöds för container Registry.

> [!IMPORTANT]
> Application Insights stöder inte distribution bakom ett virtuellt nätverk.

### <a name="only-deploy-workspace-behind-private-endpoint"></a>Distribuera endast arbets ytan bakom privat slut punkt

Om de associerade resurserna inte ligger bakom ett virtuellt nätverk kan du ange parametern **privateEndpointType** till `AutoAproval` eller `ManualApproval` för att distribuera arbets ytan bakom en privat slut punkt. Detta kan göras för både nya och befintliga arbets ytor. När du uppdaterar en befintlig arbets yta fyller du i mallparametrar med informationen från den befintliga arbets ytan.

> [!IMPORTANT]
> Användning av en Azure Machine Learning arbets yta med privat länk är inte tillgänglig i Azure Government regionerna.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>Använd ett nytt virtuellt nätverk

Om du vill distribuera en resurs bakom ett nytt virtuellt nätverk anger du **vnetOption** till **New** tillsammans med inställningarna för det virtuella nätverket för respektive resurs. I distributionen nedan visas hur du distribuerar en arbets yta med lagrings konto resursen bakom ett nytt virtuellt nätverk.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

Du kan också distribuera flera eller alla beroende resurser bakom ett virtuellt nätverk.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>Använda ett befintligt virtuellt nätverk & resurser

Om du vill distribuera en arbets yta med befintliga kopplade resurser måste du ange parametern **vnetOption** till **befintlig** tillsammans med under näts parametrar. Du måste dock skapa tjänst slut punkter i det virtuella nätverket för var och en av resurserna **innan** du distribuerar. Precis som med nya virtuella nätverks distributioner kan du ha en eller flera av dina resurser bakom ett virtuellt nätverk.

> [!IMPORTANT]
> Under nätet måste ha `Microsoft.Storage` tjänst slut punkt

> [!IMPORTANT]
> Undernät tillåter inte att privata slut punkter skapas. Inaktivera privat slut punkt för att aktivera undernät.

1. Aktivera tjänstens slut punkter för resurserna.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. Distribuera arbets ytan

    # <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. Följ stegen i [distribuera resurser från den anpassade mallen](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template). När du kommer till skärmen __Välj en mall__ väljer du mallen **201-Machine-Learning-Avancerat** i list rutan.
1. Välj __Välj mall__ för att använda mallen. Ange följande obligatoriska information och andra parametrar beroende på ditt distributions scenario.

   * Prenumeration: Välj den Azure-prenumeration som ska användas för dessa resurser.
   * Resurs grupp: Välj eller skapa en resurs grupp som ska innehålla tjänsterna.
   * Region: Välj den Azure-region där resurserna ska skapas.
   * Namn på arbets yta: namnet som ska användas för Azure Machine Learning arbets ytan som ska skapas. Arbets ytans namn måste innehålla mellan 3 och 33 tecken. Det får bara innehålla alfanumeriska tecken och "-".
   * Plats: Välj den plats där resurserna ska skapas.
1. Välj __Granska + skapa__.
1. På skärmen __Granska + skapa__ godkänner du de angivna villkoren och väljer __skapa__.

Mer information finns i [distribuera resurser från en anpassad mall](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resurs leverantörs fel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault åtkomst princip och Azure Resource Manager mallar

När du använder en Azure Resource Manager mall för att skapa arbets ytan och associerade resurser (inklusive Azure Key Vault), flera gånger. Du kan till exempel använda mallen flera gånger med samma parametrar som en del av en kontinuerlig integrering och distributions pipeline.

De flesta åtgärder för att skapa resurser via mallar är idempotenta, men Key Vault rensar åtkomst principerna varje gång mallen används. Om du rensar åtkomst principerna bryts åtkomsten till Key Vault för en befintlig arbets yta som använder den. Till exempel kan stoppa/skapa-funktioner i Azure Notebooks virtuella datorn Miss lyckas.  

För att undvika det här problemet rekommenderar vi en av följande metoder:

* Distribuera inte mallen mer än en gång för samma parametrar. Eller ta bort de befintliga resurserna innan du använder mallen för att återskapa dem.

* Granska Key Vault åtkomst principer och Använd sedan dessa principer för att ange `accessPolicies` mallens egenskap. Om du vill visa åtkomst principerna använder du följande Azure CLI-kommando:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Mer information om hur du använder `accessPolicies` avsnittet i mallen finns i referens för [AccessPolicyEntry-objekt](/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Kontrol lera om Key Vault resursen redan finns. Om det gör det, ska du inte återskapa det via mallen. Om du till exempel vill använda den befintliga Key Vault i stället för att skapa en ny, gör du följande ändringar i mallen:

    * **Lägg till** en parameter som godkänner ID: t för en befintlig Key Vault-resurs:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Ta bort** avsnittet som skapar en Key Vault-resurs:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Ta bort** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` raden från `dependsOn` avsnittet i arbets ytan. **Ändra** också `keyVault` posten i `properties` avsnittet i arbets ytan så att den refererar till `keyVaultId` parametern:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Efter dessa ändringar kan du ange ID: t för den befintliga Key Vault-resursen när du kör mallen. Mallen återanvänds sedan Key Vault genom att ställa in `keyVault` arbets ytans egenskap till dess ID.

    Om du vill hämta ID: t för Key Vault kan du referera till utdata från den ursprungliga mallen, köra eller använda Azure CLI. Följande kommando är ett exempel på hur du använder Azure CLI för att hämta Key Vault resurs-ID:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Det här kommandot returnerar ett värde som liknar följande text:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

### <a name="virtual-network-not-linked-to-private-dns-zone"></a>Virtuellt nätverk som inte är länkat till en privat DNS-zon

När du skapar en arbets yta med en privat slut punkt skapar mallen en Privat DNS zon med namnet __privatelink.API.azureml.MS__. En __virtuell nätverks länk__ läggs automatiskt till i den här privata DNS-zonen. Länken läggs bara till för den första arbets ytan och den privata slut punkten som du skapar i en resurs grupp. Om du skapar ett annat virtuellt nätverk och en arbets yta med en privat slut punkt i samma resurs grupp, kan det andra virtuella nätverket inte läggas till i den privata DNS-zonen.

Om du vill visa de virtuella nätverks länkar som redan finns för den privata DNS-zonen använder du följande Azure CLI-kommando:

```azurecli
az network private-dns link vnet list --zone-name privatelink.api.azureml.ms --resource-group myresourcegroup
```

Använd följande steg för att lägga till det virtuella nätverket som innehåller en annan arbets yta och privat slut punkt:

1. Använd följande kommando för att hitta det virtuella nätverks-ID: t för det nätverk som du vill lägga till:

    ```azurecli
    az network vnet show --name myvnet --resource-group myresourcegroup --query id
    ```
    
    Det här kommandot returnerar ett värde som liknar ""/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet "". Spara det här värdet och Använd det i nästa steg.

2. Använd följande kommando för att lägga till en virtuell nätverks länk i privatelink.api.azureml.ms Privat DNS zon. För `--virtual-network` parametern använder du utdata från föregående kommando:

    ```azurecli
    az network private-dns link vnet create --name mylinkname --registration-enabled true --resource-group myresourcegroup --virtual-network myvirtualnetworkid --zone-name privatelink.api.azureml.ms
    ```

## <a name="next-steps"></a>Nästa steg

* [Distribuera resurser med Resource Manager-mallar och Resource Manager-REST API](../azure-resource-manager/templates/deploy-rest.md).
* [Skapa och Distribuera Azure-resurs grupper via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
* [Andra mallar som rör Azure Machine Learning finns i lagrings platsen för Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates)