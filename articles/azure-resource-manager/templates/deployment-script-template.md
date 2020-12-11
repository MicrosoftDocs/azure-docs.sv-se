---
title: Använda distributions skript i mallar | Microsoft Docs
description: Använd distributions skript i Azure Resource Manager mallar.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: jgao
ms.openlocfilehash: 3a229d1e6752eabd099a5bc60ef93f1d4e85a26b
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092762"
---
# <a name="use-deployment-scripts-in-arm-templates-preview"></a>Använda distributions skript i ARM-mallar (för hands version)

Lär dig hur du använder distributions skript i Azure Resource templates (ARM-mallar). Med en ny resurs typ som kallas `Microsoft.Resources/deploymentScripts` kan användare köra skript i mallar distributioner och granska körnings resultat. Dessa skript kan användas för att utföra anpassade steg som:

- lägga till användare i en katalog
- utför data Plans åtgärder, till exempel kopiera blobbar eller Dirigerings databas
- Leta upp och validera en licens nyckel
- skapa ett självsignerat certifikat
- skapa ett objekt i Azure AD
- Leta upp IP-adressblock från det anpassade systemet

Fördelarna med distributions skript:

- Enkelt att koda, använda och felsöka. Du kan utveckla distributions skript i dina favorit utvecklings miljöer. Skripten kan bäddas in i mallar eller i externa skriptfiler.
- Du kan ange språk och plattform för skript. För närvarande stöds Azure PowerShell-och Azure CLI-distributions skript i Linux-miljön.
- Tillåt att kommando rads argument skickas till skriptet.
- Kan ange utdata för skript och skicka tillbaka dem till distributionen.

