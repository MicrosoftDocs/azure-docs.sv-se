---
title: Självstudie – Skapa & distribuera mall
description: Skapa din första Azure Resource Manager-mall (ARM-mall). I självstudien får du lära dig mer om mallens syntax och hur du distribuerar ett lagrings konto.
author: mumian
ms.date: 12/17/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 001a20c38a82d053ae22a2614a32f0be9d8aabce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656996"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Självstudie: skapa och distribuera din första ARM-mall

I den här självstudien beskrivs Azure Resource Manager mallar (ARM-mallar). Det visar hur du skapar en start-mall och distribuerar den till Azure. Du lär dig mer om mallens struktur och de verktyg du behöver för att arbeta med mallar. Det tar ungefär **12 minuter** att slutföra den här självstudien, men den faktiska tiden varierar beroende på hur många verktyg du behöver installera.

Den här självstudien är den första i en serie. När du går igenom serien ändrar du stegvisa start mal len tills du har utforskat alla kärn delar av en ARM-mall. Dessa element är Bygg stenarna för mycket mer komplexa mallar. Vi hoppas i slutet av serien och du är säker på att du har skapat dina egna mallar och kan automatisera dina distributioner med mallar.

Om du vill lära dig mer om fördelarna med att använda mallar och varför du bör automatisera distributionen med mallar, se [Översikt över arm-mall](overview.md). Om du vill lära dig mer om ARM-mallar via en guidad uppsättning moduler på Microsoft Learn, se [distribuera och hantera resurser i Azure med hjälp av arm-mallar](/learn/paths/deploy-manage-resource-manager-templates/).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="get-tools"></a>Hämta verktyg

Låt oss börja med att kontrol lera att du har de verktyg du behöver för att skapa och distribuera mallar. Installera dessa verktyg på den lokala datorn.

### <a name="editor"></a>Redigerare

