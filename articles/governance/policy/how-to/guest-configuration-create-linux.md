---
title: Skapa gästkonfigurationsprinciper för Linux
description: Lär dig hur du skapar en princip för Azure Policy gäst konfiguration för Linux.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 8bf01d8f69439f7b4d60fba76de0b7abf636c274
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547728"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Skapa gästkonfigurationsprinciper för Linux

Innan du skapar anpassade principer läser du översikts informationen på [Azure policy gäst konfiguration](../concepts/guest-configuration.md).
 
Information om hur du skapar principer för gäst konfiguration för Windows finns på sidan [så här skapar du principer för gäst konfiguration för Windows](./guest-configuration-create.md)

Vid Linux-granskning använder gästkonfiguration [Chef InSpec](https://www.inspec.io/). InSpec-profilen definierar det tillstånd som datorn ska ha. Om utvärderingen av konfigurationen Miss lyckas utlöses **auditIfNotExists** för princip inställningen och datorn betraktas som **icke-kompatibel**.

[Azure policy gäst konfiguration](../concepts/guest-configuration.md) kan bara användas för att granska inställningar i datorer. Reparationen av inställningar i datorer är inte tillgänglig ännu.

Använd följande åtgärder för att skapa en egen konfiguration för att verifiera tillstånd för en Azure-eller icke-Azure-dator.

> [!IMPORTANT]
> Anpassade principer med gäst konfiguration är en förhands gransknings funktion.
>
> Gästkonfigurationstillägget krävs för att utföra granskningar på virtuella Azure-datorer. Om du vill distribuera tillägget i skala över alla Linux-datorer tilldelar du följande princip definition:
> - [Distribuera krav för att aktivera principen för gäst konfiguration på virtuella Linux-datorer.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

## <a name="install-the-powershell-module"></a>Installera PowerShell-modulen

Modulen för gäst konfiguration automatiserar processen med att skapa anpassat innehåll, inklusive:

- Skapar en innehålls artefakt för gäst konfiguration (. zip)
- Automatiserad testning av artefakten
- Skapa en princip definition
- Publicera principen

Modulen kan installeras på en dator som kör Windows, macOS eller Linux med PowerShell 6,2 eller senare som körs lokalt, eller med [Azure Cloud Shell](https://shell.azure.com)eller med [Azure PowerShell Core Docker-avbildningen](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilering av konfigurationer stöds inte i Linux.

### <a name="base-requirements"></a>Grundläggande krav

Operativ system där modulen kan installeras:

- Linux
- macOS
- Windows

> [!NOTE]
> Cmdleten "test-GuestConfigurationPackage" kräver OpenSSL version 1,0, på grund av ett beroende på OMI. Detta orsakar ett fel i en miljö med OpenSSL 1,1 eller senare.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefakter och principer för gäst konfiguration för Linux

Till och med i Linux-miljöer använder gäst konfigurationen önskad tillstånds konfiguration som en språk abstraktion. Implementeringen är baserad i intern kod (C++) så att den inte kräver inläsning av PowerShell. Men det krävs en konfigurations-MOF som beskriver information om miljön.
DSC agerar som en omslutning för att standardisera hur den körs, hur parametrar anges och hur utdata returneras till tjänsten. Lite kunskap om DSC krävs när du arbetar med anpassad INSPEC-information.

#### <a name="configuration-requirements"></a>Konfigurations krav

Namnet på den anpassade konfigurationen måste vara konsekvent överallt. Namnet på. zip-filen för innehålls paketet, konfigurations namnet i MOF-filen och gäst tilldelnings namnet i Azure Resource Manager mall (ARM-mallen) måste vara samma.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Anpassad konfiguration av gäst konfiguration på Linux

Gäst konfiguration i Linux använder `ChefInSpecResource` resursen för att tillhandahålla motorn namnet på den [inspeca profilen](https://www.inspec.io/docs/reference/profiles/). **Name** är den enda obligatoriska resurs egenskapen. Skapa en YaML-fil och en ruby-skriptfil enligt beskrivningen nedan.

Börja med att skapa YaML-filen som används av INSPEC. Filen innehåller grundläggande information om miljön. Ett exempel anges nedan:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Spara filen med namnet i `inspec.yml` en mapp med namnet `linux-path` i din projekt katalog.

Skapa sedan ruby-filen med den inspeca-språkabstraktion som används för att granska datorn.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Spara den här filen med namnet `linux-path.rb` i en ny mapp som heter `controls` inuti `linux-path` katalogen.

Slutligen skapar du en konfiguration, importerar **PSDesiredStateConfiguration** -modulen och kompilerar konfigurationen.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

Spara filen med namnet `config.ps1` i projektmappen. Kör den i PowerShell genom att köra `./config.ps1` i terminalen. En ny MOF-fil kommer att skapas.

`Node AuditFilePathExists`Kommandot är inte tekniskt obligatoriskt, utan skapar en fil med namnet `AuditFilePathExists.mof` istället för standardvärdet `localhost.mof` . Med hjälp av MOF-filnamn följer du konfigurationen och gör det enkelt att ordna många filer när de körs i stor skala.

Nu bör du ha en projekt struktur enligt nedan:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

De stödfiler som krävs måste paketeras tillsammans. Det slutförda paketet används av gäst konfigurationen för att skapa Azure Policy-definitioner.

`New-GuestConfigurationPackage`Cmdleten skapar paketet. Parametrar för `New-GuestConfigurationPackage` cmdleten vid skapande av Linux-innehåll:

- **Namn**: namn på gäst konfigurations paket.
- **Konfiguration**: kompilerad fullständig sökväg till konfigurations dokument.
- **Sökväg**: sökväg till utmatnings katalog. Den här parametern är valfri. Om det inte anges skapas paketet i den aktuella katalogen.
- **ChefProfilePath**: fullständig sökväg till INSPEC-profil. Den här parametern stöds bara när du skapar innehåll för att granska Linux.

Kör följande kommando för att skapa ett paket med den konfiguration som angavs i föregående steg:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

När du har skapat konfigurations paketet, men innan du publicerar det till Azure, kan du testa paketet från din arbets Station eller CI/CD-miljö. GuestConfiguration-cmdleten `Test-GuestConfigurationPackage` innehåller samma agent i utvecklings miljön som används i Azure-datorer. Med den här lösningen kan du utföra integrerings testning lokalt innan du släpper till fakturerade moln miljöer.

Eftersom agenten faktiskt utvärderar den lokala miljön måste du, i de flesta fall, köra test-cmdlet på samma OS-plattform som du planerar att granska.

Parametrar för `Test-GuestConfigurationPackage` cmdleten:

- **Namn**: princip namn för gäst konfiguration.
- **Parameter**: princip parametrar har angetts i hash-format.
- **Sökväg**: fullständig sökväg till gäst konfigurations paketet.

Kör följande kommando för att testa paketet som skapades i föregående steg:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Cmdleten stöder även inmatade från PowerShell-pipeline. Skicka utdata från `New-GuestConfigurationPackage` cmdlet till `Test-GuestConfigurationPackage` cmdleten.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
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
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 `Publish-GuestConfigurationPolicy`Cmdleten accepterar sökvägen från PowerShell-pipeline. Den här funktionen innebär att du kan skapa principfiler och publicera dem i en enda uppsättning skickas-kommandon.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Använda parametrar i anpassade gäst konfigurations principer

Gäst konfiguration stöder åsidosättande egenskaper för en konfiguration vid körning. Den här funktionen innebär att värdena i MOF-filen i paketet inte måste betraktas som statiska. Värdena för åsidosättningar tillhandahålls via Azure Policy och påverkar inte hur konfigurationerna skapas eller kompileras.

Med INSPEC hanteras parametrar vanligt vis som inmatade antingen vid körning eller som kod med hjälp av attribut. Obfuscates den här processen kan anges i gäst konfigurationen när principen tilldelas. En attribut fil skapas automatiskt på datorn. Du behöver inte skapa och lägga till en fil i projektet. Det finns två steg för att lägga till parametrar till ditt Linux audit-projekt.

Definiera indata i ruby-filen där du skripterar vad som ska granskas på datorn. Ett exempel anges nedan.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Cmdletarna `New-GuestConfigurationPolicy` och `Test-GuestConfigurationPolicyPackage` innehåller en parameter med namnet **parameter**. Den här parametern tar en hash-mängd inklusive all information om varje parameter och skapar automatiskt alla nödvändiga avsnitt för de filer som används för att skapa varje Azure Policy definition.

I följande exempel skapas en princip definition för att granska en fil Sök väg där användaren anger sökvägen vid tidpunkten för princip tilldelningen.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

För Linux-principer inkluderar du egenskapen **AttributesYmlContent** i konfigurationen och skriver över värdena efter behov. Konfigurations agenten för gäster skapar automatiskt den YAML-fil som används av INSPEC för att lagra attribut. Se exemplet nedan.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>Princip livs cykel

Om du vill frigöra en uppdatering till princip definitionen finns det två fält som kräver åtgärder.

- **Version**: när du kör `New-GuestConfigurationPolicy` cmdleten måste du ange ett versions nummer som är större än det som för närvarande är publicerat. Egenskapen uppdaterar versionen av gäst konfigurations tilldelningen så att agenten identifierar det uppdaterade paketet.
- **contentHash**: den här egenskapen uppdateras automatiskt av `New-GuestConfigurationPolicy` cmdleten. Det är ett hash-värde för det paket som skapats av `New-GuestConfigurationPackage` . Egenskapen måste vara korrekt för den `.zip` fil som du publicerar. Om endast egenskapen **contentUri** uppdateras, accepterar inte tillägget innehålls paketet.

Det enklaste sättet att frigöra ett uppdaterat paket är att upprepa processen som beskrivs i den här artikeln och ange ett uppdaterat versions nummer. Den processen garanterar att alla egenskaper har uppdaterats korrekt.


### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrera principer för gäst konfiguration med Taggar

Principerna som skapats av cmdlets i modulen gäst konfiguration kan eventuellt innehålla ett filter för taggar. Parametern **-tag** i `New-GuestConfigurationPolicy` stöder en matris med hash som innehåller enskilda taggar. Taggarna läggs till i `If` avsnittet i princip definitionen och kan inte ändras av en princip tilldelning.

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
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>Valfritt: signering av gäst konfigurations paket

Anpassade principer för gäst konfiguration använder SHA256-hash för att verifiera att princip paketet inte har ändrats.
Kunder kan också använda ett certifikat för att signera paket och tvinga gäst konfigurations tillägget att bara tillåta signerat innehåll.

Det finns två steg som du måste utföra för att aktivera det här scenariot. Kör cmdleten för att signera innehålls paketet och Lägg till en tagg till de datorer som kräver att kod signeras.

Om du vill använda funktionen för signaturverifiering kör du `Protect-GuestConfigurationPackage` cmdleten för att signera paketet innan det publiceras. Denna cmdlet kräver ett certifikat för kod signering.

Parametrar för `Protect-GuestConfigurationPackage` cmdleten:

- **Sökväg**: fullständig sökväg till gäst konfigurations paketet.
- **PublicGpgKeyPath**: offentlig GPG-nyckel Sök väg. Den här parametern stöds bara när du signerar innehåll för Linux.

En referens för att skapa GPG-nycklar som ska användas med Linux-datorer finns i en artikel på GitHub, vilket [genererar en ny GPG-nyckel](https://help.github.com/en/articles/generating-a-new-gpg-key).

GuestConfiguration-agenten förväntar sig att certifikatets offentliga nyckel finns i sökvägen `/usr/local/share/ca-certificates/extra` på Linux-datorer. För noden för att verifiera signerat innehåll installerar du certifikatets offentliga nyckel på datorn innan du tillämpar den anpassade principen. Den här processen kan utföras med hjälp av valfri teknik i den virtuella datorn eller med hjälp av Azure Policy. [Här](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)finns en exempel mall.
Principen för Key Vault åtkomst måste tillåta att beräknings resurs leverantören får åtkomst till certifikat under distributioner. Detaljerade anvisningar finns i [konfigurera Key Vault för virtuella datorer i Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

När innehållet har publicerats lägger du till en tagg med namn `GuestConfigPolicyCertificateValidation` och värde `enabled` för alla virtuella datorer där kod signering ska krävas. Se [taggens exempel](../samples/built-in-policies.md#tags) för hur taggar kan levereras i skala med hjälp av Azure policy. När den här taggen är på plats kan princip definitionen som genereras med hjälp av cmdlet: en `New-GuestConfigurationPolicy` Aktivera kravet via gäst konfigurations tillägget.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Fel sökning av princip tilldelningar för gäst konfiguration (för hands version)

Ett verktyg är tillgängligt i för hands versionen för att hjälpa till med fel sökning Azure Policy gäst konfigurations tilldelningar. Verktyget är i för hands version och har publicerats till PowerShell-galleriet som Modulnamn [fel sökning av gäst konfiguration](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Mer information om cmdletarna i det här verktyget får du genom att använda kommandot Get-Help i PowerShell för att visa den inbyggda vägledningen. När verktyget uppdateras ofta är det bästa sättet att hämta den senaste informationen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att granska virtuella datorer med [gäst konfiguration](../concepts/guest-configuration.md).
- Lär dig att [program mässigt skapa principer](./programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](./get-compliance-data.md).