Distributions skript resursen är bara tillgänglig i de regioner där Azure Container instance är tillgängligt.  Se [resurs tillgänglighet för Azure Container instances i Azure-regioner](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Ett lagrings konto och en behållar instans krävs för skript körning och fel sökning. Du har möjlighet att ange ett befintligt lagrings konto, annars skapas lagrings kontot tillsammans med behållar instansen automatiskt av skript tjänsten. De två automatiskt skapade resurserna tas vanligt vis bort av skript tjänsten när distributions skript körningen blir i ett Terminal-tillstånd. Du debiteras för resurserna tills resurserna tas bort. Läs mer i avsnittet om hur du [säkerhetskopierar skript resurser](#clean-up-deployment-script-resources).

> [!IMPORTANT]
> DeploymentScripts Resource API version 2020-10-01 stöder [OnBehalfofTokens (OBO)](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md). Genom att använda OBO använder distributions skript tjänsten distributions huvudets token för att skapa de underliggande resurserna för att köra distributions skript, som innehåller Azure Container instance, Azure Storage-konto och roll tilldelningar för den hanterade identiteten. I äldre API-version används den hanterade identiteten för att skapa dessa resurser.
> Omprövnings logik för Azure-inloggning är nu inbyggt i omslutnings skriptet. Om du beviljar behörigheter i samma mall där du kör distributions skript.  Distributions skript tjänsten försöker logga in i 10 minuter med 10 sekunders tid tills tilldelningen av den hanterade identitets rollen replikeras.

## <a name="prerequisites"></a>Förutsättningar

- **(Valfritt) en användardefinierad hanterad identitet med nödvändiga behörigheter för att utföra åtgärderna i skriptet**. För distributions skript-API version 2020-10-01 eller senare används distributions objekt för att skapa underliggande resurser. Om skriptet behöver autentisera till Azure och utföra Azure-/regionsspecifika åtgärder rekommenderar vi att du tillhandahåller skriptet med en användardefinierad hanterad identitet. Den hanterade identiteten måste ha nödvändig åtkomst i mål resurs gruppen för att slutföra åtgärden i skriptet. Du kan också logga in på Azure i distributions skriptet. Om du vill utföra åtgärder utanför resurs gruppen måste du bevilja ytterligare behörighet. Du kan till exempel tilldela identiteten till prenumerations nivån om du vill skapa en ny resurs grupp. 

  Information om hur du skapar en identitet finns i [skapa en användardefinierad hanterad identitet med hjälp av Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), eller med [hjälp av Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)eller [genom att använda Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Du behöver identitets-ID när du distribuerar mallen. Formatet på identiteten är:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Använd följande CLI-eller PowerShell-skript för att hämta ID: t genom att ange resurs gruppens namn och identitetens namn.

  # <a name="cli"></a>[CLI](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g $resourceGroupName -n $idName --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** eller **Azure CLI**. Se en lista över [Azure PowerShell-versioner som stöds](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Se en lista över [Azure CLI-versioner som stöds](https://mcr.microsoft.com/v2/azure-cli/tags/list).

    >[!IMPORTANT]
    > Distributions skriptet använder de tillgängliga CLI-avbildningarna från Microsoft Container Registry (MCR). Det tar ungefär en månad att certifiera en CLI-avbildning för distributions skript. Använd inte de CLI-versioner som släpptes inom 30 dagar. För att hitta versions datumen för avbildningarna, se [versions information för Azure CLI](/cli/azure/release-notes-azure-cli?view=azure-cli-latest&preserve-view=true). Om en version som inte stöds används listas de versioner som stöds i fel meddelandet.

    Du behöver inte dessa versioner för att distribuera mallar. Men de här versionerna behövs för att testa distributions skript lokalt. Se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps). Du kan använda en förkonfigurerad Docker-avbildning.  Se [Konfigurera utvecklings miljö](#configure-development-environment).

## <a name="sample-templates"></a>Exempelmallar

Följande JSON är ett exempel.  Du hittar det senaste mallnamnet [här](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Exemplet är i demonstrations syfte.  **scriptContent** och **primaryScriptUri** kan inte finnas i en mall.

Information om egenskaps värde:

- **Identitet**: för distribution script-API version 2020-10-01 eller senare är en användardefinierad hanterad identitet valfri, om du inte behöver utföra några Azure-/regionsspecifika åtgärder i skriptet.  För API-versionen 2019-10-01 – för hands version krävs en hanterad identitet som distributions skript tjänsten använder den för att köra skripten. För närvarande stöds endast användardefinierad hanterad identitet.
- **typ**: ange typ av skript. För närvarande stöds Azure PowerShell-och Azure CLI-skript. Värdena är **AzurePowerShell** och **AzureCLI**.
- **forceUpdateTag**: om du ändrar det här värdet mellan mallens distributioner tvingas distributions skriptet att köras igen. Om du använder funktionen newGuid () eller utcNow () kan båda funktionerna endast användas i standardvärdet för en parameter. Mer information finns i [Kör skript mer än en gång](#run-script-more-than-once).
- **containerSettings**: Ange inställningarna för att anpassa Azure Container instance.  **containerGroupName** används för att ange behållar gruppens namn.  Om inget anges skapas grupp namnet automatiskt.
- **storageAccountSettings**: Ange inställningarna för att använda ett befintligt lagrings konto. Om inget anges skapas ett lagrings konto automatiskt. Se [Använd ett befintligt lagrings konto](#use-existing-storage-account).
- **azPowerShellVersion** / **azCliVersion**: Ange den version av modulen som ska användas. En lista över PowerShell-och CLI-versioner som stöds finns i [krav](#prerequisites).
- **argument**: ange parameter värden. Värdena avgränsas med blank steg.

    Distributions skript delar argumenten i en sträng mat ris genom att anropa system anropet [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) . Det här steget är nödvändigt eftersom argumenten skickas som en [kommando egenskap](/rest/api/container-instances/containergroups/createorupdate#containerexec) till Azure Container instance, och kommando egenskapen är en sträng mat ris.

    Om argumenten innehåller Escaped tecken, använder du [JsonEscaper](https://www.jsonescaper.com/) för att dubbla escape-tecknen. Klistra in den ursprungliga undantagna strängen i verktyget och välj sedan **Escape**.  Verktyget matar ut en dubbelt undantagen sträng. I föregående exempel-mall är argumentet till exempel **-Name \\ "John Dole \\ "**.  Den undantagna strängen är **-Name \\ \\ \\ "John Dole \\ \\ \\ "**.

    Om du vill skicka en arm-mallparameter av typen Object som ett argument konverterar du objektet till en sträng med hjälp av funktionen [String ()](./template-functions-string.md#string) och använder sedan funktionen [replace ()](./template-functions-string.md#replace) för att ersätta alla **\\ "** i **\\ \\ \\ "**. Exempel:

    ```json
    replace(string(parameters('tables')), '\"', '\\\"')
    ```

    Om du vill se en exempel mall väljer du [här](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- **environmentVariables**: Ange de miljövariabler som ska överföras till skriptet. Mer information finns i [utveckla distributions skript](#develop-deployment-scripts).
- **scriptContent**: ange skript innehållet. Använd i stället om du vill köra ett externt skript `primaryScriptUri` . Exempel finns i [använda infogat skript](#use-inline-scripts) och [Använd externt skript](#use-external-scripts).
- **primaryScriptUri**: Ange en offentligt tillgänglig URL till det primära distributions skriptet med fil namns tillägg som stöds.
- **supportingScriptUris**: Ange en matris med offentligt tillgängliga URL: er till stöd för filer som anropas i antingen `ScriptContent` eller `PrimaryScriptUri` .
- **timeout**: Ange högsta tillåtna körnings tid för skript som anges i [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601). Standardvärdet är **P1D**.
- **cleanupPreference**. Ange inställningen för att rensa distributions resurser när skript körningen blir i ett Terminal-tillstånd. Standardinställningen är **Always**, vilket innebär att resurserna tas bort trots att terminalens tillstånd (lyckades, misslyckades, avbröts). Mer information finns i [Rensa distribution skript resurser](#clean-up-deployment-script-resources).
- **retentionInterval**: Ange intervallet som tjänsten behåller distributions skript resurserna efter när distributions skript körningen har nått ett terminalfönster. Distributions skript resurserna tas bort när denna varaktighet upphör att gälla. Varaktigheten baseras på [ISO 8601-mönstret](https://en.wikipedia.org/wiki/ISO_8601). Kvarhållningsintervallet är mellan 1 och 26 timmar (PT26H). Den här egenskapen används när cleanupPreference är inställt på *OnExpiration*. Egenskapen *OnExpiration* har inte Aktiver ATS för närvarande. Mer information finns i [Rensa distribution skript resurser](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Ytterligare exempel

- [Exempel 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): skapa ett nyckel valv och Använd distributions skriptet för att tilldela ett certifikat till nyckel valvet.
- [Exempel 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): skapa en resurs grupp på prenumerations nivån, skapa ett nyckel valv i resurs gruppen och Använd sedan distributions skriptet för att tilldela ett certifikat till nyckel valvet.
- [Exempel 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): skapa en användardefinierad hanterad identitet, tilldela deltagar rollen till identiteten på resurs grupps nivå, skapa ett nyckel valv och Använd sedan distributions skriptet för att tilldela ett certifikat till nyckel valvet.

## <a name="use-inline-scripts"></a>Använd infogade skript

Följande mall har en resurs definierad med `Microsoft.Resources/deploymentScripts` typen. Den markerade delen är det infogade skriptet.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Eftersom de infogade distributions skripten omges av dubbla citat tecken måste strängarna i distributions skripten undantas genom att använda en **&#92;** eller omges av enkla citat tecken. Du kan också överväga att använda sträng ersättning som det visas i föregående JSON-exempel.

Skriptet tar en parameter och matar ut parametervärdet. **DeploymentScriptOutputs** används för att lagra utdata.  I avsnittet utdata visar **värde** raden hur du kommer åt de lagrade värdena. `Write-Output` används för fel söknings syfte. Information om hur du kommer åt utdatafilen finns i [övervaka och felsöka distributions skript](#monitor-and-troubleshoot-deployment-scripts).  För egenskaps beskrivningar, se [exempel-mallar](#sample-templates).

Om du vill köra skriptet väljer du **prova** att öppna Cloud Shell och klistrar in följande kod i rutan Shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

Utdata ser ut så här:

![Distributions skript Hello World-utdata i Resource Manager-mall](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Använd externa skript

Förutom infogade skript kan du också använda externa skriptfiler. Endast primära PowerShell-skript med fil namns tillägget **ps1** stöds. För CLI-skript kan primära skript ha alla tillägg (eller utan tillägg), så länge skripten är giltiga bash-skript. Om du vill använda externa skriptfiler ersätter du `scriptContent` med `primaryScriptUri` . Exempel:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Om du vill se ett exempel väljer du [här](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

De externa skriptfilerna måste vara tillgängliga.  Information om hur du skyddar skriptfilerna som lagras i Azure Storage-konton finns i [distribuera privat arm-mall med SAS-token](./secure-template-with-sas-token.md).

Du ansvarar för att säkerställa integriteten för skript som refereras till av distributions skriptet, antingen **PrimaryScriptUri** eller **SupportingScriptUris**.  Referera endast till skript som du litar på.

## <a name="use-supporting-scripts"></a>Använda stöd skript

Du kan separera komplicerade logiker till en eller flera stödfiler. Med `supportingScriptUris` egenskapen kan du ange en matris med URI: er till de stödda skriptfilerna vid behov:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Stöd för skriptfiler kan anropas från både infogade skript och primära skriptfiler. Stöd för skriptfiler har inga begränsningar för fil namns tillägget.

De stödfiler som kopieras kopieras till `azscripts/azscriptinput` vid körningen. Använd relativ sökväg för att referera till stödjande filer från infogade skript och primära skriptfiler.

## <a name="work-with-outputs-from-powershell-script"></a>Arbeta med utdata från PowerShell-skript

Följande mall visar hur du överför värden mellan två deploymentScripts-resurser:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

I den första resursen definierar du en variabel som heter **$DeploymentScriptOutputs** och använder den för att lagra värdena för utdata. Använd följande för att få åtkomst till resultatvärdet från en annan resurs i mallen:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Arbeta med utdata från CLI-skript

Det skiljer sig från PowerShell-distributions skriptet, CLI/bash-stöd ger inte en gemensam variabel för att lagra skript utdata, i stället finns en miljö variabel som kallas **AZ_SCRIPTS_OUTPUT_PATH** som lagrar platsen där filen med skriptets utdata finns. Om ett distributions skript körs från en Resource Manager-mall, ställs den här miljövariabeln in automatiskt åt dig av bash-gränssnittet.

Utdata för distributions skript måste sparas på AZ_SCRIPTS_OUTPUT_PATH plats och utmatningarna måste vara ett giltigt JSON-String-objekt. Filens innehåll måste sparas som ett nyckel/värde-par. Till exempel lagras en sträng mat ris som {"mina resultat": ["foo", "bar"]}.  Att bara lagra mat ris resultaten, till exempel ["foo", "bar"], är ogiltigt.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[JQ](https://stedolan.github.io/jq/) används i föregående exempel. Den levereras med behållar avbildningarna. Se [Konfigurera utvecklings miljö](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Använd befintligt lagrings konto

Ett lagrings konto och en behållar instans krävs för skript körning och fel sökning. Du har möjlighet att ange ett befintligt lagrings konto, annars skapas lagrings kontot tillsammans med behållar instansen automatiskt av skript tjänsten. Kraven för att använda ett befintligt lagrings konto:

- De typer av lagrings konton som stöds är:

    | SKU             | Typ som stöds     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Lagring, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Lagring, StorageV2 |
    | Standard_RAGRS  | Lagring, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Dessa kombinationer stöder fil resurs.  Mer information finns i [skapa en Azure-filresurs](../../storage/files/storage-how-to-create-file-share.md) och [typer av lagrings konton](../../storage/common/storage-account-overview.md).
- Brand Väggs regler för lagrings konto stöds inte än. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../../storage/common/storage-network-security.md).
- Distributions huvud kontot måste ha behörighet att hantera lagrings kontot, som innehåller läsa, skapa, ta bort fil resurser.

Om du vill ange ett befintligt lagrings konto lägger du till följande JSON till egenskaps elementet för `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName**: Ange namnet på lagrings kontot.
- **storageAccountKey "**: Ange en av lagrings konto nycklarna. Du kan använda [`listKeys()`](./template-functions-resource.md#listkeys) funktionen för att hämta nyckeln. Exempel:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Se [exempel på mallar](#sample-templates) för ett komplett `Microsoft.Resources/deploymentScripts` definitions exempel.

När ett befintligt lagrings konto används skapar skript tjänsten en fil resurs med ett unikt namn. Se [Rensa distributions skript resurser](#clean-up-deployment-script-resources) för hur skript tjänsten rensar fil resursen.

## <a name="develop-deployment-scripts"></a>Utveckla distributions skript

### <a name="handle-non-terminating-errors"></a>Hantera icke-avslutande fel

Du kan styra hur PowerShell svarar på icke-avslutande fel med hjälp av **$ErrorActionPreference** -variabeln i distributions skriptet. Om variabeln inte anges i distributions skriptet använder skript tjänsten standardvärdet Continue ( **Fortsätt**).

Skript tjänsten anger resurs etablerings statusen till **misslyckad** när skriptet påträffar ett fel trots inställningen för $ErrorActionPreference.

### <a name="pass-secured-strings-to-deployment-script"></a>Skicka skyddade strängar till distributions skript

Genom att ställa in miljövariabler (EnvironmentVariable) i behållar instanser kan du tillhandahålla dynamisk konfiguration av programmet eller skriptet som körs av behållaren. Distributions skriptet hanterar icke-skyddade och skyddade miljövariabler på samma sätt som Azure Container instance. Mer information finns i [Ange miljövariabler i container instances](../../container-instances/container-instances-environment-variables.md#secure-values).

Den högsta tillåtna storleken för miljövariabler är 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Övervaka och felsöka distributions skript

Skript tjänsten skapar ett [lagrings konto](../../storage/common/storage-account-overview.md) (om du inte anger ett befintligt lagrings konto) och en [behållar instans](../../container-instances/container-instances-overview.md) för skript körning. Om dessa resurser skapas automatiskt av skript tjänsten, har båda resurserna `azscripts` suffixet i resurs namnen.

![Distributions skript resurs namn för Resource Manager-mall](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Användar skriptet, körnings resultaten och STDOUT-filen lagras i lagrings kontots fil resurser. Det finns en mapp som heter `azscripts` . I mappen finns det två fler mappar för indata och utdatafiler: `azscriptinput` och `azscriptoutput` .

Mappen utdata innehåller en **executionresult.jspå** och skript utdatafilen. Du kan se fel meddelandet för skript körning i **executionresult.jspå**. Utdatafilen skapas endast när skriptet har körts. Mappen indata innehåller en system PowerShell-skriptfil och skript filen för användar distribution. Du kan ersätta skript filen för användar distribution med en ändrad, och köra distributions skriptet på nytt från Azure Container instance.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

När du har distribuerat en distributions skript resurs visas resursen under resurs gruppen i Azure Portal. Följande skärm bild visar översikts sidan för en distributions skript resurs:

![Översikt över distribution av skript portalen för Resource Manager-mall](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

På sidan Översikt visas viktig information om resursen, till exempel **etablerings status**, **lagrings konto**, **behållar instans** och **loggar**.

På den vänstra menyn kan du Visa distributions skriptets innehåll, argumenten som skickas till skriptet och utdata.  Du kan också exportera en mall för distributions skriptet, inklusive distributions skriptet.

### <a name="use-powershell"></a>Använd PowerShell

Med hjälp av Azure PowerShell kan du hantera distributions skript på prenumerations-eller resurs grupps omfång:

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): hämtar eller visar distributions skript.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): hämtar loggen för en körning av distributions skript.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): tar bort ett distributions skript och dess associerade resurser.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): sparar loggen för en distributions skript körning till disk.

Get-AzDeploymentScript utdata ser ut ungefär så här:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Använda Azure CLI

Med Azure CLI kan du hantera distributions skript på prenumerations-eller resurs grupps omfång:

- [AZ distribution-skript ta bort](/cli/azure/deployment-scripts?view=azure-cli-latest&preserve-view=true#az-deployment-scripts-delete): ta bort ett distributions skript.
- [AZ distribution – skript lista](/cli/azure/deployment-scripts?view=azure-cli-latest&preserve-view=true#az-deployment-scripts-list): visar alla distributions skript.
- [AZ distribution – skript Visa](/cli/azure/deployment-scripts?view=azure-cli-latest&preserve-view=true#az-deployment-scripts-show): Hämta ett distributions skript.
- [AZ distribution – skript Visa-logg](/cli/azure/deployment-scripts?view=azure-cli-lates&preserve-view=truet#az-deployment-scripts-show-log): Visa distributions skript loggar.

Utdata för list kommandot liknar:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>Använd REST API

Du kan hämta distributions information för distributions skriptets resurs på resurs grupps nivå och prenumerations nivå genom att använda REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

I följande exempel används [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Utdatan liknar följande:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

Följande REST API returnerar loggen:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Den fungerar bara innan distributions skript resurserna tas bort.

Om du vill se deploymentScripts-resursen i portalen väljer du **Visa dolda typer**:

![Distributions skript för Resource Manager-mall, Visa dolda typer, Portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Rensa distributions skript resurser

Ett lagrings konto och en behållar instans krävs för skript körning och fel sökning. Du har möjlighet att ange ett befintligt lagrings konto, annars skapas ett lagrings konto tillsammans med en behållar instans automatiskt av skript tjänsten. De två automatiskt skapade resurserna tas bort av skript tjänsten när distributions skript körningen blir i ett Terminal-tillstånd. Du debiteras för resurserna tills resurserna tas bort. Pris informationen finns i [container instances priser](https://azure.microsoft.com/pricing/details/container-instances/) och [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).

Livs cykeln för de här resurserna styrs av följande egenskaper i mallen:

- **cleanupPreference**: rensnings inställning när skript körningen får statusen Terminal. De värden som stöds är:

  - **Always**: ta bort de automatiskt skapade resurserna när skript körningen blir i ett Terminal-tillstånd. Om ett befintligt lagrings konto används, tar skript tjänsten bort fil resursen som skapats i lagrings kontot. Eftersom deploymentScripts-resursen kanske fortfarande finns när resurserna har rensats, behåller skript tjänsten skript körnings resultatet, till exempel STDOUT, utdata, retur värde osv. innan resurserna tas bort.
  - **OnSuccess**: ta bara bort de automatiskt skapade resurserna när skript körningen har slutförts. Om ett befintligt lagrings konto används tar skript tjänsten bara bort fil resursen när skript körningen har slutförts. Du kan fortfarande komma åt resurserna för att hitta felsöknings information.
  - **OnExpiration**: ta bort de automatiskt skapade resurserna endast när **retentionInterval** -inställningen har upphört att gälla. Om ett befintligt lagrings konto används, tar skript tjänsten bort fil resursen, men behåller lagrings kontot.

- **retentionInterval**: Ange det tidsintervall som en skript resurs kommer att behållas och därefter upphör att gälla och tas bort.

> [!NOTE]
> Vi rekommenderar inte att du använder lagrings kontot och behållar instansen som genereras av skript tjänsten för andra skäl. De två resurserna kan tas bort beroende på skript livs cykeln.

Om du vill behålla behållar instansen och lagrings kontot för fel sökning kan du lägga till ett vila-kommando i skriptet.  Använd till exempel [Start-vilo läge](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/start-sleep).

## <a name="run-script-more-than-once"></a>Kör skript mer än en gång

Körning av distributions skript är en idempotenta åtgärd. Om ingen av resurs egenskaperna för deploymentScripts (inklusive det infogade skriptet) ändras, körs inte skriptet när du distribuerar om mallen. Distributions skript tjänsten jämför resurs namnen i mallen med de befintliga resurserna i samma resurs grupp. Det finns två alternativ om du vill köra samma distributions skript flera gånger:

- Ändra namnet på din deploymentScripts-resurs. Använd till exempel funktionen [utcNow](./template-functions-date.md#utcnow) -mall som resurs namn eller som en del av resurs namnet. Om du ändrar resurs namnet skapas en ny deploymentScripts-resurs. Det är lämpligt att spara en historik över skript körningen.

    > [!NOTE]
    > Funktionen utcNow kan bara användas i standardvärdet för en parameter.

- Ange ett annat värde i `forceUpdateTag` egenskapen Template.  Använd till exempel utcNow som värde.

> [!NOTE]
> Skriv de distributions skript som är idempotenta. Detta säkerställer att om de körs igen av misstag, kommer de inte att orsaka system ändringar. Om distributions skriptet exempelvis används för att skapa en Azure-resurs kontrollerar du att resursen inte finns innan du skapar den, så skriptet kommer att lyckas eller så skapar du inte resursen igen.

## <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

Du kan använda en förkonfigurerad behållar avbildning som distributions miljö för skript utveckling. Mer information finns i avsnittet [Konfigurera utvecklings miljö för distributions skript i mallar](./deployment-script-template-configure-dev.md).

När skriptet har testats kan du använda det som ett distributions skript i mallarna.

## <a name="deployment-script-error-codes"></a>Fel koder för distributions skript

| Felkod | Description |
|------------|-------------|
| DeploymentScriptInvalidOperation | Resurs definitionen för distributions skriptet i mallen innehåller ogiltiga egenskaps namn. |
| DeploymentScriptResourceConflict | Det går inte att ta bort en distributions skript resurs som är i icke-terminal-tillstånd och körningen har inte överskridit 1 timme. Eller också kan du inte köra samma distributions skript igen med samma resurs-ID (samma prenumeration, resurs grupp namn och resurs namn), men olika skript texts innehåll på samma tid. |
| DeploymentScriptOperationFailed | Distributions skript åtgärden misslyckades internt. Kontakta Microsoft-supporten. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | Åtkomst nyckeln har inte angetts för det befintliga lagrings kontot.|
| DeploymentScriptContainerGroupContainsInvalidContainers | En behållar grupp som skapats av distributions skript tjänsten har ändrats externt och ogiltiga behållare lades till. |
| DeploymentScriptContainerGroupInNonterminalState | Minst två distributions skript resurser använder samma instans namn för Azure Container i samma resurs grupp och en av dem har inte slutfört körningen ännu. |
| DeploymentScriptStorageAccountInvalidKind | Det befintliga lagrings kontot för BlobBlobStorage-eller BlobStorage-typen stöder inte fil resurser och kan inte användas. |
| DeploymentScriptStorageAccountInvalidKindAndSku | Det befintliga lagrings kontot har inte stöd för fil resurser. En lista över typer av lagrings konton som stöds finns i [Använd befintligt lagrings konto](#use-existing-storage-account). |
| DeploymentScriptStorageAccountNotFound | Lagrings kontot finns inte eller har tagits bort av en extern process eller ett verktyg. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | Det angivna lagrings kontot har en tjänst slut punkt. Det finns inte stöd för ett lagrings konto med en tjänst slut punkt. |
| DeploymentScriptStorageAccountInvalidAccessKey | En ogiltig åtkomst nyckel har angetts för det befintliga lagrings kontot. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Ogiltigt nyckel format för lagrings konto. Se [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md). |
| DeploymentScriptExceededMaxAllowedTime | Körnings tiden för distributions skriptet överskred det timeout-värde som angavs i resurs definitionen för distributions skriptet. |
| DeploymentScriptInvalidOutputs | Distributions skriptets utdata är inte ett giltigt JSON-objekt. |
| DeploymentScriptContainerInstancesServiceLoginFailure | Den tilldelade hanterade identiteten kunde inte logga in efter 10 försök med ett intervall på 1 minut. |
| DeploymentScriptContainerGroupNotFound | En behållar grupp som skapats av distributions skript tjänsten togs bort av ett externt verktyg eller en process. |
| DeploymentScriptDownloadFailure | Det gick inte att hämta ett stöd skript. Se [använda stöd skript](#use-supporting-scripts).|
| DeploymentScriptError | Ett fel inträffade i användar skriptet. |
| DeploymentScriptBootstrapScriptExecutionFailed | Ett fel inträffade i bootstrap-skriptet. Bootstrap-skript är det system skript som dirigerar distributionen av skript. |
| DeploymentScriptExecutionFailed | Ett okänt fel inträffade under körningen av distributions skriptet. |
| DeploymentScriptContainerInstancesServiceUnavailable | När du skapar Azure Container Instance (ACI) utlöste ACI ett otillgängligt tjänst fel. |
| DeploymentScriptContainerGroupInNonterminalState | När du skapar Azure Container Instance (ACI) använder ett annat distributions skript samma ACI-namn i samma omfång (samma prenumeration, resurs grupp namn och resurs namn). |
| DeploymentScriptContainerGroupNameInvalid | Det angivna Azure Container instance-namnet (ACI) uppfyller inte kraven för ACI. Se [Felsöka vanliga problem i Azure Container instances](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder distributions skript. För att gå igenom en själv studie kurs om distributions skript:

> [!div class="nextstepaction"]
> [Självstudie: använda distributions skript i Azure Resource Manager mallar](./template-tutorial-deployment-script.md)
