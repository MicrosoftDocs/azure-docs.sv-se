---
title: Aktivera diagnostik i Azure Cloud Services (klassisk) med hjälp av PowerShell | Microsoft Docs
description: Lär dig hur du använder PowerShell för att aktivera insamling av diagnostikdata från en Azure-moln tjänst med Azure-diagnostik-tillägget.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 23528190d2eb60cc6ea3fe94bb9f7a2374526f6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102618448"
---
# <a name="enable-diagnostics-in-azure-cloud-services-classic-using-powershell"></a>Aktivera diagnostik i Azure Cloud Services (klassisk) med hjälp av PowerShell

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Du kan samla in diagnostikdata som program loggar, prestanda räknare osv. från en moln tjänst med hjälp av Azure-diagnostik tillägget. I den här artikeln beskrivs hur du aktiverar Azure-diagnostik-tillägget för en moln tjänst med hjälp av PowerShell.  Se [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/) för de krav som krävs för den här artikeln.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Aktivera diagnostiktillägget som en del av distributionen av en molntjänst
Den här metoden gäller för kontinuerlig integrations typ av scenarier där diagnostikprogrammet kan aktive ras som en del av distributionen av moln tjänsten. När du skapar en ny moln tjänst distribution kan du aktivera tillägget för diagnostik genom att skicka in parametern *ExtensionConfiguration* till cmdleten [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment) . Parametern *ExtensionConfiguration* tar en matris med diagnostiska konfigurationer som kan skapas med cmdleten [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure.service/new-azureservicediagnosticsextensionconfig) .

I följande exempel visas hur du kan aktivera diagnostik för en moln tjänst med en webrole-och WorkerRole, som var och en har en annan diagnostisk konfiguration.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Om diagnostikens konfigurations fil anger ett `StorageAccount` element med ett lagrings konto namn `New-AzureServiceDiagnosticsExtensionConfig` använder cmdleten automatiskt det lagrings kontot. För att detta ska fungera måste lagrings kontot finnas i samma prenumeration som moln tjänsten som distribueras.

Från Azure SDK 2,6 kommer det att finnas lagrings konto namnet baserat på den diagnostiska konfigurations sträng som anges i tjänst konfigurations filen (. cscfg). Skriptet nedan visar hur du kan parsa konfigurationsfilerna för tillägg från publicerings målets utdata och konfigurera diagnostikprogrammet för varje roll när du distribuerar moln tjänsten.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online använder en liknande metod för automatiserade distributioner av Cloud Services med tillägget Diagnostics. Se [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) för ett fullständigt exempel.

Om inget `StorageAccount` har angetts i diagnostik-konfigurationen måste du skicka in parametern *StorageAccountName* till cmdleten. Om parametern *StorageAccountName* anges använder cmdlet: en alltid det lagrings konto som anges i parametern och inte den som anges i konfigurations filen för diagnostik.

Om ditt lagrings konto för diagnostik finns i en annan prenumeration än moln tjänsten måste du uttryckligen skicka in parametrarna *StorageAccountName* och *StorageAccountKey* till cmdleten. *StorageAccountKey* -parametern behövs inte när ditt lagrings konto för diagnostik finns i samma prenumeration, eftersom cmdleten automatiskt kan fråga efter och ange nyckel värde när du aktiverar tillägget diagnostik. Men om kontot för diagnostik-diagnostik finns i en annan prenumeration kanske cmdleten inte kan hämta nyckeln automatiskt och du måste uttryckligen ange nyckeln via parametern *StorageAccountKey* .

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Aktivera diagnostiktillägg i en befintlig molntjänst
Du kan använda cmdleten [set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azureservicediagnosticsextension) för att aktivera eller uppdatera diagnostikinställningar på en moln tjänst som redan körs.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Hämta den aktuella konfigurationen för diagnostiktillägg
Använd cmdleten [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/get-azureservicediagnosticsextension) för att hämta den aktuella diagnostik-konfigurationen för en moln tjänst.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Ta bort diagnostiktillägg
Om du vill inaktivera diagnostik på en moln tjänst kan du använda cmdleten [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/remove-azureservicediagnosticsextension) .

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Om du har aktiverat tillägget för diagnostik med antingen *set-AzureServiceDiagnosticsExtension* eller *New-AzureServiceDiagnosticsExtensionConfig* utan *roll* parameter kan du ta bort tillägget med hjälp av *Remove-AzureServiceDiagnosticsExtension* utan *roll* parametern. Om *roll* parametern användes när du aktiverade tillägget måste den också användas när du tar bort tillägget.

Så här tar du bort diagnostiktillägget från varje enskild roll:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Nästa steg
* Ytterligare vägledning om hur du använder Azure Diagnostics och andra tekniker för att felsöka problem finns i [Aktivera diagnostik i Azure Cloud Services och Virtual Machines](cloud-services-dotnet-diagnostics.md).
* [Konfigurations schemat för diagnostik](../azure-monitor/agents/diagnostics-extension-schema-windows.md) förklarar olika alternativ för XML-konfigurationer för tillägget för diagnostik.
* Information om hur du aktiverar diagnostik-tillägget för Virtual Machines finns i [skapa en virtuell Windows-dator med övervakning och diagnostik med Azure Resource Manager mall](../virtual-machines/extensions/diagnostics-template.md)