---
title: Självstudie – Skapa & Distribuera Azure Resource Manager bicep-filer
description: Skapa din första bicep-fil för att distribuera Azure-resurser. I självstudien får du lära dig om bicep-filsyntaxen och hur du distribuerar ett lagrings konto.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8979585d7ec0fa6eac1866375fe1e80214f2d2e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594282"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>Självstudie: skapa och distribuera den första Azure Resource Manager bicep-filen

I den här självstudien beskrivs [bicep](./bicep-overview.md). Det visar hur du skapar en start bicep-fil och distribuerar den till Azure. Du lär dig mer om strukturen för bicep-filen och de verktyg du behöver för att arbeta med bicep-filer. Det tar ungefär **12 minuter** att slutföra den här självstudien, men den faktiska tiden varierar beroende på hur många verktyg du behöver installera.

Den här självstudien är den första i en serie. När du går igenom serien ändrar du Start bicep-filen steg för steg tills du har utforskat alla kärn delar av en bicep-fil. Dessa element är Bygg stenarna för mycket mer komplexa bicep-filer. Vi hoppas i slutet av serien och du är säker på att du har skapat dina egna bicep-filer och är redo att automatisera dina distributioner med bicep-filer.

Om du vill lära dig mer om fördelarna med att använda bicep och varför du bör automatisera distributionen med bicep-filer, se [bicep](./bicep-overview.md).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>Hämta verktyg

Vi börjar med att se till att du har de verktyg du behöver för att skapa och distribuera bicep-filer. Installera dessa verktyg på den lokala datorn.

### <a name="editor"></a>Redigerare