Mallar är JSON-filer. Om du vill skapa mallar behöver du en lämplig JSON-redigerare. Vi rekommenderar Visual Studio Code med Resource Manager Tools-tillägget. Om du behöver installera dessa verktyg, se [snabb start: skapa arm-mallar med Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

### <a name="command-line-deployment"></a>Kommando rads distribution

Du behöver också antingen Azure PowerShell eller Azure CLI för att distribuera mallen. Om du använder Azure CLI måste du ha den senaste versionen. Installations anvisningar finns i:

- [Installera Azure PowerShell](/powershell/azure/install-az-ps)
- [Installera Azure CLI på Windows](/cli/azure/install-azure-cli-windows)
- [Installera Azure CLI på Linux](/cli/azure/install-azure-cli-linux)
- [Installera Azure CLI på macOS](/cli/azure/install-azure-cli-macos)

När du har installerat antingen Azure PowerShell eller Azure CLI kontrollerar du att du loggar in för första gången. Mer information finns i [Logga in-PowerShell](/powershell/azure/install-az-ps#sign-in) eller [Logga in – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

> [!IMPORTANT]
> Om du använder Azure CLI kontrollerar du att du har version 2,6 eller senare. Kommandona som visas i den här själv studie kursen fungerar inte om du använder tidigare versioner. Om du vill kontrol lera den installerade versionen använder du: `az --version` .

OK, du är redo att börja lära dig om mallar.

## <a name="create-your-first-template"></a>Skapa din första mall

1. Öppna Visual Studio Code med tillägget Resource Manager Tools installerat.
1. I menyn **Arkiv** väljer du **ny fil** för att skapa en ny fil.
1. Välj **Spara som** på **Arkiv** -menyn.
1. Ge filen namnet _azuredeploy_ och välj tillägget _JSON_ -fil. Det fullständiga namnet på filen är _azuredeploy.jspå_.
1. Spara filen på din arbets Station. Välj en sökväg som är lätt att komma ihåg eftersom du kommer att ange den sökvägen senare när du distribuerar mallen.
1. Kopiera och klistra in följande JSON i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Så här ser din Visual Studio Code-miljö ut:

    ![Den första mallen för ARM-mallens Visual Studio Code](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Den här mallen distribuerar inga resurser. Vi börjar med en tom mall så att du kan bekanta dig med stegen för att distribuera en mall och minimera risken för att något går fel.

    JSON-filen innehåller följande element:

    - `$schema`: Anger platsen för JSON-schemafilen. Schema filen beskriver de egenskaper som är tillgängliga i en mall. Till exempel definierar schemat `resources` som en av de giltiga egenskaperna för en mall. Bry dig inte om att datumet för schemat är 2019-04-01. Den här schema versionen är aktuell och innehåller alla de senaste funktionerna. Schema datumet har inte ändrats på grund av att det inte har skett några ändringar sedan introduktionen.
    - `contentVersion`: Anger versionen för mallen (till exempel 1.0.0.0). Du kan ange valfritt värde för det här elementet. Använd det här värdet om du vill dokumentera viktiga ändringar i mallen. När du distribuerar resurser med hjälp av mallen kan det här värdet användas för att se till att rätt mall används.
    - `resources`: Innehåller de resurser som du vill distribuera eller uppdatera. För närvarande är det tomt, men du kommer att lägga till resurser senare.

1. Spara filen.

Grattis, du har skapat din första mall.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in med dina Azure-autentiseringsuppgifter om du vill börja arbeta med Azure PowerShell/Azure CLI.

Välj flikarna i följande kod avsnitt om du vill välja mellan Azure PowerShell och Azure CLI. CLI-exemplen i den här artikeln är skrivna för bash-gränssnittet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Om du har flera Azure-prenumerationer väljer du den prenumeration som du vill använda. Ersätt `[SubscriptionID/SubscriptionName]` och hakparenteserna `[]` med din prenumerations information:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Skapa resursgrupp

När du distribuerar en mall anger du en resurs grupp som ska innehålla resurserna. Innan du kör distributionskommandot skapar du resursgruppen med antingen Azure CLI eller Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Distribuera mallen

Om du vill distribuera mallen använder du antingen Azure CLI eller Azure PowerShell. Använd den resurs grupp som du skapade. Ge distributionen ett namn så att du enkelt kan identifiera den i distributions historiken. För enkelhetens skull kan du också skapa en variabel som lagrar sökvägen till mallfilen. Den här variabeln gör det enklare för dig att köra distributionskommandon eftersom du inte behöver ange sökvägen varje gång du distribuerar. Ersätt `{provide-the-path-to-the-template-file}` och typografiska klammerparenteser `{}` med sökvägen till mallfilen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Distributions kommandot returnerar resultat. Leta efter för `ProvisioningState` att se om distributionen har slutförts.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![Etablerings status för PowerShell-distribution](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

![Etablerings status för Azure CLI-distribution](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> Om distributionen misslyckades använder du `verbose` växeln för att hämta information om de resurser som skapas. Använd `debug` växeln för att få mer information om fel sökning.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan kontrol lera distributionen genom att utforska resurs gruppen från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **resurs grupper** på den vänstra menyn.

1. Välj resurs grupps distributionen i den senaste proceduren. Standard namnet är **myResourceGroup**. Du får inte se någon resurs som distribuerats i resurs gruppen.

1. Observera att statusen för distributionen visas i det övre högra hörnet i översikten. Select **1 lyckades**.

   ![Visa distributions status](./media/template-tutorial-create-first-template/deployment-status.png)

1. Du ser en historik över distributionen av resurs gruppen. Välj **blanktemplate**.

   ![Välj distribution](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. En sammanfattning av distributionen visas. I det här fallet finns det inte mycket att se eftersom inga resurser har distribuerats. Senare i den här serien kan det vara bra att granska sammanfattningen i distributions historiken. Lägg märke till vänster om du vill visa indata, utdata och mallen som används under distributionen.

   ![Visa distributions Sammanfattning](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

Du har skapat en enkel mall för att distribuera till Azure. I nästa självstudie lägger du till ett lagrings konto i mallen och distribuerar det till din resurs grupp.

> [!div class="nextstepaction"]
> [Lägg till resurs](template-tutorial-add-resource.md)
