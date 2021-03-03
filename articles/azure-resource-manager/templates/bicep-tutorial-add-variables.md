---
title: Självstudie – Lägg till variabel till Azure Resource Manager bicep-fil
description: Lägg till variabler i bicep-filen för att förenkla syntaxen.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 1706bafb85834cfd3abae1f5d6a0090da9041bb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748273"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>Självstudie: lägga till variabler i Azure Resource Manager bicep-filen

I den här självstudien får du lära dig hur du lägger till en variabel i din bicep-fil. Variabler fören klar dina bicep-filer genom att göra det möjligt att skriva ett uttryck en gång och återanvända det i bicep-filen. Den här självstudien tar **7 minuter** att slutföra.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om functions](bicep-tutorial-add-functions.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med bicep-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [bicep-verktyg](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Granska bicep-fil

I slutet av den föregående själv studie kursen hade bicep-filen följande innehåll:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

Parametern för lagrings konto namnet är svår att använda eftersom du måste ange ett unikt namn. Om du har slutfört de tidigare självstudierna i den här serien är du förmodligen trött på att gissa ett unikt namn. Du löser problemet genom att lägga till en variabel som skapar ett unikt namn för lagrings kontot.

## <a name="use-variable"></a>Använd variabel

I följande exempel visas ändringarna för att lägga till en variabel i din bicep-fil som skapar ett unikt lagrings konto namn. Kopiera hela filen och ersätt din bicep-fil med dess innehåll.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

Observera att den innehåller en variabel med namnet `uniqueStorageName` . Den här variabeln använder tre funktioner för att skapa ett sträng värde.

Du är bekant med funktionen [resourceGroup](template-functions-resource.md#resourcegroup) . I det här fallet får du `id` egenskapen i stället för `location` egenskapen, som du ser i föregående självstudie. `id`Egenskapen returnerar resurs gruppens fullständiga identifierare, inklusive prenumerations-ID och resurs gruppens namn.

Funktionen [uniqueString](template-functions-string.md#uniquestring) skapar en 13-siffrig hash-värde. Det returnerade värdet bestäms av de parametrar som du skickar i. I den här självstudien använder du resurs gruppens ID som inmatad för hash-värdet. Det innebär att du kan distribuera den här bicep-filen till olika resurs grupper och hämta ett annat unikt sträng värde. Men du får samma värde om du distribuerar till samma resurs grupp.

Bicep stöder en syntax för [String-interpolation](https://en.wikipedia.org/wiki/String_interpolation#) . För den här variabeln tar den strängen från parametern och strängen från `uniqueString` funktionen och kombinerar dem till en sträng.

Med den `storagePrefix` här parametern kan du skicka ett prefix som hjälper dig att identifiera lagrings konton. Du kan skapa en egen namngivnings konvention som gör det lättare att identifiera lagrings konton efter distribution från en lång lista med resurser.

Lägg slutligen märke till att lagrings namnet nu är inställt på variabeln i stället för en parameter.

## <a name="deploy-bicep-file"></a>Distribuera bicep-fil

Nu ska vi distribuera bicep-filen. Att distribuera den här bicep-filen är enklare än tidigare bicep-filer eftersom du bara anger prefixet för lagrings namnet.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](bicep-tutorial-create-first-bicep.md#create-resource-group). Exemplet förutsätter att du har angett `bicepFile` variabeln till sökvägen till bicep-filen, som du ser i den [första självstudien](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För att kunna köra denna distributions-cmdlet måste du ha den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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
> [Lägg till utdata](bicep-tutorial-add-outputs.md)
