---
title: Felsöka Azure-diagnostik-tillägg
description: Felsök problem när du använder Azure Diagnostics i Azure Virtual Machines Service Fabric eller Cloud Services.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/08/2019
ms.openlocfilehash: 431b19595fbe2f5bc1f989e712c9c104af8e839b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711526"
---
# <a name="azure-diagnostics-troubleshooting"></a>Azure Diagnostics-felsökning
I den här artikeln beskrivs felsöknings information som är relevant för att använda Azure-diagnostik. Mer information om Azure Diagnostics finns i [Azure-diagnostik översikt](diagnostics-extension-overview.md).

## <a name="logical-components"></a>Logiska komponenter
**Start program för diagnostik-plugin (DiagnosticsPluginLauncher.exe)**: startar tillägget Azure-diagnostik. Fungerar som start punkts process.

**Plugin-program för diagnostik (DiagnosticsPlugin.exe)**: konfigurerar, startar och hanterar övervaknings agentens livs längd. Det är den huvudsakliga processen som startas av Start programmet.

**Övervaknings agent (MonAgent \* . exe-processer)**: övervakar, samlar in och överför diagnostikdata.  

## <a name="logartifact-paths"></a>Logg-/artefakt Sök vägar
Här följer Sök vägarna till några viktiga loggar och artefakter. Vi refererar till den här informationen i resten av dokumentet.

### <a name="azure-cloud-services"></a>Azure Cloud Services
| Artefakt | Sökväg |
| --- | --- |
| **Azure-diagnostik konfigurations fil** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics \<version>\Config.txt |
| **Loggfiler** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **Lokalt Arkiv för diagnostikdata** | C:\Resources\Directory \<CloudServiceDeploymentID> . \<RoleName> . DiagnosticStore\WAD0107\Tables |
| **Övervaknings agentens konfigurations fil** | C:\Resources\Directory \<CloudServiceDeploymentID> . \<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure-diagnostik tilläggs paket** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **Sökväg till logg insamlings verktyget** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost logg fil** | C:\Resources\Directory \<CloudServiceDeploymentID> . \<RoleName> . DiagnosticStore\WAD0107\Configuration\MonAgentHost. <seq_num>. log |

### <a name="virtual-machines"></a>Virtuella datorer
| Artefakt | Sökväg |
| --- | --- |
| **Azure-diagnostik konfigurations fil** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \RuntimeSettings |
| **Loggfiler** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\ |
| **Lokalt Arkiv för diagnostikdata** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Tables |
| **Övervaknings agentens konfigurations fil** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **Status fil** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<version> \Status |
| **Azure-diagnostik tilläggs paket** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **Sökväg till logg insamlings verktyget** | C:\WindowsAzure\Logs\WaAppAgent.log |
| **MonAgentHost logg fil** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics \<DiagnosticsVersion> \WAD0107\Configuration\MonAgentHost. <seq_num>. log |

## <a name="metric-data-doesnt-appear-in-the-azure-portal"></a>Måttdata visas inte i Azure-portalen
Azure-diagnostik tillhandahåller mått data som kan visas i Azure Portal. Om du har problem med att se data i portalen kontrollerar du \* tabellen WADMetrics i Azure-diagnostik lagrings konto för att se om motsvarande mått poster finns där och kontrollerar att [resurs leverantören](../../azure-resource-manager/management/resource-providers-and-types.md) Microsoft. Insights har registrerats.

Här är **PartitionKey** för tabellen resurs-ID, virtuell dator eller skal uppsättning för virtuell dator. **RowKey** är mått namnet (även kallat prestanda räknarens namn).

Om resurs-ID: t är felaktigt  kontrollerar du **konfigurations**  >  **måtten**  >   för diagnostik för att se om resurs-ID: t har angetts korrekt.

Om det inte finns några data för det aktuella måttet **kontrollerar**  >  du **PerformanceCounter** för att se om måttet (prestanda räknaren) ingår. Vi aktiverar följande räknare som standard:
- \Processor(_Total)\% processortid
- \Memory\Tillgängliga byte
- \ ASP.NET program (__totalt__) \ begär anden/SEK
- \ ASP.NET program (__totalt__) \Errors totalt/SEK
- \ASP.NET\Requests i kö
- \ASP.NET\Requests nekades
- \Processor (W3wp) \% processor tid
- \Process (W3wp) \Privata byte
- \Process (WaIISHost) \% processor tid
- \Process (WaIISHost) \Privata byte
- \Process (WaWorkerHost) \% processor tid
- \Process (WaWorkerHost) \Privata byte
- \Memory\Page-fel/SEK
- \.NET CLR-minne (_Global_) \% i GC
- \Logisk disk (C:) \ disk skrivna byte/s
- \Logisk disk (C:) \ disk lästa byte/s
- \Logisk disk (D:) \ disk skrivna byte/s
- \Logisk disk (D:) \ disk lästa byte/s

