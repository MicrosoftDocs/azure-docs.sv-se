---
title: Förbereda data för Custom Speech tal-tjänsten
titleSuffix: Azure Cognitive Services
description: När du testar precisionen för Microsoft tal igenkänning eller tränar dina anpassade modeller, behöver du ljud-och text data. På den här sidan tar vi upp de typer av data, hur de används och hur de hanteras.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 2c98546d20e9f977a605ccbac21010aa9b1dbadc
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232502"
---
# <a name="prepare-data-for-custom-speech"></a>Förbereda data för Custom Speech

När du testar precisionen för Microsoft tal igenkänning eller tränar dina anpassade modeller, behöver du ljud-och text data. På den här sidan tar vi upp de typer av data som en anpassad tal modell behöver.

## <a name="data-diversity"></a>Datamångfald

Text och ljud som används för att testa och träna en anpassad modell behöver innehålla exempel från en mängd olika högtalare och scenarier som du behöver din modell för att kunna identifiera.
Tänk på följande faktorer när du samlar in data för testning och utbildning av anpassade modeller:

* Dina text-och tal ljud data måste ta upp de typer av muntliga uttryck som användarna kommer att vidta när de interagerar med din modell. Till exempel kan en modell som höjer och sänker behovet av temperatur utbildning på utdrag som personer kan göra för att begära sådana ändringar.
* Dina data måste innehålla alla tal avvikelser som din modell måste känna igen. Många faktorer kan variera tal, inklusive accenter, dialekter, språk – mixning, ålder, kön, röst ton, stress nivå och tid på dagen.
* Du måste inkludera exempel från olika miljöer (inomhus, Utomhus, väg brus) där din modell ska användas.
* Ljud måste samlas in med maskin varu enheter som produktions systemet ska använda. Om din modell behöver identifiera tal som spelas in på inspelnings enheter av varierande kvalitet, måste de ljud data som du anger för att träna modellen även representera dessa olika scenarier.
* Du kan lägga till mer data i din modell senare, men var noga med att hålla data uppsättningen skild och representativ för dina projekt behov.
* Även om data som *inte* ligger inom dina anpassade modell igenkännings behov kan skada igenkännings kvaliteten, så inkludera inte data som inte behövs i din modell.

En modell som är utbildad i en delmängd scenarier kan bara fungera bra i dessa scenarier. Välj noggrant data som representerar det fullständiga omfånget av scenarier som du behöver din anpassade modell för att känna igen.

> [!TIP]
> Börja med små mängder exempel data som matchar språket och akustiskt din modell.
> Registrera till exempel ett litet men representativt exempel på ljud på samma maskin vara och i samma akustiska miljö som din modell kommer att se i produktions scenarier.
> Små data uppsättningar med representativa data kan exponera problem innan du har investerat i att samla in en mycket större data uppsättningar för utbildning.
>
> Överväg att använda exempel data för att snabbt komma igång. <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">Exempel Custom Speech data</a> finns i den här GitHub-lagringsplatsen

## <a name="data-types"></a>Datatyper

I den här tabellen listas godkända data typer, när varje datatyp ska användas och den rekommenderade kvantiteten. Det krävs inte alla data typer för att skapa en modell. Data kraven varierar beroende på om du skapar ett test eller tränar en modell.

