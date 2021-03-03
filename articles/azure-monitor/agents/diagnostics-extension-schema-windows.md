---
title: Windows Diagnostics-tilläggsprogram schema
description: Konfigurations schema referens för Windows Diagnostics-tillägget (WAD) i Azure Monitor.
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: 4b4b893b222a62619a36d59c2cf042f1aa5590bc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708636"
---
# <a name="windows-diagnostics-extension-schema"></a>Windows Diagnostics-tilläggsprogram schema
Azure-diagnostik tillägget är en agent i Azure Monitor som samlar in övervaknings data från gäst operativ systemet och arbets belastningar i Azure Compute-resurser. Den här artikeln beskriver det schema som används för konfiguration av Diagnostics-tillägget på virtuella Windows-datorer och andra beräknings resurser.

> [!NOTE]
> Schemat i den här artikeln gäller för version 1,3 och senare (Azure SDK 2,4 och senare). Nyare konfigurations avsnitt är kommenterade för att visa i vilken version de lades till. Version 1,0 och 1,2 av schemat har arkiverats och är inte längre tillgängligt. 

## <a name="public-configuration-file-schema"></a>Fil schema för offentlig konfiguration

Hämta schema definitionen för den offentliga konfigurations filen genom att köra följande PowerShell-kommando:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Vanliga attributtyper  
 **scheduledTransferPeriod** -attributet visas i flera element. Det är intervallet mellan schemalagda överföringar till lagrings utrymmet, avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet.](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration-element  
 *Träd: rot-DiagnosticsConfiguration*

Tillagt i version 1,3.  

Elementet på den översta nivån i konfigurations filen för diagnostik.  

**Attribut**  för xmlns – XML-namnområdet för diagnostikens konfigurations fil är:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**PublicConfig**|Krävs. Se beskrivningen på en annan plats på den här sidan.|  
|**PrivateConfig**|Valfritt. Se beskrivningen på en annan plats på den här sidan.|  
|**IsEnabled**|Booleskt. Se beskrivningen på en annan plats på den här sidan.|  

## <a name="publicconfig-element"></a>PublicConfig-element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig*

 Beskriver konfigurationen för den offentliga diagnostiken.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**WadCfg**|Krävs. Se beskrivningen på en annan plats på den här sidan.|  
|**StorageAccount**|Namnet på det Azure Storage konto som data ska lagras i. Kan också anges som en parameter när du kör Set-AzureServiceDiagnosticsExtension-cmdleten.|  
|**StorageType**|Kan vara *Table*, *BLOB* eller *TableAndBlob*. Tabellen är standard. När TableAndBlob väljs skrivs diagnostikdata två gånger – en gång till varje typ.|  
|**LocalResourceDirectory**|Katalogen på den virtuella datorn där övervaknings agenten lagrar händelse data. Om inte, ange, används standard katalogen:<br /><br /> För en Worker/Web-roll: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> För en virtuell dator: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Obligatoriska attribut är:<br /><br /> - **sökväg** – katalogen i systemet som ska användas av Azure-diagnostik.<br /><br /> - **expandEnvironment** – styr om miljövariablerna expanderas i Sök vägs namnet.|  

## <a name="wadcfg-element"></a>WadCFG-element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG*

 Identifierar och konfigurerar telemetri-data som ska samlas in.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration-element
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration*

 Obligatorisk

|Attribut|Beskrivning|  
|----------------|-----------------|  
| **overallQuotaInMB** | Den maximala mängd lokalt disk utrymme som kan användas av de olika typerna av diagnostikdata som samlas in av Azure-diagnostik. Standardvärdet är 4096 MB.<br />
|**useProxyServer** | Konfigurera Azure-diagnostik att använda inställningarna för proxyservern som anges i IE-inställningar.|
|**mottagare** | Tillagt i 1,5. Valfritt. Pekar på en mottagar plats för att även skicka diagnostikdata för alla underordnade element som stöder mottagare. Sink-exemplet är Application Insights eller Event Hubs. Observera att du måste lägga till egenskapen *resourceId* under *mått* elementet om du vill att händelser som laddats upp till Event Hubs har ett resurs-ID. |  


<br /> <br />

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**CrashDumps**|Se beskrivningen på en annan plats på den här sidan.|  
|**DiagnosticInfrastructureLogs**|Aktivera samling av loggar som genereras av Azure-diagnostik. De diagnostiska infrastruktur loggarna är användbara för att felsöka själva diagnostik systemet. Valfria attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** – konfigurerar den lägsta allvarlighets graden för loggarna som samlas in.<br /><br /> - **scheduledTransferPeriod** – intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Kataloger**|Se beskrivningen på en annan plats på den här sidan.|  
|**EtwProviders**|Se beskrivningen på en annan plats på den här sidan.|  
|**Mått**|Se beskrivningen på en annan plats på den här sidan.|  
|**PerformanceCounters**|Se beskrivningen på en annan plats på den här sidan.|  
|**WindowsEventLog**|Se beskrivningen på en annan plats på den här sidan.|
|**DockerSources**|Se beskrivningen på en annan plats på den här sidan. |



## <a name="crashdumps-element"></a>CrashDumps-element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-CrashDumps*

 Aktivera samlingen av krasch dum par.  

|Attribut|Beskrivning|  
|----------------|-----------------|  
|**containerName**|Valfritt. Namnet på BLOB-behållaren i Azure Storage kontot som ska användas för att lagra krasch dum par.|  
|**crashDumpType**|Valfritt.  Konfigurerar Azure-diagnostik att samla in mini-eller fullständiga krasch dum par.|  
|**directoryQuotaPercentage**|Valfritt.  Konfigurerar procent andelen av **overallQuotaInMB** som ska reserveras för krasch dum par på den virtuella datorn.|  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Krävs. Definierar konfigurations värden för varje process.<br /><br /> Följande attribut krävs också:<br /><br /> **processName** – namnet på den process som du vill Azure-diagnostik för att samla in en kraschdump för.|  

## <a name="directories-element"></a>Katalog element
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-kataloger*

 Aktiverar insamling av innehållet i en katalog, IIS nekade åtkomst till begär ande loggar och/eller IIS-loggar.  

 Valfritt **scheduledTransferPeriod** -attribut. Se förklaring tidigare.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**IISLogs**|Genom att inkludera det här elementet i konfigurationen möjliggörs insamling av IIS-loggar:<br /><br /> **containerName** – namnet på BLOB-behållaren i Azure Storage-kontot som ska användas för att lagra IIS-loggarna.|   
|**FailedRequestLogs**|Genom att inkludera det här elementet i konfigurationen kan du samla in loggar över misslyckade förfrågningar till en IIS-webbplats eller ett program. Du måste också aktivera spårnings alternativ under **system. -Webbserver** i **Web.config**.|  
|**Data källor**|En lista över kataloger som ska övervakas.|




## <a name="datasources-element"></a>Data källa element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-kataloger-data källor*

 En lista över kataloger som ska övervakas.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Krävs. Nödvändigt attribut:<br /><br /> **containerName** – namnet på BLOB-behållaren i Azure Storage-kontot som ska användas för att lagra loggfilerna.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration-element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-kataloger-data källor-DirectoryConfiguration*

 Kan innehålla antingen det **absoluta** eller **LocalResource** -elementet, men inte båda.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**Absolut**|Den absoluta sökvägen till den katalog som ska övervakas. Följande attribut krävs:<br /><br /> - **Sökväg** – den absoluta sökvägen till den katalog som ska övervakas.<br /><br /> - **expandEnvironment** – konfigurerar om miljövariabler i sökvägen ska expanderas.|  
|**LocalResource**|Sökvägen i förhållande till en lokal resurs som ska övervakas. Obligatoriska attribut är:<br /><br /> - **Namn** – den lokala resurs som innehåller den katalog som ska övervakas<br /><br /> - **relativePath** – sökvägen är relativ till det namn som innehåller den katalog som ska övervakas|  



## <a name="etwproviders-element"></a>EtwProviders-element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders*

 Konfigurerar insamling av ETW-händelser från EventSource och/eller ETW-manifest baserade providers.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurerar insamling av händelser som genereras från [EventSource-klassen](/dotnet/api/system.diagnostics.tracing.eventsource). Nödvändigt attribut:<br /><br /> **Provider** – klass namnet för EventSource-händelsen.<br /><br /> Valfria attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** – den minsta allvarlighets grad som ska överföras till ditt lagrings konto.<br /><br /> - **scheduledTransferPeriod** – intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Nödvändigt attribut:<br /><br /> **Provider** – GUID för händelse leverantören<br /><br /> Valfria attribut är:<br /><br /> - **scheduledTransferLogLevelFilter** – den minsta allvarlighets grad som ska överföras till ditt lagrings konto.<br /><br /> - **scheduledTransferPeriod** – intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration-element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwEventSourceProviderConfiguration*

 Konfigurerar insamling av händelser som genereras från [EventSource-klassen](/dotnet/api/system.diagnostics.tracing.eventsource).  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**DefaultEvents**|Valfritt attribut:<br/><br/> **eventDestination** – namnet på den tabell där händelserna ska lagras|  
|**Händelse**|Nödvändigt attribut:<br /><br /> **ID** – händelsens ID.<br /><br /> Valfritt attribut:<br /><br /> **eventDestination** – namnet på den tabell där händelserna ska lagras|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration-element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwManifestProviderConfiguration*

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**DefaultEvents**|Valfritt attribut:<br /><br /> **eventDestination** – namnet på den tabell där händelserna ska lagras|  
|**Händelse**|Nödvändigt attribut:<br /><br /> **ID** – händelsens ID.<br /><br /> Valfritt attribut:<br /><br /> **eventDestination** – namnet på den tabell där händelserna ska lagras|  



## <a name="metrics-element"></a>Mått element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Metrics*

 Gör att du kan generera en prestanda räknar tabell som är optimerad för snabba frågor. Varje prestanda räknare som definieras i **PerformanceCounters** -elementet lagras i mått tabellen förutom prestanda räknar tabellen.  

 Attributet **resourceId** måste anges.  Resurs-ID för den virtuella datorn eller skalnings uppsättningen för den virtuella datorn som du distribuerar Azure-diagnostik till. Hämta **resourceID** från [Azure Portal](https://portal.azure.com). Välj **Bläddra**  ->  **resurs grupper**  ->  **<namn \>**. Klicka på panelen **Egenskaper** och kopiera värdet från fältet **ID** .  Den här egenskapen resourceID används för att både skicka anpassade mått och för att lägga till en resourceID-egenskap till data som skickas till Event Hubs. Observera att du måste lägga till egenskapen *resourceId* under *mått* elementet om du vill att händelser som laddats upp till Event Hubs har ett resurs-ID.

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**MetricAggregation**|Nödvändigt attribut:<br /><br /> **scheduledTransferPeriod** – intervallet mellan schemalagda överföringar till lagring avrundat uppåt till närmaste minut. Värdet är en [data typ för XML-varaktighet.](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters-element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-PerformanceCounters*

 Aktiverar insamling av prestanda räknare.  

 Valfritt attribut:  

 Valfritt **scheduledTransferPeriod** -attribut. Se förklaring tidigare.

|Underordnat element|Beskrivning|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Följande attribut krävs:<br /><br /> - **counterSpecifier** – namnet på prestanda räknaren. Till exempel `\Processor(_Total)\% Processor Time`. Om du vill hämta en lista över prestanda räknare på värden kör du kommandot `typeperf` .<br /><br /> - **sampleRate** – hur ofta räknaren ska samplas.<br /><br /> Valfritt attribut:<br /><br /> **enhet** – enhets måttet för räknaren. Värdena är tillgängliga i [UnitType-klassen](/dotnet/api/microsoft.azure.management.sql.models.unittype) |
|**mottagare** | Tillagt i 1,5. Valfritt. Pekar på en mottagar plats för att även skicka diagnostikdata. Till exempel Azure Monitor eller Event Hubs. Observera att du måste lägga till egenskapen *resourceId* under *mått* elementet om du vill att händelser som laddats upp till Event Hubs har ett resurs-ID.|    




## <a name="windowseventlog-element"></a>WindowsEventLog-element
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-WindowsEventLog*

 Aktiverar insamling av händelse loggar i Windows.  

 Valfritt **scheduledTransferPeriod** -attribut. Se förklaring tidigare.  

|Underordnat element|Beskrivning|  
|-------------------|-----------------|  
|**Datakälla**|Händelse loggarna i Windows som ska samlas in. Nödvändigt attribut:<br /><br /> **namn** – XPath-frågan som beskriver de Windows-händelser som ska samlas in. Exempel:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Om du vill samla in alla händelser anger du "*" |
|**mottagare** | Tillagt i 1,5. Valfritt. Pekar på en mottagar plats för att även skicka diagnostikdata för alla underordnade element som stöder mottagare. Sink-exemplet är Application Insights eller Event Hubs.|  


## <a name="logs-element"></a>Loggar element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Logs*

 Förekommer i version 1,0 och 1,1. Saknas i 1,2. Lades tillbaka i 1,3.  

 Definierar buffertens konfiguration för grundläggande Azure-loggar.  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Valfritt. Anger den maximala mängden fil system lagring som är tillgänglig för angivna data.<br /><br /> Standardvärdet är 0.|  
|**scheduledTransferLogLevelFilter**|**nollängd**|Valfritt. Anger den lägsta allvarlighets graden för logg poster som överförs. Standardvärdet är **odefinierat**, vilket överför alla loggar. Andra möjliga värden (i högst minst information) är **utförlig**, **information**, **Varning**, **fel** och **kritisk**.|  
|**scheduledTransferPeriod**|**giltighet**|Valfritt. Anger intervallet mellan schemalagda data överföringar, avrundade uppåt till närmaste minut.<br /><br /> Standardvärdet är PT0S.|  
|**mottagare** |**sträng**| Tillagt i 1,5. Valfritt. Pekar på en mottagar plats för att även skicka diagnostikdata. Till exempel Application Insights eller Event Hubs. Observera att du måste lägga till egenskapen *resourceId* under *mått* elementet om du vill att händelser som laddats upp till Event Hubs har ett resurs-ID.|  

## <a name="dockersources"></a>DockerSources
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-DockerSources*

 Tillagt i 1,9.

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Spelarna**|Instruerar systemet att samla in statistik för Docker-behållare|  

## <a name="sinksconfig-element"></a>SinksConfig-element  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig*

 En lista med platser för att skicka diagnostikdata till och konfigurationen som är kopplad till dessa platser.  

|Elementnamn|Beskrivning|  
|------------------|-----------------|  
|**Kanalmottagare**|Se beskrivningen på en annan plats på den här sidan.|  

## <a name="sink-element"></a>Sink-element
 *Träd: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink*

 Tillagt i version 1,5.  

 Definierar platser för att skicka diagnostikdata till. Till exempel tjänsten Application Insights.  

|Attribut|Typ|Beskrivning|  
|---------------|----------|-----------------|  
|**Namn**|sträng|En sträng som identifierar sinkname.|  

|Element|Typ|Beskrivning|  
|-------------|----------|-----------------|  
|**Application Insights**|sträng|Används endast när du skickar data till Application Insights. Innehåller Instrumentation-nyckeln för ett aktivt Application Insights-konto som du har åtkomst till.|  
|**Kanaler**|sträng|En för varje ytterligare filtrering som strömmar som du|  

## <a name="channels-element"></a>Element för kanaler  
 *Träd: rot-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink-Channels*

 Tillagt i version 1,5.  

 Definierar filter för data strömmar av loggdata som passerar genom en mottagare.  

|Element|Typ|Beskrivning|  
|-------------|----------|-----------------|  
|**Kanal**|sträng|Se beskrivningen på en annan plats på den här sidan.|  

## <a name="channel-element"></a>Kanal element
 *Träd: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-Sink-Channels-Channel*

 Tillagt i version 1,5.  

 Definierar platser för att skicka diagnostikdata till. Till exempel tjänsten Application Insights.  

|Attribut|Typ|Beskrivning|  
|----------------|----------|-----------------|  
|**logLevel**|**sträng**|Anger den lägsta allvarlighets graden för logg poster som överförs. Standardvärdet är **odefinierat**, vilket överför alla loggar. Andra möjliga värden (i högst minst information) är **utförlig**, **information**, **Varning**, **fel** och **kritisk**.|  
|**Namn**|**sträng**|Ett unikt namn på den kanal som ska referera till|  


## <a name="privateconfig-element"></a>PrivateConfig-element
 *Träd: rot-DiagnosticsConfiguration-PrivateConfig*

 Tillagt i version 1,3.  

 Valfritt  

 Lagrar privat information om lagrings kontot (namn, nyckel och slut punkt). Den här informationen skickas till den virtuella datorn, men kan inte hämtas från den.  

|Underordnade element|Beskrivning|  
|--------------------|-----------------|  
|**StorageAccount**|Det lagrings konto som ska användas. Följande attribut krävs<br /><br /> - **namn** – namnet på lagrings kontot.<br /><br /> - **nyckel** – nyckeln till lagrings kontot.<br /><br /> - **slut punkt** – slut punkten för åtkomst till lagrings kontot. <br /><br /> -**sasToken** (Tillagd 1.8.1) – du kan ange en SAS-token i stället för en lagrings konto nyckel i den privata konfigurationen. Om det här alternativet anges ignoreras lagrings konto nyckeln. <br />Krav för SAS-token: <br />– Stöder endast SAS-token för konto <br />- *b*, *t* . ex. krävs tjänst typer. <br /> - *a*, *c*, *u*, *w* behörigheter krävs. <br /> - *c*, *o* resurs typer krävs. <br /> -Stöder endast HTTPS-protokollet <br /> -Start-och utgångs tid måste vara giltiga.|  


## <a name="isenabled-element"></a>IsEnabled-element  
 *Träd: rot-DiagnosticsConfiguration-IsEnabled*

 Booleskt. Använd `true` om du vill aktivera diagnostiken eller `false` inaktivera diagnostiken.

## <a name="example-configuration"></a>Exempelkonfiguration
 Nedan följer en fullständig exempel konfiguration för Windows Diagnostics-tillägget som visas i både JSON och XML.

 
### <a name="json"></a>JSON

*PublicConfig* och *PrivateConfig* separeras eftersom i de flesta JSON-användnings fall skickas de som olika variabler. I dessa fall ingår Resource Manager-mallar, PowerShell och Visual Studio.

> [!NOTE]
> Den offentliga konfigurations Azure Monitor mottagar definitionen har två egenskaper, *resourceId* och *region*. Dessa krävs endast för klassiska virtuella datorer och klassiska moln tjänster. Egenskapen *region* ska inte användas för andra resurser, egenskapen *ResourceID* används på virtuella ARM-datorer för att fylla i fältet ResourceID i loggar som laddats upp till Event Hubs.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> Den privata konfigurations Azure Monitor mottagar definitionen har två egenskaper, *PrincipalId* och *hemlighet*. Dessa krävs endast för klassiska virtuella datorer och klassiska moln tjänster. Dessa egenskaper bör inte användas för andra resurser.


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> Den offentliga konfigurations Azure Monitor mottagar definitionen har två egenskaper, resourceId och region. Dessa krävs endast för klassiska virtuella datorer och klassiska moln tjänster. Dessa egenskaper bör inte användas för Resource Manager-Virtual Machines eller skalnings uppsättningar för virtuella datorer.
> Det finns också ett ytterligare privat konfigurations element för Azure Monitor-mottagaren, som passerar i ett ägar-ID och hemligt. Detta krävs endast för klassiska virtuella datorer och klassiska Cloud Services. För virtuella Resource Manager-datorer och VMSS kan Azure Monitors definitionen i det privata konfigurations elementet undantas.
>
