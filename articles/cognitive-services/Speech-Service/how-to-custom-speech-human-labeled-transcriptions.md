---
title: Rikt linjer för mänsklig avskrift – tal tjänst
titleSuffix: Azure Cognitive Services
description: För att förbättra tal igenkännings precisionen, t. ex. När ord tas bort eller felaktigt ersätts, kan du använda välmärkta avskrifter tillsammans med dina ljuddata. Medmärkta avskrifter är ord för ord, orda Grant avskrifter av en ljudfil.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: eafdebc795d5bede7fa52a3562d33c806035697f
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704439"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Så här skapar du medmärkade avskrifter

Om du vill förbättra igenkännings precisionen, särskilt problem som orsakas när ord tas bort eller på ett felaktigt sätt, bör du använda välmärkta avskrifter tillsammans med dina ljuddata. Vad är medmärkta avskrifter? Det är enkelt, de är ord för ord, orda Grant avskrifter av en ljudfil.

Ett stort exempel på avskrifts data krävs för att förbättra igenkänningen. vi föreslår att du tillhandahåller mellan 10 och 20 timmar avskrifts data. På den här sidan kommer vi att gå igenom rikt linjerna som är utformade för att hjälpa dig att skapa hög kvalitets avskrifter. Den här guiden är uppdelad efter nationella inställningar, med avsnitt för amerikansk engelska, mandariner kinesiska och tyska.

> [!NOTE]
> Alla bas modeller stöder inte anpassning med ljudfiler. Om en bas modell inte stöder den, kommer träningen bara att använda texten i avskrifterna på samma sätt som relaterad text används.

## <a name="us-english-en-us"></a>AMERIKANSK engelska (en-US)

Välmärkta avskrifter för engelska ljud måste anges som oformaterad text, endast med ASCII-tecken. Undvik att använda latinska-1-eller Unicode-skiljetecken. De här tecknen läggs ofta till när du kopierar text från ett ordbehandlingsprogram eller OLE-data från webb sidor. Om dessa tecken finns, se till att uppdatera dem med lämplig ASCII-ersättning.

Några exempel:

| Tecken för att undvika | Ersättning | Kommentarer |
| ------------------- | ------------ | ----- |
| "Hello World" | "Hello world" | De inledande och avslutande citat tecknen har ersatts med lämpliga ASCII-tecken. |
| John dag | John dag | Apostrofen har ersatts med lämpligt ASCII-tecken. |
| Det var bra, nej, det var bra! | Det var bra – Nej, det var bra! | Tank strecket ersattes med två bindestreck. |

### <a name="text-normalization-for-us-english"></a>Text normalisering för amerikansk engelska

Text normalisering är en transformering av ord i ett konsekvent format som används vid inlärning av en modell. Vissa normaliserings regler tillämpas automatiskt i text, men vi rekommenderar att du använder dessa rikt linjer när du förbereder dina person uppgifter:

- Skriv ut förkortningar i ord.
- Skriva ut numeriska strängar som inte är standard i ord (till exempel redovisnings villkor).
- Icke-alfabetiska tecken eller blandade alfanumeriska tecken ska skrivas av som uttalade.
- Förkortningar som uttalats som ord bör inte redige ras (till exempel "polärdiagram", "Laser", "RAM" eller "NATO").
- Skriv ut förkortningar som uttalas som separata bokstäver med varje bokstav avgränsat med ett blank steg.
- Om du använder ljud, ska du skriva om siffror som ord som matchar ljudet (till exempel "101" kan uttalas som "1 0 1" eller "101").
- Undvik upprepade tecken, ord eller grupper med ord över tre gånger, till exempel "Ja Ja Ja". Rader med sådana repetitioner kan släppas av tal tjänsten.

Här följer några exempel på normalisering som du bör utföra på avskriften:

| Ursprunglig text               | Text efter normalisering              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce banderoll            | Bruce-banderoll för läkare                   |
| Jonas obligation, 007             | Jonas obligation, dubbelt så sju           |
| Ke $ ha                       | Kesha                                 |
| Hur länge är 2x4         | Hur lång tid är två av fyra           |
| Mötet går från 1 – 3pm | Mötet går från en till tre PM |
| Min blod typ är O +         | Min blod typ är O-positiv           |
| Vattnet är H20                | Vatten är H 2 O                        |
| Spela OU812 av Van Halen     | Spela upp O U 8 1 2 av Van Halen           |
| UTF-8 med BOM              | U T F 8 med struktur                      |

Följande normaliserings regler tillämpas automatiskt på avskrifter:

- Använd små bokstäver.
- Ta bort alla skiljetecken utom apostrofer i ord.
- Expandera tal i ord/talade form, till exempel dollar belopp.

Här följer några exempel på normalisering som utförs automatiskt på avskriften:

| Ursprunglig text                          | Text efter normalisering          |
| -------------------------------------- | --------------------------------- |
| "Heliga ko!" Detta Batman.               | heliga ko Batman              |
| "Vad?" Detta Batmans sidekick, Tax. | Vad sägs om Batmans sidekick Tax |
| Gå till get-EM!                            | Go-Hämta EM                         |
| Jag är dubbelt gemensam                     | Jag är dubbelt gemensam                |
| 104, gymnasium, gata                         | 1 0 4, gymnasium, gata            |
| Justera till 102,7                          | Justera till 1 0 2 punkt sju    |
| PI är cirka 3,14                       | PI är cirka tre punkter 1 4  |
| IT-kostnader \$ 3,14                        | IT-kostnader 3 14           |

