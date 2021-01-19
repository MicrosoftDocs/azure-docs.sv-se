---
title: Förbereda data för anpassade röst tal tjänster
titleSuffix: Azure Cognitive Services
description: Skapa en anpassad röst för ditt varumärke med röst tjänsten. Du anger Studio-inspelningar och tillhör ande skript, genererar tjänsten en unik röst modell som är anpassad till den inspelade rösten. Använd den här rösten för att syntetisera tal i dina produkter, verktyg och program.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 563a3e224ffedc98bcc3102ea865f06315294365
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573113"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Förbereda data för att skapa en anpassad röst

När du är redo att skapa en anpassad text till tal-röst för ditt program är det första steget att samla in ljud inspelningar och tillhör ande skript för att börja träna röst modellen. Tal tjänsten använder dessa data för att skapa en unik röst som passar rösten i inspelningarna. När du har tränat rösten kan du börja syntetisera tal i dina program.

Innan du kan träna din egen text till tal-röst modell behöver du ljud inspelningar och tillhör ande text avskrifter. På den här sidan granskar vi data typer, hur de används och hur du hanterar dem.

> [!NOTE]
> Om du vill träna en neurala röst måste du ange en röst personal-profil med den ljud medgivande fil som du har fått från röst personal som kan bekräfta att hans/hennes tal-data används för att träna en anpassad röst modell. När du förbereder ditt inspelnings skript måste du se till att ta med nedanstående mening. 

