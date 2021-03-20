---
title: Använda batch-avskrifter – tal tjänst
titleSuffix: Azure Cognitive Services
description: Batch-avskrift är idealiskt om du vill skriva över en stor mängd ljud i lagring, till exempel Azure-blobbar. Genom att använda den dedikerade REST API kan du peka på ljudfiler med en SAS-URI (signatur för delad åtkomst) och få asynkront erhålla avskrifter.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/23/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: e48fead4d4364fd84f178388dbfb9158296e687b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659979"
---
# <a name="how-to-use-batch-transcription"></a>Använda batch-avskriftering

Batch-avskrift är en uppsättning REST API åtgärder som gör det möjligt att skriva av en stor mängd ljud i lagring. Du kan peka på ljudfiler med en typisk URI eller en URL för [signatur för delad åtkomst (SAS)](../../storage/common/storage-sas-overview.md) och ta emot avskrifts resultat asynkront. Med v 3.0-API: et kan du skriva över en eller flera ljudfiler eller bearbeta en hel lagrings behållare.

Du kan använda REST-API: er för batch-avskrift för att anropa följande metoder:

|    Batch-avskrifts åtgärd                                             |    Metod    |    REST API-anrop                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Skapar en ny avskrift.                                              |    POST      |    speechtotext/v 3.0/avskrifter            |
|    Hämtar en lista över avskrifter för den autentiserade prenumerationen.    |    GET       |    speechtotext/v 3.0/avskrifter            |
|    Hämtar en lista över språk som stöds för offline-avskrifter.              |    GET       |    speechtotext/v 3.0/avskrifter/språk    |
|    Uppdaterar föränderligt-informationen för avskriften som identifieras av dess ID.    |    9.0a     |    speechtotext/v 3.0/avskrifter/{ID}       |
|    Tar bort den angivna avskrifts uppgiften.                                 |    DELETE    |    speechtotext/v 3.0/avskrifter/{ID}       |
|    Hämtar avskriften som identifieras av det angivna ID: t.                        |    GET       |    speechtotext/v 3.0/avskrifter/{ID}       |
|    Hämtar resultat filen för avskriften som identifieras av det angivna ID: t.    |    GET       |    speechtotext/v 3.0/avskrifter/{ID}/filer |