| Datatyp | Används för testning | Rekommenderad kvantitet | Används för utbildning | Rekommenderad kvantitet |
|-----------|-----------------|----------|-------------------|----------|
| [Ljud](#audio-data-for-testing) | Ja<br>Används för visuell granskning | 5 + ljudfiler | Inga | Ej tillämpligt |
| [Ljud + medmärkta avskrifter](#audio--human-labeled-transcript-data-for-testingtraining) | Ja<br>Används för att utvärdera noggrannhet | 0,5 – 5 timmars ljud | Ja | 1-20 timmars ljud |
| [Relaterad text](#related-text-data-for-training) | Inga | Ej tillämpligt | Ja | 1-200 MB relaterad text |

Filerna ska grupperas efter typ i en data uppsättning och laddas upp som en zip-fil. Varje data uppsättning får bara innehålla en enda datatyp.

> [!TIP]
> När du tränar en ny modell börjar du med [relaterad text](#related-text-data-for-training). Dessa data kommer redan att förbättra igenkänningen av särskilda termer och fraser. Det går mycket snabbare att träna med text än att träna med ljud (minuter jämfört med dagar).

> [!NOTE]
> Det är inte alla bas modeller som stöder utbildning med ljud. Om en bas modell inte stöder den, kommer tal tjänsten endast använda texten från avskrifterna och ignorera ljudet. Se [språk stöd](language-support.md#speech-to-text) för en lista över bas modeller som stöder utbildning med ljud data. Även om en bas modell stöder utbildning med ljuddata, kan tjänsten endast använda en del av ljudet. Fortfarande kommer den att använda alla avskrifter.
>
> I fall när du ändrar bas modellen som används för utbildning och du har ljud i träning-datauppsättningen, kontrollerar du *alltid* om den nya valda bas modellen [stöder utbildning med ljuddata](language-support.md#speech-to-text). Om den tidigare använda bas modellen inte har stöd för utbildning med ljuddata, och hierarkin data uppsättning innehåller ljud, ökar inlärnings tiden med den nya bas modellen **drastiskt** , och det kan enkelt gå från flera timmar till flera dagar. Detta gäller särskilt om din röst tjänst prenumeration **inte** finns i en [region med den dedikerade maskin varan](custom-speech-overview.md#set-up-your-azure-account) för utbildning.
>
> Om du möter problemet som beskrivs i stycket ovan kan du snabbt minska inlärnings tiden genom att minska mängden ljud i data uppsättningen eller ta bort det helt och hållet texten kvar. Det sistnämnda alternativet är starkt rekommenderat om din prenumeration på röst tjänsten **inte** finns i en [region med den dedikerade maskin varan](custom-speech-overview.md#set-up-your-azure-account) för utbildning.
>
> I regioner med dedikerad maskin vara för utbildning kommer tal tjänsten att använda upp till 20 timmars ljud för utbildning. I andra regioner kommer den bara att använda upp till 8 timmar ljud.

## <a name="upload-data"></a>Ladda upp data

Om du vill överföra dina data navigerar du till <a href="https://speech.microsoft.com/customspeech" target="_blank">tal Studio </a>. Från portalen klickar du på **överför data** för att starta guiden och skapa din första data uppsättning. Du uppmanas att välja en tal data typ för din data uppsättning innan du tillåter att du överför dina data.

![Skärm bild som visar ljud överförings alternativet från tal portalen.](./media/custom-speech/custom-speech-select-audio.png)

Varje data uppsättning som du överför måste uppfylla kraven för den datatyp som du väljer. Dina data måste vara korrekt formaterade innan de överförs. Korrekt formaterade data säkerställer att de bearbetas korrekt av tjänsten Custom Speech. Kraven visas i följande avsnitt.

När din data uppsättning har laddats upp har du några alternativ:

* Du kan navigera till fliken **test** och visuellt inspektera enbart ljud eller ljud + mänskligt avskrifts data.
* Du kan navigera till fliken **utbildning** och använda ljud-och färg SKRIFTS data eller relaterade text data för att träna en anpassad modell.

## <a name="audio-data-for-testing"></a>Ljud data för testning

Ljuddata är optimala för att testa noggrannheten hos Microsofts bas linje tal-till-text-modell eller en anpassad modell. Kom ihåg att ljuddata används för att kontrol lera precisionen för tal med avseende på en speciell modells prestanda. Om du vill kvantifiera precisionen för en modell använder du [ljud + mänskligt avskrifts data](#audio--human-labeled-transcript-data-for-testingtraining).

Använd den här tabellen för att se till att ljudfilerna är korrekt formaterade för användning med Custom Speech:

| Egenskap                 | Värde                 |
|--------------------------|-----------------------|
| Filformat              | RIFF (WAV)            |
| Samplings frekvens              | 8 000 Hz eller 16 000 Hz |
| Kanaler                 | 1 (mono)              |
| Maximal längd per ljud | 2 timmar               |
| Samplingsformat            | PCM, 16-bitars           |
| Arkiv format           | .zip                  |
| Maximal Arkiv storlek     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> När du överför utbildning och testar data får. zip-filens storlek inte överskrida 2 GB. Om du behöver mer data för utbildning delar du in det i flera. zip-filer och laddar upp dem separat. Senare kan du välja att träna från *flera* data uppsättningar. Du kan dock bara testa från en *enda* data uppsättning.

Använd <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX </a> för att verifiera ljud egenskaperna eller konvertera det befintliga ljudet till rätt format. Nedan visas några exempel på hur var och en av dessa aktiviteter kan göras via SoX-kommando raden:

| Aktivitet | Beskrivning | SoX-kommando |
|----------|-------------|-------------|
| Kontrol lera ljud formatet | Använd det här kommandot för att kontrol lera<br>ljud fil formatet. | `sox --i <filename>` |
| Konvertera ljud format | Använd det här kommandot för att konvertera<br>ljud filen till en kanal, 16-bitars 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Ljud + mänskligt avskrifts data för testning/utbildning

För att mäta noggrannheten hos Microsofts tal-till-text-precision vid bearbetning av ljudfiler, måste du tillhandahålla medmärkta avskrifter (ord för ord) för jämförelse. Även om det ofta finns tids krävande avskrifter, är det nödvändigt att utvärdera precisionen och träna modellen för dina användnings fall. Kom ihåg att förbättringarna i igenkänningen bara är lika lämpliga som de data som tillhandahålls. Därför är det viktigt att endast avskrifter av hög kvalitet överförs.

Ljudfiler kan ha tystnad i början och slutet av inspelningen. Om möjligt, inkludera minst en halv sekunds tystnad före och efter tal i varje exempel fil. Ljud med låg inspelnings volym eller störande bakgrunds brus är inte användbart, men det bör inte försämra din anpassade modell. Överväg alltid att uppgradera dina mikrofoner och signal bearbetnings maskin vara innan du samlar in ljud exempel.

| Egenskap                 | Värde                               |
|--------------------------|-------------------------------------|
| Filformat              | RIFF (WAV)                          |
| Samplings frekvens              | 8 000 Hz eller 16 000 Hz               |
| Kanaler                 | 1 (mono)                            |
| Maximal längd per ljud | 2 timmar (testning)/60 s (utbildning) |
| Samplingsformat            | PCM, 16-bitars                         |
| Arkiv format           | .zip                                |
| Maximal zip-storlek         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> När du överför utbildning och testar data får. zip-filens storlek inte överskrida 2 GB. Du kan bara testa från en *enda* data uppsättning, se till att hålla den inom lämplig fil storlek. Dessutom kan varje tränings fil inte överstiga 60 sekunder, annars kommer den att vara fel.

För att lösa problem som Word-borttagning eller ersättning krävs en stor mängd data för att förbättra igenkänningen. Vanligt vis rekommenderar vi att du ger ord för ord-avskrifter i 1 till 20 timmars ljud. Men även om det är ett litet antal minuter kan det hjälpa till att förbättra igenkännings resultatet. Transkriptioner för alla WAV-filer bör ingå i en enda fil med oformaterad text. Varje rad i transkriptionsfilen ska innehålla namnet på en av ljudfilerna följt av motsvarande transkription. Filnamnet och transkriptionen ska separeras med ett tabbtecken (\t).

Exempel:

<!-- The following example contains tabs. Don't accidentally convert these into spaces. -->

```input
speech01.wav    speech recognition is awesome
speech02.wav    the quick brown fox jumped all over the place
speech03.wav    the lazy dog was not amused
```

> [!IMPORTANT]
> Transkriptionen ska kodas som UTF-8-byteordningsmärke (BOM).

Transkriptionerna textnormaliseras så att de kan bearbetas av systemet. Det finns dock några viktiga normaliseringar som måste utföras innan data överförs till tal Studio. För det språk som ska användas när du förbereder dina avskrifter, se [så här skapar du en](how-to-custom-speech-human-labeled-transcriptions.md) medhjälpad avskrift

När du har samlat in dina ljudfiler och motsvarande avskrifter, paketera dem som en enda. zip-fil innan du laddar upp till <a href="https://speech.microsoft.com/customspeech" target="_blank">tal Studio </a>. Nedan visas ett exempel på en data uppsättning med tre ljudfiler och en fil med mänsklig märkning:

> [!div class="mx-imgBorder"]
> ![Välj ljud från tal portalen](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Se [Konfigurera ditt Azure-konto](custom-speech-overview.md#set-up-your-azure-account) för en lista över rekommenderade regioner för dina röst tjänst prenumerationer. Om du ställer in tal prenumerationerna i någon av dessa regioner kommer det att minska den tid det tar att träna modellen. I dessa regioner kan utbildning bearbeta cirka 10 timmar ljud per dag jämfört med bara 1 timme per dag i andra regioner. Om modell träningen inte kan slutföras inom en vecka markeras modellen som misslyckad.

Alla bas modeller har inte stöd för utbildning med ljuddata. Om bas modellen inte stöder den, kommer tjänsten att ignorera ljudet och bara träna med texten i avskrifterna. I det här fallet är utbildningen detsamma som utbildning med relaterad text. Se [språk stöd](language-support.md#speech-to-text) för en lista över bas modeller som stöder utbildning med ljud data.

## <a name="related-text-data-for-training"></a>Relaterade text data för utbildning

Produkt namn eller funktioner som är unika bör innehålla relaterade text data för utbildning. Relaterad text hjälper till att säkerställa korrekt igenkänning. Två typer av relaterade text data kan tillhandahållas för att förbättra igenkänningen:

| Datatyp | Hur dessa data förbättrar igenkänningen |
|-----------|------------------------------------|
| Meningar (yttranden) | Förbättra precisionen när du känner igen produkt namn eller branschspecifika vokabulär inom ramen för en mening. |
| Uttal | Förbättra uttal av ovanliga termer, akronymer eller andra ord med odefinierade uttal. |

Meningar kan anges som en enda textfil eller flera textfiler. För att förbättra precisionen använder du text data som är närmare den förväntade talade yttranden. Uttal ska anges som en enskild textfil. Allt kan paketeras som en enda zip-fil och överföras till <a href="https://speech.microsoft.com/customspeech" target="_blank">tal Studio </a>.

Utbildning med relaterad text slutförs vanligt vis inom några minuter.

### <a name="guidelines-to-create-a-sentences-file"></a>Rikt linjer för att skapa en menings fil

Om du vill skapa en anpassad modell med hjälp av meningar måste du ange en lista över exempel-yttranden. Yttranden behöver _inte_ vara slutförd eller grammatiskt korrekt, men de måste avspegla den talade ingången som du förväntar dig i produktionen. Om du vill att vissa villkor har ökat vikt, lägger du till flera meningar som innehåller dessa specifika villkor.

Som allmän vägledning är modell anpassningen effektiv när övnings texten är så nära som möjligt för den verkliga text som förväntas i produktionen. Kundspecifika jargong och fraser som du riktar in dig på bör tas med i övnings texten. När det är möjligt kan du försöka att ha en mening eller ett nyckelord som styrs på en separat rad. För nyckelord och fraser som är viktiga för dig (till exempel produkt namn) kan du kopiera dem några gånger. Men kom ihåg att inte kopiera för mycket – det kan påverka den övergripande igenkännings hastigheten.

Använd den här tabellen för att se till att din relaterade datafil för yttranden är korrekt formaterad:

| Egenskap | Värde |
|----------|-------|
| Textkodning | UTF-8 BOM |
| antal yttrande per rad | 1 |
| Maximal filstorlek | 200 MB |

Dessutom bör du ta hänsyn till följande begränsningar:

* Undvik upprepade tecken, ord eller grupper med ord över tre gånger. Exempel: "AAAA", "Ja Ja Ja" eller "det är det som är det". Röst tjänsten kan ta bort rader med för många repetitioner.
* Använd inte specialtecken eller UTF-8-tecken ovan `U+00A1` .
* URI: er kommer att avvisas.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Rikt linjer för att skapa en uttal-fil

Om det finns ovanliga termer utan standard uttal som användarna kommer att stöta på eller använder, kan du ange en anpassad uttal-fil för att förbättra igenkänningen.

> [!IMPORTANT]
> Vi rekommenderar inte att du använder anpassade uttal-filer för att ändra uttal av vanliga ord.

Detta inkluderar exempel på en talade uttryck och ett anpassat uttal för var och en:

| Identifierat/visat formulär | Tal format |
|--------------|--------------------------|
| 3CPO | tre c p o |
| CNTK | c n t k |
| STANDARDEN | i tredubbel e |

Det talade formuläret är den fonetiska sekvensen som har stavats ut. Det kan bestå av bokstäver, ord, stavelser eller en kombination av alla tre.

Ett anpassat uttal är tillgängligt på engelska ( `en-US` ) och tyska ( `de-DE` ). I den här tabellen visas tecken som stöds efter språk:

| Språk | Nationell inställning | Tabbtecken |
|----------|--------|------------|
| Engelska | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Tyska | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Använd följande tabell för att kontrol lera att den relaterade data filen för uttal är korrekt formaterad. Uttal-filerna är små och får bara bestå av några kilobyte i storlek.

| Egenskap | Värde |
|----------|-------|
| Textkodning | UTF-8-struktur (ANSI stöds också för engelska) |
| antal uttal per rad | 1 |
| Maximal filstorlek | 1 MB (1 KB ledig nivå) |

## <a name="next-steps"></a>Nästa steg

* [Inspektera dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna modellen](how-to-custom-speech-train-model.md)
* [Distribuera din modell](./how-to-custom-speech-train-model.md)