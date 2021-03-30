---
title: Profilera produktions program i Azure med Application Insights Profiler
description: Identifiera den frekventa sökvägen i din webb server kod med en lågnivå profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0d3074d58560df5cb5bd6bdc2c0437a4be828918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86499400"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profilera produktions program i Azure med Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Aktivera Application Insights Profiler för ditt program

Azure Application Insights profiler tillhandahåller prestanda spårningar för program som körs i produktion i Azure. Profileraren fångar data automatiskt i skala utan att påverka användarna negativt. Profiler hjälper dig att identifiera den "frekvent" kod Sök väg som tar den längsta tid när den hanterar en viss webbegäran. 

Profiler fungerar med .NET-program som distribueras på följande Azure-tjänster. I länkarna nedan finns mer information om hur du aktiverar profiler för varje tjänst typ.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines och skalnings uppsättningar för virtuella datorer](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [För **hands version** ASP.NET Core Azure Linux Web Apps](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Om du har aktiverat profiler men inte ser spår, se vår [fel söknings guide](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Visa profiler-data

För att profiler ska kunna överföra spår måste ditt program aktivt hantera förfrågningar. Om du gör ett experiment kan du generera förfrågningar till din webbapp genom att använda [Application Insights prestanda testning](/vsts/load-test/app-service-web-app-performance-test). Om du nyligen har aktiverat profiler kan du köra ett kort belastnings test. När belastnings testet körs väljer du knappen **profil nu** i fönstret Profiler [ **Inställningar**](profiler-settings.md). När profiler körs slumpmässigt en gång per timme och under en varaktighet på två minuter. Om ditt program hanterar en stadig ström med begär Anden, laddar profileringen spår varje timme.

När programmet har tagit emot viss trafik och profileraren har tid att ladda upp spårningarna bör du ha spår att visa. Den här processen kan ta 5 till 10 minuter. Om du vill visa spårningar väljer du **vidta åtgärder** i fönstret **prestanda** och väljer sedan **spårnings** knappen för profiler.

![Förhands granskning av profiler i Application Insights prestanda fönstret][performance-blade]

Välj ett exempel för att visa en analys på kod nivå av tids åtgång för att köra begäran.

![Application Insights spårnings Utforskaren][trace-explorer]

Spårnings Utforskaren visar följande information:

* **Visa frekvent sökväg**: öppnar den största lövnoder-noden eller minst något nära. I de flesta fall är den här noden nära en prestanda Flask hals.
* **Etikett**: namnet på funktionen eller händelsen. Trädet visar en blandning av kod och händelser som har inträffat, till exempel SQL-och HTTP-händelser. Den översta händelsen representerar den övergripande varaktigheten för begäran.
* **Förfluten** tid: tidsintervall mellan åtgärdens början och åtgärdens slut.
* **När**: tiden då funktionen eller händelsen kördes i förhållande till andra funktioner.

## <a name="how-to-read-performance-data"></a>Läsa prestanda data

Microsoft Service profiler använder en kombination av provtagnings metoder och instrumentering för att analysera programmets prestanda. När detaljerad insamling pågår, exempel på instruktions pekaren för varje maskin-CPU var millisekund. Varje exempel fångar upp den fullständiga anrops stacken för den tråd som körs för tillfället. Den ger detaljerad information om vad tråden gjorde, på både en hög nivå och en låg abstraktions nivå. Tjänst profiler samlar också in andra händelser för att spåra aktivitets korrelation och orsakssamband, inklusive kontext växlings händelser, TPL-händelser (Task Parallel Library) och Thread pool-händelser.

Anrops stacken som visas i vyn tids linje är resultatet av sampling och Instrumentation. Eftersom varje exempel fångar upp den kompletta anrops stacken i tråden, innehåller den kod från Microsoft .NET Framework och från andra ramverk som du refererar till.

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>Objekt tilldelning (CLR! JIT- \_ ny eller CLR! JIT \_ -Newarr1)

**CLR! JIT- \_ ny** och **CLR! JIT- \_ Newarr1** är hjälp funktioner i .NET Framework som allokerar minne från en hanterad heap. **CLR! JIT \_ New** anropas när ett objekt tilldelas. **CLR! JIT- \_ Newarr1** anropas när en objekt mat ris allokeras. De här två funktionerna är vanligt vis snabba och tar relativt små mängder tid. Om **CLR! JIT- \_ ny** eller **CLR! JIT- \_ Newarr1** tar mycket tid på din tids linje, koden kan allokeras många objekt och förbruka betydande mängder minne.

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>Läser in kod (CLR! ThePreStub)

**CLR! ThePreStub** är en hjälp funktion i .NET Framework som förbereder koden som ska köras för första gången. Den här körningen inkluderar vanligt vis, men är inte begränsad till JIT-kompilering (just-in-Time). För varje C#-metod, **CLR! ThePreStub** bör anropas högst en gång under en process.

Om **CLR! ThePreStub** tar lång tid för en begäran är begäran den första för att köra metoden. Tiden för .NET Framework körning för att läsa in den första metoden är signifikant. Du kan överväga att använda en uppvärmnings-process som kör den delen av koden innan användarna får åtkomst till den eller fundera på att köra Native Image Generator (ngen.exe) på dina sammansättningar.

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>Lås konkurrens (CLR! JITutil \_ MonContention eller CLR! JITutil \_ MonEnterWorker)

**CLR! JITutil \_ MonContention** eller **CLR! JITutil \_ MonEnterWorker** anger att den aktuella tråden väntar på att ett lås ska släppas. Den här texten visas ofta när du kör en C# **lock** -instruktion, anropa metoden **Monitor. Enter** eller anropa en metod med attributet **MethodImplOptions. Synchronized** . Lås konkurrens uppstår vanligt vis när tråd _a_ skaffar ett lås och tråd _B_ försöker hämta samma lås innan tråd _a_ släpper det.

### <a name="loading-code-cold"></a><a id="ngencold"></a>Läser in kod ([kall])

Om metod namnet innehåller **[kall]**, till exempel **mscorlib.ni! [ KALL] system. Reflection. CustomAttribute. IsDefined**, .NET Framework runtime kör kod för första gången som inte är optimerad för den [guidade optimeringen av profilen](/cpp/build/profile-guided-optimizations). För varje metod bör den visas högst en gång under processen.

Om inläsning av kod tar lång tid för en begäran, är begäran den första för att köra den icke-optimerade delen av metoden. Överväg att använda en uppvärmnings-process som kör den delen av koden innan användarna får åtkomst till den.

### <a name="send-http-request"></a><a id="httpclientsend"></a>Skicka HTTP-begäran

Metoder som **httpclient. send** anger att koden väntar på att en http-begäran ska slutföras.

### <a name="database-operation"></a><a id="sqlcommand"></a>Databas åtgärd

Metoder som **SqlCommand.Exesöta** anger att koden väntar på att en databas åtgärd ska slutföras.

### <a name="waiting-await_time"></a><a id="await"></a>Väntar (väntar \_ tid)

**Väntar \_ TIME** anger att koden väntar på att en annan aktivitet ska slutföras. Den här fördröjningen sker vanligt vis med C# **AWAIT** -instruktionen. När koden **gör att en** C# **väntar**, tar tråden över och returnerar kontrollen till trådpoolen, och det finns ingen tråd som är blockerad och väntar på att slutföras. Den tråd som gjorde **AWAIT** är dock "blockerad" och väntar på att åtgärden ska slutföras. Instruktionen **AWAIT \_ Time** anger den blockerade tid som väntar på att aktiviteten ska slutföras.

### <a name="blocked-time"></a><a id="block"></a>Blockerad tid

**BLOCKED_TIME** anger att koden väntar på att en annan resurs ska vara tillgänglig. Det kan till exempel vara en väntan på ett synkroniseringsobjekt, för att en tråd ska vara tillgänglig eller för att en begäran ska slutföras.

### <a name="unmanaged-async"></a>Ohanterad asynkron

.NET Framework genererar ETW-händelser och skickar aktivitets-ID: n mellan trådar så att asynkrona anrop kan spåras över trådar. Ohanterad kod (inbyggd kod) och vissa äldre format för asynkron kod saknar dessa händelser och aktivitets-ID: t, så att profileraren inte kan avgöra vilken tråd och vilka funktioner som körs på tråden. Det här heter "ohanterad asynkron" i anrops stacken. Om du hämtar ETW-filen kan du kanske använda [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md)  för att få bättre insikt i vad som händer.

### <a name="cpu-time"></a><a id="cpu"></a>CPU-tid

PROCESSORN är upptagen med att köra anvisningarna.

### <a name="disk-time"></a><a id="disk"></a>Disk tid

Programmet utför disk åtgärder.

### <a name="network-time"></a><a id="network"></a>Nätverks tid

Programmet utför nätverks åtgärder.

### <a name="when-column"></a><a id="when"></a>När kolumn

Kolumnen **när** är en visualisering av hur inkluderade exempel som samlas in för en nod varierar över tid. Det totala intervallet för begäran är indelat i 32 tids buckets. Inkluderade exempel för noden samlas i dessa 32-buckets. Varje Bucket visas som en stapel. Stapelns höjd representerar ett skalat värde. För noder som är markerade **CPU_TIME** eller **BLOCKED_TIME**, eller om det finns en uppenbar relation för att konsumera en resurs (t. ex. en processor, disk eller tråd), representerar fältet förbrukningen av en av resurserna under Bucket. För dessa mått är det möjligt att få ett värde som är större än 100 procent genom att förbruka flera resurser. Om du till exempel använder, i genomsnitt, två processorer under ett intervall, får du 200 procent.

## <a name="limitations"></a>Begränsningar

Standardvärdet för kvarhållning av data är fem dagar. De maximala data som matas in per dag är 10 GB.

Det kostar inget att använda profilerings tjänsten. För att du ska kunna använda den måste din webbapp finnas på minst nivån Basic i Web Apps-funktionen i Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Algoritm för omkostnader och sampling

Profileraren kör slumpmässigt två minuter varje timme på varje virtuell dator som är värd för det program där profileraren har Aktiver ATS för att samla in spår. När profiler körs lägger den till mellan 5 och 15 procents processor belastning på servern.

## <a name="next-steps"></a>Nästa steg
Aktivera Application Insights Profiler för ditt Azure-program. Se även:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines och skalnings uppsättningar för virtuella datorer](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