Om konfigurationen är korrekt men du fortfarande inte kan se mått data kan du använda följande rikt linjer för att felsöka.


## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics startar inte
Information om varför Azure-diagnostik inte kunde starta finns i filen **DiagnosticsPluginLauncher. log** och **DiagnosticsPlugin. log** på platsen för loggfiler som angavs tidigare.

Om dessa loggar indikerar det `Monitoring Agent not reporting success after launch` innebär det att det uppstod ett problem med att starta MonAgentHost.exe. Titta på loggarna på den plats som anges `MonAgentHost log file` i föregående avsnitt.

Den sista raden i loggfilerna innehåller slut koden.  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
Om du hittar en **negativ** slutkod, se i [slut kods tabellen](#azure-diagnostics-plugin-exit-codes) i [avsnittet referenser](#references).

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Diagnostikdata loggas inte till Azure Storage
Avgör om ingen av dessa data visas eller om vissa data visas.

### <a name="diagnostics-infrastructure-logs"></a>Infrastruktur loggar för diagnostik
Diagnostik loggar alla fel i loggar för diagnostik-infrastruktur. Kontrol lera att du har aktiverat [insamlingen av infrastruktur loggar för diagnostik i konfigurationen](#how-to-check-diagnostics-extension-configuration). Sedan kan du snabbt söka efter relevanta fel som visas i `DiagnosticInfrastructureLogsTable` tabellen i ditt konfigurerade lagrings konto.

### <a name="no-data-is-appearing"></a>Inga data visas
Den vanligaste orsaken till att händelse data inte visas alls är att lagrings konto informationen har definierats felaktigt.

Lösning: korrigera konfigurationen för diagnostik och installera om diagnostik.

Om lagrings kontot har kon figurer ATS korrekt, fjärråtkomst till datorn och kontrol lera att *DiagnosticsPlugin.exe* och *MonAgentCore.exe* körs. Om de inte körs följer du stegen i [Azure-diagnostik startar inte](#azure-diagnostics-is-not-starting).

Om processerna körs går du till [data som samlas in lokalt?](#is-data-getting-captured-locally) och följer anvisningarna där.

Om detta inte löser problemet försöker du:

1. Avinstallera agenten
2. Ta bort katalog C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics
3. Installera Agent igen


### <a name="part-of-the-data-is-missing"></a>En del av data saknas
Om du hämtar vissa data men inte alla, innebär det att pipeline för data insamling/överföring är korrekt inställd. Följ underavsnitten här för att begränsa problemet.

#### <a name="is-the-collection-configured"></a>Är samlingen konfigurerad?
Diagnostik-konfigurationen innehåller instruktioner för en viss typ av data som ska samlas in. [Granska konfigurationen](#how-to-check-diagnostics-extension-configuration) för att kontrol lera att du bara söker efter data som du har konfigurerat för samlingen.

#### <a name="is-the-host-generating-data"></a>Genererar värden data?
- **Prestanda räknare**: öppna perfmon och kontrol lera räknaren.

- **Spårnings loggar**: fjärråtkomst till den virtuella datorn och lägger till en TextWriterTraceListener i appens konfigurations fil.  Se https://msdn.microsoft.com/library/sk36c28t.aspx så här konfigurerar du text lyssnaren.  Kontrol lera att `<trace>` elementet har `<trace autoflush="true">` .<br />
Om du inte ser spårnings loggar som genereras, se mer information om spårnings loggar som saknas.

- **ETW-spår**: fjärråtkomst till den virtuella datorn och installera PerfView.  I **PerfView kör du**  >  **kommandot User**  >  **Listener etwprovder1**  >  **etwprovider2** och så vidare. **Lyssnings** kommandot är Skift läges känsligt och det får inte innehålla blank steg mellan den kommaavgränsade listan över ETW-providers. Om kommandot inte kan köras kan du välja knappen **Logga** längst ned till höger i Perfview-verktyget för att se vad som har försökt köra och vad resultatet var.  Om du antar att indatatypen är korrekt öppnas ett nytt fönster. Om några sekunder börjar du se ETW-spår.

- **Händelse loggar**: fjärråtkomst till den virtuella datorn. Öppna `Event Viewer` och kontrol lera att händelserna finns.

#### <a name="is-data-getting-captured-locally"></a>Samlas data in lokalt?
Se sedan till att data samlas in lokalt.
Data lagras lokalt i `*.tsf` filer i det lokala arkivet för diagnostikdata. Olika typer av loggar samlas in i olika `.tsf` filer. Namnen liknar tabell namnen i Azure Storage.

Till exempel `Performance Counters` hämtas i `PerformanceCountersTable.tsf` . Händelse loggar samlas in i `WindowsEventLogsTable.tsf` . Använd instruktionerna i avsnittet [lokal logg extrahering](#local-log-extraction) för att öppna de lokala samlings filerna och kontrol lera att de samlas in på disk.

Om du inte ser loggar som samlas in lokalt och redan har verifierat att värden genererar data, så har du troligen ett konfigurations problem. Granska konfigurationen noggrant.

Granska även konfigurationen som genererades för monitor inga Gent MaConfig.xml. Kontrol lera att det finns ett avsnitt som beskriver relevant logg källa. Kontrol lera sedan att den inte förloras i översättningen mellan diagnostikinställningar och övervaknings agent konfigurationen.

#### <a name="is-data-getting-transferred"></a>Överförs data?
Utför följande steg om du har verifierat att data samlas in lokalt men du fortfarande inte ser den i ditt lagrings konto:

- Kontrol lera att du har angett rätt lagrings konto och att du inte har registrerat över nycklar för det angivna lagrings kontot. För Azure Cloud Services ser vi ibland att människor inte uppdaterar `useDevelopmentStorage=true` .

- Kontrol lera att det angivna lagrings kontot är korrekt. Se till att du inte har nätverks begränsningar som förhindrar att komponenterna når offentliga lagrings slut punkter. Ett sätt att göra det är att fjärrans luta till datorn och sedan försöka skriva något till samma lagrings konto själv.

- Slutligen kan du titta på vilka problem som rapporteras av övervaknings agenten. Övervaknings agenten skriver sina loggar i `maeventtable.tsf` , som finns i det lokala arkivet för diagnostikdata. Följ anvisningarna i avsnittet [lokal logg extrahering](#local-log-extraction) för att öppna den här filen. Försök sedan att avgöra om det finns något som indikerar att det inte går att `errors` läsa lokala filer till lagringen.

### <a name="capturing-and-archiving-logs"></a>Samla in och arkivera loggar
Om du tänker på att kontakta supporten kan det första du ber dig att samla in loggar från din dator. Du kan spara tid genom att göra det själv. Kör `CollectGuestLogs.exe` verktyget i logg insamlingens verktygs Sök väg. Den genererar en zip-fil med alla relevanta Azure-loggar i samma mapp.

## <a name="diagnostics-data-tables-not-found"></a>Det gick inte att hitta diagnostikdatatabeller
Tabellerna i Azure Storage som innehåller ETW-händelser namnges med hjälp av följande kod:

```csharp
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

Här är ett exempel:

```XML
        <EtwEventSourceProviderConfiguration provider="prov1">
          <Event id="1" />
          <Event id="2" eventDestination="dest1" />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider="prov2">
          <DefaultEvents eventDestination="dest2" />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
Den här koden genererar fyra tabeller:

| Händelse | Tabellnamn |
| --- | --- |
| Provider = "Prov1" &lt; händelse-ID = "1"/&gt; |WADEvent + MD5 ("Prov1") + "1" |
| Provider = "Prov1" &lt; händelse-ID = "2" eventDestination = "dest1"/&gt; |WADdest1 |
| Provider = "Prov1" &lt; DefaultEvents/&gt; |WADDefault + MD5 ("Prov1") |
| Provider = "prov2" &lt; DefaultEvents eventDestination = "dest2"/&gt; |WADdest2 |

## <a name="references"></a>Referenser

### <a name="how-to-check-diagnostics-extension-configuration"></a>Så här kontrollerar du konfiguration av diagnostiskt tillägg
Det enklaste sättet att kontrol lera konfiguration av tillägg är att gå till [Azure Resource Explorer](https://resources.azure.com)och sedan gå till den virtuella datorn eller moln tjänsten där Azure-diagnostik tillägget (IaaSDiagnostics/PaaDiagnostics) är.

Alternativt kan du fjärrskriva Skriv bordet till datorn och titta på Azure-diagnostik konfigurations filen som beskrivs i avsnittet logg artefakter Sök väg.

I båda fallen kan du söka efter **Microsoft. Azure. Diagnostics** och sedan för fältet **xmlCfg** eller **WadCfg** .

Om du söker på en virtuell dator och fältet **WadCfg** finns, betyder det att config är i JSON-format. Om fältet **xmlCfg** finns, innebär det att config är i XML och Base64-kodad. Du måste [avkoda den](https://www.bing.com/search?q=base64+decoder) för att se XML-koden som lästes in av diagnostik.

Om du väljer konfiguration från disk för moln tjänst rollen är data base64-kodade, så du måste [avkoda det](https://www.bing.com/search?q=base64+decoder) för att se XML-koden som lästes in av diagnostik.

### <a name="azure-diagnostics-plugin-exit-codes"></a>Slut koder för Azure-diagnostik-plugin
Plugin-programmet returnerar följande avslutnings koder:

| Slutkod | Beskrivning |
| --- | --- |
| 0 |Åtgärden lyckades. |
| -1 |Allmänt fel. |
| -2 |Det gick inte att läsa in RCF-filen.<p>Det här interna felet bör bara inträffa om gäst agentens plugin-program startas manuellt på den virtuella datorn. |
| -3 |Det går inte att läsa in konfigurations filen för diagnostik.<p><p>Lösning: orsakas av en konfigurations fil som inte skickar schema validering. Lösningen är att tillhandahålla en konfigurations fil som följer schemat. |
| -4 |En annan instans av övervaknings agentens diagnostik använder redan den lokala resurs katalogen.<p><p>Lösning: Ange ett annat värde för **LocalResourceDirectory**. |
| −6 |Start programmet för gäst agents-plugin försökte starta diagnostik med en ogiltig kommando rad.<p><p>Det här interna felet bör bara inträffa om gäst agentens plugin-program startas manuellt på den virtuella datorn. |
| −10 |Diagnostik-plugin-programmet avslutades med ett ohanterat undantag. |
| -11 |Gäst agenten kunde inte skapa den process som ansvarar för att starta och övervaka övervaknings agenten.<p><p>Lösning: kontrol lera att det finns tillräckligt med system resurser för att starta nya processer.<p> |
| – 101 |Ogiltiga argument vid anrop till plugin-programmet för diagnostik.<p><p>Det här interna felet bör bara inträffa om gäst agentens plugin-program startas manuellt på den virtuella datorn. |
| – 102 |Det går inte att initiera plugin-processen.<p><p>Lösning: kontrol lera att det finns tillräckligt med system resurser för att starta nya processer. |
| – 103 |Det går inte att initiera plugin-processen. Mer specifikt kan du inte skapa objektet logga objekt.<p><p>Lösning: kontrol lera att det finns tillräckligt med system resurser för att starta nya processer. |
| – 104 |Det gick inte att läsa in RCF-filen från gäst agenten.<p><p>Det här interna felet bör bara inträffa om gäst agentens plugin-program startas manuellt på den virtuella datorn. |
| – 105 |Diagnostik-plugin-programmet kan inte öppna diagnostikens konfigurations fil.<p><p>Det här interna felet bör bara inträffa om diagnostikprogrammet-plugin-programmet startas manuellt på den virtuella datorn. |
| – 106 |Det går inte att läsa konfigurations filen för diagnostik.<p><p>Orsakas av en konfigurations fil som inte skickar schema validering. <br><br>Lösning: Ange en konfigurations fil som följer schemat. Mer information finns i [så här kontrollerar du konfiguration av diagnostiskt tillägg](#how-to-check-diagnostics-extension-configuration). |
| – 107 |Resurs katalogs passet i övervaknings agenten är ogiltigt.<p><p>Det här interna felet bör bara inträffa om övervaknings agenten startas manuellt på den virtuella datorn.</p> |
| – 108 |Det gick inte att konvertera konfigurations filen för diagnostik till övervaknings agentens konfigurations fil.<p><p>Det här interna felet bör bara inträffa om diagnostikprogrammet plugin-programmet startas manuellt med en ogiltig konfigurations fil. |
| – 110 |Konfigurations fel för allmän diagnostik.<p><p>Det här interna felet bör bara inträffa om diagnostikprogrammet plugin-programmet startas manuellt med en ogiltig konfigurations fil. |
| – 111 |Det gick inte att starta övervaknings agenten.<p><p>Lösning: kontrol lera att det finns tillräckligt med system resurser. |
| – 112 |Allmänt fel |

### <a name="local-log-extraction"></a>Lokal logg extrahering
Övervaknings agenten samlar in loggar och artefakter som `.tsf` filer. `.tsf`Filen kan inte läsas, men du kan konvertera den till följande `.csv` :

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
En ny fil `<relevantLogFile>.csv` som heter skapas i samma sökväg som motsvarande `.tsf` fil.

>[!NOTE]
> Du behöver bara köra det här verktyget mot filen main. TSF (till exempel PerformanceCountersTable. TSF). De medföljande filerna (till exempel PerformanceCountersTables_ \* \* 001. tsf, PerformanceCountersTables_ \* \* 002. TSF och så vidare) bearbetas automatiskt.

### <a name="more-about-missing-trace-logs"></a>Mer om spårnings loggar som saknas

>[!NOTE]
> Följande information gäller främst för Azure Cloud Services om du inte har konfigurerat DiagnosticsMonitorTraceListener för ett program som körs på den virtuella datorn IaaS.

- Kontrol lera att **DiagnosticMonitorTraceListener** har kon figurer ats i web.config eller app.config.  Detta konfigureras som standard i moln tjänst projekt. Men vissa kunder kommenterar ut det, vilket leder till att spårnings instruktionerna inte samlas in av diagnostik.

- Om loggarna inte skrivs från metoden **OnStart** eller **Run** kontrollerar du att **DiagnosticMonitorTraceListener** finns i app.config.  Som standard är det i web.config, men det gäller endast kod som körs i w3wp.exe. Så du behöver det i app.config för att kunna avbilda spårningar som körs i WaIISHost.exe.

- Kontrol lera att du använder **Diagnostics. trace. TraceXXX** i stället för **Diagnostics. debug. WriteXXX.** Fel söknings instruktionerna tas bort från en versions version.

- Kontrol lera att den kompilerade koden faktiskt har **diagnostik. trace-rader** (Använd reflektor, ildasm eller ILSpy för att verifiera). **Diagnostics. trace** -kommandon tas bort från den kompilerade binärfilen om du inte använder symbolen för villkorlig spårning för spårning. Detta är ett vanligt problem som uppstår när du använder MSBuild för att bygga ett projekt.   

## <a name="known-issues-and-mitigations"></a>Kända problem och begränsningar
Här är en lista över kända problem med kända begränsningar:

**1. .NET 4,5-beroende**

Windows Azure-diagnostik-tillägget har ett runtime-beroende på .NET 4,5 Framework eller senare. Vid tidpunkten för skrivning har alla datorer som är etablerade för Azure Cloud Services, samt alla officiella avbildningar som baseras på virtuella Azure-datorer, .NET 4,5 eller senare installerat.

Det är fortfarande möjligt att upptäcka en situation där du försöker köra Windows Azure-diagnostik-tillägget på en dator som inte har .NET 4,5 eller senare. Detta händer när du skapar en dator från en gammal avbildning eller ögonblicks bild eller när du använder en egen anpassad disk.

Detta manifest är vanligt vis som avslutnings kod **255** när **DiagnosticsPluginLauncher.exe körs.** Felet beror på följande ohanterade undantag:
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**Minskning:** Installera .NET 4,5 eller senare på datorn.

**2. prestanda räknar data är tillgängliga i lagring men visas inte i portalen**

Portal upplevelsen på de virtuella datorerna visar vissa prestanda räknare som standard. Om du inte ser prestanda räknarna och du vet att data genereras på grund av att de är tillgängliga i lagring, kontrollerar du följande:

- Om data i lagring har räknar namn på engelska. Om räknar namnen inte är på engelska kan Portal mått diagrammet inte identifiera det. **Minskning**: ändra datorns språk till engelska för system konton. Det gör du genom att välja inställningar för  >    >  **administrations**  >  **kopiering** på kontroll panelens region. Avmarkera sedan **välkomst skärmen och system konton** så att det anpassade språket inte tillämpas på system kontot.

- Om du använder jokertecken ( \* ) i prestanda räknar namnen kan portalen inte korrelera den konfigurerade och insamlade räknaren när prestanda räknarna skickas till Azure Storage Sink. **Minskning**: se till att du kan använda jokertecken och expandera ( \* ) genom att dirigera prestanda räknarna till Azure Monitor Sink.
