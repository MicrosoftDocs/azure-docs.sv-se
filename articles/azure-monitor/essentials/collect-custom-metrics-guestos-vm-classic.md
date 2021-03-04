---
title: Skicka de klassiska Windows-DATORernas mått till Azure Monitor mått databasen
description: Skicka gäst operativ systemets mått till Azure Monitor data lager för en virtuell Windows-dator (klassisk)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: dcb2c0ceb3092bb6905ac1eb9fc58093da935f2f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048989"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Skicka gäst operativ systemets mått till Azure Monitor Metrics-databasen för en virtuell Windows-dator (klassisk)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Med Azure Monitor [Diagnostics-tillägget](../agents/diagnostics-extension-overview.md) (kallas "wad" eller "diagnostik") kan du samla in mått och loggar från gäst operativ systemet (gäst operativ system) som körs som en del av en virtuell dator, moln tjänst eller Service Fabric kluster. Tillägget kan skicka telemetri till [flera olika platser.](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

I den här artikeln beskrivs processen för att skicka gäst operativ systemets prestanda mått för en virtuell Windows-dator (klassisk) till Azure Monitor Metric-databasen. Från och med diagnostik version 1,11 kan du skriva mått direkt till lagrings platsen Azure Monitor mått, där standard plattforms mått redan har samlats in. 

Genom att lagra dem på den här platsen kan du komma åt samma åtgärder som du gör för plattforms mått. Åtgärder är nästan real tids avisering, diagram, routning, åtkomst från en REST API med mera. Tidigare skrev diagnostikprogrammet-tillägget till Azure Storage, men inte till Azure Monitor data lager. 

Processen som beskrivs i den här artikeln fungerar bara på klassiska virtuella datorer som kör Windows operativ system.

## <a name="prerequisites"></a>Förutsättningar

- Du måste vara [tjänst administratör eller delad administratör](../../cost-management-billing/manage/add-change-subscription-administrator.md) på din Azure-prenumeration. 

- Din prenumeration måste vara registrerad med [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Du måste antingen ha [Azure PowerShell](/powershell/azure) eller [Azure Cloud Shell](../../cloud-shell/overview.md) installerat.

- Din VM-resurs måste finnas i en [region som stöder anpassade mått](./metrics-custom-overview.md#supported-regions).

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Skapa en klassisk virtuell dator och ett lagrings konto

1. Skapa en klassisk virtuell dator med hjälp av Azure Portal.
   ![Skapa en klassisk virtuell dator](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. När du skapar den här virtuella datorn väljer du alternativet för att skapa ett nytt klassiskt lagrings konto. Vi använder det här lagrings kontot i senare steg.

1. I Azure Portal går du till resurs bladet **lagrings konton** . Välj **nycklar** och anteckna lagrings kontots namn och lagrings konto nyckel. Du behöver den här informationen i senare steg.
   ![Lagrings åtkomst nycklar](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Skapa ett huvud namn för tjänsten i din Azure Active Directory klient genom att följa anvisningarna i [skapa ett huvud namn för tjänsten](../../active-directory/develop/howto-create-service-principal-portal.md). Tänk på följande när du går igenom den här processen: 
- Skapa ny klient hemlighet för den här appen.
- Spara nyckeln och klient-ID: t för användning i senare steg.

Ge den här appen "Monitoring Metrics Publisher" behörigheter till den resurs som du vill skapa mått för. Du kan använda en resurs grupp eller en hel prenumeration.  

> [!NOTE]
> Tillägget för diagnostik använder tjänstens huvud namn för att autentisera mot Azure Monitor och genererar mått för den klassiska virtuella datorn.

## <a name="author-diagnostics-extension-configuration"></a>Redigera konfiguration av Diagnostics-tillägg

1. Förbered konfigurations filen för Diagnostics Extension. Den här filen avgör vilka loggar och prestanda räknare som Diagnostics-tillägget ska samla in för den klassiska virtuella datorn. Här är ett exempel:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. I avsnittet "SinksConfig" i diagnostik-filen definierar du en ny Azure Monitor-mottagare enligt följande:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. I avsnittet i konfigurations filen där listan över prestanda räknare som ska samlas in visas dirigerar du prestanda räknarna till Azure Monitor Sink "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. I den privata konfigurationen definierar du Azure Monitor kontot. Lägg sedan till tjänstens huvud namns information som används för att generera mått.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Spara filen lokalt.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Distribuera tillägget för diagnostik till moln tjänsten

1. Starta PowerShell och logga in.

    ```powershell
    Login-AzAccount
    ```

1. Börja med att ställa in kontexten för den klassiska virtuella datorn.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Ange kontexten för det klassiska lagrings kontot som skapades med den virtuella datorn.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Ange filens diagnostiska sökväg till en variabel med hjälp av följande kommando:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Förbered uppdateringen för den klassiska virtuella datorn med den diagnostiska fil som har den Azure Monitor mottagaren konfigurerad.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Distribuera uppdateringen till den virtuella datorn genom att köra följande kommando:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Det är fortfarande obligatoriskt att ange ett lagrings konto som en del av installationen av tillägget för diagnostik. Loggar eller prestanda räknare som anges i konfigurations filen för diagnostik skrivs till det angivna lagrings kontot.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Rita måtten i Azure Portal

1.  Gå till Azure-portalen. 

1.  På den vänstra menyn väljer du **övervaka.**

1.  Välj **mått** på bladet **övervaka** .

    ![Navigera mått](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. I list menyn resurser väljer du den klassiska virtuella datorn.

1. I den nedrullningsbara menyn namn områden väljer du **Azure. VM. Windows. gäst**.

1. I den nedrullningsbara menyn mått väljer du **Minne\dedikerade byte som används**.
   ![Rita mått](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [anpassade mått](./metrics-custom-overview.md).