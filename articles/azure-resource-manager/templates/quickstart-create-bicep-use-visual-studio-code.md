---
title: Skapa bicep-filer – Visual Studio Code
description: Använd Visual Studio Code och bicep-tillägget till bicep-filer för att distribuera Azure-resurser
author: mumian
ms.date: 03/02/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: ab1d7b88321ce5959b99423ae2ca1332369ef691
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179001"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>Snabb start: skapa bicep-filer med Visual Studio Code

Bicep-tillägget för Visual Studio Code ger språk stöd och slut för ande av resurser. Dessa verktyg hjälper dig att skapa och validera [bicep](./bicep-overview.md) -filer. I den här snabb starten använder du tillägget för att skapa en bicep-fil från grunden. När du gör det kommer du att få tilläggets funktioner som verifiering och slut för ande.

För att slutföra den här snabb starten behöver du [Visual Studio Code](https://code.visualstudio.com/)med [bicep-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) installerat. Du behöver också antingen den senaste versionen av [Azure CLI](/cli/azure/) eller den senaste [Azure PowerShell modulen](/powershell/azure/new-azureps-module-az?view=azps-3.7.0&preserve-view=true) installerad och autentiserad.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-bicep-file"></a>Skapa en bicep-fil

Skapa och öppna med Visual Studio Code en ny fil med namnet *azuredeploy. bicep*.

## <a name="add-an-azure-resource"></a>Lägg till en Azure-resurs

Lägg till en grundläggande lagrings konto resurs i bicep-filen.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

Resurs deklarationen har fyra komponenter:

- **resurs**: nyckelord.
- **symboliskt namn** (STG): symboliskt namn är en identifierare för att referera till resursen i hela bicep-filen. Det är inte vad namnet på resursen kommer att vara när det distribueras. Namnet på resursen definieras av egenskapen **Name** .  Se den fjärde komponenten i den här listan.
- **resurs typ** ( Microsoft.Storage/storageAccounts@2019-06-01 ): den består av resurs leverantören (Microsoft. Storage), resurs typ (StorageAccounts) och API version (2019-06-01). Varje resurs leverantör publicerar sina egna API-versioner, så det här värdet är specifika för typen. Du hittar fler typer och apiVersions för olika Azure-resurser från [referens för ARM-mallar](/azure/templates/).
- **Egenskaper** (allt i = {...}): Ange egenskaper för resurs typen. Varje resurs har en `name` egenskap. De flesta resurser har också en `location` egenskap som anger den region där resursen distribueras. De andra egenskaperna varierar beroende på resurs typ och API-version.

## <a name="completion-and-validation"></a>Slutför och verifiera

En av de mest kraftfulla funktionerna i tillägget är dess integrering med Azure-scheman. Azure-scheman ger tillägget med funktioner för validering och resurs medveten slut för ande. Nu ska vi ändra lagrings kontot för att se hur validering och slut för ande fungerar.

Uppdatera först lagrings kontots typ till ett ogiltigt värde, till exempel `megaStorage` . Observera att den här åtgärden skapar en varning som anger att `megaStorage` inte är ett giltigt värde.

![Bild som visar en ogiltig lagrings konfiguration](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

Om du vill använda slut för ande funktioner tar du bort `megaStorage` , placerar markören inuti de enkla citat tecknen och trycker på `ctrl`  +  `space` . Den här åtgärden visar en lista över giltiga värden för slut för ande.

![Bild som visar automatisk komplettering av tillägget](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>Lägga till parametrar

Skapa och Använd en parameter för att ange lagrings kontots namn.

Lägg till följande kod i början av filen:

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

Namn på Azure Storage-konton får innehålla minst 3 tecken och högst 24. Använd `minLength` och `maxLength` för att ange lämpliga värden.

På lagrings resursen uppdaterar du sedan egenskapen namn för att använda parametern. Det gör du genom att ta bort namnet på den aktuella lagrings resursen inklusive enkla citat tecken. Tryck på `ctrl`  +  `space` . Välj parametern **storageAccountName** i listan. Observera att parametrarna kan refereras direkt genom att använda deras namn i bicep. JSON-mallarna kräver en parameter ()-funktion.

![Bild som visar automatisk komplettering när parametrar används i bicep-resurser](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>Distribuera bicep-filen

Öppna den integrerade Visual Studio Code-terminalen med `ctrl`  +  ```` ` ```` tangentkombinationen, ändra den aktuella katalogen till platsen där bicep-filen finns och Använd sedan antingen Azure CLI-eller Azure PowerShell-modulen för att distribuera bicep-filen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
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
> [Självstudier om nybörjare bicep](./bicep-tutorial-create-first-bicep.md)
