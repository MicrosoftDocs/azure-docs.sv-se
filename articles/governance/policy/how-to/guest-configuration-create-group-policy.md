---
title: Så här skapar du princip definitioner för gäst konfiguration från grupprincip bas linje för Windows
description: Lär dig hur du konverterar grupprincip från säkerhets bas linjen för Windows Server 2019 till en princip definition.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 58fe4fa3e5056192fa5febe4883a1457d130871b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547776"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Så här skapar du princip definitioner för gäst konfiguration från grupprincip bas linje för Windows

Innan du skapar anpassade princip definitioner, är det en bra idé att läsa den konceptuella översikts informationen på [Azure policy gäst konfiguration](../concepts/guest-configuration.md). Information om hur du skapar anpassade princip definitioner för gäst konfiguration för Linux finns i [så här skapar du principer för gäst konfiguration för Linux](./guest-configuration-create-linux.md). Information om hur du skapar anpassade princip definitioner för gäst konfiguration för Windows finns i [så här skapar du principer för gäst konfiguration för Windows](./guest-configuration-create.md).

Vid Windows-granskning använder gästkonfigurationen en [DSC](/powershell/scripting/dsc/overview/overview)-resursmodul (Desired State Configuration) för att skapa konfigurations filen. DSC-konfigurationen definierar det tillstånd som datorn ska ha. Om utvärderingen av konfigurationen är **icke-kompatibel**utlöses *auditIfNotExists* princip påverkan.
[Azure policy endast gäst konfiguration](../concepts/guest-configuration.md) granskar inställningar i datorer.

> [!IMPORTANT]
> Anpassade princip definitioner med gäst konfiguration är en förhands gransknings funktion.
>
> Gästkonfigurationstillägget krävs för att utföra granskningar på virtuella Azure-datorer. Tilldela följande princip definitioner för att distribuera tillägget i skala över alla Windows-datorer:
> - [Distribuera krav för att aktivera principen för gäst konfiguration på virtuella Windows-datorer.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

DSC-communityn har publicerat [BaselineManagement-modulen](https://github.com/microsoft/BaselineManagement) för att konvertera exporterade grupprincip-mallar till DSC-format. Tillsammans med GuestConfiguration-cmdleten skapar BaselineManagement-modulen Azure Policy gäst konfigurations paket för Windows från grupprincip innehåll. Mer information om hur du använder BaselineManagement-modulen finns i artikeln [snabb start: konvertera Grupprincip till DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).

I den här guiden går vi igenom processen för att skapa ett Azure Policy gäst konfigurations paket från ett grupprincip objekt (GPO). När genom gången beskriver konverteringen av säkerhets bas linjen för Windows Server 2019 kan samma process tillämpas på andra grup princip objekt.  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>Hämta säkerhets bas linjen för Windows Server 2019 och installera relaterade PowerShell-moduler

Så här installerar du **DSC**, **GuestConfiguration**, **bas linje hantering**och relaterade Azure-moduler i PowerShell:

1. Kör följande kommando från en PowerShell-kommandotolk:

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. Skapa en katalog för och hämta säkerhets bas linjen för Windows Server 2019 från Windows Security Compliance Toolkit.

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. Avblockera och expandera den nedladdade Server 2019-bas linjen.

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. Verifiera Server 2019s bas linje innehåll med **MapGuidsToGpoNames.ps1**.

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>Konvertera från grupprincip till Azure Policy gäst konfiguration

Sedan konverterar vi den hämtade Server 2019-bas linjen till ett gäst konfigurations paket med hjälp av modulerna gäst konfiguration och bas linje hantering.

1. Konvertera grupprincip till önskad tillstånds konfiguration med modulen för bas linje hantering.

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. Byt namn på, formatera om och kör de konverterade skripten innan du skapar ett princip innehålls paket.

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Skapa ett innehålls paket för Azure Policy-gäst konfiguration.

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Skapa Azure Policy gäst konfiguration

Nästa steg är att publicera filen till Blob Storage. 

1. Skriptet nedan innehåller en funktion som du kan använda för att automatisera den här uppgiften. Observera att de kommandon som används i `publish` funktionen kräver `Az.Storage` modulen.

   ```azurepowershell-interactive
    function Publish-Configuration {
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
   ```

1. Skapa parametrar för att definiera den unika resurs gruppen, lagrings kontot och behållaren. 
   
   ```azurepowershell-interactive
    # Replace the $resourceGroup, $storageAccount, and $storageContainer values below.
    $resourceGroup = 'rfc_customguestconfig'
    $storageAccount = 'guestconfiguration'
    $storageContainer = 'content'
    $path = 'c:\git\policyfiles\Server2019Baseline\Server2019Baseline.zip'
    $blob = 'Server2019Baseline.zip' 
    ```

1. Använd funktionen Publish med de tilldelade parametrarna för att publicera gäst konfigurations paketet till den offentliga blob-lagringen.


   ```azurepowershell-interactive
   $PublishConfigurationSplat = @{
       resourceGroup = $resourceGroup
       storageAccountName = $storageAccount
       storageContainerName = $storageContainer
       filePath = $path
       blobName = $blob
       FullUri = $true
   }
   $uri = Publish-Configuration @PublishConfigurationSplat
    ```
1. När ett anpassat princip paket för gäst konfiguration har skapats och överförts skapar du princip definitionen för gäst konfiguration. Använd `New-GuestConfigurationPolicy` cmdleten för att skapa gäst konfigurationen.

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. Publicera princip definitionerna med hjälp av `Publish-GuestConfigurationPolicy` cmdleten. Cmdleten har bara **Sök vägs** parametern som pekar på platsen för de JSON-filer som skapas av `New-GuestConfigurationPolicy` . Om du vill köra kommandot Publicera måste du ha åtkomst till skapa princip definitioner i Azure. De särskilda kraven för auktorisering finns dokumenterade på sidan [Azure policy översikt](../overview.md#getting-started) . Den bästa inbyggda rollen är **resurs princip deltagare**.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>Tilldela en princip definition för gäst konfiguration

När den här principen har skapats i Azure är det sista steget att tilldela initiativet. Se hur du tilldelar initiativet till [portalen](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)och [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Princip definitioner för gäst konfiguration måste **alltid** tilldelas med det initiativ som kombinerar principerna _AuditIfNotExists_ och _DeployIfNotExists_ . Om endast _AuditIfNotExists_ -principen tilldelas distribueras kraven och principen visar alltid att "0"-servrar är kompatibla.

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

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att granska virtuella datorer med [gäst konfiguration](../concepts/guest-configuration.md).
- Lär dig att [program mässigt skapa principer](./programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](./get-compliance-data.md).
