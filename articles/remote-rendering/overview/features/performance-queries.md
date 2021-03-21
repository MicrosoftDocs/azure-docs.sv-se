---
title: Prestandafrågor på serversidan
description: Så här utför du prestanda frågor på Server sidan via API-anrop
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 30b8104a9596f0b32f731c507b513b204f5d1acd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594103"
---
# <a name="server-side-performance-queries"></a>Prestandafrågor på serversidan

Prestanda för bästa åter givning på servern är avgörande för stabila bild hastigheter och en bättre användar upplevelse. Det är viktigt att övervaka prestanda egenskaperna på servern noggrant och optimera vid behov. Prestanda data kan frågas via dedikerade API-funktioner.

Den mest påverkan som påverkar åter givnings prestanda är modellens indata. Du kan justera indata enligt beskrivningen i [Konfigurera modell konverteringen](../../how-tos/conversion/configure-model-conversion.md).

Program prestanda på klient sidan kan vara en Flask hals. Vi rekommenderar att du tar en djupgående analys av prestanda på klient sidan [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) .

## <a name="clientserver-timeline"></a>Tids linje för klient/server

Innan du går in på detalj information om de olika latens värdena är det ett förblickande att ha en titt på platserna mellan klienten och servern på tids linjen:

![Pipeline-tidslinje](./media/server-client-timeline.png)

Bilden visar hur:

* en *attityd beräkning* har inaktiverats av klienten vid konstant 60 – Hz bild hastighet (var 16,6 MS)
* servern börjar sedan rendera, baserat på attityd
* servern skickar tillbaka den kodade video avbildningen
* klienten avkodar avbildningen, utför en del processor-och GPU-arbete ovanpå den och visar sedan avbildningen

## <a name="frame-statistics-queries"></a>Frågor för ram statistik

Ram statistik ger viss information på hög nivå för den sista ramen, till exempel svars tid. De data som anges i `FrameStatistics` strukturen mäts på klient sidan, så API: et är ett synkront anrop:

