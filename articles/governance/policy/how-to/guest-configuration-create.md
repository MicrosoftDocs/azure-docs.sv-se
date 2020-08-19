---
title: Så här skapar du gästkonfigurationsprinciper för Windows
description: Lär dig hur du skapar en princip för Azure Policy gäst konfiguration för Windows.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 4ee0c9d1912338235e53eb287bfc86a14b75cc97
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547672"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Så här skapar du gästkonfigurationsprinciper för Windows

Innan du skapar anpassade princip definitioner, är det en bra idé att läsa den konceptuella översikts informationen på sidan [Azure policy gäst konfiguration](../concepts/guest-configuration.md).
 
Information om hur du skapar principer för gäst konfiguration för Linux finns på sidan [hur du skapar principer för gäst konfiguration för Linux](./guest-configuration-create-linux.md)

Vid Windows-granskning använder gästkonfigurationen en [DSC](/powershell/scripting/dsc/overview/overview)-resursmodul (Desired State Configuration) för att skapa konfigurations filen. DSC-konfigurationen definierar det tillstånd som datorn ska ha. Om utvärderingen av konfigurationen Miss lyckas utlöses **auditIfNotExists** för princip inställningen och datorn betraktas som **icke-kompatibel**.

[Azure policy gäst konfiguration](../concepts/guest-configuration.md) kan bara användas för att granska inställningar i datorer. Reparationen av inställningar i datorer är inte tillgänglig ännu.

Använd följande åtgärder för att skapa en egen konfiguration för att verifiera tillstånd för en Azure-eller icke-Azure-dator.

