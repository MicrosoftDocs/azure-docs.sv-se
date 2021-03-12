---
title: Självstudie – lägga till parametrar till Azure Resource Manager bicep-fil
description: Lägg till parametrar till din bicep-fil så att den kan användas igen.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 00df2ffc6272011127c5a1eb0c1e302011f8de5f
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632789"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>Självstudie: lägga till parametrar i Azure Resource Manager bicep-filen

I den [föregående själv studie kursen](bicep-tutorial-create-first-bicep.md)har du lärt dig hur du distribuerar ett lagrings konto. I den här självstudien får du lära dig hur du kan förbättra bicep-filen genom att lägga till parametrar. Den här självstudien tar cirka **14 minuter** att slutföra.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [filen skapa den första bicep](bicep-tutorial-create-first-bicep.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med bicep-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [bicep-verktyg](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Granska bicep-fil

I slutet av den föregående själv studie kursen ser din bicep ut så här:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

Du kanske har märkt att det är problem med den här bicep-filen. Lagringskontots namn är hårdkodat. Du kan bara använda den här bicep-filen för att distribuera samma lagrings konto varje gång. Om du vill distribuera ett lagrings konto med ett annat namn måste du skapa en ny bicep-fil som uppenbart inte är ett praktiskt sätt att automatisera dina distributioner.

## <a name="make-bicep-file-reusable"></a>Gör bicep-filen återanvändbar

Vi lägger till en parameter som du kan använda för att skicka in ett lagrings konto namn för att göra din bicep-fil oanvändbar. Följande bicep-fil visar vad som har ändrats i filen. `storageName`Parametern identifieras som en sträng. Den maximala längden är inställd på 24 tecken för att förhindra namn som är för långa.

Kopiera hela filen och ersätt den med följande innehåll.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

Observera att parametrarna kan refereras direkt genom att använda deras namn i bicep, jämfört med att kräva [parameters (' storageName ')] i ARM JSON-mallen.

## <a name="deploy-bicep-file"></a>Distribuera bicep-fil

Nu ska vi distribuera bicep-filen. I följande exempel distribueras bicep-filen med Azure CLI eller PowerShell. Lägg märke till att du anger namnet på lagrings kontot som ett av värdena i distributions kommandot. För lagrings konto namnet anger du samma namn som du använde i föregående självstudie.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](bicep-tutorial-create-first-bicep.md#create-resource-group). Exemplet förutsätter att du har angett `bicepFile` variabeln till sökvägen till bicep-filen, som du ser i den [första självstudien](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För att kunna köra denna distributions-cmdlet måste du ha den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Förstå resurs uppdateringar

I föregående avsnitt har du distribuerat ett lagrings konto med samma namn som du skapade tidigare. Du kanske undrar hur resursen påverkas av omdistributionen.

Om resursen redan finns och ingen ändring har identifierats i egenskaperna vidtas ingen åtgärd. Om resursen redan finns och en egenskap har ändrats uppdateras resursen. Om resursen inte finns skapas den.

Det här sättet att hantera uppdateringar innebär att din bicep-fil kan innehålla alla resurser du behöver för en Azure-lösning. Du kan på ett säkert sätt distribuera bicep-filen och se till att resurser ändras eller skapas endast när det behövs. Om du till exempel har lagt till filer till ditt lagrings konto kan du distribuera om lagrings kontot utan att förlora dessa filer.

## <a name="customize-by-environment"></a>Anpassa efter miljö

Med parametrar kan du anpassa distributionen genom att tillhandahålla värden som är skräddarsydda för en viss miljö. Du kan till exempel skicka olika värden baserat på om du distribuerar till en miljö för utveckling, testning och produktion.

Den tidigare bicep-filen har alltid distribuerat ett **Standard_LRS** lagrings konto. Du kanske vill ha flexibiliteten att distribuera olika SKU: er beroende på miljön. I följande exempel visas ändringarna för att lägga till en parameter för SKU. Kopiera hela filen och klistra in över din bicep-fil.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

`storageSKU`Parametern har ett standardvärde. Det här värdet används när ett värde inte anges under distributionen. Den innehåller också en lista över tillåtna värden. Värdena matchar de värden som behövs för att skapa ett lagrings konto. Du vill inte att användare av bicep-filen ska skicka in SKU: er som inte fungerar.

## <a name="redeploy-bicep-file"></a>Distribuera om bicep-filen

Du är redo att distribuera igen. Eftersom standard-SKU: n är inställt på **Standard_LRS**, behöver du inte ange ett värde för den parametern.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Om distributionen misslyckades använder du `verbose` växeln för att hämta information om de resurser som skapas. Använd `debug` växeln för att få mer information om fel sökning.

För att se flexibiliteten i din bicep-fil, ska vi distribuera igen. Den här gången angav SKU-parametern till **Standard_GRS**. Du kan antingen skicka ett nytt namn för att skapa ett annat lagrings konto eller använda samma namn för att uppdatera ditt befintliga lagrings konto. Båda alternativen fungerar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Slutligen ska vi köra ytterligare ett test och se vad som händer när du skickar en SKU som inte är ett av de tillåtna värdena. I det här fallet testar vi scenariot där en användare av din bicep-fil tror att **Basic** är en av SKU: erna.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

Kommandot Miss lyckas omedelbart med ett fel meddelande som anger vilka värden som tillåts. Resource Manager identifierar felet innan distributionen startar.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

Du har förbättrat bicep-filen som skapades i den [första självstudien](bicep-tutorial-create-first-bicep.md) genom att lägga till parametrar. I nästa självstudie får du lära dig mer om bicep-funktioner.

> [!div class="nextstepaction"]
> [Lägg till funktioner](bicep-tutorial-add-functions.md)
