---
title: Ställ en fråga data kan svara – ML Studio (klassisk) – Azure
description: Lär dig att formulera en skarp data vetenskaps fråga i data vetenskap för nybörjare, video 3. Innehåller en jämförelse av klassificerings-och Regressions frågor.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: 2ff440801db7ba8c5bfc1785fc7a399a1d773b73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517033"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Ställ en fråga som du kan svara på med data
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: data vetenskap för nybörjare-serien
Lär dig att formulera ett data vetenskaps problem till en fråga i data vetenskap för nybörjare, video 3. Den här videon innehåller en jämförelse av frågor för klassificerings-och Regressions algoritmer.

För att få ut det mesta av serien, se alla. [Gå till listan med videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Data vetenskap för nybörjare* är en snabb introduktion till data vetenskap i fem korta videor.

* Video 1: [5 frågor data vetenskaps svar](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 SEK)*
* Video 2: [är dina data klara för data vetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 SEK)*
* Video 3: Ställ en fråga som du kan besvara med data
* Video 4: [förutsäga ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 SEK)*
* Video 5: [Kopiera andras arbete för att göra data vetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 SEK)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Avskrift: Ställ en fråga som du kan besvara med data
Välkommen till den tredje videon i serien "data vetenskap för nybörjare".  

I den här artikeln får du några tips för att skapa en fråga som du kan besvara med data.

Du kan få ut mer av den här videon om du först tittar på de två tidigare videor i den här serien: "5 frågor data vetenskap kan svara" och "är dina data redo för data vetenskap?"

## <a name="ask-a-sharp-question"></a>Ställ en skarp fråga
Vi har talat om hur data vetenskap är processen med att använda namn (kallas även kategorier eller etiketter) och siffror för att förutsäga ett svar på en fråga. Men det kan inte vara bara en fråga. Det måste vara en *skarp fråga.*

En vagt-fråga behöver inte besvaras med ett namn eller en siffra. En skarp fråga måste anges.

Anta att du har hittat en Magic lampa med en Genie som kommer att sanningsenligt kan besvara alla frågor som du ställer. Men det är en mischievous-Genie, som försöker göra sitt svar som vagt och vara förvirrande så att de kan komma igång. Du vill fästa dem med en fråga så att de inte kan hjälpa dig, men berätta vad du vill veta.

Om du skulle ställa en vagt fråga, t. ex. "Vad händer med min aktie?", kan Genie svara "priset kommer att ändras". Det är ett sanningsenliga svar, men det är inte särskilt användbart.

Men om du skulle ställa en skarp fråga, t. ex. "vad kommer min akties försäljnings pris att vara nästa vecka?", kan Genie inte hjälpa till att ge dig ett särskilt svar och förutsäga ett försäljnings pris.

## <a name="examples-of-your-answer-target-data"></a>Exempel på ditt svar: mål data
När du har formulerat din fråga kan du kontrol lera om du har exempel på svaret i dina data.

Om vår fråga är "vad kommer min aktie försäljnings pris att vara nästa vecka?" sedan måste vi se till att våra data innehåller aktie pris historiken.

Om vår fråga är "vilken bil i min flotta kommer att sluta fungera först?" sedan måste vi se till att våra data innehåller information om tidigare haverier.

![Mål data – exempel på ditt svar. Formulera en data vetenskaps fråga.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Dessa exempel på svar kallas för ett mål. Ett mål är vad vi försöker förutse om framtida data punkter, oavsett om det är en kategori eller ett tal.

Om du inte har några mål data måste du skaffa några. Du kan inte svara på din fråga utan den.

## <a name="reformulate-your-question"></a>Formulera om din fråga
Ibland kan du formulera om din fråga för att få ett mer användbart svar.

Frågan "är den här data punkten A eller B?" förutsäger kategorin (eller namnet eller etiketten) för något. För att besvara det använder vi en *klassificerings algoritm*.

Frågan "hur mycket?" eller "hur många?" förutsäger ett belopp. För att besvara det använder vi en *Regressions algoritm*.

För att se hur vi kan omvandla dessa, ska vi titta på frågan, vilket nyhets berättelser är den mest intressanta för läsaren? " Den ber om en förutsägelse av ett enda val från många möjligheter – med andra ord "är detta A eller B eller C eller D?" -och använder en klassificerings algoritm.

Men det kan vara lättare att besvara den här frågan om du formulerar om den som "hur intressant är varje berättelse i den här listan till den här läsaren?" Nu kan du ge varje artikel en numerisk Poäng och sedan är det enkelt att identifiera den senaste Poäng artikeln. Detta är en omformulerande av klassificerings frågan i en Regressions fråga eller hur mycket?

![Formulera om din fråga. Klassificerings fråga mot Regressions fråga.](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Hur du ställer en fråga är en uppfattning om vilken algoritm som kan ge dig ett svar.

Du ser att vissa familjer av algoritmer – som de i vårt nyhets artikel exempel – är nära relaterade. Du kan formulera om din fråga om du vill använda algoritmen som ger dig det mest användbara svaret.

Men det viktigaste är att fråga den skarpa frågan – frågan som du kan besvara med data. Se till att du har rätt data att svara på.

Vi har talat om några grundläggande principer för att ställa en fråga som du kan besvara med data.

Se till att ta en titt på de andra videorna i "data vetenskap för nybörjare" från Microsoft Azure Machine Learning Studio (klassisk).

## <a name="next-steps"></a>Nästa steg
* [Testa ett första data vetenskaps experiment med Machine Learning Studio (klassisk)](create-experiment.md)
* [Få en introduktion till Machine Learning på Microsoft Azure](../overview-what-is-azure-ml.md)