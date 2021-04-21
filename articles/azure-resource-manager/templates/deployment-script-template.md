---
title: Använda distributionsskript i mallar | Microsoft Docs
description: använda distributionsskript i Azure Resource Manager mallar.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: jgao
ms.openlocfilehash: c39b332e9ee62a8502d5e2fdf155819194a30e34
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762199"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Använda distributionsskript i ARM-mallar

Lär dig hur du använder distributionsskript i Azure-resursmallar (ARM-mallar). Med en ny resurstyp med `Microsoft.Resources/deploymentScripts` namnet kan användarna köra skript i malldistributioner och granska körningsresultaten. Dessa skript kan användas för att utföra anpassade steg, till exempel:

- lägga till användare i en katalog
- utföra dataplansåtgärder, till exempel kopiera blobar eller startdatabas
- leta upp och verifiera en licensnyckel
- skapa ett själv signerat certifikat
- skapa ett objekt i Azure AD
- leta upp IP-adressblock från ett anpassat system

Fördelarna med distributionsskriptet:

- Lätt att koda, använda och felsöka. Du kan utveckla distributionsskript i dina favoritutvecklingsmiljöer. Skripten kan bäddas in i mallar eller i externa skriptfiler.
- Du kan ange skriptspråk och plattform. För närvarande Azure PowerShell och Azure CLI-distributionsskript i Linux-miljön.
- Tillåt att kommandoradsargumenten kan skickas till skriptet.
- Kan ange skriptutdata och skicka dem tillbaka till distributionen.

Distributionsskriptresursen är bara tillgänglig i de regioner där Azure Container Instance är tillgängligt.  Se [Resurstillgänglighet för Azure Container Instances i Azure-regioner.](../../container-instances/container-instances-region-availability.md)

