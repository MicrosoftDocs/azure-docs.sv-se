---
title: Självstudie – exportera JSON-mall från Azure Portal för utveckling av bicep
description: Lär dig hur du använder en exporterad JSON-mall för att slutföra din bicep-utveckling.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3bc7ed4ada4f7810e9864778c7f76a0573c9dc89
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632567"
---
# <a name="tutorial-use-exported-json-template-from-the-azure-portal"></a>Självstudie: Använd exporterad JSON-mall från Azure Portal

I den här själv studie serien har du skapat en bicep-fil för att distribuera ett Azure Storage-konto. I de kommande två självstudierna lägger du till en *App Service plan* och en *webbplats*. I stället för att skapa mallar från grunden får du lära dig hur du exporterar JSON-mallar från Azure Portal och hur du använder exempel mallar från [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/). Du anpassar mallarna efter din användning. Den här självstudien fokuserar på att exportera mallar och anpassa resultatet för din bicep-fil. Det tar ungefär **14 minuter** att slutföra.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om utdata](bicep-tutorial-add-outputs.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med bicep-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [bicep-verktyg](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Granska bicep-fil

I slutet av den föregående själv studie kursen hade bicep-filen följande innehåll:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep":::

Den här bicep-filen fungerar bra för att distribuera lagrings konton, men du kanske vill lägga till fler resurser i den. Du kan exportera en JSON-mall från en befintlig resurs för att snabbt hämta JSON för resursen. Konvertera sedan JSON till bicep innan du kan lägga till den i din bicep-fil.

## <a name="create-app-service-plan"></a>Skapa apptjänstplan

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Skapa en resurs**.
1. I **Sök på Marketplace** anger **App Service plan** och väljer sedan **App Service plan**.  Välj inte **App Service plan (klassisk)**
1. Välj **Skapa**.
1. Skriv:

    - **Prenumeration**: Välj din Azure-prenumeration.
    - **Resurs grupp**: Välj **Skapa ny** och ange sedan ett namn. Ange ett annat resurs grupps namn än det som du har använt i den här själv studie serien.
    - **Namn**: Ange ett namn för App Service-planen.
    - **Operativ system**: Välj **Linux**.
    - **Region**: Välj en Azure-plats. Välj till exempel **USA, centrala**.
    - **Pris nivå**: om du vill spara kostnader ändrar du SKU till **Basic B1** (under utveckling/testning).

    ![Exportera mall-Portal för Resource Manager-mall](./media/bicep-tutorial-export-template/resource-manager-template-export.png)
1. Välj **Granska och skapa**.
1. Välj **Skapa**. Det tar en stund att skapa resursen.

## <a name="export-template"></a>Exportera mall

För närvarande stöder Azure Portal bara export av JSON-mallar. Det finns verktyg som du kan använda för att dekompilera JSON-mallar till bicep-filer.

1. Välj **Gå till resurs**.

    ![Gå till resurs](./media/bicep-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Välj **Exportera mall**.

    ![Exportera mall för Resource Manager-mall](./media/bicep-tutorial-export-template/resource-manager-template-export-template.png)

   Funktionen Exportera mall tar det aktuella läget för en resurs och skapar en mall för att distribuera den. Att exportera en mall kan vara ett användbart sätt att snabbt hämta den JSON du behöver för att distribuera en resurs.

1. `Microsoft.Web/serverfarms`Definitionen och parameter definitionen är de delar som du behöver.

    ![Exportera mall för mall i Resource Manager-mall](./media/bicep-tutorial-export-template/resource-manager-template-exported-template.png)

    > [!IMPORTANT]
    > Normalt är den exporterade mallen mer utförlig än du kanske vill när du skapar en mall. Till exempel har SKU-objektet i den exporterade mallen fem egenskaper. Den här mallen fungerar, men du kan bara använda `name` egenskapen. Du kan börja med den exporterade mallen och sedan ändra den så att den passar dina behov.

1. Välj **Hämta**.  Den hämtade ZIP-filen innehåller en **template.jspå** och en **parameters.jspå**. Zippa upp filerna.
1. Bläddra till **https://bicepdemo.z22.web.core.windows.net/** , Välj **dekompilera** och öppna **template.jspå**. Du får mallen i bicep.

## <a name="revise-existing-bicep-file"></a>Ändra befintlig bicep-fil

Den uppföljna exporterade mallen ger dig de flesta bicep som du behöver, men du måste anpassa den för din bicep-fil. Var särskilt uppmärksam på skillnader i parametrar och variabler mellan bicep-filen och den exporterade bicep-filen. Det är självklart att export processen inte känner till de parametrar och variabler som du redan har definierat i din bicep-fil.

I följande exempel visas tilläggen till din bicep-fil. Den innehåller den exporterade koden och vissa ändringar. Först ändras namnet på parametern så att det matchar din namngivnings konvention. För det andra använder den plats parametern för platsen för App Service-planen. Tredje, tar den bort några av egenskaperna där standardvärdet är korrekt.

Kopiera hela filen och ersätt din bicep-fil med dess innehåll.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep" range="1-53" highlight="18,34-51":::

## <a name="deploy-bicep-file"></a>Distribuera bicep-fil

Använd antingen Azure CLI eller Azure PowerShell för att distribuera en bicep-fil.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](bicep-tutorial-create-first-bicep.md#create-resource-group). Exemplet förutsätter att du har angett `bicepFile` variabeln till sökvägen till bicep-filen, som du ser i den [första självstudien](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För att kunna köra denna distributions-cmdlet måste du ha den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addappserviceplan \
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
1. Resurs gruppen innehåller ett lagrings konto och en App Service plan.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du exporterar en JSON-mall från Azure Portal, hur du konverterar JSON-mallen till en bicep-fil och hur du använder den exporterade mallen för din bicep-utveckling. Du kan också använda Azures snabb starts mallar för att förenkla bicep-utvecklingen.

> [!div class="nextstepaction"]
> [Använda Azure snabb starts mallar](bicep-tutorial-quickstart-template.md)