```cs
void QueryFrameData(RenderingSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<RenderingSession> session)
{
    FrameStatistics frameStatistics;
    if (session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

Det hämtade `FrameStatistics` objektet innehåller följande medlemmar:

| Medlem | Förklaring |
|:-|:-|
| LatencyPoseToReceive | Svars tid från kamerans attityd uppskattning på klient enheten tills en server ram för den här attityden är helt tillgänglig för klient programmet. Det här värdet omfattar nätverks tur, Server åter givnings tid, video avkodning och darr-kompensation. Se **intervall 1 i bilden ovan.**|
| LatencyReceiveToPresent | Svars tid från tillgänglighet för en mottagen fjärrram tills klient appen anropar PresentFrame på processorn. Se **intervall 2 i bilden ovan.**|
| LatencyPresentToDisplay  | Svars tiden från att presentera en bild ruta på processorn tills skärmarna visas. Det här värdet omfattar klientens GPU-tid, eventuella ram-buffring som utförs av operativ systemet, omprojektion av maskin vara och enhets beroende bildskärms genomsökning. Se **intervall 3 i bilden ovan.**|
| TimeSinceLastPresent | Tiden mellan efterföljande anrop till PresentFrame på processorn. Värden som är större än visnings längden (till exempel 16,6 MS på en klient enhet med 60 Hz) indikerar problem som orsakas av att klient programmet inte avslutar sin processor belastning i tid.|
| VideoFramesReceived | Antalet ramar som tagits emot från servern under den senaste sekunden. |
| VideoFrameReusedCount | Antal mottagna ramar i den senaste sekunden som användes på enheten mer än en gång. Värden som inte är noll anger att bild rutorna måste återanvändas och projiceras antingen på grund av nätverks Darr eller överdriven Server åter givnings tid. |
| VideoFramesSkipped | Antal mottagna ramar i den senaste sekunden som avkodats, men som inte visas på skärmen, eftersom en nyare ram har anlänt. Värden som inte är noll anger att nätverks skakningar orsakade att flera ramar fördröjs och sedan anländer till klient enheten i en burst. |
| VideoFramesDiscarded | Mycket likt **VideoFramesSkipped**, men anledningen till att den ignoreras är att en ram har varit i så sent att den inte ens kan korreleras med väntande attityder längre. Om detta händer finns det en allvarlig nätverks konkurrens.|
| VideoFrameMinDelta | Minsta tid mellan två på varandra följande ramar som kommer under den senaste sekunden. Tillsammans med VideoFrameMaxDelta ger detta intervall en indikation på Darr som orsakas antingen av nätverks-eller video-codecen. |
| VideoFrameMaxDelta | Maximal tid mellan två på varandra följande ramar som kommer under den senaste sekunden. Tillsammans med VideoFrameMinDelta ger detta intervall en indikation på Darr som orsakas antingen av nätverks-eller video-codecen. |

Summan av alla latens-värden är vanligt vis mycket större än den tillgängliga bild perioden vid 60 Hz. Det här är OK eftersom flera ramar håller på att vara parallella och nya begär Anden om ramar inaktive ras enligt önskad bild Rute hastighet, som visas i bilden. Men om fördröjningen blir för stor, påverkar det kvaliteten på [omprojektionen av den sena fasen](../../overview/features/late-stage-reprojection.md)och kan äventyra den övergripande upplevelsen.

`VideoFramesReceived`, `VideoFrameReusedCount` och `VideoFramesDiscarded` kan användas för att mäta nätverks-och Server prestanda. En kombination av ett lågt `VideoFramesReceived` värde och ett högt `VideoFrameReusedCount` värde kan indikera nätverks överbelastning eller dåliga Server prestanda. Ett högt `VideoFramesDiscarded` värde indikerar också överbelastning av nätverket.

Till sist,, `TimeSinceLastPresent` `VideoFrameMinDelta` och `VideoFrameMaxDelta` ger en uppfattning om var Ian sen för inkommande video bild rutor och lokala presentations samtal. Hög varians innebär instabil bild hastighet.

Inget av värdena ovan ger tydliga indikationer på en ren nätverks fördröjning (de röda pilarna i bilden), eftersom den exakta tiden som servern är upptagen med åter givning måste subtraheras från värdet för tur och retur `LatencyPoseToReceive` . Server sidan av den totala svars tiden är information som inte är tillgänglig för klienten. Nästa stycke förklarar dock hur det här värdet är ungefärligt genom ytterligare indatatyper från servern och exponeras genom `NetworkLatency` värdet.

## <a name="performance-assessment-queries"></a>Frågor om prestanda bedömning

*Frågor om prestanda bedömning* ger mer detaljerad information om processor-och GPU-arbetsbelastningen på servern. Eftersom data begärs från servern, efter fråga en ögonblicks bild av prestanda, följer det vanliga asynkrona mönstret:

```cs
async void QueryPerformanceAssessment(RenderingSession session)
{
    try
    {
        PerformanceAssessment result = await session.Connection.QueryServerPerformanceAssessmentAsync();
        // do something with result...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void QueryPerformanceAssessment(ApiHandle<RenderingSession> session)
{
    session->Connection()->QueryServerPerformanceAssessmentAsync([](Status status, PerformanceAssessment result) {
        if (status == Status::OK)
        {
            // do something with result...
        }
    });
}
```

`FrameStatistics`Objektet är i motsats till objektet och `PerformanceAssessment` innehåller information på Server sidan:

| Medlem | Förklaring |
|:-|:-|
| TimeCPU | Genomsnittlig CPU-tid per ram i millisekunder för Server |
| TimeGPU | Genomsnittlig GPU-tid per ram i millisekunder för Server |
| UtilizationCPU | Total processor användning i procent för Server |
| UtilizationGPU | Total GPU-användning i procent för Server |
| MemoryCPU | Total belastning på serverns huvud minne i procent |
| MemoryGPU | Total dedikerad video minnes användning i procent av serverns GPU |
| Nätverksfördröjning | Den ungefärliga genomsnittliga fördröjningen i millisekunder för nätverks fördröjningen. I bilden ovan motsvarar det här värdet summan av de röda pilarna. Värdet beräknas genom att dra ifrån den faktiska Server åter givnings tiden från `LatencyPoseToReceive` värdet för `FrameStatistics` . Även om den här uppskattningen inte är korrekt ger den en indikation på nätverks fördröjningen, isolerad från latens värden som beräknas på klienten. |
| PolygonsRendered | Antalet trianglar som återges i en ram. Det här talet inkluderar även de trianglar som slaktas senare under åter givning. Det innebär att det här talet inte varierar mycket mellan olika kamera lägen, men prestanda kan variera drastiskt beroende på triangelns culling-pris.|

För att hjälpa dig att utvärdera värdena kommer varje del att ha en kvalitets klassificering som **fantastiska**, **bra**, **mediocre** eller **dåligt**.
Detta bedömnings mått ger en grov indikation på serverns hälsa, men bör inte ses som absolut. Anta till exempel att du ser en "mediocre"-Poäng för GPU-tiden. Det betraktas som mediocre eftersom det ligger nära gränsen för den övergripande tids perioden för ramar. I så fall kan det dock vara ett användbart värde trots att du återger en komplex modell.

## <a name="statistics-debug-output"></a>Statistik fel söknings utdata

Klassen `ServiceStatistics` är en C#-klass som radbryts runt både ram statistik och prestanda utvärderings frågor och ger praktiska funktioner för att returnera statistik som sammanställda värden eller som en förskapad sträng. Följande kod är det enklaste sättet att visa statistik på Server sidan i klient programmet.

```cs
ServiceStatistics _stats = null;

void OnConnect()
{
    _stats = new ServiceStatistics();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

Koden ovan fyller i text etiketten med följande text:

![Utdata för ArrServiceStats-sträng](./media/arr-service-stats.png)

`GetStatsString`API: et formaterar en sträng med alla värden, men varje enskilt värde kan också frågas program mässigt från `ServiceStatistics` instansen.

Det finns också varianter av medlemmarna, som sammanställer värdena över tid. Se medlemmar med suffix `*Avg` , `*Max` eller `*Total` . Medlemmen `FramesUsedForAverage` anger hur många ramar som har använts för denna agg regering.

## <a name="api-documentation"></a>API-dokumentation

* [C# RenderingConnection. QueryServerPerformanceAssessmentAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.queryserverperformanceassessmentasync)
* [C++ RenderingConnection:: QueryServerPerformanceAssessmentAsync ()](/cpp/api/remote-rendering/renderingconnection#queryserverperformanceassessmentasync)

## <a name="next-steps"></a>Nästa steg

* [Skapa prestanda spårningar](../../how-tos/performance-tracing.md)
* [Konfigurera modell konverteringen](../../how-tos/conversion/configure-model-conversion.md)