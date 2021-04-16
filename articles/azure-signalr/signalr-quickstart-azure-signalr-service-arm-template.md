---
title: 'Snabbstart: Skapa en Azure SignalR Service – ARM-mall'
description: I den här snabbstarten lär du dig hur du skapar en Azure SignalR Service med en Azure Resource Manager mall (ARM-mall).
author: sffamily
ms.author: zhshang
ms.date: 10/02/2020
ms.topic: quickstart
ms.service: signalr
ms.custom:
- subject-armqs
- devx-track-azurecli
- mode-arm
ms.openlocfilehash: 075915f2a06e29eb5b80425913c6e7a2c476ff99
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536515"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Snabbstart: Använda en ARM-mall för att distribuera Azure SignalR Service

Den här snabbstarten beskriver hur du använder en Azure Resource Manager (ARM-mall) för att skapa en Azure SignalR Service. Du kan distribuera Azure SignalR Service via Azure Portal, PowerShell eller CLI.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas i Azure Portal när du loggar in.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Knapp för att Azure SignalR Service till Azure med hjälp av en ARM-mall i Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

## <a name="prerequisites"></a>Förutsättningar

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/)
* Om du vill köra koden lokalt kan du [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[CLI](#tab/CLI)

* Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free/)
* Om du vill köra koden lokalt:
    * Ett Bash-gränssnitt (till exempel Git Bash, som ingår i [Git för Windows](https://gitforwindows.org)).
    * [Azure CLI](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-signalr/).

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

Mallen definierar en Azure-resurs:

* [**Microsoft.SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>Distribuera mallen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Välj följande länk för att distribuera Azure SignalR Service med arm-mallen i Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Knapp för att Azure SignalR Service till Azure med hjälp av ARM-mallen i Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

På sidan **Distribuera en Azure SignalR Service:**

1. Om du vill kan du ändra **Prenumeration** från standardvärdet.

2. För **Resursgrupp** väljer du **Skapa ny,** anger ett namn för den nya resursgruppen och väljer **OK.**

3. Om du har skapat en ny resursgrupp väljer **du en** Region för resursgruppen.

4. Om du vill anger du ett nytt **Namn** och **Plats** (till exempel **eastus2**) för Azure SignalR Service. Om du inte anger något namn genereras det automatiskt. Platsen för Azure SignalR Service kan vara samma som eller en annan än regionen för resursgruppen. Om du inte anger en plats anges den till samma region som resursgruppen.

5. Välj **Prisnivå** (**Free_F1** eller **Standard_S1**), ange **Kapacitet (antal** SignalR-enheter)  och välj tjänstläget Standard **(kräver** hubbserver), **Serverlös** (tillåter inte serveranslutning) eller **Klassisk** (dirigeras endast till hubbservern om hubben har en serveranslutning). Välj sedan om du vill **aktivera anslutningsloggar eller** **Aktivera meddelandeloggar.**

    > [!NOTE]
    > För **Free_F1** prisnivån är kapaciteten begränsad till 1 enhet.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Skärmbild av ARM-mallen för att skapa Azure SignalR Service i Azure Portal.":::

6. Välj **Granska + skapa**.

7. Läs villkoren och välj sedan **Skapa.**

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Om du vill köra PowerShell-skripten lokalt anger du först för `Connect-AzAccount` att konfigurera dina autentiseringsuppgifter för Azure.

Använd följande kod för att distribuera Azure SignalR Service med arm-mallen. Koden uppmanar dig att ange följande objekt:

* Namn och region för den nya Azure SignalR Service
* Namn och region för en ny resursgrupp
* Azure-prisnivån (**Free_F1** eller **Standard_S1**)
* SignalR-enhetens kapacitet (1, 2, 5, 10, 20, 50 eller 100)
  > [!NOTE]
  > För **Free_F1** prisnivån är kapaciteten begränsad till 1 enhet.
* Tjänstläge: Kräver **som** standard en hubbserver, **serverlös** för att  inte tillåta någon serveranslutning eller klassisk för att dirigera till en hubbserver endast om hubben har en serveranslutning
* Om du vill aktivera loggar för anslutning eller meddelanden **(sant** eller **falskt)**

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

Använd följande kod för att distribuera Azure SignalR Service med hjälp av ARM-mallen. Koden uppmanar dig att ange följande objekt:

* Namn och region för den nya Azure SignalR Service
* Namn och region för en ny resursgrupp
* Azure-prisnivån (**Free_F1** eller **Standard_S1**)
* SignalR-enhetens kapacitet (1, 2, 5, 10, 20, 50 eller 100)
    > [!NOTE]
    > För **Free_F1** prisnivån är kapaciteten begränsad till 1 enhet.
* Tjänstläge: Kräver **som** standard en hubbserver, **serverlös** för att  inte tillåta någon serveranslutning eller klassisk för att dirigera till en hubbserver endast om hubben har en serveranslutning
* Om du vill aktivera loggar för anslutning eller meddelanden **(sant** eller **falskt)**

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
> Det kan ta några minuter att slutföra distributionen. Anteckna namnen för Azure SignalR Service och resursgruppen, som du använder för att granska de distribuerade resurserna senare.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill se en översikt över din Azure SignalR Service:

1. I [Azure Portal](https://portal.azure.com)du efter och väljer **SignalR**.

2. I listan SignalR väljer du den nya tjänsten. Sidan **Översikt** för den nya Azure SignalR Service visas.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Kör följande interaktiva kod för att visa information om Azure SignalR Service. Du måste ange namnet på den nya tjänsten och resursgruppen.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Kör följande interaktiva kod för att visa information om Azure SignalR Service. Du måste ange namnet på den nya tjänsten och resursgruppen.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar du bort resursgruppen, vilket tar bort resurserna i resursgruppen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)du efter och väljer **Resursgrupper**.

2. Välj namnet på resursgruppen i listan med resursgrupper.

3. På sidan **Översikt** för resursgruppen väljer du Ta **bort resursgrupp.**

4. I bekräftelsedialogrutan skriver du namnet på resursgruppen och väljer sedan Ta **bort.**

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

En stegvis självstudiekurs som vägleder dig genom processen för att skapa en ARM-mall finns i:

> [!div class="nextstepaction"]
> [ Självstudie: Skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
