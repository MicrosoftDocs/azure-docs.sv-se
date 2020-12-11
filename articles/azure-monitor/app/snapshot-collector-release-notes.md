---
title: Viktig information för Microsoft. ApplicationInsights. SnapshotCollector NuGet Package-Application Insights
description: Viktig information för paketet Microsoft. ApplicationInsights. SnapshotCollector NuGet som används av Application Insights Snapshot Debugger.
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: 4a787c6e2a9b59874f965a2bbcebea9ce02d8082
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093268"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Viktig information för Microsoft. ApplicationInsights. SnapshotCollector

Den här artikeln innehåller viktig information för Microsoft. ApplicationInsights. SnapshotCollector NuGet-paketet för .NET-program som används av Application Insights Snapshot Debugger.

[Läs](./snapshot-debugger.md) mer om Application Insights Snapshot debugger för .NET-program.

För fel rapporter och feedback, öppna ett ärende på GitHub på https://github.com/microsoft/ApplicationInsights-SnapshotCollector

## <a name="release-notes"></a>Viktig information

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
En punkt version som Backport en korrigering från 1.4.0-Pre.
### <a name="bug-fixes"></a>Felkorrigeringar
- Åtgärda [ObjectDisposedException vid avstängning](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097).

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
En punkt version som åtgärdar ett problem som upptäckts i testnings Azure App Service kundens kod för kod kopplings scenario.
### <a name="changes"></a>Ändringar
- Netcoreapp 3.0-målet är nu beroende av Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (tidigare >= 2.1.2).

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
En punkt version som åtgärdar ett par problem med hög påverkan.
### <a name="bug-fixes"></a>Felkorrigeringar
- Fast PDB-identifiering i mappen wwwroot/bin, som var bruten när vi ändrade algoritmen för symbol sökning i 1.3.6.
- Fast brus ExtractWasCalledMultipleTimesException i telemetri.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>Ändringar
- Netcoreapp 2.0-målet för SnapshotCollector är beroende av Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (igen). Detta återställer beteendet till hur det var innan 1.3.5. Vi försökte uppgradera det i 1.3.6, men du kan inte frigöra några Azure App Service scenarier.
### <a name="new-features"></a>Nya funktioner
- Snapshot Collector läser och tolkar ConnectionString från APPLICATIONINSIGHTS_CONNECTION_STRING-miljövariabeln eller från TelemetryConfiguration. I huvudsak används detta för att ange slut punkten för att ansluta till Snapshot-tjänsten. Mer information finns i dokumentationen för [anslutnings strängar](./sdk-connection-string.md).
### <a name="bug-fixes"></a>Felkorrigeringar
- Växlade till att använda HttpClient för alla mål utom Net45 eftersom webbegäran misslyckades i vissa miljöer på grund av en inkompatibel SecurityProtocol (kräver TLS 1,2).

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>Ändringar
- SnapshotCollector är nu beroende av Microsoft. ApplicationInsights >= 2.5.1 för alla mål ramverk. Detta kan vara en avbrytande ändring om programmet är beroende av en äldre version av Microsoft. ApplicationInsights SDK.
- Ta bort stödet för TLS 1,0 och 1,1 i överföring av ögonblicks bilder.
- Perioden för PDB-sökningar nu är standard 24 timmar i stället för 15 minuter. Kan konfigureras via PdbRescanInterval på SnapshotCollectorConfiguration.
- PDB-skanning söker endast i mappar på den översta nivån, i stället för rekursiva. Detta kan vara en avbrytande ändring om dina symboler finns i undermappar i den binära mappen.
### <a name="new-features"></a>Nya funktioner
- Logga rotation i SnapshotUploader för att undvika att fylla i loggmappen-mappen med gamla filer.
- Stöd för deoptimering (via ReJIT vid anslutning) för .NET Core 3,0-program.
- Lägg till symboler i NuGet-paketet.
- Ange ytterligare metadata när du laddar upp minidumpar.
- En initierad egenskap har lagts till i SnapshotCollectorTelemetryProcessor. Det är en CancellationToken som kommer att avbrytas när Snapshot Collector är fullständigt initierad och ansluten till tjänstens slut punkt.
- Ögonblicks bilder kan nu fångas för undantag i dynamiskt genererade metoder. Till exempel skapas de kompilerade uttrycks träden av Entity Framework frågor.
### <a name="bug-fixes"></a>Felkorrigeringar
- AmbiguousMatchException läser in Snapshot Collector på grund av Statusövervakare.
- GetSnapshotCollector-tilläggs metoden söker nu igenom alla TelemetrySinks.
- Starta inte uppladdning av ögonblicks bilder på plattformar som inte stöds.
- Hantera InvalidOperationException när du deoptimerar dynamiska metoder (till exempel Entity Framework)

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- Lägg till stöd för suveräna moln (äldre versioner fungerar inte i suveräna moln)
- Det blir enklare att lägga till ögonblicks bilds insamling med AddSnapshotCollector (). Mer information hittar du [här](./snapshot-debugger-appservice.md).
- Använd FISMA MD5-inställningen för att verifiera blob-block. Detta förhindrar standardalgoritmen för .NET MD5-kryptografi, som inte är tillgänglig när operativ systemet är inställt på FIPS-kompatibelt läge.
- Ignorera .NET Framework ramar när du deoptimerar funktions anrop. Detta beteende kan styras av konfigurations inställningen DeoptimizeIgnoredModules.
- Lägg till `DeoptimizeMethodCount` konfigurations inställning som tillåter deoptimering av fler än ett funktions anrop. Mer information finns här

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- Tillåt strukturerade Instrumentation-nycklar.
- Öka SnapshotUploader robusthet – Fortsätt start även om det inte går att flytta gamla överförings loggar.
- Återaktiverad rapportering av ytterligare telemetri när SnapshotUploader.exe avslutas omedelbart (inaktiverades i 1.3.3).
- Förenkla intern telemetri.
- _Experimentell funktion_: Snappoint samlings planer: Lägg till "snapshotOnFirstOccurence". Mer information finns [här](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- Åtgärdat fel som gjorde att SnapshotUploader.exe slutade svara och inte överför ögonblicks bilder för .NET Core-appar.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _Experimentell funktion_: Snappoint samlings planer. Mer information finns [här](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).
- SnapshotUploader.exe avslutas när körningen tar bort den AppDomain som SnapshotCollector läses in från, i stället för att vänta på att processen avslutas. Detta förbättrar insamlarens tillförlitlighet när IIS körs.
- Lägg till konfiguration för att tillåta flera SnapshotCollector-instanser som använder samma Instrumentation-nyckel för att dela samma SnapshotUploader-process: ShareUploaderProcess (standardvärdet `true` ).
- Rapportera ytterligare telemetri när SnapshotUploader.exe avslutas direkt.
- Minska antalet stödfiler SnapshotUploader.exe behöver skriva till disk.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- Ta bort stöd för att samla in ögonblicks bilder med RtlCloneUserProcess-API: et och stöder bara PssCaptureSnapshots API.
- Öka standard gränsen för hur många ögonblicks bilder som kan samlas in på 10 minuter från 1 till 3.
- Tillåt SnapshotUploader.exe att förhandla TLS 1,1 och 1,2
- Rapportera ytterligare telemetri när SnapshotUploader loggar en varning eller ett fel
- Sluta ta ögonblicks bilder när backend-tjänsten rapporterar att den dagliga kvoten nåtts (50 ögonblicks bilder per dag)
- Lägg till extra inchecknings SnapshotUploader.exe så att två instanser inte kan köras samtidigt.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>Ändringar
- För program som är riktade till .NET Framework är Snapshot Collector nu beroende av Microsoft. ApplicationInsights-version 2.3.0 eller senare.
Den används för att vara 2.2.0 eller högre.
Vi tror att detta inte är ett problem för de flesta program, men låt oss veta om den här ändringen förhindrar att du använder den senaste Snapshot Collector.
- Använd exponentiella fördröjningar i överföring av ögonblicks bilder när du försöker utföra misslyckade överföringar igen.
- Använd ServerTelemetryChannel (om tillgängligt) för mer tillförlitlig rapportering av telemetri.
- Använd "SdkInternalOperationsMonitor" på den första anslutningen till Snapshot Debugger tjänsten så att den ignoreras av beroende spårning.
- Förbättra Telemetrin runt den inledande anslutningen till Snapshot Debugger tjänsten.
- Rapportera ytterligare telemetri för:
  - Azure App Service version.
  - Azure Compute instances.
  - Artiklar.
  - Azure Function-app.
### <a name="bug-fixes"></a>Felkorrigeringar
- När återställnings intervallet för problem räknaren är inställt på 24 dagar tolkar du det som 24 timmar.
- En bugg har åtgärd ATS där överföring av ögonblicks bilder skulle upphöra att bearbeta nya ögonblicks bilder om ett undantag inträffade när en ögonblicks bild togs bort.

## <a name="123"></a>[nedan](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- Åtgärda starkt namn signering med binärfiler för uppladdning av ögonblicks bilder.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>Ändringar
- Filerna som krävs för SnapshotUploader (64). exe är nu inbäddade som resurser i huvud-DLL-filen. Det innebär att SnapshotCollectorFiles-mappen inte längre skapas, fören klar bygge och distribution och minskar oredan i Solution Explorer. Var försiktig när du uppgraderar för att granska ändringarna i `.csproj` filen. `Microsoft.ApplicationInsights.SnapshotCollector.targets`Filen behövs inte längre.
- Telemetri loggas till din Application Insights-resurs även om ProvideAnonymousTelemetry har angetts till false. Detta är så att vi kan implementera en hälso kontroll funktion i Azure Portal. ProvideAnonymousTelemetry påverkar endast telemetri som skickas till Microsoft för produkt support och förbättringar.
- När TempFolder eller ShadowCopyFolder omdirigeras till miljövariabler bör insamlaren vara inaktiv tills dessa miljövariabler har angetts.
- För program som ansluter till Internet via en proxyserver identifierar Snapshot Collector nu automatiskt eventuella proxyinställningar och skickar dem vidare till SnapshotUploader.exe.
- Minska SnapshotUplaoder-processens prioritet (där det är möjligt). Den här prioriteten kan åsidosättas via alternativet IsLowPrioirtySnapshotUploader.
- Du har lagt till en GetSnapshotCollector-tilläggs metod på TelemetryConfiguration för scenarier där du vill konfigurera Snapshot Collector program mässigt.
- Ange Application Insights SDK-version (i stället för program versionen) i kundorienterad telemetri.
- Skicka den första pulsslags händelsen efter två minuter.
### <a name="bug-fixes"></a>Felkorrigeringar
- Åtgärda NullReferenceException när undantag har null eller oföränderliga data ord listor.
- I överförings försöket gör du ett nytt försök med PDB-matchningen om vi får ett delnings fel.
- Åtgärda dubblett av telemetri när fler än en tråd anropar i den telemetri pipelinen vid start.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>Ändringar
- Kommentars filen för XML-dokument ingår nu i NuGet-paketet.
- Har lagt till en ExcludeFromSnapshotting-tilläggs metod i `System.Exception` för scenarier där du vet att du har ett brus undantag och vill undvika att skapa ögonblicks bilder för det.
- En konfigurations egenskap för IsEnabledWhenProfiling har lagts till som standard som true. Detta är en ändring från tidigare versioner där ögonblicks bilds skapande tillfälligt inaktiverades om Application Insights Profiler utförde en detaljerad samling. Det gamla beteendet kan återställas genom att ange den här egenskapen till falsk.
### <a name="bug-fixes"></a>Felkorrigeringar
- Signera SnapshotUploader64.exe korrekt.
- Skydda mot dubbel initiering av telemetri-processorn.
- Förhindra dubbel loggning av telemetri i appar med flera pipeliner.
- Åtgärda ett fel med förfallo tiden för en samlings plan, vilket kan förhindra ögonblicks bilder efter 24 timmar.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
Den största ändringen i den här versionen (därför flyttas till ett nytt lägre versions nummer) är en omskrivning av pipelinen för ögonblicks bild skapande och hantering. I tidigare versioner implementerades den här funktionen i intern kod (ProductionBreakpoints *. dll och SnapshotHolder*. exe). Den nya implementeringen är all hanterad kod med P/Invokes. För den första versionen som använder den nya pipelinen har vi inte överbelagts mycket från det ursprungliga beteendet. Den nya implementeringen ger bättre fel rapportering och ställer in oss för framtida förbättringar.

### <a name="other-changes-in-this-version"></a>Andra ändringar i den här versionen
- MinidumpUploader.exe har bytt namn till SnapshotUploader.exe (eller SnapshotUploader64.exe).
- Tids telemetri har lagts till för att optimera/återoptimera begär Anden.
- Gzip-komprimering har lagts till för Minidump-uppladdningar.
- Ett problem har åtgärd ATS där PDBs låstes för att förhindra plats uppgradering.
- Logga namnet på den ursprungliga mappen (SnapshotCollectorFiles) vid skugg kopiering.
- Justera minnes gränserna för 64-bitars processer för att förhindra att platsen startas om på grund av OOM.
- Åtgärda ett problem där ögonblicks bilder fortfarande samlades in trots att de har inaktiverats.
- Logga pulsslags händelser till kundens AI-resurs.
- Förbättra ögonblicks bilds hastigheten genom att ta bort källan från problem-ID: t.

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>Ändringar
Förhöjd användning av telemetri
- Identifiera och rapportera .NET-version och OS
- Identifiera och rapportera ytterligare Azure-miljöer (moln tjänst, Service Fabric)
- Registrera och rapportera undantags mått (antal första slump undantag och antalet TrackException-anrop) i pulsslag-telemetri.
### <a name="bug-fixes"></a>Felkorrigeringar
- Korrekt hantering av SqlException där det inre undantaget (Win32Exception) inte har utlösts.
- Trimma efterföljande blank steg på symboler, vilket orsakade ett felaktigt parsning av kommando rads argument till MinidumpUploader.
- Förhindra oändligt återförsök för misslyckade anslutningar till Snapshot Debugger agentens slut punkt.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>Ändringar
- Minnes skyddet för värd har lagts till. Den här funktionen minskar datorns minnes storlek.
- Förbättra visnings upplevelsen för Azure Portal bilder.