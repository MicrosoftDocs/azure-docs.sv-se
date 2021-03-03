---
title: Självstudie – Använd parameter fil för att distribuera Azure Resource Manager bicep-fil
description: Använd parameterstyrda filer som innehåller de värden som ska användas för att distribuera bicep-filen.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 233bab26d659fef2da9a9f5a7080d4b3ecbce7d4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748284"
---
# <a name="tutorial-use-parameter-files-to-deploy-azure-resource-manager-bicep-file"></a>Självstudie: använda parameter-filer för att distribuera Azure Resource Manager bicep-fil

I den här självstudien får du lära dig hur du använder [parameter-filer](parameter-files.md) för att lagra de värden som du skickar i under distributionen. I de föregående självstudierna använde du infogade parametrar med ditt distributions kommando. Den här metoden fungerade för att testa din bicep-fil, men när du automatiserar distributionen kan det vara lättare att skicka en uppsättning värden för din miljö. Parameter-filer gör det lättare att paketera parameter värden för en speciell miljö. Du använder samma JSON-parameter fil som du distribuerar en JSON-mall. I den här självstudien skapar du parameterstyrda filer för utvecklings-och produktions miljöer. Det tar ungefär **12 minuter** att slutföra.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om Taggar](bicep-tutorial-add-tags.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med bicep-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [bicep-verktyg](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Granska bicep-fil

Din bicep-fil har många parametrar som du kan ange under distributionen. I slutet av den föregående själv studie kursen såg din bicep-fil som:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Den här bicep-filen fungerar bra, men nu vill du enkelt hantera de parametrar som du skickar i bicep-filen.

## <a name="add-parameter-files"></a>Lägg till parameter filer

Parameter-filer är JSON-filer med en struktur som liknar JSON-mallar. I filen anger du de parameter värden som du vill skicka under distributionen.

I parameter filen anger du värden för parametrarna i din bicep-fil. Namnet på varje parameter i parameter filen måste matcha namnet på en parameter i din bicep-fil. Namnet är Skift läges känsligt men för att enkelt se matchande värden rekommenderar vi att du matchar Skift läget från bicep-filen.

Du behöver inte ange ett värde för varje parameter. Om en ospecificerad parameter har ett standardvärde används det värdet under distributionen. Om en parameter inte har något standardvärde och inte anges i parameter filen uppmanas du att ange ett värde under distributionen.

Du kan inte ange ett parameter namn i parameter filen som inte matchar ett parameter namn i bicep-filen. Du får ett fel meddelande när du har angett okända parametrar.

Skapa en ny fil med följande innehåll i Visual Studio Code. Spara filen med namnet _azuredeploy.parameters.dev.jspå_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Den här filen är din parameter fil för utvecklings miljön. Observera att den använder **Standard_LRS** för lagrings kontot, namnger resurser med ett **dev** -prefix och anger `Environment` taggen till **dev**.

Skapa en ny fil med följande innehåll. Spara filen med namnet _azuredeploy.parameters.prod.jspå_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Den här filen är parameter filen för produktions miljön. Observera att den använder **Standard_GRS** för lagrings kontot, namnger resurser med ett **contoso** -prefix och anger `Environment` taggen till **produktion**. I en verklig produktions miljö vill du också använda en app service med en annan SKU än kostnads fri, men vi fortsätter att använda SKU: n för den här självstudien.

## <a name="deploy-bicep-file"></a>Distribuera bicep-fil

Använd antingen Azure CLI eller Azure PowerShell för att distribuera bicep-filen.

I den här självstudien skapar vi två nya resurs grupper. En för utvecklings miljön och en för produktions miljön.

För mall-och parameter-variablerna ersätter du, `{path-to-the-bicep-file}` `{path-to-azuredeploy.parameters.dev.json}` , `{path-to-azuredeploy.parameters.prod.json}` och klammerparenteser `{}` med din bicep-fil och sökvägar för parameter fil.

Först ska vi distribuera till utvecklings miljön.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För att kunna köra denna distributions-cmdlet måste du ha den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

```azurepowershell
$bicepFile = "{path-to-the-bicep-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $bicepFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
bicepFile="{path-to-the-bicep-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $bicepFile \
  --parameters $devParameterFile
```

---

Nu ska vi distribuera till produktions miljön.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Om distributionen misslyckades använder du `verbose` växeln för att hämta information om de resurser som skapas. Använd `debug` växeln för att få mer information om fel sökning.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan kontrol lera distributionen genom att utforska resurs grupperna från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper** på den vänstra menyn.
1. Du ser de två nya resurs grupper som du har distribuerat i den här självstudien.
1. Välj antingen resurs grupp och Visa de distribuerade resurserna. Observera att de stämmer överens med de värden som du har angett i parameter filen för den miljön.

## <a name="clean-up-resources"></a>Rensa resurser

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använder du en parameter fil för att distribuera bicep-filen. I nästa självstudie får du lära dig hur du modularize dina bicep-filer.

> [!div class="nextstepaction"]
> [Lägga till moduler](./bicep-tutorial-add-modules.md)
