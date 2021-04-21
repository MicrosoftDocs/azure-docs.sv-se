---
title: Självstudie – lägga till funktioner i Azure Resource Manager Bicep-filer
description: Lägg till funktioner i dina Bicep-filer för att konstruera värden.
author: mumian
ms.date: 04/20/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: 43dec6ceb21a6604bc0034b3f14b79ffd2cbe263
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773809"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Självstudie: Lägga till funktioner i Azure Resource Manager Bicep-fil

I den här självstudien får du lära dig hur du [lägger till mallfunktioner](template-functions.md) i din Bicep-fil. Du använder funktioner för att dynamiskt konstruera värden. Bicep stöder för närvarande inte användardefinierade funktioner. Den här **självstudien tar 7 minuter** att slutföra.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om](bicep-tutorial-add-parameters.md)parametrar, men det krävs inte.

Du måste ha Visual Studio Code med Bicep-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [Bicep-verktyg.](bicep-tutorial-create-first-bicep.md#get-tools)

## <a name="review-bicep-file"></a>Granska Bicep-filen

I slutet av den föregående självstudien hade bicep-filen följande innehåll:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

Lagringskontots plats är hårdkodad till **USA, östra.** Du kan dock behöva distribuera lagringskontot till andra regioner. Du har återigen problem med att bicep-filen saknar flexibilitet. Du kan lägga till en parameter för plats, men det skulle vara bra om dess standardvärde vore mer meningsfullt än bara ett hårdkodat värde.

## <a name="use-function"></a>Använd funktion

Funktioner ger bicep-filen flexibilitet genom att hämta värden dynamiskt under distributionen. I den här självstudien använder du en funktion för att hämta platsen för den resursgrupp som du använder för distribution.

I följande exempel visas ändringarna för att lägga till en parameter med namnet `location` . Parameterns standardvärde anropar [resourceGroup-funktionen.](template-functions-resource.md#resourcegroup) Den här funktionen returnerar ett -objekt med information om resursgruppen som används för distribution. En av egenskaperna för objektet är en platsegenskap. När du använder standardvärdet har lagringskontots plats samma plats som resursgruppen. Resurserna i en resursgrupp behöver inte dela samma plats. Du kan också ange en annan plats när det behövs.

Kopiera hela filen och ersätt bicep-filen med dess innehåll.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Distribuera Bicep-fil

I föregående självstudier skapade du ett lagringskonto i USA, östra, men resursgruppen skapades i USA, centrala. I den här självstudien skapas ditt lagringskonto i samma region som resursgruppen. Använd standardvärdet för plats, så du behöver inte ange det parametervärdet. Du måste ange ett nytt namn för lagringskontot eftersom du skapar ett lagringskonto på en annan plats. Använd till exempel **store2 som** prefix i stället för **store1**.

Om du inte har skapat resursgruppen kan du se [Skapa resursgrupp.](bicep-tutorial-create-first-bicep.md#create-resource-group) I exemplet förutsätter vi att du har angett variabeln till sökvägen `bicepFile` till Bicep-filen, som du ser i den första [självstudien](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill köra den här distributions-cmdleten måste du ha [den senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Om distributionen misslyckades använder du `verbose` växeln för att hämta information om de resurser som skapas. Använd `debug` växeln för att få mer information för felsökning.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan verifiera distributionen genom att utforska resursgruppen från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj Resursgrupper på den **vänstra menyn.**
1. Välj den resursgrupp som du distribuerade till.
1. Du ser att en lagringskontoresurs har distribuerats och har samma plats som resursgruppen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resursgruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resursgruppen.

1. I Azure Portal väljer du **Resursgrupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **Ta bort resursgrupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du en funktion när du definierade standardvärdet för en parameter. I den här självstudieserien fortsätter du att använda funktioner. I slutet av serien lägger du till funktioner i varje avsnitt i Bicep-filen.

> [!div class="nextstepaction"]
> [Lägga till variabler](bicep-tutorial-add-variables.md)
