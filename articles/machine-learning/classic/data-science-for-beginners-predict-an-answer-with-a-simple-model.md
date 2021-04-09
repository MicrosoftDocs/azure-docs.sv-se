---
title: 'ML Studio (klassisk): förutsäga svar med Regressions modeller – Azure'
description: Så här skapar du en enkel Regressions modell för att förutsäga ett pris i data vetenskap för nybörjare, video 4. Innehåller en linjär regression med mål data.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: c6d78b10b98abe33744673459b3d31d8941aac89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520416"
---
# <a name="predict-an-answer-with-a-simple-model"></a>Förutsäga ett svar med en enkel modell


## <a name="video-4-data-science-for-beginners-series"></a>Video 4: data vetenskap för nybörjare-serien
Lär dig hur du skapar en enkel Regressions modell för att förutsäga priset på en romb i data vetenskap för nybörjare, video 4. Vi ska rita en Regressions modell med mål data.

För att få ut det mesta av serien, se alla. [Gå till listan med videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-predict-an-answer-with-a-simple-model/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Data vetenskap för nybörjare* är en snabb introduktion till data vetenskap i fem korta videor.

* Video 1: [5 frågor data vetenskaps svar](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 SEK)*
* Video 2: [är dina data klara för data vetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 SEK)*
* Video 3: [Ställ en fråga som du kan besvara med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 SEK)*
* Video 4: förutsäga ett svar med en enkel modell
* Video 5: [Kopiera andras arbete för att göra data vetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 SEK)*

## <a name="transcript-predict-an-answer-with-a-simple-model"></a>Avskrift: förutsäga ett svar med en enkel modell
Välkommen till den fjärde videon i serien "data vetenskap för nybörjare". I den här versionen skapar vi en enkel modell och gör en förutsägelse.

En *modell* är en förenklad berättelse om våra data. Jag visar vad jag menar.

## <a name="collect-relevant-accurate-connected-enough-data"></a>Samla in relevanta, korrekta, anslutna och tillräckligt med data
Anta att jag vill handla för en romb. Jag har en ring som tillhörde min mormor med en inställning för en 1,35 cirkumflex romb och jag vill få en uppfattning om hur mycket den kommer att kosta. Jag gör ett anteckningar och en penna till smyckespecifikationer Store och skriver ned priset på alla diamanter i ärendet och hur mycket de väger i Carats. Från och med den första romben – den är 1,01 Carats och $7 366.

Nu kan jag gå igenom och göra detta för alla andra diamanter i butiken.

![Kolumner med Diamond-data](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/diamond-data.png)

Observera att listan har två kolumner. Varje kolumn har en annan viktning i Carats och pris-och varje rad är en enskild data punkt som representerar en enskild romb.

Vi har faktiskt skapat en liten data uppsättning här – en tabell. Observera att det uppfyller våra kriterier för kvalitet:

* Datan är **relevant** -vikt är definitivt relaterad till pris
* Det är **korrekt** – vi dubbelklickar på priserna som vi skriver ned
* Den är **ansluten** – det finns inga tomma utrymmen i någon av dessa kolumner
* Och vi ser att det finns **tillräckligt med** data för att besvara vår fråga

## <a name="ask-a-sharp-question"></a>Ställ en skarp fråga
Nu ska vi lägga vår fråga på ett skarpt sätt: "hur mycket kostar det att köpa en 1,35 cirkumflex Diamond?"

Listan innehåller inte någon 1,35 cirkumflex Diamond, så vi måste använda resten av våra data för att få svar på frågan.

## <a name="plot-the-existing-data"></a>Rita befintliga data
Det första vi ska göra är att rita en vågrät siffer linje, som kallas en axel, för att skapa ett diagram över vikterna. Intervallet av vikterna är 0 till 2, så vi ska rita en linje som täcker intervallet och lägga till Tick för varje halv cirkumflex.

Nu ska vi rita en lodrät axel för att registrera priset och ansluta det till den vågräta vikt axeln. Detta är i valutaenheter. Nu har vi en uppsättning koordinat axlar.

![Vikt-och pris axlar](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/weight-and-price-axes.png)

Vi ska ta med dessa data nu och omvandla dem till ett *punkt diagram*. Detta är ett bra sätt att visualisera numeriska data uppsättningar.

För den första data punkten ögonglobsikonen vi en lodrät linje vid 1,01 Carats. Sedan ögonglobsikonen vi en vågrät linje vid $7 366. Där de möts ritar vi en punkt. Detta representerar vår första romb.

Nu ska vi gå igenom varje Diamond i listan och göra samma sak. När vi är igång är det vad vi får: en massa med punkter, en för varje romb.

![Punkt diagram](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/scatter-plot.png)

## <a name="draw-the-model-through-the-data-points"></a>Rita modellen genom data punkterna
Nu om du tittar på punkterna och Squint, ser samlingen ut som en fat-och fuzzy-linje. Vi kan ta vår markör och rita en rak linje genom den.

Genom att rita en linje skapade vi en *modell*. Tänk på detta som att ta den verkliga världen och skapa en förenklad-version av den. Nu är teckningen fel – raden går inte igenom alla data punkter. Men det är en användbar förenkling.

![Linjär Regressions linje](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/linear-regression-line.png)

Det faktum att alla punkter inte går exakt genom raden är OK. Data experter förklarar detta genom att säga att det finns en modell som är linjen – och att varje punkt har en viss *brus* eller *varians* som är kopplad till den. Det finns en underliggande relation och det finns Gritty, verklig värld som ger brus och osäkerhet.

Eftersom vi försöker besvara frågan *hur mycket?* detta kallas för *regression*. Och eftersom vi använder en rak linje är det en *linjär regression*.

## <a name="use-the-model-to-find-the-answer"></a>Använd modellen för att hitta svaret
Nu har vi en modell och vi ber den om vår fråga: hur mycket kostar en 1,35 cirkumflex Diamond?

För att besvara vår fråga ögonglobsikonen vi 1,35 Carats och rita en lodrät linje. När den korsar modell linjen ögonglobsikonen vi en vågrät linje till dollar axeln. Den träffar precis vid 10 000. Sådär! Det är svaret: en 1,35 cirkumflex Diamond-kostnad om $10 000.

![Hitta svaret på modellen](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/find-the-answer.png)

## <a name="create-a-confidence-interval"></a>Skapa ett konfidens intervall
Det är naturligt att undrar hur exakt denna förutsägelse är. Det är användbart att veta om 1,35-cirkumflex romben kommer att vara mycket nära $10 000 eller mycket högre eller lägre. Vi ritar ett kuvert runt Regressions linjen som innehåller de flesta punkterna. Det här kuvertet kallas vårt *konfidens intervall*: vi är ganska säkra på att priserna faller inom det här kuvertet, eftersom de flesta har de senaste. Vi kan rita två fler horisontella linjer från där 1,35 cirkumflex-linjen korsar den övre kanten och längst ned på kuvertet.

![Konfidensintervall](./media/data-science-for-beginners-predict-an-answer-with-a-simple-model/confidence-interval.png)

Nu kan vi säga något om vårt konfidens intervall: vi kan säga att priset på en 1,35 cirkumflex Diamond är cirka $10 000 – men det kan vara så lågt som $8 000 och det kan vara så högt som $12 000.

## <a name="were-done-with-no-math-or-computers"></a>Vi är klar, utan matematik eller datorer
Vi gjorde vad data experter betalar för att göra och vi gjorde det bara genom att rita:

* Vi har begärt en fråga som vi kunde besvara med data
* Vi har byggt en *modell* med *linjär regression*
* Vi har gjort en *förutsägelse*, komplett med ett *konfidens intervall*

Och vi använde inte matematik eller datorer för att göra det.

Nu om vi hade mer information, t. ex....

* rombens klipp
* färg variationer (hur nära romben är att vara vit)
* antalet inkluderingar i romben

... sedan skulle vi ha haft fler kolumner. I så fall blir matematik användbart. Om du har fler än två kolumner är det svårt att rita punkter på papperet. Med matematik kan du anpassa den linjen eller det planet till dina data på ett mycket snyggt sätt.

Om du i stället för bara en fåtal av diamanter hade 2000 eller 2 000 000, kan du göra det mycket snabbare med en dator.

Idag har vi talat om hur du utför linjär regression och vi har gjort en förutsägelse med hjälp av data.

Se till att ta en titt på de andra videorna i "data vetenskap för nybörjare" från Microsoft Azure Machine Learning Studio (klassisk).

## <a name="next-steps"></a>Nästa steg
* [Testa ett första data vetenskaps experiment med Machine Learning Studio (klassisk)](create-experiment.md)
* [Få en introduktion till Machine Learning på Microsoft Azure](../overview-what-is-azure-ml.md)