Om du vill skapa bicep-filer behöver du en lämplig redigerare. Vi rekommenderar Visual Studio Code med bicep-tillägget. Om du behöver installera dessa verktyg går du till [Konfigurera bicep Development Environment](./bicep-install.md#development-environment).

### <a name="command-line-deployment"></a>Kommando rads distribution

Du kan distribuera bicep-filer med hjälp av Azure CLI eller Azure PowerShell. För Azure CLI behöver du version 2.20.0 eller senare. för Azure PowerShell behöver du version 5.6.0 eller senare. Installations anvisningar finns i:

- [Installera Azure PowerShell](/powershell/azure/install-az-ps)
- [Installera Azure CLI på Windows](/cli/azure/install-azure-cli-windows)
- [Installera Azure CLI på Linux](/cli/azure/install-azure-cli-linux)
- [Installera Azure CLI på macOS](/cli/azure/install-azure-cli-macos)

När du har installerat antingen Azure PowerShell eller Azure CLI kontrollerar du att du loggar in för första gången. Mer information finns i [Logga in-PowerShell](/powershell/azure/install-az-ps#sign-in) eller [Logga in – Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

OK, du är redo att börja lära dig om bicep.

## <a name="create-your-first-bicep-file"></a>Skapa din första bicep-fil

1. Öppna Visual Studio Code med bicep-tillägget installerat.
1. I menyn **Arkiv** väljer du **ny fil** för att skapa en ny fil.
1. Välj **Spara som** på **Arkiv** -menyn.
1. Ge filen namnet _azuredeploy_ och välj fil namns tillägget _bicep_ . Det fullständiga namnet på filen är _azuredeploy. bicep_.
1. Spara filen på din arbets Station. Välj en sökväg som är lätt att komma ihåg eftersom du kommer att ange den sökvägen senare när du distribuerar bicep-filen.
1. Kopiera och klistra in följande innehåll i filen:

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    Så här ser din Visual Studio Code-miljö ut:

    ![Visual Studio Code First bicep-fil](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    Resurs deklarationen har fyra komponenter:

    - **resurs**: nyckelord.
    - **symboliskt namn** (STG): ett symboliskt namn är en identifierare för att referera till resursen i hela bicep-filen. Det är inte vad namnet på resursen kommer att vara när det distribueras. Namnet på resursen definieras av egenskapen **Name** .  Se den fjärde komponenten i den här listan. För att göra självstudierna enkla att följa, används **STG** som symboliskt namn för lagrings konto resursen i den här själv studie serien. Information om hur du använder det symboliska namnet för att få en fullständig lista över objekt egenskaperna finns i [lägga till utdata](./bicep-tutorial-add-outputs.md).
    - **resurs typ** ( Microsoft.Storage/storageAccounts@2019-06-01 ): den består av resurs leverantören (Microsoft. Storage), resurs typ (StorageAccounts) och API version (2019-06-01). Varje resurs leverantör publicerar sina egna API-versioner, så det här värdet är specifika för typen. Du hittar fler typer och apiVersions för olika Azure-resurser från [referens för ARM-mallar](/azure/templates/).
    - **Egenskaper** (allt inuti = {...}): dessa är de egenskaper som du vill ange för den angivna resurs typen. Detta är exakt samma egenskaper som är tillgängliga i en ARM-mall. Varje resurs har en `name` egenskap. De flesta resurser har också en `location` egenskap som anger den region där resursen distribueras. De andra egenskaperna varierar beroende på resurs typ och API-version. Det är viktigt att förstå anslutningen mellan API-versionen och de tillgängliga egenskaperna, så vi går vidare till mer information.

        För det här lagrings kontot kan du se att API-versionen är på [storageAccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts). Observera att du inte har lagt till alla egenskaper i din bicep-fil. Många av egenskaperna är valfria. `Microsoft.Storage`Resurs leverantören kan frigöra en ny API-version, men den version som du distribuerar behöver inte ändras. Du kan fortsätta att använda den versionen och veta att resultatet av distributionen blir konsekvent.

        Om du visar en äldre API-version, till exempel [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), ser du att det finns en mindre uppsättning egenskaper.

        Om du bestämmer dig för att ändra API-versionen för en resurs, se till att du utvärderar egenskaperna för den versionen och justerar din bicep-fil på lämpligt sätt.

1. Ersätt `{provide-unique-name}` inklusive klammerparenteser `{}` med ett unikt lagrings konto namn.

    > [!IMPORTANT]
    > Namnet på lagringskontot måste vara unikt i Azure. Namnet får bara innehålla gemena bokstäver eller siffror. Det får inte vara längre än 24 tecken. Du kan prova ett namngivnings mönster som att använda **store1** som prefix och sedan lägga till dina initialer och dagens datum. Till exempel kan namnet som du använder se ut som **store1abc09092019**.

    Att gissa ett unikt namn för ett lagrings konto är inte enkelt och fungerar inte bra för att automatisera stora distributioner. Senare i den här själv studie serien använder du bicep-funktioner som gör det enklare att skapa ett unikt namn.

1. Spara filen.

Grattis, du har skapat din första bicep-fil.

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

När du distribuerar en bicep-fil anger du en resurs grupp som ska innehålla resurserna. Innan du kör distributionskommandot skapar du resursgruppen med antingen Azure CLI eller Azure PowerShell.

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

## <a name="deploy-bicep-file"></a>Distribuera bicep-fil

Bicep är en transparent abstraktion över Azure Resource Manager mallar (ARM-mallar). Varje bicep-fil kompileras till en standard ARM-mall innan den distribueras. Du kan antingen kompilera din bicep-fil till en ARM-mall innan du distribuerar den eller distribuera bicep-filen direkt. Om du vill distribuera bicep-filen använder du antingen Azure CLI eller Azure PowerShell. Använd den resurs grupp som du skapade. Ge distributionen ett namn så att du enkelt kan identifiera den i distributions historiken. För enkelhetens skull kan du också skapa en variabel som lagrar sökvägen till bicep-filen. Den här variabeln gör det enklare för dig att köra distributionskommandon eftersom du inte behöver ange sökvägen varje gång du distribuerar. Ersätt `{provide-the-path-to-the-bicep-file}` inklusive klammerparenteser `{}` med sökvägen till din bicep-fil med fil namns tillägget _. bicep_ .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För att kunna köra denna distributions-cmdlet måste du ha den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell.

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

Distributions kommandot returnerar resultat. Leta efter för `ProvisioningState` att se om distributionen har slutförts.

> [!NOTE]
> Om distributionen misslyckades använder du `verbose` växeln för att hämta information om de resurser som skapas. Använd `debug` växeln för att få mer information om fel sökning.

## <a name="verify-deployment"></a>Verifiera distributionen

Du kan kontrol lera distributionen genom att utforska resurs gruppen från Azure Portal.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **resurs grupper** på den vänstra menyn.

1. Välj resurs grupps distributionen i den senaste proceduren. Standard namnet är **myResourceGroup**. Du får inte se någon resurs som distribuerats i resurs gruppen.

1. Observera att statusen för distributionen visas i det övre högra hörnet i översikten. Select **1 lyckades**.

   ![Visa distributions status](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. Du ser en historik över distributionen av resurs gruppen. Välj **firstbicep**.

   ![Välj distribution](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. En sammanfattning av distributionen visas. Ett lagrings konto har distribuerats. Lägg märke till vänster om du vill visa indata, utdata och mallen som används under distributionen.

   ![Visa distributions Sammanfattning](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du går vidare till nästa självstudie behöver du inte ta bort resurs gruppen.

Om du stoppar nu kanske du vill ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

Du har skapat en enkel bicep-fil för att distribuera till Azure.  I senare självstudier får du lära dig hur du lägger till parametrar, variabler, utdata och moduler i en bicep-fil. Dessa funktioner är Bygg stenar för mycket mer komplexa bicep-filer.

> [!div class="nextstepaction"]
> [Lägga till parametrar](bicep-tutorial-add-parameters.md)
