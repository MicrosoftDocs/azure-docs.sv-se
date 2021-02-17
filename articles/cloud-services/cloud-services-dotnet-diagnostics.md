---
title: Så här använder du Azure Diagnostics (.NET) med Cloud Services (klassisk) | Microsoft Docs
description: Använd Azure Diagnostics för att samla in data från Azure Cloud Services för fel sökning, mätning av prestanda, övervakning, trafik analys med mera.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: e4b5372a2b6e9b833f3a0213d69649b75ae2589b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578180"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services-classic"></a>Aktivera Azure-diagnostik i Azure Cloud Services (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Se [Azure-diagnostik översikt](../azure-diagnostics.md) för en bakgrund på Azure-diagnostik.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Så här aktiverar du diagnostik i en arbets roll
Den här genom gången beskriver hur du implementerar en Azure Worker-roll som utvärderar telemetridata med hjälp av .NET EventSource-klassen. Azure-diagnostik används för att samla in telemetri data och lagra dem i ett Azure Storage-konto. När du skapar en arbets roll aktiverar Visual Studio automatiskt diagnostik 1,0 som en del av lösningen i Azure SDK: er för .NET 2,4 och tidigare. Följande anvisningar beskriver processen för att skapa arbets rollen, inaktivera diagnostik 1,0 från lösningen och distribuera diagnostik 1,2 eller 1,3 till din arbets roll.

### <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har en Azure-prenumeration och använder Visual Studio med Azure SDK. Om du inte har någon Azure-prenumeration kan du registrera dig för den [kostnads fria utvärderings versionen][Free Trial]. Se till att [Installera och konfigurera Azure PowerShell version 0.8.7 eller senare][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Steg 1: skapa en arbets roll
1. Starta **Visual Studio**.
2. Skapa ett **Azure Cloud Service-** projekt från den **moln** mal len som är mål .NET Framework 4,5.  Ge projektet namnet "WadExample" och klicka på OK.
3. Välj **arbets roll** och klicka på OK. Projektet kommer att skapas.
4. I **Solution Explorer** dubbelklickar du på egenskaps filen **WorkerRole1** .
5. Avmarkera **Aktivera diagnostik** på fliken **konfiguration** för att inaktivera diagnostik 1,0 (Azure SDK 2,4 och tidigare).
6. Bygg din lösning för att kontrol lera att du inte har några fel.

### <a name="step-2-instrument-your-code"></a>Steg 2: instrumentera din kod
Ersätt innehållet i WorkerRole.cs med följande kod. Klassen SampleEventSourceWriter, som ärvts från [EventSource-klassen][EventSource Class], implementerar fyra loggnings metoder: **SendEnums**, **MessageMethod**, **SetOther** och **HighFreq**. Den första parametern till **WriteEvent** -metoden definierar ID: t för respektive händelse. Metoden Run implementerar en oändlig loop som anropar var och en av de loggnings metoder som implementeras i **SampleEventSourceWriter** -klassen var 10: e sekund.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Steg 3: distribuera din arbets roll

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Distribuera din arbets roll till Azure inifrån Visual Studio genom att välja **WadExample** -projektet i Solution Explorer sedan **publicera** från menyn **build** .
2. Välj din prenumeration.
3. I dialog rutan **Microsoft Azure publicerings inställningar** väljer du **Skapa ny..**..
4. I dialog rutan **skapa moln tjänst och lagrings konto** anger du ett **namn** (till exempel "WadExample") och väljer en region eller tillhörighets grupp.
5. Konfigurera **miljön** för **mellanlagring**.
6. Ändra eventuella andra **Inställningar** efter behov och klicka på **publicera**.
7. När distributionen har slutförts kontrollerar du i Azure Portal att moln tjänsten är i ett **Kör** tillstånd.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Steg 4: skapa en konfigurations fil för diagnostik och installera tillägget
1. Hämta schema definitionen för den offentliga konfigurations filen genom att köra följande PowerShell-kommando:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Lägg till en XML-fil till ditt **WorkerRole1** -projekt genom att högerklicka på projektet **WorkerRole1** och välja **Lägg till**  ->  **nytt objekt...** -> **Visual C#-objekt**  ->  **Data**  ->  **XML-fil**. Ge filen namnet "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Associera WadConfig. xsd med konfigurations filen. Kontrol lera att WadExample.xml redigerings fönstret är det aktiva fönstret. Tryck på **F4** för att öppna fönstret **Egenskaper** . Klicka på **schema** -egenskapen i fönstret **Egenskaper** . Klicka på **...** i egenskapen **schemas** . Klicka på **Lägg …** för att gå till den plats där du sparade XSD-filen och välja filen WadConfig. xsd. Klicka på **OK**.

4. Ersätt innehållet i WadExample.xml konfigurations filen med följande XML och spara filen. Den här konfigurations filen definierar ett par prestanda räknare som ska samlas in: en för processor användning och en för minnes användning. Sedan definierar konfigurationen de fyra händelser som motsvarar metoderna i SampleEventSourceWriter-klassen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Steg 5: installera diagnostik på din arbets roll
PowerShell-cmdletar för att hantera diagnostik på en webb-eller arbets roll är: set-AzureServiceDiagnosticsExtension, get-AzureServiceDiagnosticsExtension och Remove-AzureServiceDiagnosticsExtension.

1. Öppna Azure PowerShell.
2. Kör skriptet för att installera diagnostik i arbets rollen (Ersätt *StorageAccountKey* med lagrings konto nyckeln för ditt wadexample-lagrings konto och *config_path* med sökvägen till *WadExample.xml* -filen):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Steg 6: titta på dina telemetridata
I Visual Studio- **Server Explorer** navigerar du till lagrings kontot för wadexample. När moln tjänsten har körts ungefär fem (5) minuter bör tabellerna **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** och **WADSetOtherTable** visas. Dubbelklicka på en av tabellerna om du vill visa Telemetrin som har samlats in.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Konfigurations filens schema
Konfigurations filen för diagnostik definierar värden som används för att initiera konfigurations inställningar för diagnostik när diagnostik-agenten startar. Se den [senaste schema referensen](../azure-monitor/agents/diagnostics-extension-versions.md) för giltiga värden och exempel.

## <a name="troubleshooting"></a>Felsökning
Om du har problem kan du läsa [fel sökning Azure-diagnostik](../azure-monitor/agents/diagnostics-extension-troubleshooting.md) för att få hjälp med vanliga problem.

## <a name="next-steps"></a>Nästa steg
[Se en lista över relaterade Azure Virtual-Machine-diagnostiska artiklar](../azure-monitor/agents/diagnostics-extension-overview.md) för att ändra de data som du samlar in, felsöka problem eller Lär dig mer om diagnostik i allmänhet.

[EventSource Class]: /dotnet/api/system.diagnostics.tracing.eventsource

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: /previous-versions/azure/gg433048(v=azure.100)
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: /powershell/azure/