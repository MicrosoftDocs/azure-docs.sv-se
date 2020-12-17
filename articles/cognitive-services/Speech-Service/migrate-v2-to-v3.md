---
title: Migrera från v2 till v3 REST API-Speech service
titleSuffix: Azure Cognitive Services
description: Det här dokumentet hjälper utvecklare att migrera kod från v2 till v3 av i tal-till-text-REST API.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: e9e5db87f983c5db59715eb8b6a9561acf5fad14
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630623"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>Migrera kod från v 2.0 till v 3.0 av REST API

Jämfört med v2 är v3-versionen av tal tjänster REST API för tal-till-text mer tillförlitlig, enklare att använda och mer konsekvent med API: er för liknande tjänster. De flesta team kan migrera från v2 till v3 på en dag eller två.

## <a name="forward-compatibility"></a>Vidarebefordra kompatibilitet

Alla entiteter från v2 kan också finnas i v3-API: t under samma identitet. Om schemat för ett resultat har ändrats, (till exempel avskrifter), kommer resultatet av en GET i v3-versionen av API: et att använda v3-schemat. Resultatet av ett GET i v2-versionen av API: et använder samma v2-schema. Nyligen skapade entiteter på v3 är **inte** tillgängliga i resultat från v2 API: er.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

Listan över avbrytande ändringar har sorterats efter storleken på de ändringar som krävs för att anpassa. Endast några ändringar kräver icke-triviala ändringar i anrops koden. De flesta ändringar kräver bara en ändring av objekt namn.

### <a name="host-name-changes"></a>Värd namns ändringar

Slut punkts värd namn har ändrats från `{region}.cris.ai` till `{region}.api.cognitive.microsoft.com` . Sökvägar till de nya slut punkterna innehåller inte längre `api/` den är en del av värd namnet. [Swagger-dokumentet](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) listar giltiga regioner och sökvägar.
>[!IMPORTANT]
>Ändra värd namnet från `{region}.cris.ai` till `{region}.api.cognitive.microsoft.com` där region är den region där din tal prenumeration finns. Ta även bort `api/` från valfri sökväg i klient koden.

### <a name="identity-of-an-entity"></a>Identitet för en entitet

Egenskapen `id` är nu `self` . I v2 var en API-användare medveten om hur våra sökvägar i API: et skapades. Detta var inte utöknings Bart och krävde onödigt arbete från användaren. Egenskapen `id` (UUID) ersätts av `self` (sträng), vilket är platsen för entiteten (URL). Värdet är fortfarande unikt mellan alla dina entiteter. Om `id` lagras som en sträng i koden räcker det att byta namn för att stödja det nya schemat. Nu kan du använda `self` innehållet som URL för `GET` -, `PATCH` -och- `DELETE` rest-anrop för entiteten.

Om entiteten har ytterligare funktioner som är tillgängliga via andra sökvägar visas de under `links` . I följande exempel för avskrift visas en separat metod för `GET` innehållet i avskriften:
>[!IMPORTANT]
>Byt namn på egenskapen `id` till `self` i klient koden. Ändra typen från `uuid` till `string` vid behov. 

**v2-avskrift:**

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

**v3-avskriftering:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

Beroende på kodens implementering kan det bero på att det inte räcker att byta namn på egenskapen. Vi rekommenderar att du använder de returnerade `self` och `links` värdena som mål-URL: er för dina rest-anrop, i stället för att skapa sökvägar i klienten. Genom att använda de returnerade URL: erna kan du vara säker på att framtida ändringar i sökvägar inte kommer att bryta din klient kod.

### <a name="working-with-collections-of-entities"></a>Arbeta med samling av entiteter

Tidigare var v2-API: t returnerade alla tillgängliga entiteter i ett resultat. För att ge en mer detaljerad kontroll över förväntad svars storlek i v3 är alla insamlings resultat sid brytningar. Du har kontroll över antalet returnerade entiteter och start förskjutningen för sidan. Det här beteendet gör det enkelt att förutsäga körningen av svars processorn.

