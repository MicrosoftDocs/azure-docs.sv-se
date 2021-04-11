---
title: Azure Event Grid scheman för Media Services händelser
description: Lär dig mer om de egenskaper som har angetts för Media Services händelser med Azure Event Grid.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: reference
ms.date: 03/17/2021
ms.author: inhenkel
ms.openlocfilehash: 6b9d3f6973c210e7bd91c088ee7a276bfbcc48ce
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121990"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure Event Grid scheman för Media Services händelser

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Den här artikeln innehåller scheman och egenskaper för Media Services händelser.

En lista över exempel skript och självstudier finns i [Media Services händelse källa](../../../event-grid/event-schema-subscriptions.md).

## <a name="job-related-event-types"></a>Jobb relaterade händelse typer

Media Services betecknar **jobbet** relaterade händelse typer som beskrivs nedan. Det finns två kategorier för projektrelaterade händelser: " **övervakning av jobb** status ändringar" och "ändringar av utdata för jobb status". 

Du kan registrera dig för alla händelser genom att prenumerera på JobStateChange-händelsen. Eller så kan du bara prenumerera på vissa händelser (till exempel Final-tillstånd som JobErrored, JobFinished och JobCanceled).   

### <a name="monitoring-job-state-changes"></a>Status ändringar för övervaknings jobb

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. Media. JobStateChange| Få en händelse för alla ändringar av jobb status. |
| Microsoft. Media. JobScheduled| Hämta en händelse när jobb över gångar till schemalagt tillstånd. |
| Microsoft. Media. JobProcessing| Få en händelse när jobb över gångar till bearbetnings tillstånd. |
| Microsoft. Media. JobCanceling| Hämta en händelse när jobb över gångar för att avbryta status. |
| Microsoft. Media. JobFinished| Hämta en händelse när jobb över gångar till klart läge. Detta är ett slutgiltigt tillstånd som innehåller jobb utmatningar.|
| Microsoft. Media. JobCanceled| Hämta en händelse när jobb över gångar till avbrutet tillstånd. Detta är ett slutgiltigt tillstånd som innehåller jobb utmatningar.|
| Microsoft. Media. JobErrored| Hämta en händelse när jobb över gångar till fel tillstånd. Detta är ett slutgiltigt tillstånd som innehåller jobb utmatningar.|

