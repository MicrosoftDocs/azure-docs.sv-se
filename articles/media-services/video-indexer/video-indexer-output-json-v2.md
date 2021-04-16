---
title: Granska de Video Indexer som produceras av v2 API – Azure
titleSuffix: Azure Media Services
description: Det här avsnittet undersöker de Azure Media Services Video Indexer som produceras av v2-API:et.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 84bb4766b3a896823dd0bef023f8042965a85846
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532863"
---
# <a name="examine-the-video-indexer-output"></a>Granska Video Indexer utdata

När en video indexeras skapar Video Indexer JSON-innehåll som innehåller information om de angivna videoinsikterna. Insikterna omfattar: avskrifter, OCR, ansikten, ämnen, block osv. Varje insiktstyp innehåller instanser av tidsintervall som visar när insikten visas i videon. 

Du kan visuellt granska videons sammanfattade insikter genom att trycka på **knappen Spela** upp på videon på [Video Indexer](https://www.videoindexer.ai/) webbplats. 

Du kan också använda API:et genom att anropa API:et Get **Video Index** och svarsstatusen är OK. Du får detaljerade JSON-utdata som svarsinnehåll.

![Insikter](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Den här artikeln undersöker Video Indexer utdata (JSON-innehåll). <br/>Information om vilka funktioner och insikter som är tillgängliga för dig finns i [Video Indexer insikter.](video-indexer-overview.md#video-insights)

> [!NOTE]
> Förfallodatumet för alla åtkomsttoken i Video Indexer är en timme.

## <a name="get-the-insights"></a>Få insikterna

### <a name="insightsoutput-produced-in-the-websiteportal"></a>Insikter/utdata som skapas på webbplatsen/portalen

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
1. Hitta en video som du vill undersöka utdata från.
1. Tryck på **Spela upp**.
1. Välj fliken **Insikter** (sammanfattade insikter) eller fliken **Tidslinje** (tillåter att relevanta insikter filtreras).
1. Ladda ned artefakter och vad som finns i dem.

Mer information finns i [Visa och redigera videoinsikter.](video-indexer-view-edit.md)

## <a name="insightsoutput-produced-by-api"></a>Insikter/utdata som produceras av API

1. Hämta JSON-filen genom att anropa [Get Video Index API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Index)
1. Om du också är intresserad av specifika artefakter anropar du API:et [Hämta URL för videoartefakt](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Artifact-Download-Url)

    I API-anropet anger du den begärda artefakttypen (OCR, ansikten, nyckelbildrutor osv.)

## <a name="root-elements-of-the-insights"></a>Rotelement för insikterna

|Name|Beskrivning|
|---|---|
|accountId|Spellistans VI-konto-ID.|
|id|Spellistans ID.|
|name|Spellistans namn.|
|beskrivning|Spellistans beskrivning.|
|userName|Namnet på den användare som skapade spellistan.|
|Skapad|Spelningslistans skapandetid.|
|privacyMode|Spellistans sekretessläge (privat/offentligt).|
|state|Spellistans (uppladdad, bearbetad, misslyckad, i karantän).|
|isOwned|Anger om spellistan har skapats av den aktuella användaren.|
|isEditable|Anger om den aktuella användaren har behörighet att redigera spellistan.|
|isBase|Anger om spellistan är en basspelningslista (en video) eller en spellista som består av andra videor (härledda).|
|durationInSeconds|Den totala varaktigheten för spellistan.|
|summarizedInsights|Innehåller en [summarizedInsights](#summarizedinsights).
|videor|En lista över [videor som](#videos) skapar spellistan.<br/>Om den här spellistan innehåller tidsintervall för andra videor (härledda) innehåller videorna i den här listan endast data från de inkluderade tidsintervallen.|

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

Det här avsnittet visar en sammanfattning av insikterna.

|Attribut | Beskrivning|
|---|---|
|name|Namnet på videon. Till exempel Azure Monitor.|
|id|ID för videon. Till exempel 63c6d532ff.|
|privacyMode|Uppdelningen kan ha något av följande lägen: **Privat**, **Offentlig**. **Offentlig** – videon är synlig för alla i ditt konto och alla som har en länk till videon. **Privat** – videon är synlig för alla i ditt konto.|
|varaktighet|Innehåller en varaktighet som beskriver den tid då en insikt inträffade. Varaktigheten är i sekunder.|
|thumbnailVideoId|ID:t för videon som miniatyrbilden togs från.
|thumbnailId|Videons miniatyr-ID. Hämta den faktiska miniatyrbilden genom att anropa [Get-Thumbnail](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) och skicka den thumbnailVideoId och thumbnailId.|
|ansikten/animeradecharacters|Kan innehålla noll eller flera ansikten. Mer detaljerad information finns i [faces/animatedCharacters](#facesanimatedcharacters).|
|nyckelord|Kan innehålla noll eller flera nyckelord. Mer detaljerad information finns i [nyckelord.](#keywords)|
|Känslor|Kan innehålla noll eller flera attityder. Mer detaljerad information finns i [sentiment.](#sentiments)|
|audioEffects| Kan innehålla noll eller flera audioEffects. Mer detaljerad information finns i [audioEffects](#audioeffects-public-preview).|
|Etiketter| Kan innehålla noll eller flera etiketter. Mer information finns i [etiketterna](#labels).|
|Märken| Kan innehålla noll eller flera varumärken. Mer detaljerad information finns i [varumärken.](#brands)|
|Statistik | Mer detaljerad information finns i [statistik](#statistics).|
|Känslor| Kan innehålla noll eller flera känslor. Mer detaljerad information finns i [känslo känsloinformation.](#emotions)|
|Ämnen|Kan innehålla noll eller flera ämnen. [Avsnittsinsikter.](#topics)|

## <a name="videos"></a>videor

|Name|Beskrivning|
|---|---|
|accountId|Videons VI-konto-ID.|
|id|Videons ID.|
|name|Videons namn.
|state|Videons tillstånd (uppladdat, bearbetat, misslyckat, i karantän).|
|processingProgress|Bearbetningsförloppet under bearbetningen (till exempel 20 %).|
|failureCode|Felkoden om det inte gick att bearbeta (till exempel "UnsupportedFileType").|
|failureMessage|Felmeddelandet om det inte gick att bearbeta.|
|externalId|Videons externa ID (om det anges av användaren).|
|externalUrl|Videons externa URL (om det anges av användaren).|
|metadata|Videons externa metadata (om det anges av användaren).|
|isAdult|Anger om videon har granskats manuellt och identifierats som en video för vuxna.|
|Insikter|Insiktsobjektet. Mer information finns i [insikter.](#insights)|
|thumbnailId|Videons miniatyr-ID. För att hämta det faktiska [miniatyranropet Get-Thumbnail](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Thumbnail) och skicka det till video-ID och thumbnailId.|
|publishedUrl|En URL för att strömma videon.|
|publishedUrlProxy|En URL för att strömma videon från (för Apple-enheter).|
|viewToken|En kortlivad visningstoken för direktuppspelning av videon.|
|sourceLanguage|Videons källspråk.|
|language|Videons faktiska språk (översättning).|
|indexingPreset|Den förinställning som används för att indexera videon.|
|streamingPreset|Den förinställning som används för att publicera videon.|
|linguisticModelId|CRIS-modellen används för att transkribera videon.|
|Statistik | Mer information finns i [statistik](#statistics).|

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
### <a name="insights"></a>Insikter

Varje insikt (till exempel avskriftslinjer, ansikten, varumärken osv.) innehåller en lista över unika element (till exempel face1, face2, face3) och varje element har sina egna metadata och en lista över dess instanser (som är tidsintervall med ytterligare valfria metadata).

Ett ansikte kan ha ett ID, ett namn, en miniatyrbild, andra metadata och en lista över dess temporala instanser (till exempel: 00:00:05 – 00:00:10, 00:01:00 - 00:02:30 och 00:41:21 – 00:41:49.) Varje temporal instans kan ha ytterligare metadata. Till exempel ansiktsrektangelns koordinater (20 230 60,60).

|Version|Kodversionen|
|---|---|
|sourceLanguage|Videons källspråk (förutsatt ett huvudspråk). I form av en [BCP-47-sträng.](https://tools.ietf.org/html/bcp47)|
|language|Insiktsspråket (översatt från källspråket). I form av en [BCP-47-sträng.](https://tools.ietf.org/html/bcp47)|
|Utskrift|[Avskriftsinsikten.](#transcript)|
|Ocr|[OCR-insikten.](#ocr)|
|nyckelord|[Nyckelordsinsikten.](#keywords)|
|block|Kan innehålla ett eller flera [block](#blocks)|
|ansikten/animeradeCharacters|Insikter [om ansikten/animeradeCharacters.](#facesanimatedcharacters)|
|Etiketter|Insikten [om](#labels) etiketter.|
|Skott|[Bildinsikterna.](#shots)|
|Märken|[Varumärkesinsikterna.](#brands)|
|audioEffects|[AudioEffects-insikten.](#audioeffects-public-preview)|
|Känslor|[Attitydinsikterna.](#sentiments)|
|visualContentModeration|[VisualContentModeration-insikten.](#visualcontentmoderation)|
|textualContentModeration|[TextualContentModeration-insikten.](#textualcontentmoderation)|
|Känslor| [Känsloinsikter.](#emotions)|
|Ämnen|[Avsnittsinsikter.](#topics)|
|högtalare|[Talarinsikter.](#speakers)|

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
Instanser|En lista över tidsintervall för det här blocket.|

#### <a name="transcript"></a>Utskrift

|Name|Beskrivning|
|---|---|
|id|Rad-ID: t.|
|text|Själva avskriften.|
|konfidensbedömning|Konfidensen för avskriftsprecisionen.|
|speakerId|TALARENS ID.|
|language|Avskriftsspråket. Avsedd att stödja avskrift där varje rad kan ha olika språk.|
|Instanser|En lista över tidsintervall där den här raden visades. Om instansen är avskrift har den bara 1 instans.|

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

#### <a name="ocr"></a>Ocr

|Name|Beskrivning|
|---|---|
|id|OCR-rad-ID.|
|text|OCR-texten.|
|konfidensbedömning|Konfidensen för igenkänning.|
|language|SPRÅKET OCR.|
|Instanser|En lista över tidsintervall där ocr visas (samma OCR kan visas flera gånger).|
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
|text|Nyckelordstexten.|
|konfidensbedömning|Nyckelordets konfidens för igenkänning.|
|language|Nyckelordsspråket (när det översätts).|
|Instanser|En lista över tidsintervall där det här nyckelordet visas (ett nyckelord kan visas flera gånger).|

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

#### <a name="facesanimatedcharacters"></a>ansikten/animeradecharacters

`animatedCharacters` -elementet `faces` ersätter om videon indexerades med en modell med animerade tecken. Detta görs med hjälp av en anpassad modell i Custom Vision, Video Indexer kör den på nyckeldatorer.

Om ansikten (inte animerade tecken) finns använder Video Indexer ansikts-API på alla videons bildrutor för att identifiera ansikten och kändisar.

|Name|Beskrivning|
|---|---|
|id|Ansikts-ID: t.|
|name|Namnet på ansiktet. Det kan vara "Okänd #0, en identifierad kändis eller en kundtränad person.|
|konfidensbedömning|Ansiktsidentifieringsförtroende.|
|beskrivning|En beskrivning av kändisen. |
|thumbnailId|ID för miniatyrbilden för det ansiktet.|
|knownPersonId|Om det är en känd person, dess interna ID.|
|referenceId|Om det är en Bing-kändis, dess Bing-ID.|
|referenceType|För närvarande är det bara Bing.|
|title|Om det är en kändis, dess titel (till exempel "Microsofts VD").|
|imageUrl|Om det är en kändis, dess bild-URL.|
|Instanser|Det här är instanser där ansiktet visades inom det angivna tidsperioden. Varje instans har också ett thumbnailsId. |

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
|name|Etikettnamnet (till exempel "Dator", "TV").|
|language|Etikettnamnets språk (när det översätts). BCP-47|
|Instanser|En lista över tidsintervall där etiketten visades (en etikett kan visas flera gånger). Varje instans har ett konfidensfält. |


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

#### <a name="scenes"></a>Scener

|Name|Beskrivning|
|---|---|
|id|Scen-ID:t.|
|Instanser|En lista över tidsintervall för den här scenen (en scen kan bara ha 1 instans).|

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

#### <a name="shots"></a>Skott

|Name|Beskrivning|
|---|---|
|id|Bild-ID:t.|
|Nyckelrutor|En lista över keyFrames i bilden (var och en har ett ID och en lista över tidsintervall för instanser). Varje keyFrame-instans har ett thumbnailId-fält som innehåller keyFrames miniatyr-ID.|
|Instanser|En lista över tidsintervall för den här bilden (en bild kan bara ha 1 instans).|

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

#### <a name="brands"></a>Märken

Företags- och produktnamn identifierade i tal till text-transkription och/eller Video OCR. Detta omfattar inte visuell igenkänning av varumärken eller logotypidentifiering.

|Name|Beskrivning|
|---|---|
|id|Varumärkes-ID.|
|name|Varumärkesnamnet.|
|referenceId | Suffixet för wikipedia-url:en för varumärket. Till exempel är "Target_Corporation" suffixet [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|referenceUrl | Varumärkets Wikipedia-URL, om sådan finns. Till exempel [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|beskrivning|Varumärkesbeskrivningen.|
|tags|En lista över fördefinierade taggar som har associerats med det här varumärket.|
|konfidensbedömning|Konfidensvärdet för Video Indexer brand detector (0-1).|
|Instanser|En lista över tidsintervall för det här varumärket. Varje instans har en brandType, vilket anger om det här varumärket visades i avskriften eller i OCR.|

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

#### <a name="statistics"></a>Statistik

|Name|Beskrivning|
|---|---|
|CorrespondenceCount|Antal överensstämmelsen i videon.|
|SpeakerWordCount|Antalet ord per talare.|
|SpeakerNumberOfFragments|Mängden fragment som talaren har i en video.|
|SpeakerLongestMonolog|Talarens längsta monolog. Om talaren har tystnad i monologen inkluderas den. Tystnad i början och slutet av monologen tas bort.| 
|SpeakerTalkToListenRatio|Beräkningen baseras på den tid som ägnats åt talarens monolog (utan tystnad mittemellan) dividerat med videons totala tid. Tiden avrundas till det tredje decimaltecknet.|

#### <a name="audioeffects-public-preview"></a>audioEffects (offentlig förhandsversion)

|Name|Beskrivning
|---|---|
|id|Ljudeffekts-ID|
|typ|Ljudeffekttypen|
|Instanser|En lista över tidsintervall där den här ljudeffekten visades. Varje instans har ett konfidensfält.|

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

#### <a name="sentiments"></a>Känslor

Sentiment aggregeras med fältet sentimentType (positiv/neutral/negativ). Till exempel 0-0.1, 0.1-0.2.

|Name|Beskrivning|
|---|---|
|id|Sentiment-ID: t.|
|averageScore |Medelvärdet av alla poäng för alla instanser av den sentimenttypen – positiv/neutral/negativ|
|Instanser|En lista över tidsintervall där den här attityden visades.|
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

VisualContentModeration-blocket innehåller tidsintervall som Video Indexer potentiellt har vuxet innehåll. Om visualContentModeration är tomt finns det inget vuxet innehåll som har identifierats.

Videor som visar sig innehålla vuxet eller ojämnt innehåll kan vara tillgängliga endast för privat vy. Användare har möjlighet att skicka en begäran om en mänsklig granskning av innehållet, i vilket fall IsAdult-attributet innehåller resultatet av den mänskliga granskningen.

|Name|Beskrivning|
|---|---|
|id|Det visuella innehållsmodererings-ID:t.|
|adultScore|Poängen för vuxna (från Content Moderator).|
|racyScore|Poängen (från innehållsmoderering).|
|Instanser|En lista över tidsintervall där den här visuella innehållsmodereringen visades.|

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
|id|Textinnehållsmodererings-ID.|
|bannedWordsCount |Antalet förbjudna ord.|
|bannedWordsRatio |Förhållandet från det totala antalet ord.|

#### <a name="emotions"></a>Känslor

Video Indexer identifierar känslor baserat på tal- och ljudikoner. Den identifierade känslan kan vara: sorg, sorg, anger eller rädsla.

|Name|Beskrivning|
|---|---|
|id|Känslo-ID: t.|
|typ|Känslo momentet som identifierades baserat på tal- och ljudikoner. Känslorna kan vara: sorg, sorg, anger eller rädsla.|
|Instanser|En lista över tidsintervall där känslorna uppstod.|

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

#### <a name="topics"></a>Ämnen

Video Indexer härledning av huvudämnen från avskrifter. Om möjligt ingår IPTC-taxonomin [på](https://iptc.org/standards/media-topics/) andra nivån. 

|Name|Beskrivning|
|---|---|
|id|Ämnes-ID.|
|name|Ämnesnamnet, till exempel: "Läkemedel".|
|referenceId|Synliga sökvägar som återspeglar ämneshierarkin. Till exempel: "Hälsa och hälsotillstånd/medicin och sjukvård/läkemedel".|
|konfidensbedömning|Förtroendepoängen i intervallet [0,1]. Högre är säkrare.|
|language|Det språk som används i ämnet.|
|iptcName|Namnet på IPTC-mediekoden, om det upptäcks.|
|Instanser |För Video Indexer indexerar inte ett ämne till tidsintervall, så hela videon används som intervall.|

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
|id|Talar-ID: t.|
|name|Talarnamnet i form av "Speaker # *<number>* " (Talarnummer) till exempel: "Speaker #1".|
|Instanser |En lista över tidsintervall där talaren visades.|

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

[Video Indexer Developer Portal](https://api-portal.videoindexer.ai)

Information om hur du bäddar in widgetar i ditt program finns i Bädda in [Video Indexer widgetar i dina program.](video-indexer-embed-widgets.md) 

