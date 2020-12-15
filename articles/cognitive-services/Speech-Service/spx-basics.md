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
ms.openlocfilehash: 1b92d1b5853d6b794ebdcf0e2052b8f15081d608
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507582"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Lär dig grunderna i tal-CLI

I den här artikeln får du lära dig de grundläggande användnings mönstren för tal-CLI, ett kommando rads verktyg för att använda tal tjänsten utan att skriva kod. Du kan snabbt testa huvud funktionerna i tal tjänsten, utan att skapa utvecklings miljöer eller skriva kod, för att se om dina användnings fall kan uppfyllas korrekt. Tal CLI är produktions klart och kan användas för att automatisera enkla arbets flöden i tal tjänsten med hjälp av `.bat` eller Shell-skript.

Den här artikeln förutsätter att du har kunskaper om kommando tolken, Terminal eller PowerShell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Grundläggande användning

I det här avsnittet visas några grundläggande SPX-kommandon som ofta är användbara vid första testningen och experimentering. Börja med att visa hjälpen som är inbyggd i verktyget genom att köra följande kommando.

```shell
spx
```

Meddelande **se:** hjälp avsnitt som listas rätt av kommando parametrar. Du kan ange de här kommandona för att få detaljerad hjälp om underordnade kommandon.

Du kan söka i hjälp avsnitten efter nyckelord. Ange till exempel följande kommando för att visa en lista över exempel på tal-CLI-användning:

```shell
spx help find --topics "examples"
```

Ange följande kommando för att se alternativen för kommandot recognize:

```shell
spx help recognize
```

Nu ska vi använda tal CLI för att utföra tal igenkänning med systemets standard-mikrofon. 

>[!WARNING]
> Om du använder en Docker-behållare fungerar inte det här kommandot.

Kör följande kommando:

```shell
spx recognize --microphone
```

Med tal CLI kan du också känna igen tal från en ljudfil.

```shell
spx recognize --file /path/to/file.wav
```
> [!TIP]
> Om du känner igen tal från en ljudfil i en Docker-behållare ser du till att ljud filen finns i den katalog som du monterade i föregående steg.

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

### <a name="configuration-files-in-the-datastore"></a>Konfigurationsfiler i data lagret

Tal CLI-beteendet kan vara beroende av inställningar i konfigurationsfiler, som du kan referera till i tal CLI-anrop med hjälp av en \@ symbol.
Tal-CLI sparar en ny inställning i en ny under `./spx/data` katalog som skapas i den aktuella arbets katalogen.
När du söker efter ett konfigurations värde, ser tal-CLI ut i din aktuella arbets katalog, sedan i data lagret på `./spx/data` och sedan i andra data lager, inklusive ett slutgiltigt skrivskyddat data lager i `spx` binärfilen.
Tidigare använde du data lagret för att spara dina `@key` och `@region` -värden, så du behöver inte ange dem med varje kommando rads anrop.
Du kan också använda konfigurationsfiler för att lagra egna konfigurations inställningar, eller till och med använda dem för att skicka URL: er eller annat dynamiskt innehåll som genereras vid körning.

Det här avsnittet visar hur du använder en konfigurations fil i det lokala data lagret för att lagra och hämta kommando inställningar med `spx config` , och lagra utdata från tal CLI med `--output` alternativet.

I följande exempel rensas `@my.defaults` konfigurations filen, nyckel/värde-par för **nyckel** och **region** i filen används, och konfigurationen används i ett anrop till `spx recognize` .

```shell
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

Du kan också skriva dynamiskt innehåll till en konfigurations fil. Följande kommando skapar till exempel en anpassad tal modell och lagrar URL: en för den nya modellen i en konfigurations fil. Nästa kommando väntar tills modellen på denna URL är klar att användas innan den returneras.

```shell
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

I följande exempel skrivs två URL: er till `@my.datasets.txt` konfigurations filen.
I det här scenariot `--output` kan du **lägga till** ett valfritt nyckelord för att skapa en konfigurations fil eller lägga till det befintliga.


```shell
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Om du vill ha mer information om datalager-filer, inklusive använda standardfiler ( `@spx.default` , `@default.config` , och `@*.default.config` för kommandobaserade standardinställningar), anger du följande kommando:

```shell
spx help advanced setup
```

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

## <a name="synthesize-speech-to-a-file"></a>Syntetisera tal till en fil

Kör följande kommando för att ändra utdata från din talare till en `.wav` fil.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Tal-CLI kommer att producera naturligt språk på engelska i `greetings.wav` ljud filen.
I Windows kan du spela upp ljud filen genom att ange `start greetings.wav` .


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

* Slutför snabb starten för [tal igenkänning](get-started-speech-to-text.md?pivots=programmer-tool-spx) eller [tal syntes](get-started-text-to-speech.md?pivots=programmer-tool-spx) med tal-cli.
