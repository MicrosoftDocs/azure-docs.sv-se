---
title: Skicka klassiska Cloud Services mått till Azure Monitor mått databas
description: Beskriver processen för att skicka gäst operativ systemets prestanda mått för den klassiska Azure-Cloud Services till Azure Monitor Metric-lagret.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: 0ef9d8118a8ff1d9fdd69566dd60033f5847f810
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048972"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Skicka gäst operativ systemets mått till Azure Monitor mått lagrets klassiska Cloud Services 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Med Azure Monitor [Diagnostics-tillägget](../agents/diagnostics-extension-overview.md)kan du samla in mått och loggar från gäst operativ systemet (gäst operativ system) som körs som en del av en virtuell dator, moln tjänst eller Service Fabric kluster. Tillägget kan skicka telemetri till [flera olika platser.](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Den här artikeln beskriver processen för att skicka gäst operativ systemets prestanda mått för den klassiska Azure-Cloud Services till Azure Monitor Metric-lagret. Från och med diagnostik version 1,11 kan du skriva mått direkt till lagrings platsen Azure Monitor mått, där standard plattforms mått redan har samlats in. 

Genom att lagra dem på den här platsen kan du komma åt samma åtgärder som du kan använda för plattforms mått. Åtgärder är nästan real tids avisering, diagram, routning, åtkomst från en REST API med mera.  Tidigare skrev diagnostikprogrammet-tillägget till Azure Storage, men inte till Azure Monitor data lager.  

Processen som beskrivs i den här artikeln fungerar bara för prestanda räknare i Azure Cloud Services. Det fungerar inte för andra anpassade mått. 

## <a name="prerequisites"></a>Förutsättningar

- Du måste vara [tjänst administratör eller delad administratör](../../cost-management-billing/manage/add-change-subscription-administrator.md) på din Azure-prenumeration. 

- Din prenumeration måste vara registrerad med [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Du måste antingen ha [Azure PowerShell](/powershell/azure) eller [Azure Cloud Shell](../../cloud-shell/overview.md) installerat.

- Moln tjänsten måste vara i en [region som stöder anpassade mått](./metrics-custom-overview.md#supported-regions).

## <a name="provision-a-cloud-service-and-storage-account"></a>Etablera en moln tjänst och ett lagrings konto 

1. Skapa och distribuera en klassisk moln tjänst. Ett exempel på ett klassiskt Cloud Services-program och-distribution finns i [Kom igång med Azure Cloud Services och ASP.net](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Du kan använda ett befintligt lagrings konto eller distribuera ett nytt lagrings konto. Det är bäst om lagrings kontot finns i samma region som den klassiska moln tjänsten som du har skapat. I Azure Portal går du till resurs bladet **lagrings konton** och väljer sedan **nycklar**. Anteckna lagrings kontots namn och lagrings konto nyckeln. Du behöver den här informationen i senare steg.

   ![Lagringskontonycklar](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten 

Skapa ett huvud namn för tjänsten i din Azure Active Directory klient genom att följa anvisningarna på [Använd Portal för att skapa ett Azure Active Directory program och tjänstens huvud namn som kan komma åt resurser](../../active-directory/develop/howto-create-service-principal-portal.md). Tänk på följande när du går igenom den här processen: 

- Du kan ange valfri URL för inloggnings-URL: en.  
- Skapa ny klient hemlighet för den här appen.  
- Spara nyckeln och klient-ID: t för användning i senare steg.  

Ge appen som skapats i föregående steg *övervaknings mått utgivar* behörigheter till den resurs som du vill skapa mått för. Om du planerar att använda appen för att generera anpassade mått för många resurser kan du bevilja dessa behörigheter på resurs gruppen eller prenumerations nivån.  

> [!NOTE]
> Tillägget för diagnostik använder tjänstens huvud namn för att autentisera mot Azure Monitor och genererar mått för moln tjänsten.

## <a name="author-diagnostics-extension-configuration"></a>Redigera konfiguration av Diagnostics-tillägg 

Förbered konfigurations filen för Diagnostics Extension. Den här filen avgör vilka loggar och prestanda räknare som Diagnostics-tillägget ska samla in för din moln tjänst. Följande är ett exempel på en diagnostisk konfigurations fil:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

I avsnittet "SinksConfig" i diagnostik-filen definierar du en ny Azure Monitor mottagare: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

I avsnittet i konfigurations filen där du listar de prestanda räknare som ska samlas in lägger du till Azure Monitor-mottagaren. Den här posten säkerställer att alla prestanda räknare som du har angett dirigeras till Azure Monitor som mått. Du kan lägga till eller ta bort prestanda räknare efter dina behov. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Slutligen lägger du till ett *Azure Monitor konto* -avsnitt i den privata konfigurationen. Ange klient-ID och hemlighet för tjänstens huvud namn som du skapade tidigare. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Spara den här diagnostik-filen lokalt.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Distribuera tillägget för diagnostik till moln tjänsten 

Starta PowerShell och logga in på Azure. 

```powershell
Login-AzAccount 
```

Använd följande kommandon för att lagra information om det lagrings konto som du skapade tidigare. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

På samma sätt anger du filens diagnostiska sökväg till en variabel med hjälp av följande kommando:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Distribuera tillägget till din moln tjänst med diagnostik-filen med den Azure Monitor-mottagare som kon figurer ATS med följande kommando:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Det är fortfarande obligatoriskt att tillhandahålla ett lagrings konto som en del av installationen av tillägget för diagnostik. Loggar eller prestanda räknare som anges i konfigurations filen för diagnostik skrivs till det angivna lagrings kontot.  

## <a name="plot-metrics-in-the-azure-portal"></a>Rita mått i Azure Portal 

1. Gå till Azure-portalen. 

   ![Skärm bild som visar Azure Portal med övervakare och sedan mått som valts.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. På den vänstra menyn väljer du **övervaka.**

3. På bladet **övervakning** väljer du fliken **förhands granskning av mått** .

4. I den nedrullningsbara menyn resurser väljer du den klassiska moln tjänsten.

5. I den nedrullningsbara menyn namn områden väljer du **Azure. VM. Windows. gäst**. 

6. I den nedrullningsbara menyn mått väljer du **Minne\dedikerade byte som används**. 

Du kan använda funktionerna för dimensions filtrering och delning för att visa den totala mängden minne som används av en speciell roll eller roll instans. 

 ![Skärm bilden visar Mät data.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [anpassade mått](./metrics-custom-overview.md).