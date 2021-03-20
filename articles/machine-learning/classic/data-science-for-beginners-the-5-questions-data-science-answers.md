---
title: 'ML Studio (klassisk): data vetenskap för nybörjare – Azure'
description: Data vetenskap för nybörjare är en genom gång av grundläggande begrepp i 5 korta videor, från och med 5 frågor data vetenskaps svar. Från Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: e9bbadc95be3479da82d91e9a4a4f712fbc32c57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520399"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Datavetenskap för nybörjare, video 1: Fem frågor och svar om datavetenskap

Få en snabb introduktion till data vetenskap från *data vetenskap för nybörjare* i fem korta videor från en topp data expert. Dessa videor är grundläggande men användbara, oavsett om du är intresse rad av data vetenskap eller om du arbetar med data experter.

Den första videon är ungefär så många frågor som data vetenskap kan svara. För att få ut det mesta av serien, se alla. [Gå till listan med videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Data vetenskap för nybörjare* är en snabb introduktion till data vetenskap med en total summa på 25 minuter. Kolla ut alla fem videor:

* Video 1:5 frågor data vetenskaps svar
* Video 2: [är dina data klara för data vetenskap?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 min 56 SEK)*
* Video 3: [Ställ en fråga som du kan besvara med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 SEK)*
* Video 4: [förutsäga ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 SEK)*
* Video 5: [Kopiera andras arbete för att göra data vetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 SEK)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Avskrift: 5 frågor data vetenskaps svar
Hej! Välkommen till video seriens *data vetenskap för nybörjare*.

Data vetenskap kan vara skrämma, så jag lär dig grunderna här utan några formler eller dator programmerings jargong.

I den första videon pratar vi om "de 5 frågorna om data vetenskaps svar".

Data vetenskap använder siffror och namn (kallas även kategorier eller etiketter) för att förutse svar på frågor.

Det kan bli överrasknings, men *det finns bara fem frågor som data vetenskaps svar*:

* Är det här en eller B?
* Är det här underligt?
* Hur mycket – eller – hur många?
* Hur organiseras detta?
* Vad bör jag göra då?

Var och en av dessa frågor besvaras av en separat familj av Machine Learning-metoder som kallas algoritmer.

Det är bra att tänka på en algoritm som ett recept och dina data som ingredienser. En algoritm visar hur du kombinerar och blandar data för att få ett svar. Datorer är som en över gång. De gör det mesta av algoritmens hårda arbete och de gör det ganska snabbt.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Fråga 1: är detta A eller B? använder klassificerings-algoritmer
Vi börjar med frågan: är den här A eller B?

![Klassificerings algoritmer: är detta A eller B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Den här serien med algoritmer kallas klassificering med två klasser.

Det är användbart för alla frågor som bara har två möjliga svar.

Exempel:

* Kommer den här Tire att Miss 1 000 i nästa km: Ja eller Nej?
* Vilken går i fler kunder: a $5 kupong eller 25% rabatt?

Den här frågan kan också omformuleras för att inkludera fler än två alternativ: är detta A eller B eller C eller D osv.?  Detta kallas klassificering av multiklasser och det är användbart när du har flera, eller flera tusen, möjliga svar. Klassificering av flera klasser väljer det mest sannolika.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Fråga 2: är det här underligt? använder algoritmer för avvikelse identifiering
Nästa frågans data vetenskap kan svara: är den här underligt? Den här frågan besvaras av en familj med algoritmer som kallas avvikelse identifiering.

![Algoritmer för avvikelse identifiering: är den här underligt?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Om du har ett kredit kort har du redan fått till gång till avvikelse identifiering. Ditt kredit korts företag analyserar dina inköps mönster så att de kan meddela dig om eventuella bedrägerier. Avgifter som är "underligt" kan vara ett köp i en butik där du normalt inte köper eller köper ett ovanligt pris objekt.

Den här frågan kan vara användbar på många olika sätt. Till exempel:

* Om du har en bil med tryck mätare kanske du vill veta: är den här tryck mätaren att läsa normal?
* Om du övervakar Internet vill du veta: är det här meddelandet från Internet som standard?

Avvikelse identifiering flaggar oväntade eller ovanliga händelser eller beteenden. Det ger LED trådar där du kan söka efter problem.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Fråga 3: hur mycket? eller hur många? använder Regressions algoritmer
Machine Learning kan också förutsäga svaret på hur mycket? eller hur många? Den algoritms familj som svarar på den här frågan kallas regression.

![Regressions algoritmer: hur mycket? eller hur många?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regressions algoritmer gör numeriska förutsägelser, till exempel:

* Vad kommer temperaturen att vara nästa tisdag?  
* Vad kommer mitt fjärde kvartals försäljning?

De hjälper dig att besvara frågor som ber om ett tal.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Fråga 4: Hur organiseras detta? använder kluster algoritmer
Nu är de två sista frågorna lite mer avancerade.

Ibland vill du förstå strukturen i en data uppsättning – hur är detta ordnat? I den här frågan har du inte några exempel på att du redan känner till resultat för.

Det finns många sätt att tease data strukturen. En metod är klustring. Den separerar data till naturliga "Clumps" för enklare tolkning. Det finns inte något rätt svar med klustring.

![Klustra algoritmer: Hur organiseras detta?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Vanliga exempel på kluster frågor är:

* Vilka visnings program gillar samma typer av filmer?
* Vilka skrivar modeller fungerar på samma sätt?

Genom att förstå hur data organiseras kan du bättre förstå och förutsäga beteenden och händelser.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Fråga 5: Vad ska jag göra nu? använder förstärkta inlärnings algoritmer
Den senaste frågan – vad ska jag göra nu? – använder en familj med algoritmer som heter förstärknings undervisning.

Förstärka inlärningen inspireras av hur hjärna av rats och människor svarar på Punishment och förmåner. Dessa algoritmer lär sig från resultat och bestämmer nästa åtgärd.

Normalt är förstärknings inlärning en bra anpassning för automatiska system som måste göra många små beslut utan mänsklig vägledning.

![Algoritmer för förstärkt inlärning: Vad ska jag göra härnäst?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Frågor IT-svar är alltid om vilken åtgärd som ska vidtas – vanligt vis av en dator eller en robot. Några exempel:

* Om jag är en temperatur kontroll system för ett hus: justera temperaturen eller lämna den där?  
* Om jag är en självdrivande bil: på en gul lampa, broms eller accelerera?  
* För ett robot vakuum: Behåll vakuumning eller gå tillbaka till laddnings stationen?

Algoritmer för att förstärka inlärning samlar in data när de går, lära från utvärderings version och fel.

Så det är det – de 5 frågorna data science kan svara.

## <a name="next-steps"></a>Nästa steg
* [Testa ett första data vetenskaps experiment med Machine Learning Studio (klassisk)](create-experiment.md)
* [Få en introduktion till Machine Learning på Microsoft Azure](../overview-what-is-azure-ml.md)