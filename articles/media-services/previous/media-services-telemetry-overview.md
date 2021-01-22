---
title: Azure Media Services telemetri | Microsoft Docs
description: Den här artikeln innehåller en översikt över Microsoft Azure Media Services telemetri.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 4bf9a96d7ffc3b939abe8cfb889c5bd49fee09cc
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98694629"
---
# <a name="azure-media-services-telemetry"></a>Azure Media Services telemetri  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services (AMS) gör det möjligt att komma åt telemetri/Mät data för sina tjänster. Med den aktuella versionen av AMS kan du samla in telemetri-data för entiteter i Live **Channel**, **StreamingEndpoint** och Live **Archive** . 

Telemetri skrivs till en lagrings tabell i ett Azure Storage konto som du anger (vanligt vis använder du det lagrings konto som är associerat med ditt AMS-konto). 

Telemetri systemet hanterar inte datakvarhållning. Du kan ta bort gamla telemetridata genom att ta bort lagrings tabellerna.

I det här avsnittet beskrivs hur du konfigurerar och använder AMS-telemetri.

## <a name="configuring-telemetry"></a>Konfigurera telemetri

Du kan konfigurera telemetri på en granularitet på komponent nivå. Det finns två detalj nivåer "normal" och "utförlig". För närvarande returnerar båda nivåerna samma information. Vi rekommenderar att du använder "normal. 

I följande avsnitt visas hur du aktiverar telemetri:

[Aktivera telemetri med .NET](media-services-dotnet-telemetry.md) 

