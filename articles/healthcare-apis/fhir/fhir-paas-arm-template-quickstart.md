---
title: 'Snabb start: Distribuera Azure API för FHIR med en ARM-mall'
description: I den här snabb starten får du lära dig hur du distribuerar Azure API för snabba hälso-och sjukvårds resurser (FHIR®) genom att använda en Azure Resource Manager-mall (ARM-mall).
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: cavoeg
ms.date: 10/06/2020
ms.openlocfilehash: 8d7f71477e50e4771bbc7670312943eadf1a8657
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019711"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>Snabb start: Använd en ARM-mall för att distribuera Azure API för FHIR

I den här snabb starten får du lära dig hur du använder en Azure Resource Manager mall (ARM-mall) för att distribuera Azure API för snabba hälso samverkans resurser (FHIR®). Du kan distribuera Azure API för FHIR via Azure Portal, PowerShell eller CLI.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas i Azure Portal när du loggar in.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure en Azure API för FHIR-tjänst med en ARM-mall i Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

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

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/).

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

Mallen definierar en Azure-resurs:

* **Microsoft. HealthcareApis/Services**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>Distribuera mallen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Välj följande länk för att distribuera Azure API för FHIR med ARM-mallen i Azure Portal:

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Distribuera till Azure en Azure API för FHIR-tjänst med ARM-mallen i Azure Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

På sidan **Distribuera Azure API för FHIR** :

1. Om du vill kan du ändra **prenumerationen** från standardvärdet till en annan prenumeration.

2. För **resurs grupp** väljer du **Skapa ny**, anger ett namn för den nya resurs gruppen och väljer **OK**.

3. Om du har skapat en ny resurs grupp väljer du en **region** för resurs gruppen.

4. Ange ett nytt **tjänst namn** och välj **platsen** för Azure-API: t för FHIR. Platsen kan vara samma som eller en annan från regionen för resurs gruppen.

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="Distribuera Azure API för FHIR med ARM-mallen i Azure Portal.":::

5. Välj **Granska + skapa**.

6. Läs igenom villkoren och välj sedan **skapa**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Om du vill köra PowerShell-skripten lokalt börjar `Connect-AzAccount` du med att ange dina Azure-autentiseringsuppgifter.

Om `Microsoft.HealthcareApis` resurs leverantören inte redan har registrerats för din prenumeration kan du registrera den med följande interaktiva kod. Om du vill köra koden i Azure Cloud Shell väljer du **testa den** i det övre hörnet av ett kodblock.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Använd följande kod för att distribuera Azure API för FHIR-tjänsten med ARM-mallen. Koden efterfrågar det nya FHIR-tjänst namnet, namnet på en ny resurs grupp och platserna för var och en av dem.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Om `Microsoft.HealthcareApis` resurs leverantören inte redan har registrerats för din prenumeration kan du registrera den med följande interaktiva kod. Om du vill köra koden i Azure Cloud Shell väljer du **testa den** i det övre hörnet av ett kodblock.

```azurecli-interactive
az extension add --name healthcareapis
```

Använd följande kod för att distribuera Azure API för FHIR-tjänsten med ARM-mallen. Koden efterfrågar det nya FHIR-tjänst namnet, namnet på en ny resurs grupp och platserna för var och en av dem.

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Det tar några minuter att slutföra distributionen. Anteckna namnen på Azure API för FHIR-tjänsten och resurs gruppen, som du använder för att granska de distribuerade resurserna senare.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill se en översikt över din nya Azure API for FHIR-tjänst:

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **Azure API för FHIR**.

2. I listan FHIR väljer du den nya tjänsten. **Översikts** sidan för den nya Azure API för FHIR-tjänsten visas.

3. Om du vill kontrol lera att det nya FHIR API-kontot har tillhandahållits väljer du länken bredvid **FHIR för metadata** för att hämta API-funktionen FHIR API. Länken har formatet `https://<service-name>.azurehealthcareapis.com/metadata` . Om kontot har tillhandahållits visas en stor JSON-fil.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Kör följande interaktiva kod om du vill visa information om din Azure API for FHIR-tjänst. Du måste ange namnet på den nya tjänsten och resurs gruppen.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[CLI](#tab/CLI)

Kör följande interaktiva kod om du vill visa information om din Azure API for FHIR-tjänst. Du måste ange namnet på den nya tjänsten och resurs gruppen.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
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

En steg-för-steg-guide som vägleder dig genom processen att skapa en ARM-mall finns i [självstudien för att skapa och distribuera din första arm-mall](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

## <a name="next-steps"></a>Nästa steg

I den här snabb starts guiden har du distribuerat Azure API för FHIR i din prenumeration. Om du vill ange ytterligare inställningar i Azure-API: et för FHIR går du vidare till ytterligare inställningar instruktions guide. Läs mer om hur du registrerar program om du är redo att börja använda Azure API för FHIR.

>[!div class="nextstepaction"]
>[Ytterligare inställningar i Azure API för FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Översikt över program register](fhir-app-registration.md)
