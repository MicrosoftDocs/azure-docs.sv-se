---
title: Granska Video Indexer utdata som genereras av v2 API – Azure
titleSuffix: Azure Media Services
description: 'I det här avsnittet granskas Azure Media Services Video Indexer utdata som genereras av v2-API: et.'
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 2ac7c3c2149ce43c860c7726381733ef377de8d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530747"
---
# <a name="examine-the-video-indexer-output"></a>Granska Video Indexer utdata

När en video indexeras skapar Video Indexer JSON-innehållet som innehåller information om de angivna video insikterna. Insikterna omfattar: avskrifter, OCRs, ansikten, ämnen, block osv. Varje Insight-typ innehåller instanser av tidsintervall som visas när insikten visas i videon. 

Du kan granska videons sammanfattade insikter visuellt genom att trycka på **uppspelnings** knappen på videon på [video Indexer](https://www.videoindexer.ai/) webbplats. 

Du kan också använda API: et genom att anropa API för att **Hämta video index** och svars status är OK. du får ett detaljerat JSON-utdata som svars innehållet.

![Insikter](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

I den här artikeln granskas Video Indexer utdata (JSON-innehåll). <br/>Information om vilka funktioner och insikter som är tillgängliga finns i [video Indexer insikter](video-indexer-overview.md#video-insights).

> [!NOTE]
> Förfallo datum för alla åtkomsttoken i Video Indexer är en timme.

## <a name="get-the-insights"></a>Hämta insikter

### <a name="insightsoutput-produced-in-the-websiteportal"></a>Insikter/utdata som produceras på webbplatsen/portalen

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
1. Hitta en video som visar resultatet du vill undersöka.
1. Tryck på **Spela upp**.
1. Välj fliken **insikter** (sammanfattande insikter) eller fliken **tids linje** (gör det möjligt att filtrera relevanta insikter).
1. Hämta artefakter och vad som är i dem.

Mer information finns i [Visa och redigera video insikter](video-indexer-view-edit.md).

## <a name="insightsoutput-produced-by-api"></a>Insikter/utdata som skapats av API

1. Hämta JSON-filen genom att anropa [Get video index API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)
1. Om du också är intresse rad av vissa artefakter anropar du [Hämta video artefakt hämtning URL-API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    I API-anropet anger du den begärda artefakt typen (OCR, ansikten, nyckel ramar osv.)

## <a name="root-elements-of-the-insights"></a>Rot element i insikter

|Name|Beskrivning|
|---|---|
|accountId|Spel listans konto-ID.|
|id|Spelnings listans ID.|
|name|Spel listans namn.|
|beskrivning|Spelnings listans beskrivning.|
|userName|Namnet på den användare som skapade spelnings listan.|
|Create|Tid för skapande av spelnings lista.|
|privacyMode|Spelnings listans sekretess läge (privat/offentlig).|
|state|Spelnings listans (överförd, bearbetning, bearbetad, misslyckad, karantän).|
|isOwned|Anger om spelnings listan skapades av den aktuella användaren.|
|isEditable|Anger om den aktuella användaren har behörighet att redigera spelnings listan.|
|isBase|Anger om spelnings listan är en grundläggande spelnings lista (en video) eller en spelnings lista med andra videor (härlett).|
|durationInSeconds|Spelnings listans totala varaktighet.|
|summarizedInsights|Innehåller en [summarizedInsights](#summarizedinsights).
|videor|En lista med [videor](#videos) som konstruerar spelnings listan.<br/>Om den här spelnings listan med tidsintervaller som skapats av andra videor (härlett), kommer videor i den här listan endast innehålla data från de inkluderade tidsintervallen.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

I det här avsnittet visas en sammanfattning av insikterna.

|Attribut | Beskrivning|
|---|---|
|name|Namnet på videon. Till exempel Azure Monitor.|
|id|ID för videon. Till exempel 63c6d532ff.|
|privacyMode|Din uppdelning kan ha ett av följande lägen: **privat**, **offentlig**. **Offentligt** – videon är synlig för alla i ditt konto och alla som har en länk till videon. **Privat** – videon är synlig för alla i ditt konto.|
|varaktighet|Innehåller en varaktighet som beskriver tiden en insikt inträffade. Varaktigheten är i sekunder.|
|thumbnailVideoId|ID för videon från vilken miniatyren togs.
|thumbnailId|Videons miniatyr-ID. För att få den faktiska miniatyr bilden, anropa [Get-thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) och pass IT ThumbnailVideoId och thumbnailId.|
|ansikten/animatedCharacters|Kan innehålla noll eller flera ansikten. Mer detaljerad information finns i [ansikten/animatedCharacters](#facesanimatedcharacters).|
|nyckelord|Kan innehålla noll eller flera nyckelord. Mer detaljerad information finns i [nyckelord](#keywords).|
|sentiment|Kan innehålla noll eller flera sentiment. Mer detaljerad information finns i [sentiment](#sentiments).|
|audioEffects| Kan innehålla noll eller flera audioEffects. Mer detaljerad information finns i [audioEffects](#audioeffects-public-preview).|
|Etiketter| Får innehålla noll eller flera etiketter. Mer information finns i [Etiketter](#labels).|
|varumärken| Kan innehålla noll eller flera varumärken. Mer detaljerad information finns i [varumärken](#brands).|
|uppgifterna | Mer detaljerad information finns i [statistik](#statistics).|
|känslor| Kan innehålla noll eller flera känslor. Mer detaljerad information finns i [känslor](#emotions).|
|avsnitt|Kan innehålla noll eller flera ämnen. [Ämnena](#topics) insikter.|

## <a name="videos"></a>videor

|Name|Beskrivning|
|---|---|
|accountId|Videons konto-ID.|
|id|Videons ID.|
|name|Videons namn.
|state|Videons tillstånd (överförd, bearbetning, bearbetad, misslyckad, karantän).|
|processingProgress|Bearbetnings förlopp under bearbetning (till exempel 20%).|
|failureCode|Felkoden om det inte gick att bearbeta (till exempel "UnsupportedFileType").|
|failureMessage|Fel meddelandet om det inte gick att bearbeta.|
|externalId|Videons externa ID (om det anges av användaren).|
|externalUrl|Videons externa URL (om den anges av användaren).|
|metadata|Videons externa metadata (om de anges av användaren).|
|isAdult|Anger om videon granskades manuellt och identifierats som en vuxen video.|
|Insights|Insights-objektet. Mer information finns i [insikter](#insights).|
|thumbnailId|Videons miniatyr-ID. Hämta det faktiska miniatyr samtalet [Get-thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) och skicka det till video-ID och thumbnailId.|
|publishedUrl|En URL för att strömma videon.|
|publishedUrlProxy|En URL för att strömma videon från (för Apple-enheter).|
|viewToken|En kort livs längd Visa token för att strömma videon.|
|sourceLanguage|Videons käll språk.|
|language|Videons faktiska språk (översättning).|
|indexingPreset|Den för inställning som används för att indexera videon.|
|streamingPreset|Den för inställning som används för att publicera videon.|
|linguisticModelId|CRI: er-modellen som används för att skriva av videon.|
|uppgifterna | Mer information finns i [statistik](#statistics).|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>Insights

Varje insikt (t. ex. avskrifts rader, ansikten, varumärken osv.) innehåller en lista över unika element (till exempel face1, face2, face3) och varje element har sina egna metadata och en lista över dess instanser (som är tidsintervaller med ytterligare valfria metadata).

En ansikte kan ha ett ID, ett namn, en miniatyr bild, andra metadata och en lista över dess temporala instanser (till exempel: 00:00:05 – 00:00:10, 00:01:00-00:02:30 och 00:41:21 – 00:41:49.) Varje temporal instans kan ha ytterligare metadata. Till exempel är facets Rectangle-koordinater (20230, 60, 60).

|Version|Kod versionen|
|---|---|
|sourceLanguage|Videons käll språk (vi antar ett huvud språk). I form av en [BCP-47-](https://tools.ietf.org/html/bcp47) sträng.|
|language|Insights-språket (översatt från käll språket). I form av en [BCP-47-](https://tools.ietf.org/html/bcp47) sträng.|
|avskrifts|[Avskrifts](#transcript) insikter.|
|stöd|[OCR](#ocr) -insikter.|
|nyckelord|[Nyckelorden](#keywords) insikter.|
|block|Kan innehålla ett eller flera [block](#blocks)|
|ansikten/animatedCharacters|[Ansikten/animatedCharacters](#facesanimatedcharacters) Insight.|
|Etiketter|[Etiketterna](#labels) insikter.|
|bilder|[Bilderna](#shots) insikter.|
|varumärken|Insikter om [varumärken](#brands) .|
|audioEffects|[AudioEffects](#audioeffects-public-preview) Insight.|
|sentiment|[Sentiment](#sentiments) Insight.|
|visualContentModeration|[VisualContentModeration](#visualcontentmoderation) Insight.|
|textualContentModeration|[TextualContentModeration](#textualcontentmoderation) Insight.|
|känslor| [Känslor](#emotions) Insight.|
|avsnitt|[Ämnena](#topics) insikter.|
|högtalare|Insikter om [talare](#speakers) .|

Exempel:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>block

Attribut | Beskrivning
---|---
id|ID för blocket.|
pipe|En lista över tidsintervallen för det här blocket.|

#### <a name="transcript"></a>avskrifts

|Name|Beskrivning|
|---|---|
|id|Rad-ID.|
|text|Själva avskriften.|
|konfidensbedömning|Avskrifts noggrannhet.|
|speakerId|ID för högtalaren.|
|language|Avskrifts språket. Avsett att stödja avskrifter där varje rad kan ha ett annat språk.|
|pipe|En lista med tidsintervaller där denna rad visades. Om instansen avskrifts har den bara en instans.|

Exempel:

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "text":"ignite 2016. Your mission at Microsoft is to empower every",
  "confidence":0.8944,
  "speakerId":2,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
```

#### <a name="ocr"></a>stöd

|Name|Beskrivning|
|---|---|
|id|ID för OCR-linje.|
|text|OCR-text.|
|konfidensbedömning|Igenkännings förtroendet.|
|language|OCR-språket.|
|pipe|En lista med tidsintervall där denna OCR visades (samma OCR kan förekomma flera gånger).|
|mankhöjd|Höjden på OCR-rektangeln|
|top|Den översta platsen i px|
|vänster| Den vänstra platsen i px|
|bredd|Bredden på OCR-rektangeln|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>nyckelord

|Name|Beskrivning|
|---|---|
|id|Nyckelords-ID: t.|
|text|Nyckelords texten.|
|konfidensbedömning|Nyckelordets tolknings säkerhet.|
|language|Nyckelords språket (vid översättning).|
|pipe|En lista med tidsintervall där det här nyckelordet visades (ett nyckelord kan visas flera gånger).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="facesanimatedcharacters"></a>ansikten/animatedCharacters

`animatedCharacters` elementet ersätts om `faces` videon indexerades med en animerad tecken modell. Detta görs med hjälp av en anpassad modell i Custom Vision Video Indexer kör den på nyckel rutor.

Om det finns ansikten (inte animerade tecken) använder Video Indexer Ansikts-API på alla video bild rutor för att identifiera ansikten och kändisar.

|Name|Beskrivning|
|---|---|
|id|Ansikts-ID.|
|name|Namnet på FACET. Det kan vara okänt #0, en identifierad kändis eller en kundutbildad person.|
|konfidensbedömning|Förtroende för ansikts identifiering.|
|beskrivning|En beskrivning av kändis. |
|thumbnailId|ID för miniatyr bilden för den aktuella ytan.|
|knownPersonId|Om det är en känd person, dess interna ID.|
|referenceId|Om det är en Bing-kändis, dess Bing-ID.|
|referenceType|För närvarande, bara Bing.|
|title|Om det är en kändis, dess titel (till exempel "Microsofts VD").|
|imageUrl|Om det är en kändis, dess bild-URL.|
|pipe|Detta är instanser av där ytan fanns inom det aktuella tidsintervallet. Varje instans har också en thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

#### <a name="labels"></a>Etiketter

|Name|Beskrivning|
|---|---|
|id|Etikett-ID: t.|
|name|Etikett namnet (till exempel "dator", "TV").|
|language|Etikettens namn språk (vid översättning). BCP-47|
|pipe|En lista med tidsintervall där etiketten visas (en etikett kan visas flera gånger). Varje instans har ett konfidens fält. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

#### <a name="scenes"></a>scen

|Name|Beskrivning|
|---|---|
|id|Scen-ID: t.|
|pipe|En lista med tidsintervall för den här scenen (en scen kan bara ha 1 instans).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>bilder

|Name|Beskrivning|
|---|---|
|id|Bild-ID.|
|Nyckel rutor|En lista med nyckel rutor i bilden (var och en har ett ID och en lista över instanser av instans intervallet). Varje instans av en nyckel ruta har ett thumbnailId-fält som innehåller nyckel rutans miniatyr-ID.|
|pipe|En lista över tidsintervallen för den här instansen (en skärmdump kan bara ha en instans).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>varumärken

Företags-och produkt märkes namn identifieras i tal till text avskrift och/eller video-OCR. Detta omfattar inte visuell igenkänning av varumärken eller logo typ identifiering.

|Name|Beskrivning|
|---|---|
|id|Varumärkes-ID.|
|name|Namn på varumärken.|
|referenceId | Suffixet för varumärkes Wikipedia-URL: en. Till exempel är "Target_Corporation" suffixet till [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|referenceUrl | Varumärkets Wikipedia-URL, om sådan finns. Till exempel [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|beskrivning|Beskrivningen av varumärkena.|
|tags|En lista med fördefinierade taggar som har associerats med det här varumärket.|
|konfidensbedömning|Konfidens värdet för Video Indexer varumärkes detektor (0-1).|
|pipe|En lista med tidsintervall för det här varumärket. Varje instans har en brandType, som anger om detta varumärke visas i avskriften eller i OCR.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>uppgifterna

|Name|Beskrivning|
|---|---|
|CorrespondenceCount|Antal korrespondens i videon.|
|SpeakerWordCount|Antalet ord per talare.|
|SpeakerNumberOfFragments|Mängden fragment som talare har i en video.|
|SpeakerLongestMonolog|Föredragets längsta monolog. Om högtalaren har tystnad i monolog är den inkluderad. Tystnad i början och slutet av monolog tas bort.| 
|SpeakerTalkToListenRatio|Beräkningen baseras på den tid som ägnats åt högtalar monolog (utan tystnad mellan) dividerat med videons totala tid. Tiden avrundas till det tredje decimal tecknet.|

#### <a name="audioeffects-public-preview"></a>audioEffects (offentlig för hands version)

|Name|Beskrivning
|---|---|
|id|Ljudets effekter-ID|
|typ|Typ av ljud påverkan|
|pipe|En lista med tidsintervaller där den här ljud påverkan visades. Varje instans har ett konfidens fält.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Siren",
    "instances": [
    {
       "confidence": 0.87,
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
       "confidence": 0.87,
       "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>sentiment

Sentiment sammanställs av deras sentimentType-fält (positiv/neutral/negativ). Till exempel 0-0,1, 0,1-0,2.

|Name|Beskrivning|
|---|---|
|id|Sentiment-ID.|
|averageScore |Medelvärdet av alla resultat från alla instanser av sentiment-typ positiv/neutral/negativ|
|pipe|En lista med tidsintervaller där denna sentiment visades.|
|sentimentType |Typen kan vara "positiv", "neutral" eller "negativ".|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

VisualContentModeration-blocket innehåller tidsintervall som Video Indexer hittade för att potentiellt ha vuxen innehåll. Om visualContentModeration är tomt finns det inget olämpligt innehåll som identifierats.

Videor som innehåller vuxen eller vågat innehåll kan endast vara tillgängliga för privat vy. Användare har möjlighet att skicka en begäran om en mänsklig granskning av innehållet, i så fall kan IsAdult-attributet innehålla resultatet av mänsklig granskning.

|Name|Beskrivning|
|---|---|
|id|ID för moderator för visuellt innehåll.|
|adultScore|Den vuxen poängen (från Content moderator).|
|racyScore|Vågat-poängen (från innehålls redigering).|
|pipe|En lista med tidsintervaller där den här visuella innehålls kontrollanten visades.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Name|Beskrivning|
|---|---|
|id|ID för text innehållets moderator.|
|bannedWordsCount |Antalet förbjudna ord.|
|bannedWordsRatio |Förhållandet från det totala antalet ord.|

#### <a name="emotions"></a>känslor

Video Indexer identifierar känslor baserat på tal-och ljud signaler. Den identifierade känslo kan vara: Joy, ledsenhet, ilska eller frukt.

|Name|Beskrivning|
|---|---|
|id|Känslo-ID.|
|typ|Känslo som identifierades baserat på tal-och ljud signaler. Känslo kan vara: Joy, ledsenhet, ilska eller frukt.|
|pipe|En lista med tidsintervaller där denna känslo visades.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>avsnitt

Video Indexer gör det lättare att utföra huvud ämnena i avskrifter. När det är möjligt ingår [IPTC](https://iptc.org/standards/media-topics/) -taxonomi på andra nivån. 

|Name|Beskrivning|
|---|---|
|id|Avsnitts-ID.|
|name|Ämnes namnet, till exempel: "farmaceutiska".|
|referenceId|Spår som reflekterar ämnes hierarkin. Till exempel: "hälsa och välbefinnande/medicin, sjukvård/farmaceutiska".|
|konfidensbedömning|Förtroende poängen i intervallet [0, 1]. Högre är mer tryggare.|
|language|Det språk som används i ämnet.|
|iptcName|IPTC-mediets kod namn, om det upptäcks.|
|pipe |För närvarande kan Video Indexer inte indexera ett ämne till tidsintervall, så hela videon används som intervall.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

#### <a name="speakers"></a>högtalare

|Name|Beskrivning|
|---|---|
|id|Högtalar-ID.|
|name|Högtalar namnet i formatet "talare # *<number>* ", till exempel: "talare #1".|
|pipe |En lista med tidsintervaller där denna talare visades.|

```json
"speakers":[
{
  "id":1,
  "name":"Speaker #1",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "name":"Speaker #2",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
` ` `
```
## <a name="next-steps"></a>Nästa steg

[Video Indexer Developer-portalen](https://api-portal.videoindexer.ai)

Information om hur du bäddar in widgetar i programmet finns i [bädda in video Indexer-widgetar i dina program](video-indexer-embed-widgets.md). 

