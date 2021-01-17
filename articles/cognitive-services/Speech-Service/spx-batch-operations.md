---
title: Batch-åtgärder i tal CLI – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du gör batch-tal till text (tal igenkänning), batch-text till tal (tal syntes) med tal-CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: 60cacafc89f2335b87885e4ea11dcf8992d68c3f
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540068"
---
# <a name="speech-cli-batch-operations"></a>Batch-åtgärder för tal CLI

Vanliga uppgifter när du använder Azure Speech Services är batch-åtgärder. I den här artikeln får du lära dig att göra batch-tal till text (tal igenkänning), batch-text till tal (tal syntes) med tal-CLI. Mer specifikt får du lära dig att

* Kör tal igenkänning för batch på en katalog med ljudfiler
* Köra batch-tal syntes genom att iterera över en `.tsv` fil

## <a name="batch-speech-to-text-speech-recognition"></a>Batch-tal till text (tal igenkänning)

Tal tjänsten används ofta för att känna igen tal från ljudfiler. I det här exemplet lär du dig hur du itererar över en katalog med hjälp av tal-CLI för att samla in igenkännings resultatet för varje `.wav` fil. `--files`Flaggan används för att peka på den katalog där ljudfiler lagras och jokertecknet `*.wav` används för att INSTRUERA tal CLI att köra igenkänning på alla filer med fil namns tillägget `.wav` . Utdata för varje igenkännings fil skrivs som ett tabbavgränsade värde i `speech_output.tsv` .

> [!NOTE]
> `--threads`Argumentet kan också användas i nästa avsnitt för `spx synthesize` kommandon och de tillgängliga trådarna är beroende av CPU och dess aktuella inläsnings procent.

```console
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

Följande är ett exempel på utdatafilens struktur.

```output
audio.input.id  recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-speech-synthesis"></a>Batch-text till tal (tal syntes)

Det enklaste sättet att köra batch text-till-tal är att skapa en ny `.tsv` (tabbavgränsad) fil och använda `--foreach` kommandot i tal-cli. Du kan skapa en `.tsv` fil med hjälp av din text redigerare, i det här exemplet ska vi kalla den `text_synthesis.tsv` :

>[!IMPORTANT]
> När du kopierar innehållet i den här text filen måste du se till att filen har en **TABB** som inte är blank steg mellan fil platsen och texten. När innehållet kopieras från det här exemplet konverteras till exempel tabbar till blank steg som gör `spx` att kommandot inte fungerar när det körs.

```Input
audio.output    text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Sedan kör du ett kommando för att peka på `text_synthesis.tsv` , utföra syntes på varje `text` fält och skriva resultatet till motsvarande `audio.output` sökväg som en `.wav` fil.

```console
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Det här kommandot är detsamma som att köra `spx synthesize --text "Sample text to synthesize" --audio output C:\batch_wav_output\wav_1.wav` **för varje** post i `.tsv` filen.

Ett par saker att tänka på:

* Kolumn rubrikerna `audio.output` och `text` motsvarar kommando rads argumenten `--audio output` respektive `--text` . Kommando rads argument för flera delar som `--audio output` ska formateras i filen utan mellanslag, inga inledande streck och punkter separerar strängar, till exempel `audio.output` . Eventuella andra befintliga kommando rads argument kan läggas till i filen som ytterligare kolumner med det här mönstret.
* När filen är formaterad på det här sättet behöver inga ytterligare argument skickas till `--foreach` .
* Se till att avgränsa varje värde i `.tsv` med en **flik**.

Men om du har en `.tsv` fil som följande exempel, med kolumn rubriker som **inte matchar** kommando rads argument:

```Input
wav_path    str_text
C:\batch_wav_output\wav_1.wav   Sample text to synthesize.
C:\batch_wav_output\wav_2.wav   Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav   Some more text to test capabilities.
```

Du kan åsidosätta dessa fält namn med rätt argument med hjälp av följande syntax i `--foreach` anropet. Detta är samma anrop som ovan.

```console
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Nästa steg

* [Översikt av Speech CLI](./spx-overview.md)
* [Snabb start för tal CLI](./spx-basics.md)
