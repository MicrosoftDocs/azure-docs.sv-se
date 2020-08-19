---
title: Grunderna för tal-CLI
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder kommando verktyget för tal-CLI för att arbeta med tal tjänsten utan kod och minimal konfiguration.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 2f5a1d190c6e63056c2377641446f617edaa1bd3
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590225"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Lär dig grunderna i tal-CLI

I den här artikeln får du lära dig de grundläggande användnings mönstren för tal-CLI, ett kommando rads verktyg för att använda tal tjänsten utan att skriva kod. Du kan snabbt testa huvud funktionerna i tal tjänsten, utan att skapa utvecklings miljöer eller skriva kod, för att se om dina användnings fall kan uppfyllas korrekt. Dessutom är tal CLI produktions klart och kan användas för att automatisera enkla arbets flöden i tal tjänsten med hjälp av `.bat` eller Shell-skript.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Grundläggande användning

I det här avsnittet visas några grundläggande SPX-kommandon som ofta är användbara vid första testningen och experimentering. Börja med att visa hjälpen som är inbyggd i verktyget genom att köra följande kommando.

```shell
spx
```

Obs! **Se även** hjälp avsnitt som visas som stämmer överens med kommando parametrar. Du kan också söka i ämnen efter nyckelord. Ange till exempel följande kommando för att visa en lista över hjälp avsnitt om exempel på tal-CLI:

```shell
spx help find --topics "examples"
```

Använd nu tal tjänsten för att utföra en del tal igenkänning med hjälp av standard mikrofonen genom att köra följande kommando.

```shell
spx recognize --microphone
```

När du har angett kommandot börjar SPX att lyssna efter ljud på den aktuella aktiva inmatnings enheten och stoppa efter att du har klickat på `ENTER` . Det inspelade talet identifieras och konverteras sedan till text i konsolens utdata. Text till tal-syntes är också enkelt att göra med hjälp av tal CLI. 

Genom att köra följande kommando tar du med den angivna texten som indata och skriver ut det syntetiska talet till den aktuella aktiva utdataenheten.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Förutom tal igenkänning och syntes kan du också göra tal översättning med tal-CLI. Precis som i tal igenkännings kommandot ovan, kör du följande kommando för att avbilda ljud från standard mikrofonen och utföra översättning till text på mål språket.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

I det här kommandot anger du både källan (språk att översätta **från**) och målet (språk att översätta **till**). Genom att använda `--microphone` argumentet lyssnar du på ljud på den aktuella aktiva Indataporten och slutar när du har klickat på `ENTER` . Utdata är en text översättning till mål språket som skrivs till en textfil.

> [!NOTE]
> I [artikeln språk och nationella inställningar](language-support.md) finns en lista över alla språk som stöds med motsvarande språk koder.

## <a name="batch-operations"></a>Batch-åtgärder

Kommandona i föregående avsnitt är bra för att snabbt se hur röst tjänsten fungerar. Men när du bedömer om användningen av dina användnings fall kan uppfyllas, behöver du förmodligen utföra batch-åtgärder mot en mängd inmatade inläsningar för att se hur tjänsten hanterar en mängd olika scenarier. I det här avsnittet visas hur du:

* Kör tal igenkänning för batch på en katalog med ljudfiler
* Iterera genom en `.tsv` fil och kör batch text-till-tal syntes

## <a name="batch-speech-recognition"></a>Tal igenkänning för batch

Om du har en katalog med ljudfiler är det enkelt att använda tal-CLI för att snabbt köra batch-tal igenkänning. Kör bara följande kommando, som pekar på din katalog med `--files` kommandot. I det här exemplet lägger du till `\*.wav` katalogen för att identifiera alla `.wav` filer som finns i katalogen. Ange dessutom `--threads` argumentet för att köra igenkänningen på 10 parallella trådar.

> [!NOTE]
> `--threads`Argumentet kan också användas i nästa avsnitt för `spx synthesize` kommandon och de tillgängliga trådarna är beroende av CPU och dess aktuella inläsnings procent.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

De tal utdata som identifieras skrivs till `speech_output.tsv` med hjälp av `--output file` argumentet. Följande är ett exempel på utdatafilens struktur.

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-synthesis"></a>Sammanfattning av text till tal-sats

Det enklaste sättet att köra batch text-till-tal är att skapa en ny `.tsv` (tabbavgränsad) fil och använda `--foreach` kommandot i tal-cli. Tänk på följande fil `text_synthesis.tsv` :

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 Sedan kör du ett kommando för att peka på `text_synthesis.tsv` , utföra syntes på varje `text` fält och skriva resultatet till motsvarande `audio.output` sökväg som en `.wav` fil. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Det här kommandot är detsamma som att köra `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **för varje** post i `.tsv` filen. Ett par saker att tänka på:

* Kolumn rubrikerna `audio.output` och `text` motsvarar kommando rads argumenten respektive `--audio output` `--text` . Kommando rads argument för flera delar som `--audio output` ska formateras i filen utan mellanslag, inga inledande streck och punkter separerar strängar, t. ex. `audio.output` . Eventuella andra befintliga kommando rads argument kan läggas till i filen som ytterligare kolumner med det här mönstret.
* När filen är formaterad på det här sättet behöver inga ytterligare argument skickas till `--foreach` .
* Se till att avgränsa varje värde i `.tsv` med en **flik**.

Men om du har en `.tsv` fil som följande exempel, med kolumn rubriker som **inte matchar** kommando rads argument:

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

Du kan åsidosätta dessa fält namn med rätt argument med hjälp av följande syntax i `--foreach` anropet. Detta är samma anrop som ovan.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Nästa steg

* Slutför snabb starten för [tal igenkänning](./quickstarts/speech-to-text-from-microphone.md) eller [tal syntes](./quickstarts/text-to-speech.md) med hjälp av SDK.
