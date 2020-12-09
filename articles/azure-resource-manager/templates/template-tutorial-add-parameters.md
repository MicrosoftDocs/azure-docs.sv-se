---
title: Självstudie – lägga till parametrar till mallen
description: Lägg till parametrar till din Azure Resource Manager-mall (ARM-mall) så att den kan användas igen.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7d0743d316b9d879017f3b0fbe08ee4dc2b3e1c2
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931069"
---
# <a name="tutorial-add-parameters-to-your-arm-template"></a>Självstudie: lägga till parametrar i ARM-mallen

I den [föregående själv studie kursen](template-tutorial-add-resource.md)har du lärt dig hur du lägger till ett lagrings konto i mallen och distribuerar det. I den här självstudien får du lära dig hur du förbättrar Azure Resource Manager-mallen (ARM-mallen) genom att lägga till parametrar. Den här självstudien tar cirka **14 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför [självstudien om resurser](template-tutorial-add-resource.md), men det är inget krav.

Du måste ha Visual Studio Code med Resource Manager Tools-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [verktyg för mallar](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Granska mall

I slutet av den föregående själv studie kursen hade mallen följande JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json":::

Du kanske har lagt märke till att det finns ett problem med den här mallen. Lagringskontots namn är hårdkodat. Du kan bara använda den här mallen för att distribuera samma lagringskonto varje gång. Om du vill distribuera ett lagrings konto med ett annat namn måste du skapa en ny mall, vilket uppenbart inte är ett praktiskt sätt att automatisera dina distributioner.

## <a name="make-template-reusable"></a>Gör mall åter användning

Lägg till en parameter som du kan använda för att skicka in ett lagrings konto namn om du vill att mallen ska kunna användas. Den markerade JSON i följande exempel visar vad som har ändrats i mallen. Parametern **storageName** identifieras som en sträng. Max längden är 24 tecken för att förhindra namn som är för långa.

Kopiera hela filen och ersätt din mall med dess innehåll.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json" range="1-26" highlight="4-10,15":::

## <a name="deploy-template"></a>Distribuera mallen

Nu ska vi distribuera mallen. I följande exempel distribueras mallen med Azure CLI eller PowerShell. Lägg märke till att du anger namnet på lagrings kontot som ett av värdena i distributions kommandot. För lagrings konto namnet anger du samma namn som du använde i föregående självstudie.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett **templateFile** -variabeln till sökvägen till mallfilen, som du ser i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Förstå resurs uppdateringar

I föregående avsnitt har du distribuerat ett lagrings konto med samma namn som du skapade tidigare. Du kanske undrar hur resursen påverkas av omdistributionen.

Om resursen redan finns och ingen ändring har identifierats i egenskaperna vidtas ingen åtgärd. Om resursen redan finns och en egenskap har ändrats uppdateras resursen. Om resursen inte finns skapas den.

Det här sättet att hantera uppdateringar innebär att din mall kan innehålla alla resurser du behöver för en Azure-lösning. Du kan på ett säkert sätt distribuera om mallen och se att resurserna ändras eller skapas endast när det behövs. Om du till exempel har lagt till filer till ditt lagrings konto kan du distribuera om lagrings kontot utan att förlora dessa filer.

## <a name="customize-by-environment"></a>Anpassa efter miljö

Med parametrar kan du anpassa distributionen genom att tillhandahålla värden som är skräddarsydda för en viss miljö. Du kan till exempel skicka olika värden baserat på om du distribuerar till en miljö för utveckling, testning och produktion.

Den tidigare mallen har alltid distribuerat ett Standard_LRS lagrings konto. Du kanske vill ha flexibiliteten att distribuera olika SKU: er beroende på miljön. I följande exempel visas ändringarna för att lägga till en parameter för SKU. Kopiera hela filen och klistra in den över din mall.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json" range="1-40" highlight="10-23,32":::

**StorageSKU** -parametern har ett standardvärde. Det här värdet används när ett värde inte anges under distributionen. Den innehåller också en lista över tillåtna värden. Värdena matchar de värden som behövs för att skapa ett lagrings konto. Du vill inte att användare av mallen ska skicka in SKU: er som inte fungerar.

## <a name="redeploy-template"></a>Distribuera om mallen

Du är redo att distribuera igen. Eftersom standard-SKU: n är inställt på **Standard_LRS**, behöver du inte ange ett värde för den parametern.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Om distributionen misslyckades använder du **utförlig** växeln för att hämta information om de resurser som skapas. Använd **fel söknings** växeln för att få mer information om fel sökning.

Låt oss distribuera igen för att se hur flexibel din mall är. Den här gången angav SKU-parametern till **Standard_GRS**. Du kan antingen skicka ett nytt namn för att skapa ett annat lagrings konto eller använda samma namn för att uppdatera ditt befintliga lagrings konto. Båda alternativen fungerar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Slutligen ska vi köra ytterligare ett test och se vad som händer när du skickar en SKU som inte är ett av de tillåtna värdena. I det här fallet testar vi scenariot där en användare av mallen tror att **Basic** är en av SKU: erna.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
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

Du har förbättrat mallen som skapades i den [första självstudien](template-tutorial-create-first-template.md) genom att lägga till parametrar. I nästa självstudie får du lära dig mer om mall funktioner.

> [!div class="nextstepaction"]
> [Lägg till mall funktioner](template-tutorial-add-functions.md)
