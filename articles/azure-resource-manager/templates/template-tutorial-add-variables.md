---
title: Självstudie – Lägg till variabel till mall
description: Lägg till variabler i Azure Resource Manager-mallen (ARM-mallen) för att förenkla syntaxen.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46ed1fc55a108bf80089d249abc58bc5d1a6479a
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106962"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Självstudie: Lägg till variabler i ARM-mallen

I den här självstudien får du lära dig hur du lägger till en variabel i din Azure Resource Manager-mall (ARM-mall). Variabler fören klar dina mallar genom att göra det möjligt att skriva ett uttryck en gång och återanvända det i hela mallen. Den här självstudien tar **7 minuter** att slutföra.

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om functions](template-tutorial-add-functions.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med Resource Manager Tools-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [verktyg för mallar](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Granska mall

I slutet av den föregående själv studie kursen hade mallen följande JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

Parametern för lagrings konto namnet är svår att använda eftersom du måste ange ett unikt namn. Om du har slutfört de tidigare självstudierna i den här serien är du förmodligen trött på att gissa ett unikt namn. Du löser problemet genom att lägga till en variabel som skapar ett unikt namn för lagrings kontot.

## <a name="use-variable"></a>Använd variabel

I följande exempel visas ändringarna för att lägga till en variabel i mallen som skapar ett unikt lagrings konto namn. Kopiera hela filen och ersätt din mall med dess innehåll.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

Observera att den innehåller en variabel med namnet `uniqueStorageName` . Den här variabeln använder fyra funktioner för att skapa ett sträng värde.

Du är redan bekant med [parameter](template-functions-deployment.md#parameters) funktionen, så vi kommer inte att undersöka den.

Du är också bekant med funktionen [resourceGroup](template-functions-resource.md#resourcegroup) . I det här fallet får du `id` egenskapen i stället för `location` egenskapen, som du ser i föregående självstudie. `id`Egenskapen returnerar resurs gruppens fullständiga identifierare, inklusive prenumerations-ID och resurs gruppens namn.

Funktionen [uniqueString](template-functions-string.md#uniquestring) skapar en 13-siffrig hash-värde. Det returnerade värdet bestäms av de parametrar som du skickar i. I den här självstudien använder du resurs gruppens ID som inmatad för hash-värdet. Det innebär att du kan distribuera den här mallen till olika resurs grupper och hämta ett annat unikt sträng värde. Men du får samma värde om du distribuerar till samma resurs grupp.

Funktionen [concat](template-functions-string.md#concat) tar värden och kombinerar dem. För den här variabeln tar den strängen från parametern och strängen från `uniqueString` funktionen och kombinerar dem till en sträng.

Med den `storagePrefix` här parametern kan du skicka ett prefix som hjälper dig att identifiera lagrings konton. Du kan skapa en egen namngivnings konvention som gör det lättare att identifiera lagrings konton efter distribution från en lång lista med resurser.

Lägg slutligen märke till att lagrings namnet nu är inställt på variabeln i stället för en parameter.

## <a name="deploy-template"></a>Distribuera mallen

Nu ska vi distribuera mallen. Att distribuera den här mallen är enklare än tidigare mallar eftersom du bara anger prefixet för lagrings namnet.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett `templateFile` variabeln till sökvägen till mallfilen, som du ser i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Om distributionen misslyckades använder du `verbose` växeln för att hämta information om de resurser som skapas. Använd `debug` växeln för att få mer information om fel sökning.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan kontrol lera distributionen genom att utforska resurs gruppen från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper** på den vänstra menyn.
1. Välj den resurs grupp som du har distribuerat till.
1. Du ser att en lagrings konto resurs har distribuerats. Namnet på lagrings kontot är **lagrat** plus en sträng med slumpmässiga tecken.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en variabel som skapar ett unikt namn för ett lagrings konto. I nästa självstudie returnerar du ett värde från det distribuerade lagrings kontot.

> [!div class="nextstepaction"]
> [Lägg till utdata](template-tutorial-add-outputs.md)