Se [schema exempel](#event-schema-examples) som följer.

### <a name="monitoring-job-output-state-changes"></a>Status ändringar för övervaknings jobb

Ett jobb kan innehålla flera jobb utdata (om du har konfigurerat transformeringen så att den har flera jobb utdata.) Om du vill spåra information om det enskilda jobbets utdata ska du lyssna efter en händelse för jobbets utgående ändring.

Varje **jobb** kommer att finnas på en högre nivå än **JobOutput**, vilket innebär att jobb utmatnings händelser utlöses i ett motsvarande jobb. 

Fel meddelandena i `JobFinished` , `JobCanceled` , ger `JobError` ut de sammanställda resultaten för varje jobb-utdata – när alla är klara. De jobb utmatnings händelser som utlöses när varje aktivitet slutförs. Om du till exempel har en kodning av utdata, följt av video analys, så får du två händelser som utgångs händelser innan den slutliga JobFinished-händelsen utlöses med sammanställda data.

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. Media. JobOutputStateChange| Få en händelse för alla ändringar i jobbets utdata. |
| Microsoft. Media. JobOutputScheduled| Hämta en händelse när jobbets utdata övergår till schemalagt tillstånd. |
| Microsoft. Media. JobOutputProcessing| Få en händelse när jobbets utdata övergår till bearbetnings tillstånd. |
| Microsoft. Media. JobOutputCanceling| Hämta en händelse när jobbets utdata övergår till att avbryta status.|
| Microsoft. Media. JobOutputFinished| Hämta en händelse när jobbets utdata övergår till klart läge.|
| Microsoft. Media. JobOutputCanceled| Hämta en händelse när jobbets utdata övergår till avbrutet tillstånd.|
| Microsoft. Media. JobOutputErrored| Hämta en händelse när jobbets utdata övergår till fel tillstånd.|

Se [schema exempel](#event-schema-examples) som följer.

### <a name="monitoring-job-output-progress"></a>Förlopp för övervaknings jobbets utdata

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. Media. JobOutputProgress| Den här händelsen visar jobb processens förlopp, från 0 till 100%. Tjänsten försöker skicka en händelse om det har funnits 5% eller högre ökning i förlopp svärdet eller har varit över 30 sekunder sedan den senaste händelsen (pulsslag). Värdet för förloppet är inte garanterat att starta vid 0% eller för att uppnå 100% och det är inte säkert att öka med en konstant hastighet över tid. Den här händelsen bör inte användas för att fastställa att bearbetningen har slutförts. i stället bör du använda tillstånds ändrings händelser.|

Se [schema exempel](#event-schema-examples) som följer.

## <a name="live-event-types"></a>Direktsända händelse typer

Media Services anger också de **direktsända** händelse typer som beskrivs nedan. Det finns två kategorier för **Live** -händelser: händelser på ström nivå händelser och spår nivå händelser. 

### <a name="stream-level-events"></a>Händelser på ström nivå

Händelser på ström nivå höjs per Stream eller anslutning. Varje händelse har en `StreamId` parameter som identifierar anslutningen eller data strömmen. Varje data ström eller anslutning har en eller flera spår av olika typer. En anslutning från en kodare kan till exempel ha ett ljud spår och fyra video spår. Händelse typerna för data strömmen är:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. Media. LiveEventConnectionRejected | Kodarens anslutnings försök avvisades. |
| Microsoft. Media. LiveEventEncoderConnected | Kodare upprättar en anslutning med live event. |
| Microsoft. Media. LiveEventEncoderDisconnected | Kodaren kopplar från. |

Se [schema exempel](#event-schema-examples) som följer.

### <a name="track-level-events"></a>Händelser på spår nivå

Händelser på spår nivå höjs per spår. 

> [!NOTE]
> Alla händelser på spårnivå utlöses efter att en Live-kodare är ansluten.

Händelse typerna på spårnings nivå är:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. Media. LiveEventIncomingDataChunkDropped | Medie servern släpper data segmentet eftersom det är för sent eller har en överlappande tidstämpel (tidsstämpel för det nya data segmentet är mindre än slut tiden för det föregående data segmentet). |
| Microsoft. Media. LiveEventIncomingStreamReceived | Media Server tar emot första data segment för varje spår i strömmen eller anslutningen. |
| Microsoft. Media. LiveEventIncomingStreamsOutOfSync | Medie servern identifierar ljud-och video strömmar som inte är synkroniserade. Använd som varning eftersom användar upplevelsen kanske inte påverkas. |
| Microsoft. Media. LiveEventIncomingVideoStreamsOutOfSync | Media Server identifierar alla de två video strömmar som kommer från den externa kodaren är osynkroniserade. Använd som varning eftersom användar upplevelsen kanske inte påverkas. |
| Microsoft. Media. LiveEventIngestHeartbeat | Publiceras var 20: e sekund för varje spår när direktsänd händelse körs. Innehåller en sammanfattning av Inhämtnings hälsa.<br/><br/>När kodaren ursprungligen anslöt fortsätter pulsslags händelsen att släppas var 20: a om kodaren fortfarande är ansluten eller inte. |
| Microsoft. Media. LiveEventTrackDiscontinuityDetected | Media Server identifierar diskontinuitet i det inkommande spåret. |

Se [schema exempel](#event-schema-examples) som följer.

## <a name="event-schema-examples"></a>Exempel på händelse scheman

### <a name="jobstatechange"></a>JobStateChange

I följande exempel visas schemat för **JobStateChange** -händelsen: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| previousState | sträng | Jobbets tillstånd före händelsen. |
| state | sträng | Det nya läget för jobbet som meddelas i den här händelsen. Till exempel "schemalagd: jobbet är klart att starta" eller "slutfört: jobbet har slutförts".|

Där jobb status kan vara ett av värdena: *köade*, *schemalagda*, *bearbetade*, *avslutade*, *fel*, *avbruten*, *avbryter*

> [!NOTE]
> *Köade* finns bara i egenskapen **previousState** , men inte i egenskapen **State** .

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled, JobProcessing, JobCanceling

För varje icke-slutgiltig jobb tillstånds ändring (till exempel JobScheduled, JobProcessing, JobCanceling) ser exempel schemat ut ungefär så här:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished, JobCanceled, JobErrored

För varje slutlig jobb tillstånds ändring (till exempel JobFinished, JobCanceled, JobErrored) ser exempel schemat ut ungefär så här:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| utdata | Matris | Hämtar jobbets utdata.|

### <a name="joboutputstatechange"></a>JobOutputStateChange

I följande exempel visas schemat för **JobOutputStateChange** -händelsen:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled, JobOutputProcessing, JobOutputFinished, JobOutputCanceling, JobOutputCanceled, JobOutputErrored

För varje JobOutput tillstånds ändring ser exempel schemat ut ungefär så här:

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

Exempel schemat ser ut ungefär så här:

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

I följande exempel visas schemat för **LiveEventConnectionRejected** -händelsen: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| streamId | sträng | Identifierare för strömmen eller anslutningen. Kodare eller kund ansvarar för att lägga till detta ID i inmatnings-URL: en. |  
| ingest | sträng | Inmatnings-URL som tillhandahålls av Live-händelsen. |  
| encoderIp | sträng | Kodarens IP-adress. |
| encoderPort | sträng | Porten för kodaren från vilken strömmen kommer. |
| resultCode | sträng | Anledningen till att anslutningen avvisades. Resultat koderna visas i följande tabell. |

Du hittar fel resultat koderna i [fel koder för Live-händelser](../live-event-error-codes-reference.md).

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

I följande exempel visas schemat för **LiveEventEncoderConnected** -händelsen: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| streamId | sträng | Identifierare för strömmen eller anslutningen. Kodare eller kund ansvarar för att tillhandahålla detta ID i inmatnings-URL: en. |
| ingest | sträng | Inmatnings-URL som tillhandahålls av Live-händelsen. |
| encoderIp | sträng | Kodarens IP-adress. |
| encoderPort | sträng | Porten för kodaren från vilken strömmen kommer. |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

I följande exempel visas schemat för **LiveEventEncoderDisconnected** -händelsen: 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| streamId | sträng | Identifierare för strömmen eller anslutningen. Kodare eller kund ansvarar för att lägga till detta ID i inmatnings-URL: en. |  
| ingest | sträng | Inmatnings-URL som tillhandahålls av Live-händelsen. |  
| encoderIp | sträng | Kodarens IP-adress. |
| encoderPort | sträng | Porten för kodaren från vilken strömmen kommer. |
| resultCode | sträng | Orsaken till att kodaren kopplar från. Det kan vara en korrekt koppling eller från ett fel. Resultat koderna visas i följande tabell. |

Du hittar fel resultat koderna i [fel koder för Live-händelser](../live-event-error-codes-reference.md).

De korrekt resultat koderna för från koppling är:

| Resultatkod | Beskrivning |
| ----------- | ----------- |
| S_OK | Kodaren har kopplats från. |
| MPE_CLIENT_TERMINATED_SESSION | Kodare frånkopplad (RTMP). |
| MPE_CLIENT_DISCONNECTED | Kodare frånkopplad (FMP4). |
| MPI_REST_API_CHANNEL_RESET | Kommandot för kanal återställning tas emot. |
| MPI_REST_API_CHANNEL_STOP | Kommandot för att stoppa kanal togs emot. |
| MPI_REST_API_CHANNEL_STOP | En kanal genomgår underhåll. |
| MPI_STREAM_HIT_EOF | EOF-dataströmmen skickas av kodaren. |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

I följande exempel visas schemat för **LiveEventIncomingDataChunkDropped** -händelsen: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType | sträng | Typ av spår (ljud/video). |
| trackName | sträng | Spårets namn. |
| hastigheten | heltal | Ljudets bit hastighet. |
| timestamp | sträng | Data segmentets tidsstämpel har släppts. |
| tidsplan | sträng | Tidsstämpelns tids skala. |
| resultCode | sträng | Orsak till data segmentets Drop. **FragmentDrop_OverlapTimestamp** eller **FragmentDrop_NonIncreasingTimestamp**. |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

I följande exempel visas schemat för **LiveEventIncomingStreamReceived** -händelsen: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType | sträng | Typ av spår (ljud/video). |
| trackName | sträng | Namnet på spåret (antingen från kodaren eller, om det är RTMP, servern genererar i *TrackType_Bitrate* -format). |
| hastigheten | heltal | Ljudets bit hastighet. |
| ingest | sträng | Inmatnings-URL som tillhandahålls av Live-händelsen. |
| encoderIp | sträng  | Kodarens IP-adress. |
| encoderPort | sträng | Porten för kodaren från vilken strömmen kommer. |
| timestamp | sträng | Första tidsstämpeln för data segmentet togs emot. |
| tidsplan | sträng | Tids skalan där tidsstämpeln representeras. |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

I följande exempel visas schemat för **LiveEventIncomingStreamsOutOfSync** -händelsen: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| minLastTimestamp | sträng | Minsta antal senaste tidsstämplar bland alla spår (ljud eller video). |
| typeOfTrackWithMinLastTimestamp | sträng | Typ av spår (ljud eller video) med minst senaste tidsstämpel. |
| maxLastTimestamp | sträng | Max för alla tidsstämplar bland alla spår (ljud eller video). |
| typeOfTrackWithMaxLastTimestamp | sträng | Typ av spår (ljud eller video) med maximalt senaste tidsstämpel. |
| timescaleOfMinLastTimestamp| sträng | Hämtar tids skalan där "MinLastTimestamp" visas.|
| timescaleOfMaxLastTimestamp| sträng | Hämtar tids skalan där "MaxLastTimestamp" visas.|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

I följande exempel visas schemat för **LiveEventIncomingVideoStreamsOutOfSync** -händelsen: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| firstTimestamp | sträng | Tidsstämpeln togs emot för en av bildens spår/kvalitets nivåer av typen video. |
| firstDuration | sträng | Varaktigheten för data segmentet med den första tidsstämpeln. |
| secondTimestamp | sträng  | Tidsstämpeln togs emot för en annan spår/kvalitets nivå av typen video. |
| secondDuration | sträng | Varaktighet för data segmentet med en andra tidsstämpel. |
| tidsplan | sträng | Tids skala för tidsstämplar och varaktighet.|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

I följande exempel visas schemat för **LiveEventIngestHeartbeat** -händelsen: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType | sträng | Typ av spår (ljud/video). |
| trackName | sträng | Namnet på spåret (antingen från kodaren eller, om det är RTMP, servern genererar i *TrackType_Bitrate* -format). |
| hastigheten | heltal | Ljudets bit hastighet. |
| incomingBitrate | heltal | Beräknad bit hastighet baserat på data segment som kommer från Encoder. |
| lastTimestamp | sträng | Den senaste tidsstämpeln togs emot för ett spår under de senaste 20 sekunderna. |
| tidsplan | sträng | Tids skalan där tidsstämplar uttrycks. |
| overlapCount | heltal | Antalet data segment hade överlappande tidsstämplar de senaste 20 sekunderna. |
| discontinuityCount | heltal | Antal discontinuities som observerats under de senaste 20 sekunderna. |
| nonIncreasingCount | heltal | Antalet data segment med tidsstämplar som tidigare togs emot under de senaste 20 sekunderna. |
| unexpectedBitrate | boolesk | Om förväntade och faktiska bit hastigheter skiljer sig mer än den tillåtna gränsen under de senaste 20 sekunderna. Det är sant om och endast om, incomingBitrate >= 2 * bit hastighet eller incomingBitrate <= bit hastighet/2 eller IncomingBitrate = 0. |
| state | sträng | Live-händelsens tillstånd. |
| felfri | boolesk | Anger om inläsningen är felfri baserat på antal och flaggor. Felfri är sant om overlapCount = 0 && discontinuityCount = 0 && nonIncreasingCount = 0 && unexpectedBitrate = false. |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

I följande exempel visas schemat för **LiveEventTrackDiscontinuityDetected** -händelsen: 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| trackType | sträng | Typ av spår (ljud/video). |
| trackName | sträng | Namnet på spåret (antingen från kodaren eller, om det är RTMP, servern genererar i *TrackType_Bitrate* -format). |
| hastigheten | heltal | Ljudets bit hastighet. |
| previousTimestamp | sträng | Tidsstämpel för föregående fragment. |
| newTimestamp | sträng | Tidsstämpel för det aktuella fragmentet. |
| discontinuityGap | sträng | Mellanrummet mellan två tidsstämplar. |
| tidsplan | sträng | Tids skala i vilken både timestamp och diskontinuitets avstånd visas. |

### <a name="common-event-properties"></a>Vanliga händelse egenskaper

En händelse har följande data på översta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Avsnittet EventGrid. Den här egenskapen har resurs-ID: t för det Media Services kontot. |
| Ämne | sträng | Resurs Sök vägen för Media Servicess kanalen under Media Services kontot. Genom att sammanfoga ämnet och ämnet får du resurs-ID för jobbet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. Till exempel "Microsoft. Media. JobStateChange". |
| Händelsetid | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| id | sträng | Unikt ID för händelsen. |
| data | objekt | Media Services händelse data. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

## <a name="next-steps"></a>Nästa steg

[Registrera för jobb tillstånds ändrings händelser](../job-state-events-cli-how-to.md)

## <a name="see-also"></a>Se även

- [EventGrid .NET SDK som innehåller media service-händelser](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definitioner av Media Services händelser](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [Fel koder för Live-händelse](../live-event-error-codes-reference.md)
