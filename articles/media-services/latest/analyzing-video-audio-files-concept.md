---
title: Analysera video-och ljudfiler
description: Lär dig hur du analyserar ljud-och video innehåll med AudioAnalyzerPreset och VideoAnalyzerPreset i Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: 25b5c3163b657633ca78215468f4c2a2e40949b7
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897232"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analysera video-och ljudfiler med Azure Media Services

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med Azure Media Services v3 kan du extrahera insikter från dina video-och ljudfiler med Video Indexer. I den här artikeln beskrivs de Media Services v3 Analyzer-för hands inställningarna som används för att extrahera dessa insikter. Använd Video Indexer direkt för att indexera mer detaljerad information. Om du vill veta när du ska använda Video Indexer jämfört med Media Services Analyzer-för inställningar, kan du läsa [jämförelse dokumentet](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Det finns två lägen för för hands inställningen för ljud analys, Basic och standard. Se beskrivningen av skillnaderna i tabellen nedan.

Om du vill analysera ditt innehåll med Media Services v3-för inställningar skapar du en **transformering** och skickar ett **jobb** som använder någon av dessa för inställningar: [VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset) eller **AudioAnalyzerPreset**. En själv studie kurs som demonstrerar hur du använder **VideoAnalyzerPreset** finns i [analysera videor med Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> När du använder en video-eller ljud analys för hands inställningar använder du Azure Portal för att ange att ditt konto ska ha 10 S3-enheter reserverade enheter, även om detta inte krävs. Du kan använda antingen S1 eller S2 för ljud för inställningar. Mer information finns i [Skala mediebearbetning](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Efterlevnad, sekretess och säkerhet

Som en viktig påminnelse måste du följa alla tillämpliga lagar i din användning av Video Indexer och du får inte använda Video Indexer eller andra Azure-tjänster på ett sätt som strider mot andras rättigheter eller som kan vara skadliga för andra. Innan du laddar upp videor, inklusive bio metriska data, till den Video Indexer tjänsten för bearbetning och lagring, måste du ha alla lämpliga rättigheter, inklusive alla lämpliga medgivanden, från enskilda eller enskilda i videon. För att lära dig om efterlevnad, sekretess och säkerhet i Video Indexer, [villkoren](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)för Microsoft Cognitive Services. Information om Microsofts sekretess avtal och hantering av dina data finns i Microsofts [sekretess policy](https://privacy.microsoft.com/PrivacyStatement), [Online Services-villkoren](https://www.microsoft.com/licensing/product-licensing/products) ("ost") och tillägget för [data bearbetning](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Ytterligare sekretess information, inklusive information om datakvarhållning, borttagning/förstörelse, finns i OST-filen och [här](../video-indexer/faq.md). Genom att använda Video Indexer samtycker du till att vara kopplad till Cognitive Services villkor, OST, DPA och sekretess policyn.

## <a name="built-in-presets"></a>Inbyggda för hands inställningar

Media Services stöder för närvarande följande inbyggda Analyzer-för hands inställningar:  

|**Förvals namn**|**Scenario**|**Information**|
|---|---|---|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analysera ljud standard|För inställningen används en fördefinierad uppsättning av AI-baserade analys åtgärder, inklusive tal avskrifter. För närvarande stöder för inställningen bearbetning av innehåll med ett enda ljud spår som innehåller tal på ett och samma språk. Du kan ange språket för ljud nytto lasten i indata med BCP-47-formatet för ' language tag-region '. Språk som stöds är engelska ("en-US" och "en-GB"), spanska (' es-ES ' och ' es-MX '), franska (fr-FR), italienska ("IT-IT"), japanska (' ja-JP '). portugisiska ("pt-BR"), kinesiska (' zh-CN '), tyska ("de-DE"), arabiska (' ar-tex ' och ' ar-SY '), ryska (' ru-RU '), hindi (' Hi-IN ') och koreanska (' ko-KR ').<br/><br/> Om språket inte har angetts eller är inställt på null väljer automatisk språk identifiering det första språket som identifieras och fortsätter med det valda språket under filens varaktighet. Funktionen för automatisk språk identifiering stöder för närvarande engelska, kinesiska, franska, tyska, italienska, japanska, spanska, ryska och portugisiska. Den stöder inte dynamisk växling mellan språk när det första språket har identifierats. Funktionen för automatisk språk identifiering fungerar bäst med ljud inspelningar med tydligt discernible tal. Om automatisk språk identifiering inte kan hitta språket, går avskriften tillbaka till engelska.|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analyserar ljud Basic|I det här läget utförs tal-till-text-avskrift och generering av en VTT-under text fil. Utdata från det här läget är en JSON-fil för insikter som bara innehåller nyckelord, avskrifter och tids inställnings information. Automatisk språk identifiering och talare diarization ingår inte i det här läget. Listan över språk som stöds finns [här](#built-in-presets)|
|[VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analysera ljud och video|Extraherar insikter (avancerade metadata) från både ljud och video och matar ut en JSON-format fil. Du kan ange om du bara vill extrahera ljud insikter när du bearbetar en videofil. Mer information finns i [Analysera video](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset)|Identifiera ansikten som finns i videon|Beskriver de inställningar som ska användas när du analyserar en video för att identifiera alla ytor som finns.|

### <a name="audioanalyzerpreset-standard-mode"></a>AudioAnalyzerPreset standard läge

För inställningen gör att du kan extrahera flera ljud insikter från en ljud-eller video fil.

Utdata innehåller en JSON-fil (med alla insikter) och VTT-filen för ljud avskriften. Den här för inställningen accepterar en egenskap som anger språket för indatafilen i form av en [BCP47](https://tools.ietf.org/html/bcp47) -sträng. Ljud insikten innehåller:

* **Ljud avskrift**: en avskrift av talade ord med tidsstämplar. Det finns stöd för flera språk.
* **Högtalar indexering**: en mappning av högtalarna och motsvarande talade ord.
* **Analys av tal sentiment**: resultatet av sentiment-analysen utförs på ljud avskriften.
* **Nyckelord**: nyckelord som extraheras från ljud avskriften.

### <a name="audioanalyzerpreset-basic-mode"></a>AudioAnalyzerPreset Basic-läge

För inställningen gör att du kan extrahera flera ljud insikter från en ljud-eller video fil.

Utdata innehåller en JSON-fil och en VTT-fil för ljud avskriften. Den här för inställningen accepterar en egenskap som anger språket för indatafilen i form av en [BCP47](https://tools.ietf.org/html/bcp47) -sträng. Utdata innehåller:

* **Ljud avskrift**: en avskrift av talade ord med tidsstämplar. Flera språk stöds, men automatiska språk identifierings-och högtalar diarization ingår inte.
* **Nyckelord**: nyckelord som extraheras från ljud avskriften.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

För inställningen kan du extrahera flera ljud-och video insikter från en videofil. Utdata innehåller en JSON-fil (med alla insikter), en VTT-fil för video avskriften och en samling med miniatyrer. Den här för hands inställningen accepterar också en [BCP47](https://tools.ietf.org/html/bcp47) sträng (som representerar videons språk) som en egenskap. Video insikten innehåller alla ljud insikter som nämns ovan och följande ytterligare objekt:

* **Ansikts spårning**: den tid under vilken ansikten finns i videon. Varje ansikte har ett ansikts-ID och en motsvarande samling med miniatyrer.
* **Visuell text**: den text som identifieras via optisk tecken igenkänning. Texten är tidsstämplad och används även för att extrahera nyckelord (förutom ljud avskriften).
* **Nyckel rutor**: en samling nyckel rutor som extraheras från videon.
* **Redaktör för visuellt innehåll**: den del av videor som har flaggats som vuxen eller vågat.
* **Anteckning**: ett resultat av att kommentera videor baserat på en fördefinierad objekt modell

## <a name="insightsjson-elements"></a>insights.jspå element

Utdata innehåller en JSON-fil (insights.jspå) med alla insikter som finns i videon eller ljudet. JSON kan innehålla följande element:

### <a name="transcript"></a>avskrifts

|Name|Beskrivning|
|---|---|
|id|Rad-ID.|
|text|Själva avskriften.|
|language|Avskrifts språket. Avsett att stödja avskrifter där varje rad kan ha ett annat språk.|
|pipe|En lista med tidsintervaller där denna rad visades. Om instansen avskrifts har den bara en instans.|

Exempel:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>stöd

|Name|Beskrivning|
|---|---|
|id|ID för OCR-linje.|
|text|OCR-text.|
|konfidensbedömning|Igenkännings förtroendet.|
|language|OCR-språket.|
|pipe|En lista med tidsintervall där denna OCR visades (samma OCR kan förekomma flera gånger).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>ytor

|Name|Beskrivning|
|---|---|
|id|Ansikts-ID.|
|name|Ansikts namnet. Det kan vara okänt #0, ett identifierat kändis eller en kundutbildad person.|
|konfidensbedömning|Förtroende för ansikts identifiering.|
|beskrivning|En beskrivning av kändis. |
|thumbnailId|ID för miniatyr bilden för den aktuella ytan.|
|knownPersonId|Det interna ID: t (om det är en känd person).|
|referenceId|Bing-ID: t (om det är en Bing-kändis).|
|referenceType|Just Bing.|
|title|Rubriken (om det är en kändis, till exempel "Microsofts VD").|
|imageUrl|Bild-URL: en, om det är en kändis.|
|pipe|Instanser där ytan fanns inom det aktuella tidsintervallet. Varje instans har också en thumbnailsId. |

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

### <a name="shots"></a>bilder

|Name|Beskrivning|
|---|---|
|id|Bild-ID.|
|Nyckel rutor|En lista över nyckel bild rutor i instansen (var och en har ett ID och en lista över instanser av instans intervallet). Nyckel bilds instanser har ett thumbnailId-fält med nyckel rutans miniatyr-ID.|
|pipe|En lista över tidsintervallen för den här instansen (dum par har bara en instans).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>uppgifterna

|Name|Beskrivning|
|---|---|
|CorrespondenceCount|Antal korrespondens i videon.|
|WordCount|Antalet ord per talare.|
|SpeakerNumberOfFragments|Mängden fragment som talare har i en video.|
|SpeakerLongestMonolog|Föredragets längsta monolog. Om högtalaren har tystnad i monolog är den inkluderad. Tystnad i början och slutet av monolog tas bort.|
|SpeakerTalkToListenRatio|Beräkningen baseras på den tid som ägnats åt högtalar monolog (utan tystnad mellan) dividerat med videons totala tid. Tiden avrundas till det tredje decimal tecknet.|


### <a name="sentiments"></a>sentiment

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

### <a name="labels"></a>Etiketter

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

### <a name="keywords"></a>nyckelord

|Name|Beskrivning|
|---|---|
|id|Nyckelords-ID: t.|
|text|Nyckelords texten.|
|konfidensbedömning|Nyckelordets tolknings säkerhet.|
|language|Nyckelords språket (vid översättning).|
|pipe|En lista med tidsintervall där det här nyckelordet visades (ett nyckelord kan visas flera gånger).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

VisualContentModeration-blocket innehåller tidsintervall som Video Indexer hittade för att potentiellt ha vuxen innehåll. Om visualContentModeration är tomt finns det inget olämpligt innehåll som identifierats.

Videor som innehåller vuxen eller vågat innehåll kan endast vara tillgängliga för privat vy. Användare kan skicka en begäran om en mänsklig granskning av innehållet, vilket innebär att `IsAdult` attributet innehåller resultatet av mänsklig granskning.

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
## <a name="next-steps"></a>Nästa steg

[Självstudier: Analysera videor med Azure Media Services](analyze-videos-tutorial-with-api.md)