[Aktivera telemetri med REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Förbrukar telemetri information

Telemetri skrivs till en Azure Storage tabell i det lagrings konto som du angav när du konfigurerade telemetri för Media Services-kontot. I det här avsnittet beskrivs lagrings tabeller för måtten.

Du kan använda telemetridata på något av följande sätt:

- Läs data direkt från Azure Table Storage (t. ex. med Storage SDK). Beskrivning av tabeller för telemetri-lagring finns i informationen om att använda **telemetri** i [det här](/previous-versions/azure/mt742089(v=azure.100)) avsnittet.

Eller

- Använd supporten i Media Services .NET SDK för att läsa lagrings data, enligt beskrivningen i [det här](media-services-dotnet-telemetry.md) avsnittet. 


Telemetri-schemat som beskrivs nedan är utformat för att ge bästa prestanda inom gränserna för Azure Table Storage:

- Data partitioneras efter konto-ID och tjänst-ID för att tillåta telemetri från varje tjänst att frågas separat.
- Partitioner innehåller datum för att ge ett rimligt övre gränser för partitionens storlek.
- Rad nycklar är i omvänd ordning så att de senaste telemetri-objekten kan frågas om en specifik tjänst.

Detta bör göra att många av de vanligaste frågorna är effektiva:

- Parallell, oberoende nedladdning av data för separata tjänster.
- Hämtar alla data för en specifik tjänst inom ett datum intervall.
- Hämtar de senaste data för en tjänst.

### <a name="telemetry-table-storage-output-schema"></a>Schema för telemetri för lagring av tabeller

Telemetridata lagras i mängd i en tabell, "TelemetryMetrics20160321" där "20160321" är datum för den skapade tabellen. Telemetri-systemet skapar en separat tabell för varje ny dag baserat på 00:00 UTC. Tabellen används för att lagra återkommande värden, till exempel mata in bit hastighet inom ett angivet tidsintervall, skickade byte, osv. 

Egenskap|Värde|Exempel/anteckningar
---|---|---
PartitionKey|{konto-ID} _ {enhets-ID}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>Konto-ID ingår i partitionsnyckel för att förenkla arbets flöden där flera Media Services-konton skriver till samma lagrings konto.
RowKey|{sekunder till midnatt} _ {slump värde}|01688_00199<br/><br/>Rad nyckeln börjar med antalet sekunder till midnatt för att tillåta flest n format frågor i en partition. Mer information finns i [den här](../../cosmos-db/table-storage-design-guide.md#log-tail-pattern) artikeln. 
Timestamp|Datum/tid|Automatisk tidsstämpel från Azure-tabellen 2016-09-09T22:43:42.241 Z
Typ|Typ av entitet som tillhandahåller telemetridata|Kanal/StreamingEndpoint/Arkiv<br/><br/>Händelse typen är bara ett sträng värde.
Name|Namnet på telemetri händelsen|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|Tiden då telemetri-händelsen inträffade (UTC)|2016-09-09T22:42:36.924 Z<br/><br/>Den observerade tiden tillhandahålls av den entitet som skickar Telemetrin (till exempel en kanal). Det kan finnas tidssynkroniserings problem mellan komponenter så att värdet är ungefärligt
ServiceID|{tjänst-ID}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Entitet-/regionsspecifika egenskaper|Som definieras av händelsen|StreamName: STREAM1, bit hastighet 10123,...<br/><br/>De återstående egenskaperna har definierats för den angivna händelse typen. Azure Table Content är nyckel värdes par.  (det vill säga olika rader i tabellen har olika uppsättningar med egenskaper).

### <a name="entity-specific-schema"></a>Entitet-Specific schema

Det finns tre typer av entiteter-speciella telemetriska data poster som publiceras med följande frekvens:

- Slut punkter för direkt uppspelning: var 30: e sekund
- Live-kanaler: varje minut
- Live-Arkiv: varje minut

**Slutpunkt för direktuppspelning**

Egenskap|Värde|Exempel
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Automatisk tidsstämpel från Azure Table 2016-09-09T22:43:42.241 Z
Typ|Typ|StreamingEndpoint
Namn|Namn|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|Tjänste-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|Slut punktens värdnamn|builddemoserver.origin.mediaservices.windows.net
StatusCode|Registrerar HTTP-status|200
ResultCode|Information om resultat kod|S_OK
RequestCount|Total begäran i agg regeringen|3
Bytes sent|Sammanställda byte skickade|2987358
ServerLatency|Genomsnittlig Server latens (inklusive lagring)|129
E2ELatency|Genomsnittlig svars tid från slut punkt till slut punkt|250

**Direktsänd kanal**

Egenskap|Värde|Exempel/anteckningar
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Automatisk tidsstämpel från Azure-tabellen 2016-09-09T22:43:42.241 Z
Typ|Typ|Kanal
Namn|Namn|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|Tjänste-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Typ av spår video/ljud/text|video/ljud
TrackName|Namn på spåret|video/audio_1
Hastigheten|Spåra bit hastighet|785000
CustomAttributes||   
IncomingBitrate|Faktisk inkommande bit hastighet|784548
OverlapCount|Överlappa i inmatningen|0
DiscontinuityCount|Diskontinuitet för spår|0
LastTimestamp|Senaste inmatade data tidsstämpel|1800488800
NonincreasingCount|Antal fragment som har tagits bort på grund av en icke-ökande tidsstämpel|2
UnalignedKeyFrames|Om vi har tagit emot fragment (mellan kvalitets nivåer) där nyckel ramar inte justeras |Sant
UnalignedPresentationTime|Om vi har tagit emot fragment (mellan kvalitets nivåer/spår) där presentations tiden inte är justerad|Sant
UnexpectedBitrate|Sant, om beräknad/faktisk bit hastighet för ljud-/video spår > 40 000 bps och IncomingBitrate = = 0 eller IncomingBitrate och actualBitrate skiljer sig med 50% |Sant
Felfri|Sant, om <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> är 0|Sant<br/><br/>Felfri är en sammansatt funktion som returnerar FALSE när något av följande villkor är uppfyllt:<br/><br/>-OverlapCount > 0<br/>-DiscontinuityCount > 0<br/>-NonincreasingCount > 0<br/>-UnalignedKeyFrames = = True<br/>-UnalignedPresentationTime = = True<br/>-UnexpectedBitrate = = True

**Live-Arkiv**

Egenskap|Värde|Exempel/anteckningar
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Automatisk tidsstämpel från Azure-tabellen 2016-09-09T22:43:42.241 Z
Typ|Typ|Arkiv
Namn|Namn|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924 Z
ServiceID|Tjänste-ID|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|Programmets URL|Asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4BD2-8c01-a92a2b38c9ba. ISM
TrackName|Namn på spåret|audio_1
TrackType|Typ av spår|Ljud/video
CustomAttribute|Hex-sträng som skiljer sig mellan olika spår med samma namn och bit hastighet (flera kamera vinklar)|
Hastigheten|Spåra bit hastighet|785000
Felfri|Sant, om FragmentDiscardedCount = = 0 && ArchiveAcquisitionError = = false|Sant (dessa två värden finns inte i måttet, men de finns i käll händelsen)<br/><br/>Felfri är en sammansatt funktion som returnerar FALSE när något av följande villkor är uppfyllt:<br/><br/>-FragmentDiscardedCount > 0<br/>-ArchiveAcquisitionError = = True

## <a name="general-qa"></a>Allmän Q&A

### <a name="how-to-consume-metrics-data"></a>Hur använder man mått data?

Mått data lagras som en serie Azure-tabeller i kundens lagrings konto. Dessa data kan förbrukas med följande verktyg:

- AMS SDK
- Microsoft Azure Storage Explorer (stöder export till kommaavgränsat värde format och bearbetas i Excel)
- REST-API

### <a name="how-to-find-average-bandwidth-consumption"></a>Hur hittar du den genomsnittliga bandbredds förbrukningen?

Den genomsnittliga bandbredds förbrukningen är genomsnittet av bytes sent under ett tidsintervall.

### <a name="how-to-define-streaming-unit-count"></a>Så här definierar du antalet enheter för strömning?

Antalet enheter för strömning kan definieras som högsta data flöde från tjänstens slut punkter för direkt uppspelning dividerat med det högsta data flödet för en slut punkt för direkt uppspelning. Det högsta användbara data flödet för en slut punkt för direkt uppspelning är 160 Mbit/s.
Anta till exempel att högsta data flöde från en kunds tjänst är 40 Mbit/s (det maximala värdet för bytes sent under en tidsrymd). Antalet enheter för strömning är sedan lika med (40 Mbit/s) * (8 bitar/byte)/(160 Mbit/s) = 2 strömnings enheter.

### <a name="how-to-find-average-requestssecond"></a>Hur hittar du genomsnittliga begär Anden per sekund?

Om du vill hitta det genomsnittliga antalet begär Anden per sekund beräknar du det genomsnittliga antalet förfrågningar (RequestCount) under en tids period.

### <a name="how-to-define-channel-health"></a>Hur definierar jag kanal hälsan?

Kanal hälsan kan definieras som en sammansatt boolesk funktion så att den är falsk när något av följande villkor är uppfyllt:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames = = True 
- UnalignedPresentationTime = = True 
- UnexpectedBitrate = = True


### <a name="how-to-detect-discontinuities"></a>Så här identifierar du discontinuities?

Om du vill identifiera discontinuities letar du reda på alla kanal data poster där DiscontinuityCount > 0. Motsvarande ObservedTime-tidsstämpel anger de tider då discontinuities uppstod.

### <a name="how-to-detect-timestamp-overlaps"></a>Hur hittar du överlappande tidsstämplar?

Om du vill identifiera tidsstämplar överlappar kan du söka efter alla kanal data poster där OverlapCount > 0. Motsvarande ObservedTime-tidsstämpel anger de tider då tids stämplingen överlappar.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Hur hittar du fel och orsaker till strömnings förfrågningar?

Om du vill hitta problem med strömmande begär Anden och orsaker hittar du alla data poster för strömnings slut punkter där ResultCode inte är lika med S_OK. I fältet motsvarande StatusCode anges orsaken till att begäran inte kunde köras.

### <a name="how-to-consume-data-with-external-tools"></a>Hur använder jag data med externa verktyg?

Multimetric-data kan bearbetas och visualiseras med följande verktyg:

- Power BI
- Application Insights
- Azure Monitor (tidigare sko)
- AMS Live-instrumentpanel
- Azure Portal (väntar på utgåva)

### <a name="how-to-manage-data-retention"></a>Hur hanterar man datakvarhållning?

Telemetri systemet tillhandahåller inte hantering av datakvarhållning eller automatiskt borttagning av gamla poster. Därför måste du hantera och ta bort gamla poster manuellt från lagrings tabellen. Du kan referera till Storage SDK för hur du gör det.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
