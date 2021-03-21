---
title: Skapa mall – Visual Studio Code
description: Använd Visual Studio Code och Azure Resource Manager Tools-tillägget för att arbeta med Azure Resource Manager-mallar (ARM-mallar).
author: neilpeterson
ms.date: 08/09/2020
ms.topic: quickstart
ms.author: nepeters
ms.openlocfilehash: 3e41d4f6f8b8aeb2be376ae1385189fe02e9b897
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504665"
---
# <a name="quickstart-create-arm-templates-with-visual-studio-code"></a>Snabb start: skapa ARM-mallar med Visual Studio Code

Azure Resource Manager Tools för Visual Studio Code tillhandahåller språk stöd, resurs kods tycken och slut för ande av resurser. Dessa verktyg hjälper dig att skapa och verifiera Azure Resource Manager mallar (ARM-mallar). I den här snabb starten använder du tillägget för att skapa en ARM-mall från grunden. När du gör det kommer du att uppleva tillägg som ARM-kodfragment, verifiering, slut för ande och stöd för parameter fil.

För att slutföra den här snabb starten behöver du [Visual Studio Code](https://code.visualstudio.com/)med [tillägget Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) installerat. Du behöver också antingen [Azure CLI](/cli/azure/) eller Azure PowerShell- [modulen](/powershell/azure/new-azureps-module-az) installerad och autentiserad.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-arm-template"></a>Skapa en ARM-mall

Skapa och öppna med Visual Studio Code en ny fil med namnet *azuredeploy.jspå*. Ange `arm` i kod redigeraren, som startar Azure Resource Manager kodfragment för att ramverk ut en arm-mall.

Välj `arm!` om du vill skapa en mall för en Azure-resurs grupps distribution.

![Bild som visar Azure Resource Manager ramverk-kodfragment](./media/quickstart-create-templates-use-visual-studio-code/1.png)

Det här kodfragmentet skapar de grundläggande Bygg stenarna för en ARM-mall.

![Bild som visar en helt autogenererade ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/2.png)

Observera att Visual Studio Code language mode har ändrats från *JSON* till *Azure Resource Manager mall*. Tillägget innehåller en språkserver som är specifik för ARM-mallar som innehåller en mall för ARM-specifik verifiering, slut för ande och andra språk tjänster.

![Bild som visar Azure Resource Manager som Visual Studio Code language mode](./media/quickstart-create-templates-use-visual-studio-code/3.png)

## <a name="add-an-azure-resource"></a>Lägg till en Azure-resurs

Tillägget innehåller kodfragment för många Azure-resurser. De här kodfragmenten kan användas för att enkelt lägga till resurser i din mall-distribution.

Placera markören i blocket mallar **resurser** , Skriv in `storage` och välj *arm-lagring-* kodfragmentet.

![Bild som visar en resurs som läggs till i ARM-mallen](./media/quickstart-create-templates-use-visual-studio-code/4.png)

Den här åtgärden lägger till en lagrings resurs i mallen.

![Bild som visar en Azure Storage-resurs i en ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/5.png)

Du kan använda **TABB** -tangenten för att gå igenom konfigurerbara egenskaper på lagrings kontot.

![Bild som visar hur TABB-nyckeln kan användas för att navigera genom resurs konfigurationen](./media/quickstart-create-templates-use-visual-studio-code/6.png)

## <a name="completion-and-validation"></a>Slutför och verifiera

En av de mest kraftfulla funktionerna i tillägget är dess integrering med Azure-scheman. Azure-scheman ger tillägget med funktioner för validering och resurs medveten slut för ande. Nu ska vi ändra lagrings kontot för att se hur validering och slut för ande fungerar.

Uppdatera först lagrings kontots typ till ett ogiltigt värde, till exempel `megaStorage` . Observera att den här åtgärden skapar en varning som anger att `megaStorage` inte är ett giltigt värde.

![Bild som visar en ogiltig lagrings konfiguration](./media/quickstart-create-templates-use-visual-studio-code/7.png)

Om du vill använda slut för ande funktioner tar du bort `megaStorage` , placerar markören inuti de dubbla citat tecknen och trycker på `ctrl`  +  `space` . Den här åtgärden visar en lista över giltiga värden för slut för ande.

![Bild som visar automatisk komplettering av tillägget](./media/quickstart-create-templates-use-visual-studio-code/8.png)

## <a name="add-template-parameters"></a>Lägg till mallparametrar

Skapa och Använd en parameter för att ange lagrings kontots namn.

Placera markören i parameter block, Lägg till en vagn retur, Skriv `"` och välj sedan `new-parameter` kodfragmentet. Den här åtgärden lägger till en allmän parameter till mallen.

![Bild som visar en parameter som läggs till i ARM-mallen](./media/quickstart-create-templates-use-visual-studio-code/9.png)

Uppdatera namnet på parametern till `storageAccountName` och beskrivningen till `Storage Account Name` .

![Bild som visar den slutförda parametern i en ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/10.png)

Namn på Azure Storage-konton får innehålla minst 3 tecken och högst 24. Lägg till både `minLength` och `maxLength` till parametern och ange lämpliga värden.

![Bild som visar minLength och maxLength som läggs till i en ARM-mallparameter](./media/quickstart-create-templates-use-visual-studio-code/11.png)

På lagrings resursen uppdaterar du sedan egenskapen namn för att använda parametern. Det gör du genom att ta bort det aktuella namnet. Ange ett dubbelt citat tecken och en inledande hak paren tes `[` som skapar en lista över arm-mallens funktioner. Välj *parametrar* i listan.

![Bild som visar automatisk komplettering när du använder parametrar i ARM-mallens resurser](./media/quickstart-create-templates-use-visual-studio-code/12.png)

Om du anger ett enkelt citat tecken `'` inuti de runda hakparenteserna skapas en lista över alla parametrar som definierats i mallen, i det här fallet *storageAccountName*. Välj parametern.

![Bild som visar slutförd parameter i en ARM-mall-resurs](./media/quickstart-create-templates-use-visual-studio-code/13.png)

## <a name="create-a-parameter-file"></a>Skapa en parameter fil

Med en parameter fil för ARM-mall kan du lagra miljödefinierade parameter värden och skicka dessa värden i som en grupp vid distributions tiden. Du kan till exempel ha en parameter fil med värden som är speciella för en test miljö och en annan för en produktions miljö.

Tillägget gör det enkelt att skapa en parameter fil från dina befintliga mallar. Om du vill göra det högerklickar du på mallen i kod redigeraren och väljer `Select/Create Parameter File` .

![Bild som visar högerklickning för att skapa en parameter fil från en ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/14.png)

Välj `New`  >  `All Parameters` > Välj ett namn och en plats för parameter filen.

![Bild som visar dialog rutan namn och spara fil när du skapar en parameter fil från en ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/15.png)

Den här åtgärden skapar en ny parameter fil och mappar den till den mall som den skapades från. Du kan se och ändra den aktuella mallen för mall-/parameter fil i statusfältet i Visual Studio Code medan mallen är markerad.

![Bild som visar mall-och parameter fil mappning i statusfältet i Visual Studio Code.](./media/quickstart-create-templates-use-visual-studio-code/16.png)

Nu när parameter filen har mappats till mallen validerar tillägget både mall-och parameter filen tillsammans. Om du vill se den här verifieringen i praktiken lägger du till ett två-Character-värde i `storageAccountName` parametern i parameter filen och sparar filen.

![Bild som visar en ogiltig mall på grund av problem med parameter filen](./media/quickstart-create-templates-use-visual-studio-code/17.png)

Gå tillbaka till ARM-mallen och Lägg märke till att ett fel har Aktiver ATS som anger att värdet inte uppfyller parameter villkoren.

![Bild som visar en giltig ARM-mall](./media/quickstart-create-templates-use-visual-studio-code/18.png)

Uppdatera värdet till något lämpligt, spara filen och gå tillbaka till mallen. Observera att felet i parametern har åtgärd ATS.

## <a name="deploy-the-template"></a>Distribuera mallen

Öppna den integrerade Visual Studio Code-terminalen med hjälp av `ctrl`  +  ```` ` ```` tangentkombinationen och Använd antingen Azure CLI-eller Azure PowerShell-modulen för att distribuera mallen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.json --parameters azuredeploy.parameters.json
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.json -TemplateParameterFile ./azuredeploy.parameters.json
```
---

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs kan du använda Azure CLI-eller Azure PowerShell-modulen för att ta bort resurs gruppen snabb start.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```
---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudier för nybörjare](./template-tutorial-create-first-template.md)
