---
title: Använd Azure PowerShell för att aktivera diagnostik på en virtuell Windows-dator
description: Lär dig hur du använder PowerShell för att aktivera Azure-diagnostik på en virtuell dator som kör Windows
ms.topic: article
author: amjads1
ms.author: amjads
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.date: 12/15/2015
ms.openlocfilehash: e242a45e2c0f8247a1c23bf4d43a6e8b0a033d9b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560103"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Använd PowerShell för att aktivera Azure-diagnostik på en virtuell dator som kör Windows

Azure-diagnostik är funktionen i Azure som möjliggör insamling av diagnostikdata i ett distribuerat program. Du kan använda tillägget för diagnostik för att samla in diagnostikdata som program loggar eller prestanda räknare från en virtuell Azure-dator (VM) som kör Windows. 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Aktivera diagnostik-tillägget om du använder distributions modellen för Resource Manager
Du kan aktivera tillägget diagnostik när du skapar en virtuell Windows-dator genom Azure Resource Manager distributions modell genom att lägga till tilläggs konfigurationen i Resource Manager-mallen. Se [skapa en virtuell Windows-dator med övervakning och diagnostik med hjälp av Azure Resource Manager-mallen](diagnostics-template.md).

Om du vill aktivera diagnostik-tillägget på en befintlig virtuell dator som har skapats via distributions modellen för Resource Manager kan du använda cmdleten [set-AzVMDiagnosticsExtension](/powershell/module/az.compute/set-azvmdiagnosticsextension) PowerShell-cmdleten som visas nedan.

```azurepowershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path
```


*$diagnosticsconfig _path* är sökvägen till filen som innehåller diagnostik-konfigurationen i XML, enligt beskrivningen i [exemplet](#sample-diagnostics-configuration) nedan.  

Om diagnostikens konfigurations fil anger ett **StorageAccount** -element med ett lagrings konto namn, anger *set-AzVMDiagnosticsExtension* -skriptet automatiskt det diagnostiska tillägget för att skicka diagnostikdata till det lagrings kontot. För att detta ska fungera måste lagrings kontot finnas i samma prenumeration som den virtuella datorn.

Om ingen **StorageAccount** har angetts i diagnostik-konfigurationen måste du skicka in *StorageAccountName* -parametern till cmdleten. Om parametern *StorageAccountName* anges använder cmdlet: en alltid det lagrings konto som anges i parametern och inte den som anges i konfigurations filen för diagnostik.

Om ditt lagrings konto för diagnostik finns i en annan prenumeration än den virtuella datorn måste du uttryckligen skicka in parametrarna *StorageAccountName* och *StorageAccountKey* till cmdleten. *StorageAccountKey* -parametern behövs inte när ditt lagrings konto för diagnostik finns i samma prenumeration, eftersom cmdleten automatiskt kan fråga efter och ange nyckel värde när du aktiverar tillägget diagnostik. Men om kontot för diagnostik-diagnostik finns i en annan prenumeration kanske cmdleten inte kan hämta nyckeln automatiskt och du måste uttryckligen ange nyckeln via parametern *StorageAccountKey* .  

```azurepowershell
Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

När diagnostik-tillägget har Aktiver ATS på en virtuell dator kan du hämta de aktuella inställningarna med hjälp av cmdleten [Get-AzVmDiagnosticsExtension](/powershell/module/az.compute/get-azvmdiagnosticsextension) .

```azurepowershell
Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name
```

Cmdleten returnerar *PublicSettings*, som innehåller diagnostik-konfigurationen. Det finns två typer av konfiguration som stöds, WadCfg och xmlCfg. WadCfg är JSON-konfiguration och xmlCfg är XML-konfiguration i ett base64-kodat format. Om du vill läsa XML-filen måste du avkoda den.

```azurepowershell
$publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
Write-Host $xmlconfig
```

Cmdlet: en [Remove-AzVmDiagnosticsExtension](/powershell/module/az.compute/remove-azvmdiagnosticsextension) kan användas för att ta bort tillägget för diagnostik från den virtuella datorn.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Aktivera diagnostik-tillägget om du använder den klassiska distributions modellen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Du kan använda cmdleten [set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) för att aktivera ett diagnostiskt tillägg på en virtuell dator som du skapar via den klassiska distributions modellen. I följande exempel visas hur du skapar en ny virtuell dator via den klassiska distributions modellen med diagnostik-tillägget aktiverat.

```azurepowershell
$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM
```

Om du vill aktivera diagnostik-tillägget på en befintlig virtuell dator som har skapats via den klassiska distributions modellen använder du först cmdleten [Get-AzureVM](/powershell/module/servicemanagement/azure.service/get-azurevm) för att hämta VM-konfigurationen. Uppdatera sedan VM-konfigurationen så att den inkluderar tillägget för diagnostik med hjälp av cmdleten [set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) . Använd slutligen den uppdaterade konfigurationen på den virtuella datorn med hjälp av [Update-AzureVM](/powershell/module/servicemanagement/azure.service/update-azurevm).

```azurepowershell
$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
$VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM
```

## <a name="sample-diagnostics-configuration"></a>Exempel på diagnostisk konfiguration
Följande XML-kod kan användas för den offentliga diagnostik-konfigurationen med skripten ovan. I den här exempel konfigurationen överförs olika prestanda räknare till kontot för diagnostik-lagring, tillsammans med fel från program-, säkerhets-och system kanaler i händelse loggarna i Windows och eventuella fel från infrastruktur loggarna för diagnostik.

Konfigurationen måste uppdateras för att inkludera följande:

* Attributet *resourceID* för **mått** elementet måste uppdateras med resurs-ID: t för den virtuella datorn.
  
  * Resurs-ID: t kan skapas med följande mönster: "/Subscriptions/{*prenumerations-ID för prenumerationen med den virtuella datorn*}/resourceGroups/{*resourcegroup namn för den virtuella datorn*}/providers/Microsoft.Compute/virtualMachines/{*det virtuella dator namnet*}".
  * Om prenumerations-ID: t för prenumerationen där den virtuella datorn körs till exempel är **11111111-1111-1111-1111-111111111111** är resurs grupps namnet för resurs gruppen **MyResourceGroup** och namnet på den virtuella datorn är **MyWindowsVM**. värdet för *resourceID* skulle då vara:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Mer information om hur mått genereras baserat på konfiguration av prestanda räknare och mått finns i [Azure-diagnostik mått tabellen i Storage](diagnostics-template.md#wadmetrics-tables-in-storage).
* **StorageAccount** -elementet måste uppdateras med namnet på det diagnostiska lagrings kontot.
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du använder Azure-diagnostik funktioner och andra tekniker för att felsöka problem finns i [Aktivera diagnostik i Azure Cloud Services och Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Schemat för diagnostikdata](../../azure-monitor/agents/diagnostics-extension-versions.md) förklarar de olika alternativen för XML-konfigurationer för tillägget Diagnostics.