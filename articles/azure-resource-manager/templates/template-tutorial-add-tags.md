---
title: Självstudie – Lägg till taggar till resurser i mallen
description: Lägg till taggar till resurser som du distribuerar i din Azure Resource Manager-mall (ARM-mall). Med taggar kan du logiskt organisera resurser.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 625a88c0ee946b1ca67737d9cc67b638699d12f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97107013"
---
# <a name="tutorial-add-tags-in-your-arm-template"></a>Självstudie: Lägg till taggar i ARM-mallen

I den här självstudien får du lära dig hur du lägger till taggar till resurser i din Azure Resource Manager-mall (ARM-mall). Med [taggar](../management/tag-resources.md) kan du logiskt organisera dina resurser. De märkes värden som visas i kostnads rapporter. Den här självstudien tar **8 minuter** att slutföra.

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om snabb starts mallar](template-tutorial-quickstart-template.md), men det är inget krav.

Du måste ha Visual Studio Code med Resource Manager Tools-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [verktyg för mallar](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Granska mall

Din tidigare mall har distribuerat ett lagrings konto, App Service plan och en webbapp.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json":::

När du har distribuerat dessa resurser kan du behöva spåra kostnader och hitta resurser som tillhör en kategori. Du kan lägga till taggar för att lösa problemen.

## <a name="add-tags"></a>Lägga till taggar

Du kan tagga resurser om du vill lägga till värden som beskriver vad resurserna används för. Du kan till exempel lägga till taggar som visar miljön och projektet. Du kan lägga till taggar som identifierar ett kostnads ställe eller teamet som äger resursen. Lägg till valfria värden som passar din organisation.

I följande exempel visas ändringarna i mallen. Kopiera hela filen och ersätt din mall med dess innehåll.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json" range="1-118" highlight="46-52,64,78,102":::

## <a name="deploy-template"></a>Distribuera mallen

Det är dags att distribuera mallen och titta på resultaten.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](template-tutorial-create-first-template.md#create-resource-group). Exemplet förutsätter att du har angett `templateFile` variabeln till sökvägen till mallfilen, som du ser i den [första självstudien](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
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
  --template-file $templateFile \
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

   ![Visa Taggar](./media/template-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till taggar till resurserna. I nästa själv studie kurs får du lära dig hur du använder parameter-filer för att förenkla överföring av värden till mallen.

> [!div class="nextstepaction"]
> [Använd parameter fil](template-tutorial-use-parameter-file.md)