Den grundläggande formen för svaret är samma för alla samlingar:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

`values`Egenskapen innehåller en delmängd av de tillgängliga samlings enheterna. Antalet och förskjutningen kan kontrol leras med hjälp av `skip` `top` parametrarna och. Om `@nextLink` inte är `null` det finns fler tillgängliga data och nästa data uppsättning kan hämtas genom att göra en hämtning `$.@nextLink` .

Den här ändringen kräver att du anropar `GET` for-samlingen i en loop tills alla element har returnerats.

>[!IMPORTANT]
>När svaret på en GET till `speechtotext/v3.0/{collection}` innehåller ett värde i `$.@nextLink` fortsätter `GETs` du att utfärda det `$.@nextLink` tills `$.@nextLink` inte är inställt på att hämta alla element i samlingen.

### <a name="creating-transcriptions"></a>Skapa avskrifter

En detaljerad beskrivning av hur du skapar batchar av avskrifter finns i [batch-avskriftering](./batch-transcription.md).

Med v3-avskrifts-API: et kan du ange olika avskrifts alternativ explicit. Alla (valfria) konfigurations egenskaper kan nu anges i `properties` egenskapen.
Version v3 stöder också flera indatafiler, så det krävs en lista över URL: er i stället för en enda URL som v2. Egenskaps namnet v2 `recordingsUrl` är nu `contentUrls` i v3. Funktionerna för att analysera sentiment i avskrifter har tagits bort i v3. Se analys alternativen Microsoft kognitiv service [text analys](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) för sentiment.

Den nya egenskapen `timeToLive` under `properties` kan hjälpa dig att rensa befintliga slutförda entiteter. `timeToLive`Anger en varaktighet efter vilken en slutförd entitet tas bort automatiskt. Ange det till ett högt värde (till exempel `PT12H` ) när entiteterna kontinuerligt spåras, förbrukas och tas bort och därför vanligt vis bearbetas långt innan 12 timmar har passerat.

**v2-avskrifts INLÄGGets brödtext:**

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

**v3-avskrifts INLÄGGets brödtext:**

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```
>[!IMPORTANT]
>Byt namn på egenskapen `recordingsUrl` till `contentUrls` och skicka en matris med URL: er i stället för en enda URL. Överför inställningar för `diarizationEnabled` eller `wordLevelTimestampsEnabled` som `bool` i stället för `string` .

### <a name="format-of-v3-transcription-results"></a>Format på v3-avskrifts resultat

Schemat för avskrifts resultat har ändrats något för att justeras till avskrifter som skapats av slut punkter i real tid. Hitta en djupgående Beskrivning av det nya formatet i [batch avskriftering](./batch-transcription.md). Schemat för resultatet publiceras i vår [GitHub-exempel databas](https://aka.ms/csspeech/samples) under `samples/batch/transcriptionresult_v3.schema.json` .

Egenskaps namn är nu kamel notation-bokstäver och värdena för `channel` och `speaker` använder nu heltals typer. Formatet för varaktigheter använder nu den struktur som beskrivs i ISO 8601, som matchar varaktighets formatet som används i andra Azure API: er.

Exempel på ett v3-avskrifts resultat. Skillnaderna beskrivs i kommentarerna.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```
>[!IMPORTANT]
>Deserialisera avskrifts resultatet till den nya typen som visas ovan. I stället för en enskild fil per ljud kanal, särskilja kanaler genom att kontrol lera egenskap svärdet för `channel` för varje-element i `recognizedPhrases` . Det finns nu en enda resultat fil för varje indatafil.


### <a name="getting-the-content-of-entities-and-the-results"></a>Hämtar innehållet i entiteter och resultaten

I v2 har länkar till indata-eller resultat filerna infogats i resten av entitetens metadata. Som en förbättring av v3 finns det en tydlig åtskillnad mellan entitetens metadata (som returneras av en hämtning `$.self` ) och information och autentiseringsuppgifter för att få åtkomst till resultat filen. Den här separationen skyddar kunddata och ger en noggrann kontroll över giltighets tiden för autentiseringsuppgifterna.

