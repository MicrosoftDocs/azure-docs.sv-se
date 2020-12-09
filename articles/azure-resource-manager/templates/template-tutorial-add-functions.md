---
title: Självstudie – Lägg till mall funktioner
description: Lägg till mallar i din Azure Resource Manager-mall (ARM-mallen) för att skapa värden.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 67c88e98d966a21163aafefcad8363086d5b3bf4
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931052"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Självstudie: Lägg till mallar i ARM-mallen

I den här självstudien får du lära dig hur du lägger till [mallar](template-functions.md) till din Azure Resource Manager-mall (arm-mall). Du använder funktioner för att dynamiskt konstruera värden. Förutom dessa funktioner som tillhandahålls av systemet kan du också skapa [användardefinierade funktioner](./template-user-defined-functions.md). Den här självstudien tar **7 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför [självstudien om parametrar](template-tutorial-add-parameters.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med Resource Manager Tools-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [verktyg för mallar](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Granska mall

I slutet av den föregående själv studie kursen hade mallen följande JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

Platsen för lagrings kontot är hårdkodad till **USA, östra**. Du kan dock behöva distribuera lagrings kontot till andra regioner. Du är återigen inriktad på ett problem med din mall som saknar flexibilitet. Du kan lägga till en parameter för platsen, men det skulle vara bra om standardvärdet har gjorts mer meningsfullt än bara ett hårdkodat värde.

## <a name="use-function"></a>Använd funktion

Om du har slutfört den föregående själv studie kursen i den här serien har du redan använt en funktion. När du har lagt till `"[parameters('storageName')]"` användes [parameter](template-functions-deployment.md#parameters) funktionen. Hakparenteserna anger att syntaxen innanför hakparenteserna är ett [mall uttryck](template-expressions.md). Resource Manager matchar syntaxen i stället för att behandla den som ett litteralt värde.

Funktioner ger dig flexibilitet i mallen genom att dynamiskt hämta värden under distributionen. I den här självstudien använder du en funktion för att hämta platsen för den resurs grupp som du använder för distribution.

I följande exempel visas ändringarna för att lägga till en parameter med namnet **plats**.  Standardvärdet för parametern anropar funktionen [resourceGroup](template-functions-resource.md#resourcegroup) . Den här funktionen returnerar ett objekt med information om resurs gruppen som används för distribution. En av egenskaperna för objektet är en plats egenskap. När du använder standardvärdet har lagrings konto platsen samma plats som resurs gruppen. Resurserna i en resurs grupp behöver inte dela samma plats. Du kan också ange en annan plats när det behövs.

Kopiera hela filen och ersätt din mall med dess innehåll.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Distribuera mallen

I de föregående självstudierna skapade du ett lagrings konto i östra USA, men resurs gruppen skapades i Central USA. I den här självstudien skapas ditt lagrings konto i samma region som resurs gruppen. Använd standardvärdet för plats, så du behöver inte ange detta parameter värde. Du måste ange ett nytt namn för lagrings kontot eftersom du skapar ett lagrings konto på en annan plats. Använd till exempel **store2** som prefix i stället för **store1**.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **templateFile** -variabeln till sökvägen till mallfilen, som du ser i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Om distributionen misslyckades använder du **utförlig** växeln för att hämta information om de resurser som skapas. Använd **fel söknings** växeln för att få mer information om fel sökning.

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

I den här självstudien använde du en funktion när du definierade standardvärdet för en parameter. I den här självstudien ska du fortsätta använda functions. I slutet av serien lägger du till funktioner i alla avsnitt i mallen.

> [!div class="nextstepaction"]
> [Lägga till variabler](template-tutorial-add-variables.md)
