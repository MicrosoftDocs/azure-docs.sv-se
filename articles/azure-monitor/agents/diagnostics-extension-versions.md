---
title: Versions historik för konfigurations schema för Windows Azure-diagnostik Extension (WAD)
description: Relevant för att samla in prestanda räknare i Azure Virtual Machines VM Scale Sets, Service Fabric och Cloud Services.
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/29/2020
ms.openlocfilehash: 57ba1c760fe8919fff95493c5c81da4368894f73
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719822"
---
# <a name="windows-azure-diagnostics-extension-wad-configuration-schema-versions-and-history"></a>Windows Azure-diagnostik Extension (WAD) konfigurations schema versioner och historik
Den här artikeln innehåller versions historiken för de [Azure-diagnostik-tillägg för Windows (wad)](diagnostics-extension-overview.md) schema versioner som ingår i Microsoft Azure SDK.  


## <a name="azure-sdk-and-diagnostics-versions-shipping-chart"></a>Diagram över Azure SDK-och diagnostik-versioner som levereras  

|Azure SDK-version | Version för diagnostik-tillägg | Modell|  
|------------------|-------------------------------|------|  
|1.x               |1.0                            |plugin-program|  
|2,0 – 2,4         |1.0                            |plugin-program|  
|2.5               |1.2                            |utöka|  
|2,6               |1.3                            |"|  
|2.7               |1.4                            |"|  
|2.8               |1.5                            |"|  
|2.9               |1.6                            |"|
|2,96              |1.7                            |"|
|2,96              |1.8                            |"|
|2,96              |1.8.1                          |"|
|2,96              |1.9                            |"|
|2,96              |1,11                           |"|


 Azure-diagnostik version 1,0 först levererades i en plugin-modell, vilket innebär att när du installerade Azure SDK fick du den version av Azure Diagnostics som levererades med den.  

 Från och med SDK 2,5 (diagnostisk version 1,2), gick Azure Diagnostics till en tilläggs modell. Verktygen för att använda nya funktioner fanns bara tillgängliga i nyare Azure SDK: er, men alla tjänster som använder Azure Diagnostics skulle hämta den senaste leverans versionen direkt från Azure. Till exempel skulle alla som fortfarande använder SDK 2,5 läsa in den senaste versionen som visas i föregående tabell, oavsett om de använder de nya funktionerna.  

## <a name="schemas-index"></a>Schema index  
Olika versioner av Azure Diagnostics använder olika konfigurations scheman. Schema 1,0 och 1,2 är inaktuella. Mer information om version 1,3 och senare finns i [konfigurations schema för diagnostik 1,3 och senare](diagnostics-extension-schema-windows.md)  

## <a name="version-history"></a>Versionshistorik

### <a name="diagnostics-extension-111"></a>Diagnostik-tillägg 1,11
Stöd har lagts till för Azure Monitor mottagare. Denna mottagare kan bara användas för prestanda räknare. Möjliggör sändning av prestanda räknare som samlas in på din virtuella dator, VMSS eller moln tjänst till Azure Monitor som anpassade mått. Azure Monitor-mottagaren stöder:
* Hämta alla prestanda räknare som skickats till Azure Monitor via [API: er för Azure Monitor mått.](/rest/api/monitor/metrics/list)
* Aviseringar om alla prestanda räknare som skickas till Azure Monitor via den nya [enhetliga aviserings upplevelsen](../alerts/alerts-overview.md) i Azure Monitor
* Behandlar operator med jokertecken i prestanda räknare som "instance"-dimensionen på måttet. Om du till exempel har samlat in räknaren "logisk disk ( \* )/DiskWrites/SEC" kan du filtrera och dela på "instance"-dimensionen för att rita eller varna på disk skrivningar/s för varje logisk disk (C:, D: osv.)

Definiera Azure Monitor som ny mottagare i konfiguration av diagnostiska tillägg
```json
"SinksConfig": {
    "Sink": [
        {
            "name": "AzureMonitorSink",
            "AzureMonitor": {}
        },
    ]
}
```

```XML
<SinksConfig>  
  <Sink name="AzureMonitorSink">
      <AzureMonitor/>
  </Sink>
</SinksConfig>
```
> [!NOTE]
> Att konfigurera Azure Monitor-mottagare för klassiska virtuella datorer och den klassiska moln tjänsten kräver att fler parametrar definieras i tillägget för diagnostik-tilläggets privata konfiguration.
>
> Mer information finns i dokumentationen om [schemat för den detaljerade tillägget för diagnostik.](diagnostics-extension-schema-windows.md)

