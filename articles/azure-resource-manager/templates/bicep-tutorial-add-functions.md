---
title: Självstudie – lägga till funktioner i Azure Resource Manager bicep-filer
description: Lägg till funktioner till dina bicep-filer för att skapa värden.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: b909beb0cce9ad04ba00068ee25247520dcff47d
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633163"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Självstudie: lägga till funktioner i Azure Resource Manager bicep-fil

I den här självstudien får du lära dig hur du lägger till [mallar](template-functions.md) i din bicep-fil. Du använder funktioner för att dynamiskt konstruera värden. Förutom dessa funktioner som tillhandahålls av systemet kan du också skapa [användardefinierade funktioner](./template-user-defined-functions.md). Den här självstudien tar **7 minuter** att slutföra.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om parametrar](bicep-tutorial-add-parameters.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med bicep-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [bicep-verktyg](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Granska bicep-fil

I slutet av den föregående själv studie kursen hade bicep-filen följande innehåll:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

Platsen för lagrings kontot är hårdkodad till **USA, östra**. Du kan dock behöva distribuera lagrings kontot till andra regioner. Du är återigen inriktad på ett problem med din bicep-fil som saknar flexibilitet. Du kan lägga till en parameter för platsen, men det skulle vara bra om standardvärdet har gjorts mer meningsfullt än bara ett hårdkodat värde.

## <a name="use-function"></a>Använd funktion

Funktioner ger en flexibel bicep-fil genom att dynamiskt hämta värden under distributionen. I den här självstudien använder du en funktion för att hämta platsen för den resurs grupp som du använder för distribution.

I följande exempel visas ändringarna för att lägga till en parameter med namnet `location` . Standardvärdet för parametern anropar funktionen [resourceGroup](template-functions-resource.md#resourcegroup) . Den här funktionen returnerar ett objekt med information om resurs gruppen som används för distribution. En av egenskaperna för objektet är en plats egenskap. När du använder standardvärdet har lagrings konto platsen samma plats som resurs gruppen. Resurserna i en resurs grupp behöver inte dela samma plats. Du kan också ange en annan plats när det behövs.

Kopiera hela filen och ersätt din bicep-fil med dess innehåll.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Distribuera bicep-fil

I de föregående självstudierna skapade du ett lagrings konto i östra USA, men resurs gruppen skapades i Central USA. I den här självstudien skapas ditt lagrings konto i samma region som resurs gruppen. Använd standardvärdet för plats, så du behöver inte ange detta parameter värde. Du måste ange ett nytt namn för lagrings kontot eftersom du skapar ett lagrings konto på en annan plats. Använd till exempel **store2** som prefix i stället för **store1**.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](bicep-tutorial-create-first-bicep.md#create-resource-group). Exemplet förutsätter att du har angett `bicepFile` variabeln till sökvägen till bicep-filen, som du ser i den [första självstudien](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För att kunna köra denna distributions-cmdlet måste du ha den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

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
> Om distributionen misslyckades använder du `verbose` växeln för att hämta information om de resurser som skapas. Använd `debug` växeln för att få mer information om fel sökning.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan kontrol lera distributionen genom att utforska resurs gruppen från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper** på den vänstra menyn.
1. Välj den resurs grupp som du har distribuerat till.
1. Du ser att en lagrings konto resurs har distribuerats och har samma plats som resurs gruppen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du en funktion när du definierade standardvärdet för en parameter. I den här självstudien ska du fortsätta använda functions. I slutet av serien lägger du till funktioner i alla avsnitt i bicep-filen.

> [!div class="nextstepaction"]
> [Lägga till variabler](bicep-tutorial-add-variables.md)
