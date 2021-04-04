---
title: Självstudie – Lägg till resurs till mall
description: Beskriver stegen för att skapa din första Azure Resource Manager-mall (ARM-mall). Du lär dig mer om mallens syntax och hur du distribuerar ett lagrings konto.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 49cee5c98c4099e214a732371269e935db353152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106979"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Självstudie: Lägg till en resurs i ARM-mallen

I den [föregående själv studie kursen](template-tutorial-create-first-template.md)har du lärt dig hur du skapar en tom Azure Resource Manager-mall (arm-mall) och distribuerar den. Nu är du redo att distribuera en faktisk resurs. I den här självstudien lägger du till ett lagrings konto. Det tar ungefär **9 minuter** att slutföra den här kursen.

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [introduktions kursen om mallar](template-tutorial-create-first-template.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med Resource Manager Tools-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [verktyg för mallar](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Lägg till resurs

Om du vill lägga till en lagrings konto definition i den befintliga mallen tittar du på den markerade JSON-filen i följande exempel. I stället för att försöka kopiera avsnitt i mallen kopierar du hela filen och ersätter mallen med dess innehåll.

Ersätt `{provide-unique-name}` och typografiska klammerparenteser `{}` med ett unikt lagrings konto namn.

> [!IMPORTANT]
> Namnet på lagringskontot måste vara unikt i Azure. Namnet får bara innehålla gemena bokstäver eller siffror. Det får inte vara längre än 24 tecken. Du kan prova ett namngivnings mönster som att använda **store1** som prefix och sedan lägga till dina initialer och dagens datum. Till exempel kan namnet som du använder se ut som **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Att gissa ett unikt namn för ett lagrings konto är inte enkelt och fungerar inte bra för att automatisera stora distributioner. Senare i den här själv studie serien använder du mallar som gör det enklare att skapa ett unikt namn.

## <a name="resource-properties"></a>Resurs egenskaper

Du kanske undrar hur du hittar de egenskaper som ska användas för varje resurs typ. Du kan använda [referensen arm-mall](/azure/templates/) för att hitta de resurs typer som du vill distribuera.

Varje resurs som du distribuerar har minst följande tre egenskaper:

- `type`: Typ av resurs. Det här värdet är en kombination av resurs leverantörens namn område och resurs typ, till exempel `Microsoft.Storage/storageAccounts` .
- `apiVersion`: Den version av REST API som ska användas för att skapa resursen. Varje resurs leverantör publicerar sina egna API-versioner, så det här värdet är specifika för typen.
- `name`: Resursens namn.

De flesta resurser har också en `location` egenskap som anger den region där resursen distribueras.

De andra egenskaperna varierar beroende på resurs typ och API-version. Det är viktigt att förstå anslutningen mellan API-versionen och de tillgängliga egenskaperna, så vi går vidare till mer information.

I den här självstudien har du lagt till ett lagrings konto till mallen. Du kan se att API-versionen är på [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Observera att du inte har lagt till alla egenskaper i mallen. Många av egenskaperna är valfria. `Microsoft.Storage`Resurs leverantören kan frigöra en ny API-version, men den version som du distribuerar behöver inte ändras. Du kan fortsätta att använda den versionen och veta att resultatet av distributionen blir konsekvent.

Om du visar en äldre API-version, till exempel [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), ser du att en mindre uppsättning egenskaper är tillgängliga.

Om du bestämmer dig för att ändra API-versionen för en resurs, se till att du utvärderar egenskaperna för den versionen och justerar mallen på lämpligt sätt.

## <a name="deploy-template"></a>Distribuera mallen

Du kan distribuera mallen för att skapa lagrings kontot. Ge distributionen ett annat namn så att du enkelt kan hitta den i historiken.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett `templateFile` variabeln till sökvägen till mallfilen, som du ser i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Om distributionen misslyckades använder du `verbose` växeln för att hämta information om de resurser som skapas. Använd `debug` växeln för att få mer information om fel sökning.

Två möjliga distributions problem som kan uppstå:

- `Error: Code=AccountNameInvalid; Message={provide-unique-name}` är inte ett giltigt lagrings konto namn. Lagrings kontots namn måste vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemena bokstäver.

    Ersätt `{provide-unique-name}` med ett unikt lagrings konto namn i mallen. Se [Lägg till resurs](#add-resource).

- `Error: Code=StorageAccountAlreadyTaken; Message=The storage account named store1abc09092019` är redan upptaget.

    Försök med ett annat lagrings konto namn i mallen.

Distributionen tar längre tid än distributionen av tomma mallar eftersom lagrings kontot har skapats. Det kan ta ungefär en minut, men det är vanligt vis snabbare.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan kontrol lera distributionen genom att utforska resurs gruppen från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper** på den vänstra menyn.
1. Välj den resurs grupp som du har distribuerat till.
1. Du ser att ett lagrings konto har distribuerats.
1. Observera att distributions etiketten nu säger: **distributioner: 2 lyckades**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

Du har skapat en enkel mall för att distribuera ett Azure Storage-konto. I senare självstudier får du lära dig hur du lägger till parametrar, variabler, resurser och utdata i en mall. Dessa funktioner är Bygg stenar för mycket mer avancerade mallar.

> [!div class="nextstepaction"]
> [Lägga till parametrar](template-tutorial-add-parameters.md)