Du kan granska och testa det detaljerade API: et, som är tillgängligt som ett [Swagger-dokument](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

Jobb för batch-avskrifter schemaläggs enligt bästa prestanda.
Det går inte att beräkna när ett jobb ska ändras till körnings tillstånd, men det bör ske inom några minuter under normal system belastning. När den är i körnings tillstånd sker avskriften snabbare än uppspelnings hastigheten för ljud körningen.

## <a name="prerequisites"></a>Förutsättningar

Precis som med alla funktioner i tal tjänsten skapar du en prenumerations nyckel från [Azure Portal](https://portal.azure.com) genom att följa vår [Guide för att komma igång](overview.md#try-the-speech-service-for-free).

>[!NOTE]
> En standard prenumeration (S0) för tal tjänst krävs för att använda batch-avskriftering. Kostnads fria prenumerations nycklar (F0) fungerar inte. Mer information finns i [priser och begränsningar](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Om du planerar att anpassa modeller följer du stegen i [akustisk anpassning](./how-to-custom-speech-train-model.md) och [språk anpassning](./how-to-custom-speech-train-model.md). Om du vill använda de skapade modellerna i batch-avskriftering behöver du deras modell plats. Du kan hämta modell platsen när du har granskat informationen om modellen ( `self` egenskap). Det *behövs ingen* distribuerad anpassad slut punkt för batch-avskrifts tjänsten.

>[!NOTE]
> Som en del av REST API har batch-avskriften en uppsättning [kvoter och begränsningar](speech-services-quotas-and-limits.md#batch-transcription)som vi uppmuntrar att granska. För att dra full nytta av batch-avskrifts möjligheten att effektivt skriva över ett stort antal ljudfiler rekommenderar vi att du alltid skickar flera filer per begäran eller pekar på en Blob Storage behållare med ljudfilerna som ska skrivas över. Tjänsten kommer att skriva över filerna samtidigt som den minskar tiden för leverans. Att använda flera filer i en enskild begäran är mycket enkelt och enkelt att se [konfigurations](#configuration) avsnittet. 

## <a name="batch-transcription-api"></a>API för batch-avskriftering

API: et för batch-avskrift stöder följande format:

| Format | ADPCM | Bitar per sampel | Samplings frekvens             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16-bitars  | 8 kHz eller 16 kHz, mono eller stereo |
| MP3    | PCM   | 16-bitars  | 8 kHz eller 16 kHz, mono eller stereo |
| OGG    | OPUS  | 16-bitars  | 8 kHz eller 16 kHz, mono eller stereo |

För stereo ljud strömmar delas vänster och höger kanaler upp under avskriften. En JSON-resultat fil skapas för varje kanal.
Om du vill skapa en sorterad slutlig avskrift använder du de tidsstämplar som genereras per uttryck.

### <a name="configuration"></a>Konfiguration

Konfigurations parametrar tillhandahålls som JSON. 

**Att skriva av en eller flera enskilda filer.** Om du har fler än en fil för att skriva över rekommenderar vi att du skickar flera filer i en begäran. Exemplet nedan använder tre filer:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

**Bearbetar en hel lagrings behållare.** Behållar- [SAS](../../storage/common/storage-sas-overview.md) ska innehålla `r` (läsa) och `l` (lista) behörigheter:

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

**Använd en anpassad tränad modell i en batch-avskriftering.** Exemplet använder tre filer:

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>Konfigurations egenskaper

Använd dessa valfria egenskaper för att konfigurera avskrifter:

:::row:::
   :::column span="1":::
      **Parameter**
   :::column-end:::
   :::column span="2":::
      **Beskrivning**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Valfritt, standardvärdet är `Masked` . Anger hur du hanterar svordomar i igenkännings resultat. Godkända värden är `None` att inaktivera filtrering av svordomar, `Masked` för att ersätta svordomar med asterisker, `Removed` för att ta bort alla svordomar från resultatet eller `Tags` för att lägga till taggar av "svordomar".
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Valfritt, standardvärdet är `DictatedAndAutomatic` . Anger hur interpunktion ska hanteras i igenkännings resultat. Godkända värden är `None` att inaktivera interpunktion, `Dictated` för att göra det explicit (talade) skiljetecken `Automatic` att tillåta avkodaren att hantera skiljetecken eller `DictatedAndAutomatic` att använda dikterad och automatisk interpunktion.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Valfritt, `false` som standard. Anger om Word-nivåns tidsstämplar ska läggas till i utdata.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Valfritt, `false` som standard. Anger att diarization-analys ska utföras på indatamängden, vilket förväntas vara en svartvit kanal som innehåller två röster. Obs!: måste `wordLevelTimestampsEnabled` anges till `true` .
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Valfritt `0` och har `1` tilldelats som standard. En matris med kanal nummer att bearbeta. Här kan du ange en delmängd av tillgängliga kanaler i ljud filen som ska bearbetas (till exempel `0` endast).
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Valfritt, ingen borttagning som standard. Varaktigheten för att automatiskt ta bort avskrifter när avskriften har slutförts. `timeToLive`Är användbart vid Mass bearbetnings avskrifter för att se till att de kommer att tas bort (till exempel `PT12H` i 12 timmar).
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Valfri URL med [ad hoc SAS](../../storage/common/storage-sas-overview.md) till en skrivbar behållare i Azure. Resultatet lagras i den här behållaren. SAS med lagrad åtkomst princip stöds **inte** . Om detta inte anges lagrar Microsoft resultaten i en lagrings behållare som hanteras av Microsoft. När avskriften tas bort genom att anropar [ta bort avskrift](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)tas även resultat data bort.
:::row-end:::

### <a name="storage"></a>Storage

Batch-avskrifter kan läsa ljud från en offentlig och synlig Internet-URI och kan läsa ljud-eller Skriv avskrifter med en SAS-URI med [Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md).

## <a name="batch-transcription-result"></a>Resultat av batch-avskrift

För varje ljud inspelning skapas en resultat fil för avskrift.
Åtgärden [Hämta avskrifter](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles) returnerar en lista med resultat filer för den här avskriften. Om du vill hitta avskrifts filen för en speciell indatafil filtrerar du alla returnerade filer med `kind`  ==  `Transcription` och `name`  ==  `{originalInputName.suffix}.json` .

Varje avskrifts resultat fil har följande format:

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"          
      "speaker": 1,                     // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
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

Resultatet innehåller följande fält:

:::row:::
   :::column span="1":::
      **Fält**
   :::column-end:::
   :::column span="2":::
      **Innehåll**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      De faktiska orden som identifieras.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Inverterad text – normaliserad text för den tolkade texten. Förkortningar ("läkare Smith" till "Dr Smith"), telefonnummer och andra transformeringar tillämpas.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      REDUNDANSVÄXLINGAR-formuläret med en svordom som används.
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      Den tolkade textens visnings form. Tillagd interpunktion och versaler ingår.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Högtalar separation (diarization)

Diarization är en process för att åtskilja högtalare i ett ljud. Batch-pipeline stöder diarization och kan identifiera två högtalare på svartvita kanaler. Funktionen är inte tillgänglig i stereo inspelningar.

Resultatet av avskrift med diarization aktiverat innehåller en `Speaker` post för varje uppskriven fras. Om diarization inte används finns `Speaker` inte egenskapen i JSON-utdata. För diarization har vi stöd för två röster, så att högtalarna identifieras som `1` eller `2` .

Om du vill begära diarization, lägger du till ange `diarizationEnabled` egenskapen till `true` som http-begäran visas nedan.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Tidsstämplar på Word-nivå måste vara aktiverade eftersom parametrarna i ovanstående begäran visar.

## <a name="best-practices"></a>Bästa praxis

Batch-avskrifts tjänsten kan hantera ett stort antal skickade avskrifter. Du kan fråga efter status för dina avskrifter med [Erhåll avskrifter](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions).
Anropa [ta bort avskrift](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) regelbundet från tjänsten när du har hämtat resultatet. Alternativt kan du ange `timeToLive` egenskapen för att säkerställa eventuell borttagning av resultaten.

## <a name="sample-code"></a>Exempelkod

Fullständiga exempel är tillgängliga i [GitHub-exempel arkivet](https://aka.ms/csspeech/samples) i under `samples/batch` katalogen.

Uppdatera exempel koden med din prenumerations information, tjänste region, URI som pekar på ljud filen för att skriva och modell plats om du använder en anpassad modell.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

Exempel koden konfigurerar klienten och skickar in avskrifts förfrågan. Den söker sedan efter statusinformation och skriver ut information om avskrifts förloppet.

```csharp
// get the status of our transcriptions periodically and log results
int completed = 0, running = 0, notStarted = 0;
while (completed < 1)
{
    completed = 0; running = 0; notStarted = 0;

    // get all transcriptions for the user
    paginatedTranscriptions = null;
    do
    {
        // <transcriptionstatus>
        if (paginatedTranscriptions == null)
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync().ConfigureAwait(false);
        }
        else
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync(paginatedTranscriptions.NextLink).ConfigureAwait(false);
        }

        // delete all pre-existing completed transcriptions. If transcriptions are still running or not started, they will not be deleted
        foreach (var transcription in paginatedTranscriptions.Values)
        {
            switch (transcription.Status)
            {
                case "Failed":
                case "Succeeded":
                    // we check to see if it was one of the transcriptions we created from this client.
                    if (!createdTranscriptions.Contains(transcription.Self))
                    {
                        // not created form here, continue
                        continue;
                    }

                    completed++;

                    // if the transcription was successful, check the results
                    if (transcription.Status == "Succeeded")
                    {
                        var paginatedfiles = await client.GetTranscriptionFilesAsync(transcription.Links.Files).ConfigureAwait(false);

                        var resultFile = paginatedfiles.Values.FirstOrDefault(f => f.Kind == ArtifactKind.Transcription);
                        var result = await client.GetTranscriptionResultAsync(new Uri(resultFile.Links.ContentUrl)).ConfigureAwait(false);
                        Console.WriteLine("Transcription succeeded. Results: ");
                        Console.WriteLine(JsonConvert.SerializeObject(result, SpeechJsonContractResolver.WriterSettings));
                    }
                    else
                    {
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.Properties.Error.Message);
                    }

                    break;

                case "Running":
                    running++;
                    break;

                case "NotStarted":
                    notStarted++;
                    break;
            }
        }

        // for each transcription in the list we check the status
        Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));
    }
    while (paginatedTranscriptions.NextLink != null);

    // </transcriptionstatus>
    // check again after 1 minute
    await Task.Delay(TimeSpan.FromMinutes(1)).ConfigureAwait(false);
}
```

Fullständig information om föregående anrop finns i vårt Swagger- [dokument](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). För det fullständiga exemplet som visas här går du till [GitHub](https://aka.ms/csspeech/samples) i under `samples/batch` katalogen.

I det här exemplet används en asynkron installation för att skicka ljud-och mottagnings status.
`PostTranscriptions`Metoden skickar ljud filens information och `GetTranscriptions` metoden tar emot tillstånden.
`PostTranscriptions` Returnerar en referens och `GetTranscriptions` använder den för att skapa en referens för att hämta avskrifts status.

Den här exempel koden anger inte en anpassad modell. Tjänsten använder bas linje modellen för att skriva över filen eller filerna. Om du vill ange modellen kan du överföra modell referensen för den anpassade modellen till samma metod.

> [!NOTE]
> För bas linje avskrifter behöver du inte deklarera ID: t för bas linje modellen.

## <a name="next-steps"></a>Nästa steg

- [Tal till text v3 API-referens](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