> [!IMPORTANT]
> Ett lagringskonto och en containerinstans krävs för skriptkörning och felsökning. Du har möjlighet att ange ett befintligt lagringskonto, annars skapas lagringskontot tillsammans med containerinstansen automatiskt av skripttjänsten. De två automatiskt skapade resurserna tas vanligtvis bort av skripttjänsten när körningen av distributionsskriptet hamnar i ett terminaltillstånd. Du debiteras för resurserna tills resurserna tas bort. Mer information finns i Rensa [resurser för distributionsskript.](#clean-up-deployment-script-resources)

> [!IMPORTANT]
> Resurs-API:et deploymentScripts version 2020-10-01 stöder [OnBehalfofTokens(OBO).](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md) Med hjälp av OBO använder distributionsskripttjänsten token för distributionsobjekt för att skapa de underliggande resurserna för att köra distributionsskript, som omfattar Azure Container-instans, Azure Storage-konto och rolltilldelningar för den hanterade identiteten. I en äldre API-version används den hanterade identiteten för att skapa dessa resurser.
> Omprövningslogiken för Azure-inloggning är nu inbyggd i omslutningsskriptet. Om du beviljar behörigheter i samma mall där du kör distributionsskript. Tjänsten för distributionsskript försöker logga in i 10 minuter med 10 sekunders intervall tills rolltilldelningen för hanterad identitet replikeras.

## <a name="configure-the-minimum-permissions"></a>Konfigurera de lägsta behörigheterna

För API-version 2020-10-01 eller senare används distributionsskriptet för att skapa underliggande resurser som krävs för att distributionsskriptresursen ska kunna köras – ett lagringskonto och en Azure-containerinstans. Om skriptet behöver autentisera till Azure och utföra Azure-specifika åtgärder rekommenderar vi att du tillhandahåller skriptet med en användar tilldelad hanterad identitet. Den hanterade identiteten måste ha nödvändig åtkomst för att slutföra åtgärden i skriptet.

För att konfigurera behörigheter med minsta behörighet behöver du:

- Tilldela en anpassad roll med följande egenskaper till distributionshuvudnamn:

  ```json
  {
    "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
    "description": "Configure least privilege for the deployment principal in deployment script",
    "type": "customRole",
    "IsCustom": true,
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/*",
          "Microsoft.ContainerInstance/containerGroups/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/deploymentScripts/*"
        ],
      }
    ],
    "assignableScopes": [
      "[subscription().id]"
    ]
  }
  ```

  Om resursprovidern Azure Storage Azure Container Instance inte har registrerats måste du också lägga till `Microsoft.Storage/register/action` och `Microsoft.ContainerInstance/register/action` .

- Om en hanterad identitet används måste distributionshuvudnamn ha rollen Hanterad **identitetsoperatör** (en inbyggd roll) tilldelad till den hanterade identitetsresursen.

## <a name="sample-templates"></a>Exempelmallar

Följande JSON är ett exempel. Mer information finns i det senaste [mallschemat.](/azure/templates/microsoft.resources/deploymentscripts)

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
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
    "forceUpdateTag": "1",
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
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
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
> Exemplet är i demonstrationssyfte. Egenskaperna och `scriptContent` `primaryScriptUri` kan inte samexistera i en mall.

> [!NOTE]
> _ScriptContent visar_ ett skript med flera rader.  Pipelinen Azure Portal Azure DevOps kan inte parsa ett distributionsskript med flera rader. Du kan antingen länka PowerShell-kommandona (med semikolon eller _\\ r \\ n_ eller _\\ n_) till en rad eller använda egenskapen med `primaryScriptUri` en extern skriptfil. Det finns många kostnadsfria verktyg för JSON-sträng escape/unescape. Till exempel [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html).

Information om egenskapsvärde:

- `identity`: För API-version 2020-10-01 eller senare är en användar tilldelad hanterad identitet valfri såvida du inte behöver utföra azure-specifika åtgärder i skriptet.  För API-versionen 2019-10-01-preview krävs en hanterad identitet eftersom tjänsten för distributionsskript använder den för att köra skripten. När identitetsegenskapen anges anropar `Connect-AzAccount -Identity` skripttjänsten innan användarskriptet anropas. För närvarande stöds endast användar tilldelad hanterad identitet. Om du vill logga in med en annan identitet kan du [anropa Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) i skriptet.
- `kind`: Ange typ av skript. För närvarande Azure PowerShell och Azure CLI-skript. Värdena är **AzurePowerShell** och **AzureCLI.**
- `forceUpdateTag`: Om du ändrar det här värdet mellan malldistributioner tvingar du distributionsskriptet att köra om. Om du använder `newGuid()` funktionerna eller kan båda funktionerna endast användas i `utcNow()` standardvärdet för en parameter. Mer information finns i Köra [skript mer än en gång.](#run-script-more-than-once)
- `containerSettings`: Ange inställningarna för att anpassa Azure Container Instance. Distributionsskriptet kräver en ny Azure Container Instance. Du kan inte ange en befintlig Azure Container Instance. Du kan dock anpassa containergruppens namn med hjälp av `containerGroupName` . Om inget anges genereras gruppnamnet automatiskt.
- `storageAccountSettings`: Ange inställningarna för att använda ett befintligt lagringskonto. Om `storageAccountName` inte anges skapas ett lagringskonto automatiskt. Se [Använda ett befintligt lagringskonto.](#use-existing-storage-account)
- `azPowerShellVersion`/`azCliVersion`: Ange modulversionen som ska användas. Se en lista över [versioner Azure PowerShell stöds.](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list) Se en lista över [Azure CLI-versioner som stöds.](https://mcr.microsoft.com/v2/azure-cli/tags/list)

  >[!IMPORTANT]
  > Distributionsskriptet använder de tillgängliga CLI-avbildningarna från Microsoft Container Registry (MCR). Det tar ungefär en månad att certifiera en CLI-avbildning för distributionsskript. Använd inte CLI-versionerna som släpptes inom 30 dagar. Information om hur du hittar lanseringsdatumen för avbildningarna finns [i Azure CLI:s viktiga information.](/cli/azure/release-notes-azure-cli) Om en version som inte stöds används visas de versioner som stöds i felmeddelandet.

- `arguments`: Ange parametervärdena. Värdena avgränsas med blanksteg.

  Distributionsskript delar upp argumenten i en matris med strängar genom att anropa system-anropet [CommandLineToArgvW.](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) Det här steget är nödvändigt eftersom [](/rest/api/container-instances/containergroups/createorupdate#containerexec) argumenten skickas som en kommandoegenskap till Azure Container Instance och kommandoegenskapen är en strängmatris.

  Om argumenten innehåller tecken som inte är förrymda använder [du JsonEscaper](https://www.jsonescaper.com/) för att dubbla de tecken som har rymts. Klistra in den ursprungliga escape-strängen i verktyget och välj sedan **Escape**.  Verktyget matar ut en dubbel utrymd sträng. I den föregående exempelmallen är argumentet till exempel `-name \"John Dole\"` . Den utrymda strängen är `-name \\\"John Dole\\\"` .

  Om du vill skicka en ARM-mallparameter av typen objekt som ett argument konverterar du objektet till en sträng med hjälp av [funktionen string()](./template-functions-string.md#string) och använder sedan [funktionen replace()](./template-functions-string.md#replace) för att ersätta `\"` till `\\\"` . Exempel:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  Mer information finns i [exempelmallen](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- `environmentVariables`: Ange de miljövariabler som ska överföras till skriptet. Mer information finns i [Utveckla distributionsskript.](#develop-deployment-scripts)
- `scriptContent`: Ange skriptinnehållet. Om du vill köra ett externt skript använder du `primaryScriptUri` i stället. Exempel finns i [Använda infogade skript och](#use-inline-scripts) Använd externt [skript.](#use-external-scripts)
- `primaryScriptUri`: Ange en offentligt tillgänglig URL till det primära distributionsskriptet med filnamnstillägg som stöds. Mer information finns i [Använda externa skript.](#use-external-scripts)
- `supportingScriptUris`: Ange en matris med offentligt tillgängliga URL:er till stödfiler som anropas i antingen `scriptContent` eller `primaryScriptUri` . Mer information finns i [Använda externa skript.](#use-external-scripts)
- `timeout`: Ange den högsta tillåtna skriptkörningstiden som anges i [ISO 8601-format.](https://en.wikipedia.org/wiki/ISO_8601) Standardvärdet är **P1D**.
- `cleanupPreference`. Ange inställningen för att rensa distributionsresurser när skriptkörningen hamnar i ett terminaltillstånd. Standardinställningen är **Alltid**, vilket innebär att resurserna tas bort trots terminaltillståndet (lyckades, misslyckades, avbröts). Mer information finns i Rensa [resurser för distributionsskript.](#clean-up-deployment-script-resources)
- `retentionInterval`: Ange intervallet för vilket tjänsten behåller distributionsskriptresurserna när körningen av distributionsskriptet når ett terminaltillstånd. Resurser för distributionsskriptet tas bort när den här varaktigheten upphör att gälla. Varaktigheten baseras på [ISO 8601-mönstret](https://en.wikipedia.org/wiki/ISO_8601). Kvarhållningsintervallet är mellan 1 och 26 timmar (PT26H). Den här egenskapen används när `cleanupPreference` har angetts till **OnExpiration**. Mer information finns i Rensa [resurser för distributionsskript.](#clean-up-deployment-script-resources)

### <a name="additional-samples"></a>Ytterligare exempel

- [Exempel 1:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)Skapa ett nyckelvalv och använd distributionsskript för att tilldela ett certifikat till nyckelvalvet.
- [Exempel 2:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json)Skapa en resursgrupp på prenumerationsnivå, skapa ett nyckelvalv i resursgruppen och använd sedan distributionsskriptet för att tilldela ett certifikat till nyckelvalvet.
- [Exempel 3:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)skapa en användar tilldelad hanterad identitet, tilldela deltagarrollen till identiteten på resursgruppsnivå, skapa ett nyckelvalv och använd sedan distributionsskript för att tilldela ett certifikat till nyckelvalvet.

## <a name="use-inline-scripts"></a>Använda infogade skript

Följande mall har en resurs som definierats med `Microsoft.Resources/deploymentScripts` typen . Den markerade delen är det infogade skriptet.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Eftersom de infogade distributionsskripten omges av dubbla citattecken måste strängarna i distributionsskripten vara bundna med ett omslutet snedstreck (**&#92;**) eller omges av enkla citattecken. Du kan också överväga att använda strängersättning som det visas i föregående JSON-exempel.

Skriptet tar en parameter och matar ut parametervärdet. `DeploymentScriptOutputs` används för att lagra utdata. I avsnittet outputs (utdata) `value` visar raden hur du kommer åt de lagrade värdena. `Write-Output` används för felsökning. Information om hur du kommer åt utdatafilen finns i [Övervaka och felsöka distributionsskript.](#monitor-and-troubleshoot-deployment-scripts) Egenskapsbeskrivningarna finns i [Exempelmallar](#sample-templates).

Om du vill köra skriptet väljer **du Prova** att öppna Cloud Shell och klistrar sedan in följande kod i gränssnittsfönstret.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

Utdata ser ut så här:

![Resource Manager hello world-utdata för malldistributionsskript](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Använda externa skript

Förutom infogade skript kan du även använda externa skriptfiler. Endast primära PowerShell-skript med _filtillägget ps1_ stöds. För CLI-skript kan de primära skripten ha alla tillägg (eller utan ett tillägg), så länge skripten är giltiga bash-skript. Om du vill använda externa skriptfiler ersätter du `scriptContent` med `primaryScriptUri` . Exempel:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Mer information finns i [exempelmallen](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

De externa skriptfilerna måste vara tillgängliga. Om du vill skydda skriptfiler som lagras i Azure Storage-konton genererar du en SAS-token och inkluderar den i mallens URI. Ange förfallotiden så att det finns tillräckligt med tid för att slutföra distributionen. Mer information finns i Distribuera [en privat ARM-mall med SAS-token](./secure-template-with-sas-token.md).

Du ansvarar för att säkerställa integriteten för skripten som distributionsskriptet refererar till, antingen `primaryScriptUri` eller `supportingScriptUris` . Referera endast till skript som du litar på.

## <a name="use-supporting-scripts"></a>Använda stödskript

Du kan separera komplicerad logik i en eller flera stödskriptfiler. Med `supportingScriptUris` egenskapen kan du ange en matris med URI:er till stödskriptfilerna om det behövs:

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

Stödskriptfiler kan anropas från både infogade skript och primära skriptfiler. Stödskriptfiler har inga begränsningar för filnamnstillägget.

Stödfilerna kopieras `azscripts/azscriptinput` till vid körningen. Använd relativ sökväg för att referera till stödfilerna från infogade skript och primära skriptfiler.

## <a name="work-with-outputs-from-powershell-script"></a>Arbeta med utdata från PowerShell-skript

Följande mall visar hur du skickar värden mellan två `deploymentScripts` resurser:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

I den första resursen definierar du en variabel med namnet `$DeploymentScriptOutputs` och använder den för att lagra utdatavärdena. Om du vill komma åt utdatavärdet från en annan resurs i mallen använder du:

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>Arbeta med utdata från CLI-skript

Till skillnad från PowerShell-distributionsskriptet exponerar inte CLI/bash-stöd en gemensam variabel för att lagra skriptutdata. I stället finns det en miljövariabel med namnet som lagrar den plats där skriptets utdatafil `AZ_SCRIPTS_OUTPUT_PATH` finns. Om ett distributionsskript körs från en Resource Manager mall anges den här miljövariabeln automatiskt åt dig av Bash-gränssnittet. Värdet för `AZ_SCRIPTS_OUTPUT_PATH` är */mnt/azscripts/azscriptoutput/scriptoutputs.jspå*.

Utdata från distributionsskriptet måste sparas på `AZ_SCRIPTS_OUTPUT_PATH` platsen och utdata måste vara ett giltigt JSON-strängobjekt. Innehållet i filen måste sparas som ett nyckel/värde-par. Till exempel lagras en matris med strängar som `{ "MyResult": [ "foo", "bar"] }` .  Att bara lagra matrisresultat, till exempel `[ "foo", "bar" ]` , är ogiltigt.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) används i föregående exempel. Den levereras med containeravbildningarna. Se [Konfigurera utvecklingsmiljön.](#configure-development-environment)

## <a name="use-existing-storage-account"></a>Använda ett befintligt lagringskonto

Ett lagringskonto och en containerinstans krävs för skriptkörning och felsökning. Du har möjlighet att ange ett befintligt lagringskonto, annars skapas lagringskontot tillsammans med containerinstansen automatiskt av skripttjänsten. Kraven för att använda ett befintligt lagringskonto:

- Typer av lagringskonto som stöds är:

    | SKU             | Typ som stöds     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Storage, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Storage, StorageV2 |
    | Standard_RAGRS  | Storage, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Dessa kombinationer stöder filresurser. Mer information finns i Skapa [en Azure-filresurs och](../../storage/files/storage-how-to-create-file-share.md) [Typer av lagringskonton.](../../storage/common/storage-account-overview.md)

- Brandväggsregler för lagringskonto stöds inte ännu. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../../storage/common/storage-network-security.md).
- Distributionshuvudnamn måste ha behörighet att hantera lagringskontot, vilket innefattar filresurser för att läsa, skapa och ta bort.

Om du vill ange ett befintligt lagringskonto lägger du till följande JSON i egenskapselementet i `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: ange namnet på lagringskontot.
- `storageAccountKey`: ange en av lagringskontonycklarna. Du kan använda funktionen [listKeys()](./template-functions-resource.md#listkeys) för att hämta nyckeln. Exempel:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Se [Exempelmallar](#sample-templates) för ett fullständigt `Microsoft.Resources/deploymentScripts` definitionsexempel.

När ett befintligt lagringskonto används skapar skripttjänsten en filresurs med ett unikt namn. Se [Rensa resurser för distributionsskript](#clean-up-deployment-script-resources) för hur skripttjänsten rensar filresursen.

## <a name="develop-deployment-scripts"></a>Utveckla distributionsskript

### <a name="handle-non-terminating-errors"></a>Hantera icke-avslutande fel

Du kan styra hur PowerShell svarar på icke-avslutande fel med hjälp av `$ErrorActionPreference` variabeln i distributionsskriptet. Om variabeln inte har angetts i distributionsskriptet använder skripttjänsten standardvärdet **Fortsätt.**

Skripttjänsten anger resursetableringstillståndet **till Misslyckad** när skriptet påträffar ett fel trots inställningen `$ErrorActionPreference` .

### <a name="use-environment-variables"></a>Använda miljövariabler

Distributionsskriptet använder dessa miljövariabler:

|Miljövariabel|Standardvärde|Systemreserverade|
|--------------------|-------------|---------------|
|AZ_SCRIPTS_AZURE_ENVIRONMENT|AzureCloud|N|
|AZ_SCRIPTS_CLEANUP_PREFERENCE|OnExpiration|N|
|AZ_SCRIPTS_OUTPUT_PATH|<AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY>/<AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME>|Y|
|AZ_SCRIPTS_PATH_INPUT_DIRECTORY|/mnt/azscripts/azscriptinput|Y|
|AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY|/mnt/azscripts/azscriptoutput|Y|
|AZ_SCRIPTS_PATH_USER_SCRIPT_FILE_NAME|Azure PowerShell: userscript.ps1; Azure CLI: userscript.sh|Y|
|AZ_SCRIPTS_PATH_PRIMARY_SCRIPT_URI_FILE_NAME|primaryscripturi.config|Y|
|AZ_SCRIPTS_PATH_SUPPORTING_SCRIPT_URI_FILE_NAME|supportingscripturi.config|Y|
|AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME|scriptoutputs.jspå|Y|
|AZ_SCRIPTS_PATH_EXECUTION_RESULTS_FILE_NAME|executionresult.jspå|Y|
|AZ_SCRIPTS_USER_ASSIGNED_IDENTITY|/subscriptions/|N|

Mer information om hur du använder `AZ_SCRIPTS_OUTPUT_PATH` finns i Arbeta med [utdata från CLI-skript.](#work-with-outputs-from-cli-script)

### <a name="pass-secured-strings-to-deployment-script"></a>Skicka skyddade strängar till distributionsskriptet

Genom att ange miljövariabler (EnvironmentVariable) i dina containerinstanser kan du tillhandahålla dynamisk konfiguration av programmet eller skriptet som körs av containern. Distributionsskriptet hanterar icke-skyddade och skyddade miljövariabler på samma sätt som Azure Container Instance. Mer information finns i [Ange miljövariabler i containerinstanser](../../container-instances/container-instances-environment-variables.md#secure-values). Ett exempel finns i [Exempelmallar](#sample-templates).

Den maximala tillåtna storleken för miljövariabler är 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Övervaka och felsöka distributionsskript

Skripttjänsten skapar ett [lagringskonto](../../storage/common/storage-account-overview.md) (om du inte anger ett befintligt lagringskonto) och en [containerinstans för](../../container-instances/container-instances-overview.md) skriptkörning. Om dessa resurser skapas automatiskt av skripttjänsten har båda resurserna `azscripts` suffixet i resursnamnen.

![Resource Manager resursnamn för malldistributionsskript](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Användarskriptet, körningsresultaten och stdout-filen lagras i filresurser för lagringskontot. Det finns en mapp med namnet `azscripts` . I mappen finns det ytterligare två mappar för indata- och utdatafilerna: `azscriptinput` och `azscriptoutput` .

Utdatamappen innehåller _enexecutionresult.jspå_ och skriptets utdatafil. Du kan se felmeddelandet för skriptkörning i _executionresult.jspå_. Utdatafilen skapas bara när skriptet har körts. Indatamappen innehåller en PowerShell-systemskriptfil och skriptfilerna för användardistribution. Du kan ersätta skriptfilen för användardistribution med en reviderad skriptfil och köra distributionsskriptet igen från Azure-containerinstansen.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

När du har distribuerat en distributionsskriptresurs visas resursen under resursgruppen i Azure Portal. Följande skärmbild visar sidan Översikt **för** en distributionsskriptresurs:

![Resource Manager översikt över malldistributionsskriptportalen](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

På översiktssidan visas viktig information om resursen, till exempel **Etableringstillstånd,** **Lagringskonto,** **Containerinstans** och **Loggar**.

På den vänstra menyn kan du visa innehållet i distributionsskriptet, argumenten som skickas till skriptet och utdata. Du kan också exportera en mall för distributionsskriptet, inklusive distributionsskriptet.

### <a name="use-powershell"></a>Använd PowerShell

Med Azure PowerShell kan du hantera distributionsskript i prenumerations- eller resursgruppsomfånget:

- [Get-AzDeploymentScript:](/powershell/module/az.resources/get-azdeploymentscript)Hämtar eller visar distributionsskript.
- [Get-AzDeploymentScriptLog:](/powershell/module/az.resources/get-azdeploymentscriptlog)Hämtar loggen för en körning av ett distributionsskript.
- [Remove-AzDeploymentScript:](/powershell/module/az.resources/remove-azdeploymentscript)Tar bort ett distributionsskript och dess associerade resurser.
- [Save-AzDeploymentScriptLog:](/powershell/module/az.resources/save-azdeploymentscriptlog)Sparar loggen för en körning av distributionsskript till disk.

Utdata `Get-AzDeploymentScript` ser ut ungefär så här:

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

Med Azure CLI kan du hantera distributionsskript i prenumerations- eller resursgruppsomfånget:

- [az deployment-scripts delete:](/cli/azure/deployment-scripts#az_deployment_scripts_delete)Ta bort ett distributionsskript.
- [az deployment-scripts list:](/cli/azure/deployment-scripts#az_deployment_scripts_list)Lista alla distributionsskript.
- [az deployment-scripts show:](/cli/azure/deployment-scripts#az_deployment_scripts_show)Hämta ett distributionsskript.
- [az deployment-scripts show-log:](/cli/azure/deployment-scripts#az_deployment_scripts_show_log)Visa loggar för distributionsskript.

Listkommandoutdata liknar följande:

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

### <a name="use-rest-api"></a>Använda REST API

Du kan hämta distributionsskriptresursdistributionsinformation på resursgruppsnivå och prenumerationsnivå med hjälp av REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

I följande exempel används [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
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
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

Det fungerar bara innan distributionsskriptresurserna tas bort.

Om du vill se resursen deploymentScripts i portalen väljer du **Visa dolda typer:**

![Resource Manager skript för malldistribution, visa dolda typer, portal](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Rensa resurser för distributionsskript

Ett lagringskonto och en containerinstans krävs för skriptkörning och felsökning. Du kan ange ett befintligt lagringskonto, annars skapas ett lagringskonto tillsammans med en containerinstans automatiskt av skripttjänsten. De två automatiskt skapade resurserna tas bort av skripttjänsten när körningen av distributionsskriptet hamnar i ett terminaltillstånd. Du debiteras för resurserna tills resurserna tas bort. Prisinformationen finns i Container Instances [och](https://azure.microsoft.com/pricing/details/container-instances/) [Azure Storage priser.](https://azure.microsoft.com/pricing/details/storage/)

Livscykeln för dessa resurser styrs av följande egenskaper i mallen:

- `cleanupPreference`: Rensa inställningar när skriptkörningen hamnar i ett terminaltillstånd. Värdena som stöds är:

  - **Alltid:** Ta bort de automatiskt skapade resurserna när skriptkörningen hamnar i ett terminaltillstånd. Om ett befintligt lagringskonto används tar skripttjänsten bort filresursen som skapades i lagringskontot. Eftersom resursen fortfarande finns kvar när resurserna har rensats beständiga skripttjänsten skriptkörningsresultaten, till exempel stdout, utdata och returvärde innan resurserna `deploymentScripts` tas bort.
  - **OnSuccess:** Ta bara bort de automatiskt skapade resurserna när skriptkörningen lyckas. Om ett befintligt lagringskonto används tar skripttjänsten bort filresursen endast när skriptkörningen lyckas. Du kan fortfarande komma åt resurserna för att hitta felsökningsinformationen.
  - **OnExpiration:** Ta bara bort de automatiskt skapade resurserna när `retentionInterval` inställningen har upphört att gälla. Om ett befintligt lagringskonto används tar skripttjänsten bort filresursen, men behåller lagringskontot.

- `retentionInterval`: Ange det tidsintervall som en skriptresurs ska behållas och efter vilket den ska upphöra att gälla och tas bort.

> [!NOTE]
> Vi rekommenderar inte att du använder lagringskontot och containerinstansen som genereras av skripttjänsten för andra ändamål. De två resurserna kan tas bort beroende på skriptets livscykel.

Containerinstansen och lagringskontot tas bort enligt `cleanupPreference` . Men om skriptet misslyckas och inte `cleanupPreference` är inställt på **Alltid** håller distributionsprocessen automatiskt containern igång i en timme. Du kan använda den här timmen för att felsöka skriptet. Om du vill att containern ska fortsätta att köras efter lyckade distributioner lägger du till ett vilolägessteg i skriptet. Lägg till exempel [till Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) i slutet av skriptet. Om du inte lägger till strömsparlägessteget är containern inställd på ett terminaltillstånd och kan inte nås även om den inte har tagits bort ännu.

## <a name="run-script-more-than-once"></a>Köra skript mer än en gång

Körning av distributionsskript är en idempotent åtgärd. Om ingen av resursegenskaperna (inklusive det infogade skriptet) ändras körs inte skriptet `deploymentScripts` när du distribuerar om mallen. Tjänsten för distributionsskript jämför resursnamnen i mallen med de befintliga resurserna i samma resursgrupp. Det finns två alternativ om du vill köra samma distributionsskript flera gånger:

- Ändra namnet på `deploymentScripts` resursen. Använd till exempel [mallfunktionen utcNow](./template-functions-date.md#utcnow) som resursnamn eller som en del av resursnamnet. När du ändrar resursnamnet skapas en ny `deploymentScripts` resurs. Det är bra för att behålla en historik över skriptkörning.

    > [!NOTE]
    > Funktionen `utcNow` kan bara användas i standardvärdet för en parameter.

- Ange ett annat värde i `forceUpdateTag` mallegenskapen. Använd till exempel `utcNow` som värde.

> [!NOTE]
> Skriv de distributionsskript som är idempotenta. Detta säkerställer att om de körs igen av misstag kommer det inte att orsaka systemändringar. Om distributionsskriptet till exempel används för att skapa en Azure-resurs kontrollerar du att resursen inte finns innan du skapar den, så att skriptet lyckas eller så skapar du inte resursen igen.

## <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

Du kan använda en förkonfigurerad containeravbildning som utvecklingsmiljö för distributionsskriptet. Mer information finns i [Konfigurera utvecklingsmiljön för distributionsskript i mallar.](./deployment-script-template-configure-dev.md)

När skriptet har testats kan du använda det som ett distributionsskript i dina mallar.

## <a name="deployment-script-error-codes"></a>Felkoder för distributionsskript

| Felkod | Description |
|------------|-------------|
| DeploymentScriptInvalidOperation | Resursdefinitionen för distributionsskriptet i mallen innehåller ogiltiga egenskapsnamn. |
| DeploymentScriptResourceConflict | Det går inte att ta bort en resurs för distributionsskript som är i ett icke-terminaltillstånd och körningen inte har överskridit 1 timme. Eller så går det inte att köra samma distributionsskript igen med samma resursidentifierare (samma prenumeration, resursgruppnamn och resursnamn) utan annat innehåll i skripttexten på samma gång. |
| DeploymentScriptOperationFailed | Distributionsskriptet misslyckades internt. Kontakta Microsofts support. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | Åtkomstnyckeln har inte angetts för det befintliga lagringskontot.|
| DeploymentScriptContainerGroupContainsInvalidContainers | En containergrupp som skapades av distributionens skripttjänst ändrades externt och ogiltiga containrar lades till. |
| DeploymentScriptContainerGroupInNonterminalState | Två eller flera distributionsskriptresurser använder samma Azure-containerinstansnamn i samma resursgrupp, och en av dem har inte slutfört körningen ännu. |
| DeploymentScriptStorageAccountInvalidKind | Det befintliga lagringskontot för typen BlobBlobStorage eller BlobStorage stöder inte filresurser och kan inte användas. |
| DeploymentScriptStorageAccountInvalidKindAndSku | Det befintliga lagringskontot stöder inte filresurser. En lista över typer av lagringskonto som stöds finns i [Använda befintligt lagringskonto.](#use-existing-storage-account) |
| DeploymentScriptStorageAccountNotFound | Lagringskontot finns inte eller har tagits bort av en extern process eller ett externt verktyg. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | Det angivna lagringskontot har en tjänstslutpunkt. Ett lagringskonto med en tjänstslutpunkt stöds inte. |
| DeploymentScriptStorageAccountInvalidAccessKey | Ogiltig åtkomstnyckel har angetts för det befintliga lagringskontot. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Ogiltigt lagringskontonyckelformat. Se [Hantera åtkomstnycklar för lagringskonto.](../../storage/common/storage-account-keys-manage.md) |
| DeploymentScriptExceededMaxAllowedTime | Körningstiden för distributionsskriptet överskred det tidsgränsvärde som anges i resursdefinitionen för distributionsskriptet. |
| DeploymentScriptInvalidOutputs | Utdata från distributionsskriptet är inte ett giltigt JSON-objekt. |
| DeploymentScriptContainerInstancesServiceLoginFailure | Den användar tilldelade hanterade identiteten kunde inte logga in efter 10 försök med ett intervall på 1 minut. |
| DeploymentScriptContainerGroupNotFound | En containergrupp som skapats av skripttjänsten för distribution har tagits bort av ett externt verktyg eller en extern process. |
| DeploymentScriptDownloadFailure | Det gick inte att ladda ned ett stödskript. Se [Använda stödskript.](#use-supporting-scripts)|
| DeploymentScriptError | Användarskriptet får ett felmeddelande. |
| DeploymentScriptBootstrapScriptExecutionFailed | Bootstrap-skriptet utfelar ett fel. Bootstrap-skriptet är det systemskript som dirigerar körningen av distributionsskriptet. |
| DeploymentScriptExecutionFailed | Okänt fel under körningen av distributionsskriptet. |
| DeploymentScriptContainerInstancesServiceUnavailable | När du skapar Azure Container Instance (ACI) utbar ACI ett fel om att tjänsten inte är tillgänglig. |
| DeploymentScriptContainerGroupInNonterminalState | När du skapar Azure Container Instance (ACI) använder ett annat distributionsskript samma ACI-namn i samma omfång (samma prenumeration, resursgruppsnamn och resursnamn). |
| DeploymentScriptContainerGroupNameInvalid | Det angivna Azure-containerinstansnamnet (ACI) uppfyller inte ACI-kraven. Se [Felsöka vanliga problem i Azure Container Instances](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder distributionsskript. Så här går du igenom självstudien om distributionsskript:

> [!div class="nextstepaction"]
> [Självstudie: Använda distributionsskript i Azure Resource Manager mallar](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Learn-modul: Utöka ARM-mallar med hjälp av distributionsskript](/learn/modules/extend-resource-manager-template-deployment-scripts/)
