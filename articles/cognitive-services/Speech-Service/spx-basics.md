---
title: Snabb start för tal CLI – tal tjänst
titleSuffix: Azure Cognitive Services
description: Kom igång med Azure tal CLI. Du kan interagera med tal tjänster som tal till text, text till tal och tal översättning utan att skriva kod.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: trbye
ms.openlocfilehash: 4a6c7b36665c7a38534ce8e470bc8b327c274d95
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "99095194"
---
# <a name="get-started-with-the-azure-speech-cli"></a>Kom igång med Azure Speech CLI

I den här artikeln får du lära dig hur du använder tal-CLI, ett kommando rads gränssnitt, för att få åtkomst till tal tjänster som tal till text, text till tal och tal översättning utan att skriva kod. Tal CLI är produktions klart och kan användas för att automatisera enkla arbets flöden i tal tjänsten med hjälp av `.bat` eller Shell-skript.

Den här artikeln förutsätter att du har kunskaper om kommando tolken, terminalen eller PowerShell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Grundläggande användning

I det här avsnittet visas några grundläggande SPX-kommandon som ofta är användbara vid första testningen och experimentering. Börja med att visa hjälpen som är inbyggd i verktyget genom att köra följande kommando.

```console
spx
```

Du kan söka i hjälp avsnitten efter nyckelord. Ange till exempel följande kommando för att visa en lista över exempel på tal-CLI-användning:

```console
spx help find --topics "examples"
```

Ange följande kommando för att se alternativen för kommandot recognize:

```console
spx help recognize
```

Ytterligare hjälp kommandon visas i den högra kolumnen. Du kan ange dessa kommandon för att få detaljerad hjälp om under kommandon.

## <a name="speech-to-text-speech-recognition"></a>Tal till text (tal igenkänning)

Vi använder tal CLI för att konvertera tal till text (tal igenkänning) med systemets standard mikrofon. När du har angett kommandot börjar SPX att lyssna efter ljud på den aktuella aktiva inmatnings enheten och stoppa när du trycker på **RETUR**. Det inspelade talet identifieras och konverteras sedan till text i konsolens utdata.

>[!IMPORTANT]
> Om du använder en Docker-behållare `--microphone` fungerar det inte.

Kör följande kommando:

```console
spx recognize --microphone
```

Med tal CLI kan du också känna igen tal från en ljudfil.

```console
spx recognize --file /path/to/file.wav
```

> [!TIP]
> Om du känner igen tal från en ljudfil i en Docker-behållare ser du till att ljud filen finns i den katalog som du monterade i föregående steg.

Glöm inte att om du har fastnat eller vill lära dig mer om tal CLIs igenkännings alternativ skriver du:

```console
spx help recognize
```

## <a name="text-to-speech-speech-synthesis"></a>Text till tal (tal syntes)

Att köra följande kommando tar text som indata och matar ut det syntetiska talet till den aktuella aktiva utdataenheten (till exempel dina dator högtalare).

```console
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Du kan också spara de syntetiserade utdata i filen. I det här exemplet ska vi skapa en fil med namnet `my-sample.wav` i katalogen som kommandot körs i.

```console
spx synthesize --text "We hope that you enjoy using the Speech CLI." --audio output my-sample.wav
```

I de här exemplen förutsätter vi att du testar på engelska. Vi stöder dock tal syntes på många olika språk. Du kan hämta en fullständig lista över röster med det här kommandot eller genom att besöka [sidan språk support](./language-support.md).

```console
spx synthesize --voices
```

Så här använder du en av de röster som du har identifierat.

```console
spx synthesize --text "Bienvenue chez moi." --voice fr-CA-Caroline --speakers
```

Glöm inte, om du har fastnat eller vill lära dig mer om tal CLIs syntes alternativ, skriver du bara:

```console
spx help synthesize
```

## <a name="speech-to-text-translation"></a>Tal till text översättning

Med tal CLI kan du också göra tal till text översättning. Kör det här kommandot om du vill avbilda ljud från standard mikrofonen och att skicka ut översättningen som text. Tänk på att du måste ange-och- `source` `target` språket med `translate` kommandot.

```console
spx translate --microphone --source en-US --target ru-RU
```

När du översätter till flera språk, separata språk koder med `;` .

```console
spx translate --microphone --source en-US --target ru-RU;fr-FR;es-ES
```

Om du vill spara resultatet av din översättning använder du `--output` flaggan. I det här exemplet ska du också läsa från en fil.

```console
spx translate --file /some/file/path/input.wav --source en-US --target ru-RU --output file /some/file/path/russian_translation.txt
```

> [!NOTE]
> I [artikeln språk och nationella inställningar](language-support.md) finns en lista över alla språk som stöds med motsvarande språk koder.

Glöm inte, om du har fastnat eller vill lära dig mer om översättnings alternativ för tal CLI, skriver du bara:

```console
spx help translate
```

## <a name="next-steps"></a>Nästa steg

* [Konfigurationsalternativ för Speech CLI](./spx-data-store-configuration.md)
* [Batch-åtgärder med tal-CLI](./spx-batch-operations.md)
