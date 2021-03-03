---
title: Självstudie – Lägg till moduler i Azure Resource Manager bicep-fil
description: Använd moduler för att kapsla in komplexa Detaljer för rå resurs deklarationen.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: dfbf96f608a0cdf086f8b9eb5b8f1fc72932a772
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748296"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>Självstudie: Lägg till moduler i Azure Resource Manager bicep-filen

I den [föregående själv studie kursen](bicep-tutorial-use-parameter-file.md)har du lärt dig hur du använder en parameter fil för att distribuera bicep-filen. I den här självstudien får du lära dig hur du använder bicep-moduler för att kapsla in komplexa detaljer i rå resurs deklarationen. Modulerna kan delas och återanvändas i din lösning.  Det tar ungefär **12 minuter** att slutföra.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför [självstudien om parameter fil](bicep-tutorial-use-parameter-file.md), men det är inte obligatoriskt.

Du måste ha Visual Studio Code med bicep-tillägget och antingen Azure PowerShell eller Azure CLI. Mer information finns i [bicep-verktyg](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Granska bicep-fil

I slutet av den föregående själv studie kursen hade bicep-filen följande innehåll:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Den här bicep-filen fungerar bra. Men för större lösningar vill du bryta din bicep-fil till flera relaterade moduler så att du kan dela och återanvända dessa moduler. Den aktuella bicep-filen distribuerar ett lagrings konto, en app service-plan och en webbplats.  Nu ska vi separera lagrings kontot till en modul.

## <a name="create-bicep-module"></a>Skapa bicep-modul

Varje bicep-fil kan förbrukas som en modul, så det finns ingen speciell syntax för att definiera en modul. Skapa en _Storage. bicep_ -fil med följande innehåll:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

Den här modulen innehåller lagrings konto resursen och relaterade parametrar och variabler. Värdena för _plats_ parametern och _resourceTags_ -parametrarna har tagits bort. Värdena skickas från huvud filen för bicep.

## <a name="consume-bicep-module"></a>Konsumera bicep-modul

Ersätt lagrings kontots resurs definition i den befintliga _azuredeploy. bicep_ med följande bicep-innehåll:

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **modul**: nyckelord.
- **symboliskt namn** (STG): det här är en identifierare för modulen.
- **modul fil**: sökvägen till modulen i det här exemplet anges med en relativ sökväg (./Storage.bicep). Alla sökvägar i bicep måste anges med hjälp av katalog avgränsaren snedstreck (/) för att säkerställa konsekvent kompilering mellan plattformar. Windows snedstreck ( \) Character) stöds inte.

Hämta lagrings slut punkten genom att uppdatera utdata i _azuredeploy. bicep_ till följande bicep:

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

Den slutförda azuredeploy. bicep har följande innehåll:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>Distribuera mallen

Använd antingen Azure CLI eller Azure PowerShell för att distribuera mallen.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](bicep-tutorial-create-first-bicep.md#create-resource-group). Exemplet förutsätter att du har angett `bicepFile` variabeln till sökvägen till bicep-filen, som du ser i den [första självstudien](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För att kunna köra denna distributions-cmdlet måste du ha den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
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
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
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
2. Ange resursgruppens namn i fältet **Filtrera efter namn**. Om du har slutfört den här serien har du tre resurs grupper för att ta bort- **myResourceGroup**, **myResourceGroupDev** och **myResourceGroupProd**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört introduktionen för att distribuera bicep-filer till Azure. Berätta om du har kommentarer och förslag i feedback-avsnittet. Tack!

Nästa kurs serier visar mer information om hur du distribuerar mallar.

> [!div class="nextstepaction"]
> [Lägga till moduler](./bicep-tutorial-add-modules.md)
