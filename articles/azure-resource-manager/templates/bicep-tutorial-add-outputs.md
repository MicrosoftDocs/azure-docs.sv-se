---
title: Självstudie – Lägg till utdata i Azure Resource Manager bicep-fil
description: Lägg till utdata i bicep-filen för att förenkla syntaxen.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4b7d7a1414091c516dba2c474e1681ba357b55a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594316"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>Självstudie: lägga till utdata i Azure Resource Manager bicep-fil

I den här självstudien får du lära dig hur du returnerar ett värde från distributionen. Du använder utdata när du behöver ett värde från en distribuerad resurs. Den här självstudien tar **7 minuter** att slutföra.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om variabler](bicep-tutorial-add-variables.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med bicep-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [bicep-verktyg](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Granska bicep-fil

I slutet av den föregående själv studie kursen hade bicep-filen följande innehåll:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

Den distribuerar ett lagrings konto, men returnerar inte någon information om lagrings kontot. Du kan behöva avbilda egenskaper från en ny resurs så att de är tillgängliga senare för referens.

## <a name="add-outputs"></a>Lägg till utdata

Du kan använda utdata för att returnera värden från distributionen. Det kan till exempel vara användbart att få slut punkter för ditt nya lagrings konto.

I följande exempel visas ändringen av bicep-filen för att lägga till ett utdata-värde. Kopiera hela filen och ersätt din bicep-fil med dess innehåll.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

Det finns några viktiga saker att notera om det utdata som du har lagt till.

Det returnerade värdets typ är inställt på `object` , vilket innebär att det returnerar ett mallobjekt.

Om du vill hämta `primaryEndpoints` egenskapen från lagrings kontot använder du det symboliska namnet för lagrings kontot. Funktionen Autoavsluta i Visual Studio Code visar en fullständig lista över egenskaperna:

   ![Visual Studio Code bicep symboliskt namn objekt egenskaper](./media/bicep-tutorial-add-outputs/visual-studio-code-bicep-output-properties.png)

## <a name="deploy-bicep-file"></a>Distribuera bicep-fil

Du är redo att distribuera bicep-filen och titta på det returnerade värdet.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](bicep-tutorial-create-first-bicep.md#create-resource-group). Exemplet förutsätter att du har angett `bicepFile` variabeln till sökvägen till bicep-filen, som du ser i den [första självstudien](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

I utdata för kommandot distribution ser du ett objekt som liknar följande exempel om utdata är i JSON-format:

```json
{
  "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
  "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
  "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
  "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
  "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
  "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> Om distributionen misslyckades använder du `verbose` växeln för att hämta information om de resurser som skapas. Använd `debug` växeln för att få mer information om fel sökning.

## <a name="review-your-work"></a>Granska ditt arbete

Du har gjort en stor del av de senaste sex självstudierna. Vi tar en stund att granska vad du har gjort. Du har skapat en bicep-fil med parametrar som är lätta att tillhandahålla. Bicep-filen kan återanvändas i olika miljöer eftersom den tillåter anpassning och dynamiskt skapar nödvändiga värden. Den returnerar också information om det lagrings konto som du kan använda i skriptet.

Nu ska vi titta på resurs gruppen och distributions historiken.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper** på den vänstra menyn.
1. Välj den resurs grupp som du har distribuerat till.
1. Beroende på vilka steg du gjorde bör du ha minst ett och kanske flera lagrings konton i resurs gruppen.
1. Du bör också ha flera lyckade distributioner som listas i historiken. Välj länken.

   ![Välj distributioner](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. Du ser alla dina distributioner i historiken. Välj distributionen som kallas **addoutputs**.

   ![Visa distributions historik](./media/bicep-tutorial-add-outputs/show-history.png)

1. Du kan granska indata.

   ![Visa indata](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. Du kan granska utdata.

   ![Visa utdata](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. Du kan granska JSON-mallen.

   ![Visa mall](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till ett retur värde i bicep-filen. I nästa självstudie får du lära dig hur du exporterar en JSON-mall och använder delar av den exporterade mallen i din bicep-fil.

> [!div class="nextstepaction"]
> [Använd exporterad mall](bicep-tutorial-export-template.md)
