---
title: Självstudie – använda snabb starts mallar för Azure Resource Manager bicep-utveckling
description: Lär dig hur du använder Azures snabb starts mallar för att slutföra din bicep-utveckling.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 12dcf4bfc00c299d94d45a5bd02bf9eea576cc73
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748240"
---
# <a name="tutorial-use-azure-quickstart-templates-for-azure-resource-manager-bicep-development"></a>Självstudie: Använd Azures snabb starts mallar för Azure Resource Manager bicep-utveckling

[Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/) är en lagrings plats för community som bidragit till JSON-mallar Du kan använda exempel mallarna i din bicep-utveckling. I den här självstudien hittar du en webbplats resurs definition, dekompilerar den till bicep och lägger till den i din bicep-fil. Det tar ungefär **12 minuter** att slutföra.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om exporterade mallar](bicep-tutorial-export-template.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med bicep-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [bicep-verktyg](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Granska bicep-fil

I slutet av den föregående själv studie kursen hade bicep-filen följande innehåll:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.bicep":::

Den här bicep-filen används för att distribuera lagrings konton och App Service-planer, men du kanske vill lägga till en webbplats i den. Du kan använda färdiga mallar för att snabbt identifiera den JSON som krävs för att distribuera en resurs. Innan Azures snabb starts mallar erbjuder bicep-exempel kan du använda konverterings verktyg för att konvertera JSON-mallar till bicep-filer.

## <a name="find-template"></a>Hitta mall

För närvarande tillhandahåller Azures snabb starts mallar bara JSON-mallar. Det finns verktyg som du kan använda för att dekompilera JSON-mallar till bicep-mallar.

1. Öppna [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/)
1. I **Sök** anger du _distribuera Linux-webbapp_.
1. Välj panelen med rubriken **distribuera en grundläggande Linux-webbapp**. Om du har problem med att hitta det här är den [direkta länken](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Välj **Bläddra på GitHub**.
1. Välj _azuredeploy.jspå_. Detta är den mall som du kan använda.
1. Välj **RAW** och gör sedan en kopia av URL: en.
1. Bläddra till **https://bicepdemo.z22.web.core.windows.net/** , Välj **dekompilera** och ange sedan den råa mall-URL: en.
1. Granska bicep-mallen. Leta särskilt efter `Microsoft.Web/sites` resursen.

    ![Snabb starts webbplats för Resource Manager-mall](./media/bicep-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

    Det finns några viktiga bicep-funktioner att notera i den här nya resursen om du har arbetat med JSON-mallar.

    I ARM JSON-mallar måste du manuellt ange resurs beroenden med egenskapen _dependsOn_ . Webbplats resursen är beroende av App Service plan-resursen. Om du refererar till en resurs egenskap med hjälp av ett symboliskt namn i bicep läggs egenskapen dependsOn automatiskt till.

    Du kan enkelt referera till resurs-ID: t från det symboliska namnet för App Service-planen (appServicePlanName.id) som kommer att översättas till resourceId (...)-funktionen i den kompilerade JSON-mallen.

## <a name="revise-existing-bicep-file"></a>Ändra befintlig bicep-fil

Sammanfoga den dekompilerade snabb starts mal len med den befintliga bicep-filen. Samma som du gjorde i den föregående själv studie kursen, uppdatera resursens symboliska namn och resurs namnet för att matcha din namngivnings konvention.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep" range="1-73" highlight="20-25,28,61-71":::

Webb program namnet måste vara unikt i Azure. `webAppPortalName`Variabeln har uppdaterats från till för att förhindra dubblettnamn `var webAppPortalName_var = '${webAppName}-webapp'` `var webAppPortalName = '${webAppName}${uniqueString(resourceGroup().id)}'` .

## <a name="deploy-bicep-file"></a>Distribuera bicep-fil

Använd antingen Azure CLI eller Azure PowerShell för att distribuera en bicep-mall.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](bicep-tutorial-create-first-bicep.md#create-resource-group). Exemplet förutsätter att du har angett **bicepFile** -variabeln till sökvägen till bicep-filen, som du ser i den [första självstudien](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För att kunna köra denna distributions-cmdlet måste du ha den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
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
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> Om distributionen misslyckades använder du `verbose` växeln för att hämta information om de resurser som skapas. Använd `debug` växeln för att få mer information om fel sökning.

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder en snabb starts mall för din bicep-utveckling. I nästa självstudie lägger du till taggar till resurserna.

> [!div class="nextstepaction"]
> [Lägga till taggar](bicep-tutorial-add-tags.md)