## <a name="mandarin-chinese-zh-cn"></a>Mandariner, kinesiska (zh-CN)

Välmärkta avskrifter för ett mandariner kinesiskt ljud måste vara UTF-8-kodad med en byte-ordning markör. Undvik att använda skiljetecken med halv bredd. Dessa tecken kan tas med oavsiktligt när du förbereder data i ett ordbehandlingsprogram eller klipper data från webb sidor. Om dessa tecken finns, måste du uppdatera dem med lämplig full bredds ersättning.

Några exempel:

| Tecken för att undvika | Ersättning   | Kommentarer |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | De inledande och avslutande citat tecknen har ersatts av lämpliga tecken. |
| 需要什么帮助? | 需要什么帮助？| Frågetecknet har ersatts av rätt tecken. |

### <a name="text-normalization-for-mandarin-chinese"></a>Text normalisering för mandariner kinesiska

Text normalisering är en transformering av ord i ett konsekvent format som används vid inlärning av en modell. Vissa normaliserings regler tillämpas automatiskt i text, men vi rekommenderar att du använder dessa rikt linjer när du förbereder dina person uppgifter:

- Skriv ut förkortningar i ord.
- Skriv ut numeriska strängar i tal form.

Här följer några exempel på normalisering som du bör utföra på avskriften:

| Ursprunglig text | Text efter normalisering |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

Följande normaliserings regler tillämpas automatiskt på avskrifter:

- Ta bort alla skiljetecken
- Expandera tal till talade form
- Konvertera hel bredds bokstäver till halv bredds bokstäver
- Använda versaler för alla engelska ord

Här följer några exempel på normalisering som utförs automatiskt på avskriften:

| Ursprunglig text | Text efter normalisering |
| ------------- | ------------------------ |
| 3,1415 | 三 点 一 四 一 五 |
| ¥3,5 | 三 元 五 角 |
| v f y z | V F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Tyska (de-DE) och andra språk

Välmärkta avskrifter för tyska ljud (och andra icke-engelska eller mandariner-kinesiska språk) måste vara UTF-8-kodad med en byte-ordning markör. En meddelad avskrift bör tillhandahållas för varje ljudfil.

### <a name="text-normalization-for-german"></a>Text normalisering för tyska

Text normalisering är en transformering av ord i ett konsekvent format som används vid inlärning av en modell. Vissa normaliserings regler tillämpas automatiskt i text, men vi rekommenderar att du använder dessa rikt linjer när du förbereder dina person uppgifter:

- Skriv decimal tecken som "," och inte ".".
- Skriv tids avgränsare som ":" och inte "." (till exempel: 12:00 Uhr).
- Förkortningar som "ca". ersätts inte. Vi rekommenderar att du använder det fullständigt talade formuläret.
- De fyra viktigaste matematiska operatorerna (+,-, \* och/) tas bort. Vi rekommenderar att du ersätter dem med det skrivna formatet: "plus," "minus," "felskadligt" och "Geteilt".
- Jämförelse operatorer tas bort (=, < och >). Vi rekommenderar att du ersätter dem med "gleich", "Kleiner-sensorn" och "grösser-sensorn".
- Skriv bråktal, till exempel 3/4, i skrivet format (till exempel: "Drei Viertel" i stället för 3/4).
- Ersätt "€"-symbolen med dess skrivna form "euro".

Här följer några exempel på normalisering som du bör utföra på avskriften:

| Ursprunglig text    | Text efter användar normalisering | Text efter system-normalisering       |
| ---------------- | ----------------------------- | ------------------------------------- |
| ES IST 12,23 Uhr | ES IST 12:23 Uhr              | ES IST zwölf Uhr Drei und zwanzig Uhr |
| {12,45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3-4        | 2 plus 3 minus 4              | Zwei plus Drei minus Vier             |

Följande normaliserings regler tillämpas automatiskt på avskrifter:

- Använd gemener för all text.
- Ta bort alla skiljetecken, inklusive olika typer av citat tecken ("test", "test", "test" och «test» är OK).
- Ignorera rader med specialtecken från den här uppsättningen: ¢ ¤ ¥ ¦ § © ª ¬® ° ± ² μ × ÿ Ø ¬ ¬.
- Expandera tal till talade form, inklusive dollar eller euro belopp.
- Acceptera umlauts endast för a, o och du. Andra kommer att ersättas med "th" eller tas bort.

Här följer några exempel på normalisering som utförs automatiskt på avskriften:

| Ursprunglig text    | Text efter normalisering |
| ---------------- | ------------------------ |
| Frankfurter-Ring | Frankfurter-Ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Nästa steg

- [Förbered och testa dina data](./how-to-custom-speech-test-and-train.md)
- [Inspektera dina data](how-to-custom-speech-inspect-data.md)
- [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
- [Träna modellen](how-to-custom-speech-train-model.md)
- [Distribuera din modell](./how-to-custom-speech-train-model.md)