> "I [tillstånd ditt för-och efter namn] är du medveten om att inspelningar av min röst kommer att användas av [stat namnet på företaget] för att skapa och använda en syntetisk version av rösten."
Den här meningen används för att kontrol lera om inlärnings data utförs av samma person som gör ditt medgivande. Läs mer om [röst personal-verifiering](https://aka.ms/CNV-data-privacy) här.

> Anpassad neurala-röst är tillgänglig med begränsad åtkomst. Se till att du förstår de [ansvariga AI-kraven](https://aka.ms/gating-overview) och [Använd åtkomsten här](https://aka.ms/customneural). 

## <a name="data-types"></a>Datatyper

En data uppsättning för röst utbildning innehåller ljud inspelningar och en textfil med tillhör ande avskrifter. Varje ljudfil ska innehålla en enda uttryck (en enda mening eller en enskild tur för ett dialog system) och vara mindre än 15 sekunder lång.

I vissa fall kanske du inte har rätt data uppsättning klar och vill testa den anpassade röst utbildningen med tillgängliga ljudfiler, kort eller lång, med eller utan avskrifter. Vi tillhandahåller verktyg (beta) som hjälper dig att segmentera ditt ljud i yttranden och förbereda avskrifter med hjälp av [batch-Avskrifts-API: et](batch-transcription.md).

Den här tabellen innehåller data typer och hur de används för att skapa en anpassad röst modell för text till tal.

| Datatyp | Beskrivning | När du ska använda detta | Ytterligare bearbetning krävs | 
| --------- | ----------- | ----------- | --------------------------- |
| **Individuell yttranden + matchande avskrift** | En samling (. zip) av ljudfiler (. wav) som enskilda yttranden. Varje ljudfil bör vara högst 15 sekunder, länkad med en formaterad avskrift (. txt). | Professionella inspelningar med matchande avskrifter | Redo för utbildning. |
| **Långt ljud + avskrift (beta)** | En samling (. zip) av långa, segmenterade ljudfiler (längre än 20 sekunder), kopplad till en avskrift (. txt) som innehåller alla talade ord. | Du har ljudfiler och matchande avskrifter, men de är inte segmenterade i yttranden. | Segmentering (med batch-avskriftering).<br>Omvandling av ljud format där det behövs. | 
| **Endast ljud (beta)** | En samling (. zip) av ljudfiler utan avskrift. | Du har bara ljudfiler tillgängliga, utan avskrifter. | Segmentering och avskrifts generering (med batch-avskriftering).<br>Omvandling av ljud format där det behövs.| 

Filerna ska grupperas efter typ i en data uppsättning och laddas upp som en zip-fil. Varje data uppsättning får bara innehålla en enda datatyp.

> [!NOTE]
> Det maximala antalet data uppsättningar som får importeras per prenumeration är 10 zip-filer för kostnads fria prenumerationer (F0) och 500 för S0-användare (standard prenumeration).

## <a name="individual-utterances--matching-transcript"></a>Individuell yttranden + matchande avskrift

Du kan förbereda inspelningar av enskilda yttranden och den matchande avskriften på två sätt. Skriv antingen ett skript och låt det läsas av en röst personal eller Använd allmänt tillgänglig ljud och skriv det till text. Om du gör det senare kan du redigera disfluencies från ljudfilerna, till exempel "UM" och andra fyllnings ljud, hackare, mumbled ord eller feluttal.

Om du vill skapa en bra röst modell skapar du inspelningar i ett tyst rum med en mikrofon med hög kvalitet. Konsekvent volym, tal hastighet, talande färgdjup och lättfattliga programspecifika mannerisms av tal är viktigt.

> [!TIP]
> Om du vill skapa en röst för produktions användning rekommenderar vi att du använder en proffsig inspelning Studio och röst personal. Mer information finns i [så här spelar du in röst exempel för en anpassad röst](record-custom-voice-samples.md).

### <a name="audio-files"></a>Ljudfiler

Varje ljudfil bör innehålla en enda uttryck (en enda mening eller ett enda sätt i ett dialog system) som är mindre än 15 sekunder långt. Alla filer måste vara på samma talade språk. Anpassade text till tal-röster med flera språk stöds inte, med undantag för Chinese-English bi-språk. Varje ljudfil måste ha ett unikt numeriskt fil namn med fil namns tillägget. wav.

Följ dessa rikt linjer när du förbereder ljud.

| Egenskap | Värde |
| -------- | ----- |
| Filformat | RIFF (. wav), grupperad i en. zip-fil |
| Samplings frekvens | Minst 16 000 Hz |
| Samplingsformat | PCM, 16-bitars |
| Filnamn | Numeriskt, med fil namns tillägget. wav. Inga dubbla fil namn tillåts. |
| Ljud längd | Kortare än 15 sekunder |
| Arkiv format | .zip |
| Maximal Arkiv storlek | 2048 MB |

> [!NOTE]
> WAV-filer med en samplings frekvens som är lägre än 16 000 Hz avvisas. Om en. zip-fil innehåller. wav-filer med olika samplings frekvenser importeras bara de som är lika med eller högre än 16 000 Hz. Portalen importeras. zip-arkiv är upp till 200 MB. Men flera arkiv kan laddas upp.

### <a name="transcripts"></a>Avskrifter

Avskrifts filen är en textfil med oformaterad text. Använd dessa rikt linjer för att förbereda dina avskrifter.

| Egenskap | Värde |
| -------- | ----- |
| Filformat | Oformaterad text (. txt) |
| Kodnings format | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE eller UTF-16-vara. För zh-CN-, ANSI/ASCII-och UTF-8-kodningar stöds inte. |
| antal yttrande per rad | **En** – varje rad i avskrifts filen ska innehålla namnet på en av ljudfilerna följt av motsvarande avskrift. Filnamnet och transkriptionen ska separeras med ett tabbtecken (\t). |
| Maximal filstorlek | 2048 MB |

Nedan visas ett exempel på hur avskrifterna organiseras uttryck av uttryck i en txt-fil:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Det är viktigt att avskrifterna är 100% exakta avskrifter av motsvarande ljud. Fel i avskrifterna kommer att medföra kvalitets förluster under utbildningen.

## <a name="long-audio--transcript-beta"></a>Långt ljud + avskrift (beta)

I vissa fall kanske du inte har segmenterat ljud tillgängligt. Vi tillhandahåller en tjänst (beta) via den anpassade röst portalen som hjälper dig att segmentera långa ljudfiler och skapa avskrifter. Kom ihåg att den här tjänsten kommer att debiteras mot din prenumerations användning för tal till text.

> [!NOTE]
> Tjänsten för tjänst för lång ljuds segmentering använder funktionen för batch-avskrifter i tal-till-text, som endast stöder S0-användare (standard prenumeration). Under bearbetningen av segmentering skickas ljudfiler och avskrifter också till Custom Speech tjänsten för att förfina igenkännings modellen så att precisionen kan förbättras för dina data. Inga data kommer att behållas under den här processen. När segmenteringen är färdig lagras endast yttranden segmenterad och deras mappnings avskrifter för hämtning och träning.

### <a name="audio-files"></a>Ljudfiler

Följ dessa rikt linjer när du förbereder ljud för segmentering.

| Egenskap | Värde |
| -------- | ----- |
| Filformat | RIFF (. wav) med en samplings frekvens på minst 16 kHz – 16-bitars i PCM eller. mp3 med en bit hastighet på minst 256 kbit/s, grupperad i en. zip-fil |
| Filnamn | ASCII-och Unicode-tecken stöds. Dubbla namn tillåts inte. |
| Ljud längd | Längre än 20 sekunder |
| Arkiv format | .zip |
| Maximal Arkiv storlek | 2048 MB |

Alla ljudfiler ska grupperas i en zip-fil. Det är OK att skicka WAV-filer och. mp3-filer till ett enda ljud-zip. Du kan till exempel Ladda upp en zip-fil som innehåller en ljudfil med namnet "kingstory. wav", 45-Second-Long och ett annat ljud med namnet "queenstory.mp3", 200-sekund-lång. Alla. mp3-filer omvandlas till. wav-formatet efter bearbetning.

### <a name="transcripts"></a>Avskrifter

Avskrifter måste förberedas enligt de specifikationer som anges i den här tabellen. Varje ljudfil måste matchas med en avskrift.

| Egenskap | Värde |
| -------- | ----- |
| Filformat | Oformaterad text (. txt), grupperad i en. zip |
| Filnamn | Använd samma namn som den matchande ljud filen |
| Kodnings format | UTF-8-endast struktur |
| antal yttrande per rad | Obegränsad |
| Maximal filstorlek | 2048 MB |

Alla avskrifts filer i den här data typen ska grupperas i en zip-fil. Du har exempelvis laddat upp en zip-fil som innehåller en ljudfil med namnet "kingstory. wav", 45 sekunder och en annan med namnet "queenstory.mp3", 200 sekunder lång. Du måste ladda upp en annan zip-fil som innehåller två avskrifter, en med namnet kingstory.txt, den andra queenstory.txt. I varje oformaterad textfil kommer du att ange en fullständig korrekt avskrift för det matchande ljudet.

När din data uppsättning har laddats upp hjälper vi dig att segmentera ljud filen i yttranden baserat på avskriften. Du kan kontrol lera de segmenterade yttranden och de matchande avskrifterna genom att hämta data uppsättningen. Unika ID: n tilldelas automatiskt till segmentets yttranden. Det är viktigt att du ser till att avskrifterna du anger är 100% korrekta. Fel i avskrifterna kan minska noggrannheten under ljud segmentering och ger ytterligare kvalitets förluster i den utbildnings fas som kommer senare.

## <a name="audio-only-beta"></a>Endast ljud (beta)

Om du inte har avskrifter för ljud inspelningar använder du alternativet **endast ljud** för att ladda upp dina data. Vårt system kan hjälpa dig att segmentera och skriva av ljudfiler. Tänk på att den här tjänsten kommer att räknas mot din prenumerations användning från tal till text.

Följ dessa rikt linjer när du förbereder ljud.

> [!NOTE]
> Tjänsten för tjänst för lång ljuds segmentering använder funktionen för batch-avskrifter i tal-till-text, som endast stöder S0-användare (standard prenumeration).

| Egenskap | Värde |
| -------- | ----- |
| Filformat | RIFF (. wav) med en samplings frekvens på minst 16 kHz – 16-bitars i PCM eller. mp3 med en bit hastighet på minst 256 kbit/s, grupperad i en. zip-fil |
| Filnamn | ASCII-och Unicode-tecken stöds. Inget dubblettnamn tillåts. |
| Ljud längd | Längre än 20 sekunder |
| Arkiv format | .zip |
| Maximal Arkiv storlek | 2048 MB |

Alla ljudfiler ska grupperas i en zip-fil. När din data uppsättning har laddats upp kommer vi att hjälpa dig att segmentera ljud filen i yttranden baserat på vår Speech batch avskrifting-tjänst. Unika ID: n tilldelas automatiskt till segmentets yttranden. Matchande avskrifter genereras genom tal igenkänning. Alla. mp3-filer omvandlas till. wav-formatet efter bearbetning. Du kan kontrol lera de segmenterade yttranden och de matchande avskrifterna genom att hämta data uppsättningen.

## <a name="next-steps"></a>Nästa steg

- [Skapa en anpassad röst](how-to-custom-voice-create-voice.md)
- [Guide: spela in dina röst exempel](record-custom-voice-samples.md)