I v3 `links` inkluderar du en underordnad egenskap `files` som kallas om entiteten visar data (data uppsättningar, avskrifter, slut punkter eller utvärderingar). Ett kom igång `$.links.files` returnerar en lista över filer och en SAS-URL för att få åtkomst till innehållet i varje fil. Om du vill kontrol lera giltighets tiden för SAS-URL: erna `sasValidityInSeconds` kan du använda Frågeparametern för att ange livs längd.

**v2-avskrift:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

**v3-avskriftering:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**En hämtning `$.links.files` skulle leda till:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

`kind`Egenskapen anger formatet på filens innehåll. För avskrifter är fil typerna `TranscriptionReport` sammanfattningen av jobbet och filer av typen `Transcription` är resultatet av själva jobbet.

>[!IMPORTANT]
>Om du vill hämta resultatet av åtgärder använder du en `GET` på `/speechtotext/v3.0/{collection}/{id}/files` , de ingår inte längre i svaren på `GET` på `/speechtotext/v3.0/{collection}/{id}` eller `/speechtotext/v3.0/{collection}` .

### <a name="customizing-models"></a>Anpassa modeller

Före v3 var det en skillnad mellan en _akustisk modell_ och en _språk modell_ när en modell tränades. Den här skillnaden ledde till att du måste ange flera modeller när du skapar slut punkter eller avskrifter. För att förenkla den här processen för en anropare har vi tagit bort skillnaderna och gjort allt beroende av innehållet i de data uppsättningar som används för modell träning. Med den här ändringen stöder modell skapande nu blandade data uppsättningar (språk data och akustiska data). Slut punkter och avskrifter kräver nu bara en modell.

Med den här ändringen har behovet av en `kind` i `POST` åtgärden tagits bort och `datasets[]` matrisen kan nu innehålla flera data uppsättningar av samma eller blandade typer.

För att förbättra resultatet av en utbildad modell används akustiska data automatiskt internt under språkutbildning. I allmänhet ger modeller som skapats via v3-API: n mer exakta resultat än modeller som skapats med v2-API: et.

>[!IMPORTANT]
>Om du vill anpassa både ljud-och språk modell delen måste du skicka alla nödvändiga språk och akustiska data uppsättningar i `datasets[]` post-till `/speechtotext/v3.0/models` . Då skapas en enskild modell med båda delarna anpassade.

### <a name="retrieving-base-and-custom-models"></a>Hämtar bas-och anpassade modeller

För att förenkla hämtningen av tillgängliga modeller har v3 separerat samlingarna av "bas modeller" från kundägda "anpassade modeller". De två vägarna är nu `GET /speechtotext/v3.0/models/base` och `GET /speechtotext/v3.0/models/` .

I v2 returnerades alla modeller tillsammans i ett enda svar.

>[!IMPORTANT]
>Om du vill hämta en lista över de bas modeller som har angetts för anpassning använder du `GET` på `/speechtotext/v3.0/models/base` . Du kan hitta dina egna anpassade modeller med en `GET` på `/speechtotext/v3.0/models` .

### <a name="name-of-an-entity"></a>Namn på en entitet

`name`Egenskapen är nu `displayName` . Detta är konsekvent med andra Azure-API: er som inte indikerar identitets egenskaper. Värdet för den här egenskapen får inte vara unikt och kan ändras efter att entiteten har skapats med en `PATCH` åtgärd.

**v2-avskrift:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**v3-avskriftering:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>Byt namn på egenskapen `name` till `displayName` i klient koden.

### <a name="accessing-referenced-entities"></a>Åtkomst till refererade entiteter

I v2 var refererade entiteter alltid infogade, till exempel de använda modellerna för en slut punkt. Kapslade entiteter resulterade i stora svar och konsumenter förbrukade sällan det kapslade innehållet. Om du vill minska svars storleken och förbättra prestandan visas inte längre de refererade entiteterna i svaret. I stället visas en referens till den andra entiteten och kan användas direkt för ett senare `GET` (det är en webb adress också) och följa samma mönster som `self` länken.

