---
title: 'Snabb start: skapa en Azure SignalR service – ARM-mall'
description: I den här snabb starten får du lära dig hur du skapar en Azure SignalR-tjänst med en Azure Resource Manager-mall (ARM-mall).
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: zhshang
ms.date: 10/02/2020
ms.openlocfilehash: 4ab029048b37a4dcb44ef405249dcb9e20de70cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94841640"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Snabb start: Använd en ARM-mall för att distribuera Azure SignalR-tjänsten

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en Azure SignalR-tjänst. Du kan distribuera Azure SignalR-tjänsten via Azure Portal, PowerShell eller CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas i Azure Portal när du loggar in.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="För att distribuera Azure SignalR service till Azure med en ARM-mall i Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>Förutsättningar

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).
* Om du vill köra koden lokalt [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/).
* Om du vill köra koden lokalt:
    * Ett bash-gränssnitt (till exempel git bash, som ingår i [git för Windows](https://gitforwindows.org)).
    * [Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-signalr/).

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

Mallen definierar en Azure-resurs:

* [**Microsoft. SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>Distribuera mallen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Välj följande länk för att distribuera Azure SignalR-tjänsten med ARM-mallen i Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="För att distribuera Azure SignalR service till Azure med ARM-mallen i Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

På sidan **distribuera en Azure SignalR-tjänst** :

1. Om du vill kan du ändra **prenumerationen** från standardvärdet.

2. För **resurs grupp** väljer du **Skapa ny**, anger ett namn för den nya resurs gruppen och väljer **OK**.

3. Om du har skapat en ny resurs grupp väljer du en **region** för resurs gruppen.

4. Om du vill kan du ange ett nytt **namn** och en annan **plats** (till exempel **eastus2**) för Azure SignalR-tjänsten. Om du inte anger något namn genereras det automatiskt. Platsen för Azure SignalR-tjänsten kan vara samma som eller en annan från den region där resurs gruppen finns. Om du inte anger en plats har den angetts till samma region som resurs gruppen.

5. Välj **pris nivå** (**Free_F1** eller **Standard_S1**), ange **kapaciteten** (antal signal enheter) och välj ett **tjänst läge** som **standard** (kräver Hub-servern), utan **Server** (tillåter inte någon server anslutning) eller **klassisk** (dirigerad till NAV Server endast om hubben har server anslutning). Välj sedan om du vill **Aktivera anslutnings loggar** eller **Aktivera meddelande loggar**.

    > [!NOTE]
    > För **Free_F1** pris nivån är kapaciteten begränsad till 1 enhet.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Skärm bild av ARM-mallen för att skapa en Azure SignalR-tjänst i Azure Portal.":::

6. Välj **Granska + skapa**.

7. Läs igenom villkoren och välj sedan **skapa**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Om du vill köra PowerShell-skripten lokalt börjar `Connect-AzAccount` du med att ange dina Azure-autentiseringsuppgifter.

Använd följande kod för att distribuera Azure SignalR-tjänsten med ARM-mallen. Koden efterfrågar följande objekt:

* Namn och region för den nya Azure SignalR-tjänsten
* Namn och region för en ny resurs grupp
* Pris nivån för Azure (**Free_F1** eller **Standard_S1**)
* Signal enhetens kapacitet (1, 2, 5, 10, 20, 50 eller 100)
  > [!NOTE]
  > För **Free_F1** pris nivån är kapaciteten begränsad till 1 enhet.
* Tjänst läge: **standard** för att kräva en NAV Server, **Server** lös för att inte tillåta någon server anslutning eller **klassisk** att endast dirigera till en NAV Server om navet har en server anslutning
* Om du vill aktivera loggar för anslutning eller meddelanden (**Sant** eller **falskt**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Använd följande kod för att distribuera Azure SignalR-tjänsten med ARM-mallen. Koden efterfrågar följande objekt:

* Namn och region för den nya Azure SignalR-tjänsten
* Namn och region för en ny resurs grupp
* Pris nivån för Azure (**Free_F1** eller **Standard_S1**)
* Signal enhetens kapacitet (1, 2, 5, 10, 20, 50 eller 100)
    > [!NOTE]
    > För **Free_F1** pris nivån är kapaciteten begränsad till 1 enhet.
* Tjänst läge: **standard** för att kräva en NAV Server, **Server** lös för att inte tillåta någon server anslutning eller **klassisk** att endast dirigera till en NAV Server om navet har en server anslutning
* Om du vill aktivera loggar för anslutning eller meddelanden (**Sant** eller **falskt**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Det kan ta några minuter att slutföra distributionen. Anteckna namnen på Azure SignalR-tjänsten och resurs gruppen, som du kan använda för att granska de distribuerade resurserna senare.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill se en översikt över din nya Azure SignalR-tjänst:

1. Sök efter och välj **signaler** i [Azure Portal](https://portal.azure.com).

2. I listan signaler väljer du den nya tjänsten. **Översikts** sidan för den nya Azure SignalR-tjänsten visas.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Kör följande interaktiva kod om du vill visa information om din Azure SignalR-tjänst. Du måste ange namnet på den nya tjänsten och resurs gruppen.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Kör följande interaktiva kod om du vill visa information om din Azure SignalR-tjänst. Du måste ange namnet på den nya tjänsten och resurs gruppen.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resurs gruppen, som tar bort resurserna i resurs gruppen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **resurs grupper**.

2. I listan resurs grupp väljer du namnet på din resurs grupp.

3. På sidan **Översikt** i resurs gruppen väljer du **ta bort resurs grupp**.

4. I bekräftelse dialog rutan skriver du namnet på din resurs grupp och väljer sedan **ta bort**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Nästa steg

En steg-för-steg-guide som vägleder dig genom processen att skapa en ARM-mall finns i:

> [!div class="nextstepaction"]
> [ Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