> [!IMPORTANT]
> Anpassade principer med gäst konfiguration är en förhands gransknings funktion.
>
> Gästkonfigurationstillägget krävs för att utföra granskningar på virtuella Azure-datorer.
> Tilldela följande princip definitioner för att distribuera tillägget i skala över alla Windows-datorer:
>   - [Distribuera krav för att aktivera principen för gäst konfiguration på virtuella Windows-datorer.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>Installera PowerShell-modulen

Modulen för gäst konfiguration automatiserar processen med att skapa anpassat innehåll, inklusive:

- Skapar en innehålls artefakt för gäst konfiguration (. zip)
- Automatiserad testning av artefakten
- Skapa en princip definition
- Publicera principen

Modulen kan installeras på en dator som kör Windows, macOS eller Linux med PowerShell 6,2 eller senare som körs lokalt, eller med [Azure Cloud Shell](https://shell.azure.com)eller med [Azure PowerShell Core Docker-avbildningen](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilering av konfigurationer stöds inte ännu i Linux.

### <a name="base-requirements"></a>Grundläggande krav

Operativ system där modulen kan installeras:

- Linux
- macOS
- Windows

Resurs modulen för gäst konfiguration kräver följande program vara:

- PowerShell 6,2 eller senare. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 eller högre. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/azure/install-az-ps).
  - Endast AZ-modulerna "AZ. Accounts" och "AZ. Resources" krävs.

### <a name="install-the-module"></a>Installera modulen

Så här installerar du **GuestConfiguration** -modulen i PowerShell:

1. Kör följande kommando från en PowerShell-kommandotolk:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Verifiera att modulen har importer ATS:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefakter och principer för gäst konfiguration för Windows

Gäst konfiguration använder PowerShell önskad tillstånds konfiguration som en språk abstraktion för att skriva vad som ska granskas i Windows. Agenten läser in en fristående instans av PowerShell 6,2, så det finns ingen konflikt med användningen av PowerShell DSC i Windows PowerShell 5,1, och det finns inget krav på att förinstallera PowerShell 6,2 eller senare.

En översikt över DSC-begrepp och terminologi finns i [Översikt över POWERSHELL DSC](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Hur moduler för gäst konfiguration skiljer sig från Windows PowerShell DSC-moduler

När gäst konfigurationen granskar en dator är sekvensen av händelser annorlunda än i Windows PowerShell DSC.

1. Agenten körs först `Test-TargetResource` för att avgöra om konfigurationen är i rätt tillstånd.
1. Det booleska värde som returneras av funktionen avgör om Azure Resource Managers status för gäst tilldelningen ska vara kompatibel/inte kompatibel.
1. Providern kör `Get-TargetResource` för att returnera det aktuella läget för varje inställning så att information är tillgänglig både om varför en dator inte är kompatibel och för att bekräfta att det aktuella läget är kompatibelt.

Parametrar i Azure Policy som överför värden till gäst konfigurations tilldelningar måste vara en _sträng_ typ. Det går inte att skicka matriser via parametrar, även om DSC-resursen stöder matriser.

### <a name="get-targetresource-requirements"></a>Get-TargetResource-krav

Funktionen `Get-TargetResource` har särskilda krav för gäst konfiguration som inte behövs för Windows önskad tillstånds konfiguration.

- Den hash-tabellen som returneras måste innehålla en egenskap med namnet **skäl**.
- Egenskapen orsaker måste vara en matris.
- Varje objekt i matrisen ska vara en hash med nycklar med namnet **kod** och **fras**.

Egenskapen orsaker används av tjänsten för att standardisera hur information presenteras när en dator är inkompatibel. Du kan tänka på varje objekt av anledningen som en "orsak" som resursen inte är kompatibel. Egenskapen är en matris eftersom en resurs inte kan vara kompatibel med fler än en orsak.

Egenskaps **koden** och **frasen** förväntas av tjänsten. När du redigerar en anpassad resurs ställer du in texten (vanligt vis STDOUT) som du vill visa som orsak till att resursen inte är kompatibel med **frasens**värde. **Koden** har särskilda krav på formatering så att rapporter tydligt kan visa information om resursen som används för att utföra granskningen. Den här lösningen gör att gäst konfigurationen är utöknings bar. Alla kommandon kan köras så länge utdata kan returneras som ett sträng värde för egenskapen **fras** .

- **Code** (sträng): namnet på resursen, upprepas och sedan ett kort namn utan blank steg som en identifierare för orsaken. Dessa tre värden ska vara kolon-avgränsade utan blank steg.
  - Ett exempel är `registry:registry:keynotpresent`
- **Fras** (sträng): läslig text som förklarar varför inställningen inte är kompatibel.
  - Ett exempel är `The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

Egenskapen orsaker måste läggas till i schema-MOF för resursen som en inbäddad klass.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Konfigurations krav

Namnet på den anpassade konfigurationen måste vara konsekvent överallt. Namnet på. zip-filen för innehålls paketet, konfigurations namnet i MOF-filen och gäst tilldelnings namnet i Azure Resource Manager mall (ARM-mallen) måste vara samma.

### <a name="scaffolding-a-guest-configuration-project"></a>Ramverk ett gäst konfigurations projekt

Utvecklare som vill påskynda processen med att komma igång och arbeta från exempel kod kan installera ett community-projekt med namnet **gäst konfigurations projekt**. Projektet installerar en mall för en [gips](https://github.com/powershell/plaster) -PowerShell-modul. Det här verktyget kan användas för att Autogenerera ett projekt, inklusive en fungerande konfiguration och exempel resurs, och en uppsättning [pester](https://github.com/pester/pester) -tester för att verifiera projektet. Mallen innehåller också uppgifts utlöpare för Visual Studio Code för att automatisera skapandet och valideringen av gäst konfigurations paketet. Mer information finns i projektet GitHub Project [Guest Configuration](https://github.com/microsoft/guestconfigurationproject).

Mer information om hur du arbetar med konfigurationer i allmänhet finns i [skriva, kompilera och tillämpa en konfiguration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Förväntat innehåll i en gäst konfigurations artefakt

Det slutförda paketet används av gäst konfigurationen för att skapa Azure Policy-definitioner. Paketet består av:

- Den kompilerade DSC-konfigurationen som en MOF
- Mappen moduler
  - GuestConfiguration-modul
  - DscNativeResources-modul
  - Aktivitets DSC-resurspooler som krävs av MOF-modulen

PowerShell-cmdletar hjälper dig att skapa paketet.
Ingen mapp-eller versions katalog för rot nivå krävs.
Paket formatet måste vara en. zip-fil.

### <a name="storing-guest-configuration-artifacts"></a>Lagring av gäst konfigurations artefakter

Zip-paketet måste lagras på en plats som de hanterade virtuella datorerna kan komma åt.
Exempel är GitHub-databaser, Azure-lagrings platsen eller Azure Storage. Om du inte vill att paketet ska vara offentligt kan du ta med en [SAS-token](../../../storage/common/storage-sas-overview.md) i URL: en. Du kan också implementera [tjänstens slut punkt](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) för datorer i ett privat nätverk, även om den här konfigurationen endast gäller för åtkomst till paketet och inte kommunicerar med tjänsten.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Steg för steg, skapa en anpassad princip för gäst konfigurations granskning för Windows

Skapa en DSC-konfiguration för att granska inställningar. Följande exempel på PowerShell-skript skapar en konfiguration med namnet **AuditBitLocker**, importerar modulen **PsDscResources** och använder `Service` resursen för att granska för en tjänst som körs. Konfigurations skriptet kan köras från en Windows-eller macOS-dator.

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

Spara filen med namnet `config.ps1` i projektmappen. Kör den i PowerShell genom att köra `./config.ps1` i terminalen. En ny MOF-fil skapas.

`Node AuditBitlocker`Kommandot är inte tekniskt obligatoriskt, utan skapar en fil med namnet `AuditBitlocker.mof` istället för standardvärdet `localhost.mof` . Med hjälp av MOF-filnamn följer du konfigurationen och gör det enkelt att ordna många filer när de körs i stor skala.

När MOF-filen kompileras måste de stödfiler paketeras tillsammans. Det slutförda paketet används av gäst konfigurationen för att skapa Azure Policy-definitioner.

`New-GuestConfigurationPackage`Cmdleten skapar paketet. Moduler som krävs av konfigurationen måste vara tillgängliga i `$Env:PSModulePath` . Parametrar för `New-GuestConfigurationPackage` cmdleten när Windows-innehåll skapas:

- **Namn**: namn på gäst konfigurations paket.
- **Konfiguration**: den fullständiga sökvägen till det kompilerade DSC-konfigurationsobjektet.
- **Sökväg**: sökväg till utmatnings katalog. Den här parametern är valfri. Om det inte anges skapas paketet i den aktuella katalogen.

Kör följande kommando för att skapa ett paket med den konfiguration som angavs i föregående steg:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

När du har skapat konfigurations paketet, men innan du publicerar det till Azure, kan du testa paketet från din arbets Station eller CI/CD-miljö. GuestConfiguration-cmdleten `Test-GuestConfigurationPackage` innehåller samma agent i utvecklings miljön som används i Azure-datorer. Med den här lösningen kan du utföra integrerings testning lokalt innan du släpper till fakturerade moln miljöer.

Eftersom agenten faktiskt utvärderar den lokala miljön måste du, i de flesta fall, köra test-cmdlet på samma OS-plattform som du planerar att granska. Testet använder bara moduler som ingår i innehålls paketet.

Parametrar för `Test-GuestConfigurationPackage` cmdleten:

- **Namn**: princip namn för gäst konfiguration.
- **Parameter**: princip parametrar har angetts i hash-format.
- **Sökväg**: fullständig sökväg till gäst konfigurations paketet.

Kör följande kommando för att testa paketet som skapades i föregående steg:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Cmdleten stöder även inmatade från PowerShell-pipeline. Skicka utdata från `New-GuestConfigurationPackage` cmdlet till `Test-GuestConfigurationPackage` cmdleten.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Nästa steg är att publicera filen till Blob Storage. Skriptet nedan innehåller en funktion som du kan använda för att automatisera den här uppgiften. Kommandona som används i `publish` funktionen kräver `Az.Storage` modulen.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

När ett anpassat princip paket för gäst konfiguration har skapats och överförts skapar du princip definitionen för gäst konfiguration. `New-GuestConfigurationPolicy`Cmdleten tar ett anpassat princip paket och skapar en princip definition.

Parametrar för `New-GuestConfigurationPolicy` cmdleten:

- **ContentUri**: offentlig http (s) URI för innehålls paketet för gäst konfiguration.
- **DisplayName**: principens visnings namn.
- **Beskrivning**: princip beskrivning.
- **Parameter**: princip parametrar har angetts i hash-format.
- **Version**: princip version.
- **Sökväg**: mål Sök väg där princip definitioner skapas.
- **Plattform**: mål plattform (Windows/Linux) för gäst konfigurations princip och innehålls paket.
- **Tag** lägger till ett eller flera märkes filter i princip definitionen
- **Kategori** anger fältet Kategori metadata i princip definitionen

I följande exempel skapas princip definitionerna i en angiven sökväg från ett anpassat princip paket:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

Följande filer skapas av `New-GuestConfigurationPolicy` :

- **auditIfNotExists.jspå**
- **deployIfNotExists.jspå**
- **Initiative.jspå**

Cmdlet-utdata returnerar ett objekt som innehåller initiativets visnings namn och sökväg.

Publicera sedan princip definitionerna med hjälp av `Publish-GuestConfigurationPolicy` cmdleten. Cmdleten har bara **Sök vägs** parametern som pekar på platsen för de JSON-filer som skapas av `New-GuestConfigurationPolicy` .

Om du vill köra kommandot Publicera måste du ha åtkomst till skapa principer i Azure. De särskilda kraven för auktorisering finns dokumenterade på sidan [Azure policy översikt](../overview.md) . Den bästa inbyggda rollen är **resurs princip deltagare**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

`Publish-GuestConfigurationPolicy`Cmdleten accepterar sökvägen från PowerShell-pipeline. Den här funktionen innebär att du kan skapa principfiler och publicera dem i en enda uppsättning skickas-kommandon.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

När den här principen har skapats i Azure är det sista steget att tilldela initiativet. Se hur du tilldelar initiativet till [portalen](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)och [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Principer för gäst konfiguration måste **alltid** tilldelas med det initiativ som kombinerar principerna _AuditIfNotExists_ och _DeployIfNotExists_ . Om endast _AuditIfNotExists_ -principen tilldelas distribueras kraven och principen visar alltid att "0"-servrar är kompatibla.

För att tilldela en princip definition med _DeployIfNotExists_ -effekter krävs ytterligare åtkomst nivå. Om du vill bevilja den lägsta behörigheten kan du skapa en anpassad roll definition som utökar **resurs princip deltagare**. Exemplet nedan skapar en roll med namnet **Resource policy CONTRIBUTOR DINE** med den ytterligare behörigheten _Microsoft. Authorization/roleAssignments/Write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrera principer för gäst konfiguration med Taggar

Princip definitionerna som skapas av cmdlets i modulen gäst konfiguration kan eventuellt innehålla ett filter för taggar. **Tag** -parametern för `New-GuestConfigurationPolicy` stöder en matris med hash som innehåller enskilda taggar. Taggarna läggs till i `If` avsnittet i princip definitionen och kan inte ändras av en princip tilldelning.

Ett exempel på en princip definition som filtrerar efter Taggar anges nedan.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Använda parametrar i anpassade princip definitioner för gäst konfiguration

Gäst konfiguration stöder åsidosättande egenskaper för en konfiguration vid körning. Den här funktionen innebär att värdena i MOF-filen i paketet inte måste betraktas som statiska. Värdena för åsidosättningar tillhandahålls via Azure Policy och påverkar inte hur konfigurationerna skapas eller kompileras.

Cmdletarna `New-GuestConfigurationPolicy` och `Test-GuestConfigurationPolicyPackage` innehåller en parameter med namnet **parameter**. Den här parametern tar en hash-definition inklusive all information om varje parameter och skapar de nödvändiga avsnitten för varje fil som används för Azure Policy definitionen.

I följande exempel skapas en princip definition för granskning av en tjänst där användaren väljer från en lista vid tidpunkten för princip tilldelningen.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>Utöka gäst konfigurationen med verktyg från tredje part

Artefakt paketen för gäst konfiguration kan utökas till att omfatta verktyg från tredje part.
Att utöka gäst konfigurationen kräver utveckling av två komponenter.

- En önskad tillstånds konfigurations resurs som hanterar all aktivitet som rör hantering av verktyget från tredje part
  - Installera
  - Anropa
  - Konvertera utdata
- Innehåll i rätt format för verktyget för att konsumeras internt

DSC-resursen kräver anpassad utveckling om det inte redan finns en community-lösning.
Community-lösningar kan identifieras genom att söka PowerShell-galleriet efter taggen [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22).

> [!Note]
> Gäst konfigurationens utöknings barhet är en "ta med din egen licens"-scenario. Se till att du uppfyller villkoren för alla verktyg från tredje part innan du använder.

När DSC-resursen har installerats i utvecklings miljön använder du **FilesToInclude** -parametern för `New-GuestConfigurationPackage` att ta med innehåll för plattformen från tredje part i innehålls artefakten.

### <a name="step-by-step-creating-a-content-artifact-that-uses-third-party-tools"></a>Steg för steg, skapa en innehålls artefakt som använder verktyg från tredje part

Endast `New-GuestConfigurationPackage` cmdleten kräver en ändring från steg-för-steg-vägledningen för artefakter för DSC-innehåll. I det här exemplet använder du `gcInSpec` modulen för att utöka gäst konfigurationen till att granska Windows-datorer med hjälp av INSPEC-plattformen snarare än den inbyggda modulen som används i Linux. Community-modulen underhålls som ett [projekt med öppen källkod i GitHub](https://github.com/microsoft/gcinspec).

Installera nödvändiga moduler i utvecklings miljön:

```azurepowershell-interactive
# Update PowerShellGet if needed to allow installing PreRelease versions of modules
Install-Module PowerShellGet -Force

# Install GuestConfiguration module prerelease version
Install-Module GuestConfiguration -allowprerelease

# Install commmunity supported gcInSpec module
Install-Module gcInSpec
```

Börja med att skapa YaML-filen som används av INSPEC. Filen innehåller grundläggande information om miljön. Ett exempel anges nedan:

```YaML
name: wmi_service
title: Verify WMI service is running
maintainer: Microsoft Corporation
summary: Validates that the Windows Service 'winmgmt' is running
copyright: Microsoft Corporation
license: MIT
version: 1.0.0
supports:
  - os-family: windows
```

Spara filen `wmi_service.yml` i en mapp med namnet `wmi_service` i projekt katalogen.

Skapa sedan ruby-filen med den inspeca-språkabstraktion som används för att granska datorn.

```Ruby
control 'wmi_service' do
  impact 1.0
  title 'Verify windows service: winmgmt'
  desc 'Validates that the service, is installed, enabled, and running'

  describe service('winmgmt') do
    it { should be_installed }
    it { should be_enabled }
    it { should be_running }
  end
end

```

Spara filen `wmi_service.rb` i en ny mapp som heter `controls` inuti `wmi_service` katalogen.

Slutligen skapar du en konfiguration, importerar **GuestConfiguration** Resource module och använder `gcInSpec` resursen för att ange namnet på den inspeca profilen.

```powershell
# Define the configuration and import GuestConfiguration
Configuration wmi_service
{
    Import-DSCResource -Module @{ModuleName = 'gcInSpec'; ModuleVersion = '2.1.0'}
    node 'wmi_service'
    {
        gcInSpec wmi_service
        {
            InSpecProfileName       = 'wmi_service'
            InSpecVersion           = '3.9.3'
            WindowsServerVersion    = '2016'
        }
    }
}

# Compile the configuration to create the MOF files
wmi_service -out ./Config
```

Nu bör du ha en projekt struktur enligt nedan:

```file
/ wmi_service
    / Config
        wmi_service.mof
    / wmi_service
        wmi_service.yml
        / controls
            wmi_service.rb 
```

De stödfiler som krävs måste paketeras tillsammans. Det slutförda paketet används av gäst konfigurationen för att skapa Azure Policy-definitioner.

`New-GuestConfigurationPackage`Cmdleten skapar paketet. För innehåll från tredje part använder du parametern **FilesToInclude** för att lägga till INSPEC-innehåll i paketet. Du behöver inte ange **ChefProfilePath** som för Linux-paket.

- **Namn**: namn på gäst konfigurations paket.
- **Konfiguration**: kompilerad fullständig sökväg till konfigurations dokument.
- **Sökväg**: sökväg till utmatnings katalog. Den här parametern är valfri. Om det inte anges skapas paketet i den aktuella katalogen.
- **FilesoInclude**: fullständig sökväg till INSPEC-profil.

Kör följande kommando för att skapa ett paket med den konfiguration som angavs i föregående steg:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'wmi_service' `
  -Configuration './Config/wmi_service.mof' `
  -FilesToInclude './wmi_service'  `
  -Path './package' 
```

## <a name="policy-lifecycle"></a>Princip livs cykel

Om du vill släppa en uppdatering av principen finns det två fält som kräver åtgärd.

- **Version**: när du kör `New-GuestConfigurationPolicy` cmdleten måste du ange ett versions nummer som är större än det som för närvarande är publicerat. Egenskapen uppdaterar versionen av gäst konfigurations tilldelningen så att agenten identifierar det uppdaterade paketet.
- **contentHash**: den här egenskapen uppdateras automatiskt av `New-GuestConfigurationPolicy` cmdleten. Det är ett hash-värde för det paket som skapats av `New-GuestConfigurationPackage` . Egenskapen måste vara korrekt för den `.zip` fil som du publicerar. Om endast egenskapen **contentUri** uppdateras, accepterar inte tillägget innehålls paketet.

Det enklaste sättet att frigöra ett uppdaterat paket är att upprepa processen som beskrivs i den här artikeln och ange ett uppdaterat versions nummer. Den processen garanterar att alla egenskaper har uppdaterats korrekt.

## <a name="optional-signing-guest-configuration-packages"></a>Valfritt: signering av gäst konfigurations paket

Anpassade principer för gäst konfiguration använder SHA256-hash för att verifiera att princip paketet inte har ändrats.
Kunder kan också använda ett certifikat för att signera paket och tvinga gäst konfigurations tillägget att bara tillåta signerat innehåll.

Det finns två steg som du måste utföra för att aktivera det här scenariot. Kör cmdleten för att signera innehålls paketet och Lägg till en tagg till de datorer som kräver att kod signeras.

Om du vill använda funktionen för signaturverifiering kör du `Protect-GuestConfigurationPackage` cmdleten för att signera paketet innan det publiceras. Denna cmdlet kräver ett certifikat för kod signering.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametrar för `Protect-GuestConfigurationPackage` cmdleten:

- **Sökväg**: fullständig sökväg till gäst konfigurations paketet.
- **Certifikat**: kod signerings certifikat för att signera paketet. Den här parametern stöds bara vid signering av innehåll för Windows.

GuestConfiguration-agenten förväntar sig att certifikatets offentliga nyckel finns i "betrodda rot certifikat utfärdare" på Windows-datorer och i sökvägen `/usr/local/share/ca-certificates/extra` på Linux-datorer. För noden för att verifiera signerat innehåll installerar du certifikatets offentliga nyckel på datorn innan du tillämpar den anpassade principen. Den här processen kan utföras med hjälp av valfri teknik i den virtuella datorn eller med hjälp av Azure Policy. [Här](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)finns en exempel mall.
Principen för Key Vault åtkomst måste tillåta att beräknings resurs leverantören får åtkomst till certifikat under distributioner. Detaljerade anvisningar finns i [konfigurera Key Vault för virtuella datorer i Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Följande är ett exempel för att exportera den offentliga nyckeln från ett signerings certifikat som ska importeras till datorn.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

När innehållet har publicerats lägger du till en tagg med namn `GuestConfigPolicyCertificateValidation` och värde `enabled` för alla virtuella datorer där kod signering ska krävas. Se [taggens exempel](../samples/built-in-policies.md#tags) för hur taggar kan levereras i skala med hjälp av Azure policy. När den här taggen är på plats kan princip definitionen som genereras med hjälp av cmdlet: en `New-GuestConfigurationPolicy` Aktivera kravet via gäst konfigurations tillägget.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Fel sökning av princip tilldelningar för gäst konfiguration (för hands version)

Ett verktyg är tillgängligt i för hands versionen för att hjälpa till med fel sökning Azure Policy gäst konfigurations tilldelningar. Verktyget är i för hands version och har publicerats till PowerShell-galleriet som Modulnamn [fel sökning av gäst konfiguration](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Mer information om cmdletarna i det här verktyget får du genom att använda kommandot Get-Help i PowerShell för att visa den inbyggda vägledningen. När verktyget uppdateras ofta är det bästa sättet att hämta den senaste informationen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att granska virtuella datorer med [gäst konfiguration](../concepts/guest-configuration.md).
- Lär dig att [program mässigt skapa principer](./programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](./get-compliance-data.md).