Sedan kan du konfigurera dina prestanda räknare så att de dirigeras till den Azure Monitor mottagaren.
```json
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
```
```XML
<PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
</PerformanceCounters>
```

### <a name="diagnostics-extension-19"></a>Diagnostik-tillägg 1,9
Docker-stöd har lagts till.


### <a name="diagnostics-extension-181"></a>1.8.1 för Diagnostics-tillägg
Kan ange en SAS-token i stället för en lagrings konto nyckel i den privata konfigurationen. Om en SAS-token anges ignoreras lagrings konto nyckeln.


```json
{
    "storageAccountName": "diagstorageaccount",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    }
}
```

```xml
<PrivateConfig>
    <StorageAccount name="diagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    <SecondaryStorageAccounts>
        <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
</PrivateConfig>
```


### <a name="diagnostics-extension-18"></a>Diagnostik-tillägg 1,8
Lagrings typen har lagts till i PublicConfig. StorageType kan vara *Table*, *BLOB*, *TableAndBlob*. *Tabellen* är standard.


```json
{
    "WadCfg": {
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```xml
<PublicConfig>
    <WadCfg />
    <StorageAccount>diagstorageaccount</StorageAccount>
    <StorageType>TableAndBlob</StorageType>
</PublicConfig>
```


### <a name="diagnostics-extension-17"></a>Diagnostik-tillägg 1,7
Har lagt till möjligheten att dirigera till EventHub.

### <a name="diagnostics-extension-15"></a>Diagnostik-tillägg 1,5
Du har lagt till Sinks-elementet och möjligheten att skicka diagnostikdata till [Application Insights](../app/cloudservices.md) göra det enklare att diagnostisera problem i ditt program och system-och infrastruktur nivå.

### <a name="azure-sdk-26-and-diagnostics-extension-13"></a>Azure SDK 2,6 och diagnostiskt tillägg 1,3
Följande ändringar har gjorts för moln tjänst projekt i Visual Studio. (Dessa ändringar gäller även senare versioner av Azure SDK.)

* Den lokala emulatorn stöder nu diagnostik. Den här ändringen innebär att du kan samla in diagnostikdata och se till att programmet skapar rätt spår när du utvecklar och testar i Visual Studio. Anslutnings strängen `UseDevelopmentStorage=true` möjliggör insamling av diagnostikdata medan du kör ditt moln tjänst projekt i Visual Studio med hjälp av Azure Storage emulatorn. Alla diagnostikdata samlas in i lagrings kontot (utvecklings lagring).
* Anslutnings strängen för det diagnostiska lagrings kontot (Microsoft. WindowsAzure. plugin-program. Diagnostics. ConnectionString) lagras en gång i tjänst konfigurations filen (. cscfg). I Azure SDK 2,5 angavs ditt lagrings konto för diagnostik i filen Diagnostics. wadcfgx.

Det finns några viktiga skillnader mellan hur anslutnings strängen fungerade i Azure SDK 2,4 och tidigare och hur den fungerar i Azure SDK 2,6 och senare.

* I Azure SDK 2,4 och tidigare användes anslutnings strängen vid körning av diagnostik-plugin-programmet för att hämta lagrings konto informationen för överföring av diagnostikloggar.
* I Azure SDK 2,6 och senare använder Visual Studio diagnostik-anslutningssträngen för att konfigurera tillägget för diagnostik med lämplig lagrings konto information under publiceringen. Med anslutnings strängen kan du definiera olika lagrings konton för olika tjänst konfigurationer som ska användas i Visual Studio vid publicering. Men eftersom diagnostik-plugin-programmet inte längre är tillgängligt (efter Azure SDK 2,5) kan inte. cscfg-filen av sig själv aktivera diagnostik-tillägget. Du måste aktivera tillägget separat via verktyg som Visual Studio eller PowerShell.
* För att förenkla processen med att konfigurera diagnostik-tillägget med PowerShell innehåller paket utmatningen från Visual Studio även XML-filen för den offentliga konfigurationen för diagnostikprogrammet för varje roll. Visual Studio använder anslutnings strängen för diagnostik för att fylla i informationen om lagrings kontot som finns i den offentliga konfigurationen. De offentliga konfigurationsfiler skapas i mappen tillägg och följer mönstret `PaaSDiagnostics.<RoleName>.PubConfig.xml` . Alla PowerShell-baserade distributioner kan använda det här mönstret för att mappa varje konfiguration till en roll.
* Anslutnings strängen i. cscfg-filen används också av Azure Portal för att få åtkomst till diagnostikdata så att den kan visas på fliken **övervakning** . Anslutnings strängen krävs för att konfigurera tjänsten för att Visa utförliga övervaknings data i portalen.

#### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrera projekt till Azure SDK 2,6 och senare
När du migrerar från Azure SDK 2,5 till Azure SDK 2,6 eller senare, om du hade ett diagnostiskt lagrings konto som angetts i. wadcfgx-filen, förblir den där. Om du vill dra nytta av flexibiliteten med att använda olika lagrings konton för olika lagrings konfigurationer måste du manuellt lägga till anslutnings strängen i projektet. Om du migrerar ett projekt från Azure SDK 2,4 eller tidigare till Azure SDK 2,6 bevaras anslutnings strängarna för diagnostik. Observera dock att ändringarna i hur anslutnings strängar behandlas i Azure SDK 2,6 enligt vad som anges i föregående avsnitt.

#### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Så här identifierar Visual Studio-diagnostikens lagrings konto
* Om en diagnostik-anslutningssträng anges i. cscfg-filen använder Visual Studio den för att konfigurera tillägget för diagnostik vid publicering, och när du genererar XML-filer för den offentliga konfigurationen under packningen.
* Om ingen diagnostik-anslutningssträng anges i. cscfg-filen, går Visual Studio tillbaka till att använda det lagrings konto som anges i. wadcfgx-filen för att konfigurera diagnostikprogrammet vid publicering och generera XML-filer för den offentliga konfigurationen när de paketeras.
* Anslutnings strängen för diagnostik i. cscfg-filen har företräde framför lagrings kontot i. wadcfgx-filen. Om en diagnostik-anslutningssträng anges i. cscfg-filen använder Visual Studio det och ignorerar lagrings kontot i. wadcfgx.

#### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Vad gör "uppdatera utvecklings lagrings anslutnings strängarna..." kryss rutan gör?
Kryss rutan för att **Uppdatera utvecklings lagrings anslutnings strängar för diagnostik och cachelagring med Microsoft Azure lagrings konto uppgifter när du publicerar till Microsoft Azure** ger dig ett bekvämt sätt att uppdatera eventuella anslutnings strängar för utvecklings lagrings konton med det Azure Storage-konto som angavs under publiceringen.

Anta till exempel att du markerar den här kryss rutan och att anslutnings strängen för diagnostik anger `UseDevelopmentStorage=true` . När du publicerar projektet till Azure uppdaterar Visual Studio automatiskt den diagnostiska anslutnings strängen med det lagrings konto som du angav i publicerings guiden. Men om ett verkligt lagrings konto har angetts som anslutnings sträng för diagnostik, används detta konto i stället.

### <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Skillnader i diagnostiken mellan Azure SDK 2,4 och tidigare och Azure SDK 2,5 och senare
Om du uppgraderar projektet från Azure SDK 2,4 till Azure SDK 2,5 eller senare bör du tänka på följande skillnader i diagnostiken.

* **Konfigurations-API: er är inaktuella** – program konfigurationen för diagnostik är tillgänglig i azure SDK 2,4 eller tidigare versioner, men är inaktuell i azure SDK 2,5 och senare. Om din diagnostiska konfiguration för närvarande är definierad i kod måste du konfigurera om inställningarna från grunden i det migrerade projektet för att diagnostiken ska fortsätta att fungera. Konfigurations filen för diagnostik för Azure SDK 2,4 är Diagnostics. wadcfg och Diagnostics. wadcfgx för Azure SDK 2,5 och senare.
* **Diagnostik för moln tjänst program kan bara konfigureras på roll nivå, inte på instans nivå.**
* **Varje gång du distribuerar appen uppdateras diagnostiken** – detta kan orsaka paritets problem om du ändrar din diagnostiska konfiguration från Server Explorer och sedan distribuerar om din app.
* **I Azure SDK 2,5 och senare konfigureras krasch dum par i diagnostikens konfigurations fil, inte i kod** – om du har krasch dum par som kon figurer ATS i kod måste du manuellt överföra konfigurationen från kod till konfigurations filen, eftersom krasch dum par inte överförs under migreringen till Azure SDK 2,6.