**v2-avskrift:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
        }
      ]
    },
  ]
}
```

**v3-avskriftering:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Om du behöver använda informationen om en refererad modell, som du ser i exemplet ovan, kan du bara utfärda en Hämta `$.model.self` .

>[!IMPORTANT]
>Om du vill hämta metadata för entiteter som refereras till, kan du utfärda en hämtning `$.{referencedEntity}.self` , till exempel för att hämta modellen för en avskriftning `GET` `$.model.self` .


### <a name="retrieving-endpoint-logs"></a>Hämtar slut punkts loggar

Version v2 av tjänsten som stöder loggnings slut punkts resultat. Om du vill hämta resultatet av en slut punkt med v2 skapar du en "data export" som representerade en ögonblicks bild av resultaten som definierats i ett tidsintervall. Processen för att exportera batchar av data var inflexibla. V3-API ger åtkomst till varje enskild fil och möjliggör upprepning genom dem.

**En v3-slutpunkt som körs:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**Svar på GET `$.links.logs` :**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

Sid brytning för slut punkts loggar fungerar ungefär som alla andra samlingar, förutom att ingen förskjutning kan anges. På grund av den stora mängden tillgängliga data avgörs sid brytningen av servern.

I v3 kan varje slut punkts logg tas bort individuellt genom att en åtgärd utfärdas för en `DELETE` `self` fil eller med hjälp av `DELETE` `$.links.logs` . Om du vill ange ett slutdatum kan du lägga till frågeparametern i `endDate` begäran.

>[!IMPORTANT]
>I stället för att skapa logg exporter vid `/api/speechtotext/v2.0/endpoints/{id}/data` användning `/v3.0/endpoints/{id}/files/logs/` för att få åtkomst till loggfilerna individuellt. 

### <a name="using-custom-properties"></a>Använda anpassade egenskaper

Om du vill avgränsa anpassade egenskaper från valfria konfigurations egenskaper finns alla explicit namngivna egenskaper i `properties` egenskapen och alla egenskaper som anges av anroparna finns nu i `customProperties` egenskapen.

**v2-avskrifts enhet:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**v3-avskrifts enhet:**

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Med den här ändringen kan du också använda rätt typer på alla explicit namngivna egenskaper under `properties` (till exempel boolesk i stället för sträng).

>[!IMPORTANT]
>Skicka alla anpassade egenskaper som i `customProperties` stället för `properties` i dina `POST` begär Anden.

### <a name="response-headers"></a>Svarshuvuden

v3 returnerar inte längre `Operation-Location` rubriken förutom `Location` rubriken på `POST` begär Anden. Värdet för båda huvudena i v2 var samma. Nu `Location` returneras bara.

Eftersom den nya API-versionen nu hanteras av Azure API Management (APIM), är begränsningen av relaterade huvuden `X-RateLimit-Limit` , `X-RateLimit-Remaining` och `X-RateLimit-Reset` inte i svars huvudena.

>[!IMPORTANT]
>Läs platsen från svars huvudet `Location` i stället för `Operation-Location` . Om det handlar om en 429-svarskod läser du `Retry-After` värdet header i stället för `X-RateLimit-Limit` , `X-RateLimit-Remaining` eller `X-RateLimit-Reset` .


### <a name="accuracy-tests"></a>Test av precision

Noggrannhets test har bytt namn till utvärderingar eftersom det nya namnet beskriver bättre vad de representerar. De nya Sök vägarna är: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations` .

>[!IMPORTANT]
>Byt namn på Sök vägs segmentet `accuracytests` till `evaluations` i klient koden.


## <a name="next-steps"></a>Nästa steg

Undersök alla funktioner i dessa vanliga REST-API: er som tillhandahålls av tal tjänster:

* [REST API för tal-till-text](rest-speech-to-text.md)
* [Swagger-dokument](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) för v3 REST API
* För exempel kod för att utföra batch-avskrifter, se [GitHub-exempel lagrings platsen](https://aka.ms/csspeech/samples) i under `samples/batch` katalogen.
