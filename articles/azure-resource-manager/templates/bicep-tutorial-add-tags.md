---
title: Självstudie – Lägg till taggar till resurser i Azure Resource Manager bicep-fil
description: Lägg till taggar till resurser som du distribuerar i dina bicep-filer. Med taggar kan du logiskt organisera resurser.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ea5e078eb692d002b3f86cd43663dd042d692611
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632617"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>Självstudie: lägga till taggar i Azure Resource Manager bicep-filer

I den här självstudien får du lära dig hur du lägger till taggar till resurser i dina bicep-filer. Med [taggar](../management/tag-resources.md) kan du logiskt organisera dina resurser. De märkes värden som visas i kostnads rapporter. Den här självstudien tar **8 minuter** att slutföra.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om snabb starts mallar](bicep-tutorial-quickstart-template.md), men det är inget krav.

Du måste ha Visual Studio Code med bicep-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [bicep-verktyg](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Granska bicep-fil

Din tidigare bicep-fil har distribuerat ett lagrings konto, App Service plan och en webbapp.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

När du har distribuerat dessa resurser kan du behöva spåra kostnader och hitta resurser som tillhör en kategori. Du kan lägga till taggar för att lösa problemen.

## <a name="add-tags"></a>Lägga till taggar

Du kan tagga resurser om du vill lägga till värden som beskriver vad resurserna används för. Du kan till exempel lägga till taggar som visar miljön och projektet. Du kan lägga till taggar som identifierar ett kostnads ställe eller teamet som äger resursen. Lägg till valfria värden som passar din organisation.

I följande exempel visas ändringarna i bicep-filen. Kopiera hela filen och ersätt din bicep-fil med dess innehåll.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Distribuera bicep-fil

Det är dags att distribuera bicep-filen och titta på resultaten.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](bicep-tutorial-create-first-bicep.md#create-resource-group). Exemplet förutsätter att du har angett `bicepFile` variabeln till sökvägen till bicep-filen, som du ser i den [första självstudien](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För att kunna köra denna distributions-cmdlet måste du ha den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Om distributionen misslyckades använder du `verbose` växeln för att hämta information om de resurser som skapas. Använd `debug` växeln för att få mer information om fel sökning.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan kontrol lera distributionen genom att utforska resurs gruppen från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper** på den vänstra menyn.
1. Välj den resurs grupp som du har distribuerat till.
1. Välj en av resurserna, till exempel lagrings konto resursen. Nu ser du att det finns taggar.

   ![Visa Taggar](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till taggar till resurserna. I nästa självstudie får du lära dig hur du använder parameterstyrda filer för att förenkla överföring av värden till distributionen.

> [!div class="nextstepaction"]
> [Använd parameter fil](bicep-tutorial-use-parameter-file.md)
