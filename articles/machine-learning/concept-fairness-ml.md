---
title: Minimera eventuell rättvishet i Machine Learning-modeller (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig mer om skälighet i Machine Learning-modeller och hur Fairlearn python-paketet kan hjälpa dig att bygga bättre modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 01/26/2021
ms.custom: responsible-ml
ms.openlocfilehash: 276c91ad7fb5b09dbe18d989741f0f54b8b0eb09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101659689"
---
# <a name="mitigate-unfairness-in-machine-learning-models-preview"></a>Minimera eventuell rättvishet i Machine Learning-modeller (för hands version)

Lär dig mer om skälighet i maskin inlärning och hur python-paketet [Fairlearn](https://fairlearn.github.io/) med öppen källkod kan hjälpa dig att undvika problem med att lösa problemen i Machine Learning-modeller. Om du inte gör en satsning på att förstå skälighet problem och utvärdera skälighet när du skapar maskin inlärnings modeller kan du skapa modeller som ger oskäliga resultat.

Följande översikt över [användar handboken](https://fairlearn.github.io/main/user_guide/index.html) för Fairlearn-paketet med öppen källkod beskriver hur du använder det för att utvärdera skälighet för de AI-system som du skapar.  Fairlearn-paketet med öppen källkod kan också erbjuda alternativ som hjälper dig att minimera eller hjälpa till att minska eventuella skälighet problem.  Se [hur-till-och-](how-to-machine-learning-fairness-aml.md) [exempel antecknings böckerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) möjliggör SKÄLIGHET-utvärdering av AI-system under utbildning på Azure Machine Learning.


## <a name="what-is-fairness-in-machine-learning-models"></a>Vad är skälighet i Machine Learning-modeller?

>[!NOTE]
> Skälighet är en socioekonomisk teknisk utmaning. Många aspekter av skälighet, till exempel rättvisa och förfallna processer, samlas inte in i kvantitativa skälighet-mått. Dessutom kan många kvantitativa skälighet-mått inte vara uppfyllda samtidigt. Målet med Fairlearn-paketet med öppen källkod är att göra det möjligt för människor att utvärdera olika effekter och strategier för minskning. Slutligen är det upp till de mänskliga användarna som skapar artificiell intelligens och maskin inlärnings modeller för att göra kompromisser som är lämpliga för deras situation.

Artificiell intelligens och Machine Learning-system kan visa illojalt beteende. Ett sätt att definiera oskäligt beteende är att skada, eller påverkan på människor. Det finns många typer av skador som AI-system kan ge upphov till. Mer information finns i [NeurIPS 2017-presentationen av Kate Crawford](https://www.youtube.com/watch?v=fMym_BKWQzk) .

Två vanliga typer av AI-orsakade skador är:

- Skada på tilldelningen: ett AI-system utökar eller delar ut affärs möjligheter, resurser eller information för vissa grupper. Exempel är anställning, skol mottagningar och utlåning där en modell kan vara mycket bättre vid bästa val av en speciell grupp av personer än bland andra grupper.

- Skada för tjänst kvalitet: ett AI-system fungerar inte även för en grupp med personer som det gör för en annan. Ett tal igenkännings system kan exempelvis inte fungera både för kvinnor som det gör för män.

För att minska illojalt beteende i AI-system måste du utvärdera och minimera dessa skador.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Skälighet-utvärdering och-minskning med Fairlearn

Fairlearn är ett python-paket med öppen källkod som gör det möjligt för datorer i Machine Learning-system att utvärdera sina Systems skälighet och minska sin rättvishet.

Paketet med öppen källkod för Fairlearn har två komponenter:

- Utvärderings instrument panel: en Jupyter Notebook-widget för att bedöma hur en modells förutsägelser påverkar olika grupper. Du kan också jämföra flera modeller med skälighet och prestanda mått.
- Algoritmer för minskning: en uppsättning algoritmer som minimerar den binära klassificeringen och regressionen.

Tillsammans gör dessa komponenter det möjligt för data experter och affärs ledare att navigera i alla kompromisser mellan skälighet och prestanda och för att välja den strategi för minskning som bäst passar deras behov.

## <a name="assess-fairness-in-machine-learning-models"></a>Utvärdera skälighet i Machine Learning-modeller

I Fairlearn-paketet med öppen källkod är skälighet konceptuellt, trots en metod som kallas **Group skälighet**, som frågar: vilka grupper av individer riskerar att drabbas av skador? Relevanta grupper, även kallade del populationer, definieras genom **känsliga funktioner** eller känsliga attribut. Känsliga funktioner skickas till en uppskattare i Fairlearn-paketet med öppen källkod som en Vector eller en matris med namnet  `sensitive_features` . Termen rekommenderar att system designern bör vara känslig för dessa funktioner när du bedömer grupp skälighet. 

Något att vara mindful av är om dessa funktioner innehåller sekretess effekter på grund av privata data. Men ordet "känslig" innebär inte att dessa funktioner inte ska användas för att göra förutsägelser.

>[!NOTE]
> En skälighet-utvärdering är inte en helt teknisk övning.  Fairlearn-paketet med öppen källkod kan hjälpa dig att utvärdera skälighet för en modell, men den kommer inte att utföra utvärderingen åt dig.  Fairlearn-paketet med öppen källkod hjälper till att identifiera kvantitativa mått för att utvärdera skälighet, men utvecklare måste också utföra en kvalitativ analys för att utvärdera skälighet i sina egna modeller.  De känsliga funktioner som anges ovan är ett exempel på den här typen av kvalitativ analys.     

Under utvärderings fasen är skälighet kvantifierat genom olikheter i mått. **Olikhets mått** kan utvärdera och jämföra modellens beteende i olika grupper, antingen som förhållanden eller som skillnader. Fairlearn-paketet med öppen källkod har stöd för två klasser med olikhets mått:


- Olikheter i modell prestanda: dessa uppsättningar av mått beräknar olikheten (skillnad) i värdena för det valda prestanda måttet för olika under grupper. Några exempel är:

  - skillnader i noggrannhets grad
  - skillnader i fel frekvens
  - skillnader i precision
  - skillnader i återkallande
  - skillnader i MAE
  - många andra

- Skillnader i urvals frekvens: det här måttet innehåller skillnaden i markerings frekvens mellan olika under grupper. Ett exempel på detta är olikheter i godkännande frekvensen för lånet. Markerings frekvensen innebär en bråkdel av Datapoints i varje klass som klassificeras som 1 (i binär klassificering) eller distribution av förutsägelse värden (i regression).

## <a name="mitigate-unfairness-in-machine-learning-models"></a>Minimera eventuell rättvishet i Machine Learning-modeller

### <a name="parity-constraints"></a>Paritets begränsningar

Fairlearn-paketet med öppen källkod innehåller en mängd olika algoritmer för att minska godheten. Dessa algoritmer har stöd för en uppsättning begränsningar i förförutsägelsens beteende som kallas **paritets begränsningar** eller villkor. Paritets begränsningar kräver att vissa delar av förväntare beteendet är jämförbara i de grupper som är känsliga funktioner definierar (t. ex. olika races). Algoritmerna för att undvika problem i Fairlearn med öppen källkod använder sådana paritets begränsningar för att minimera de observerade skälighet-problemen.

>[!NOTE]
> Att begränsa en modells orättvishet innebär att minska den verkliga risken, men den här tekniska lösningen kan inte eliminera den här verkliga risken helt.  Algoritmerna för att undvika rättvishet i Fairlearn-paketet med öppen källkod kan ge förslag på rekommenderade lösningar för att minska den långsiktiga användningen av en maskin inlärnings modell, men de är inte lösningar för att eliminera en helt god nivå.  Det kan finnas andra paritets begränsningar eller kriterier som bör övervägas för varje enskild utvecklares maskin inlärnings modell. Utvecklare som använder Azure Machine Learning måste bestämma sig själva om de behöver minska sin användning och distribution av Machine Learning-modeller.  

Fairlearn-paketet med öppen källkod stöder följande typer av paritets begränsningar: 

|Paritets begränsning  | Syfte  |Machine Learning-uppgift  |
|---------|---------|---------|
|Demografisk paritet     |  Minimera tilldelnings skador | Binära klassificering, regression |
|Lika med strider  | Diagnostisera tilldelnings-och kvalitets-och tjänst skador | Binära klassificering        |
|Samma affärs möjlighet | Diagnostisera tilldelnings-och kvalitets-och tjänst skador | Binära klassificering        |
|Förlust av begränsad grupp     |  Minimera kvaliteten på tjänstens skada | Regression |



### <a name="mitigation-algorithms"></a>Dämpa algoritmer

Fairlearn-paketet med öppen källkod tillhandahåller postprocessing och minskade algoritmer för minskning av god godhet:

- Minskning: dessa algoritmer tar en standard uppskattning för maskin inlärning i svart form (t. ex. en LightGBM modell) och genererar en uppsättning omarbetade modeller med hjälp av en sekvens med åter viktade inlärnings data uppsättningar. Till exempel kan sökande till en viss kön vara viktad eller nedviktad för att omträna modeller och minska olikheter i olika jämställdhets grupper. Användarna kan sedan välja en modell som ger bästa möjliga kompromisser mellan precisionen (eller andra prestanda mått) och olikheter, som vanligt vis skulle behöva baseras på affärs regler och kostnads beräkningar.  
- Efter bearbetning: dessa algoritmer tar en befintlig klassificerare och den känsliga funktionen som indata. Sedan härleder de en transformering av klassificerarens förutsägelse för att genomdriva de angivna skälighet-begränsningarna. Den största fördelen med tröskelvärdes optimering är dess enkelhet och flexibilitet eftersom den inte behöver träna om modellen. 

| Integritetsalgoritm | Beskrivning | Machine Learning-uppgift | Känsliga funktioner | Paritets begränsningar som stöds | Typ av algoritm |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | En svart ruta till god klassificering som beskrivs i [en minsknings metod för god klassificering](https://arxiv.org/abs/1803.02453) | Binära klassificering | Kategoriska | [Demografisk paritet](#parity-constraints), [desamma strider](#parity-constraints) | Minskning |
| `GridSearch` | Svart ruta som beskrivs i [en minsknings metod för god klassificering](https://arxiv.org/abs/1803.02453)| Binära klassificering | Binär | [Demografisk paritet](#parity-constraints), [desamma strider](#parity-constraints) | Minskning |
| `GridSearch` | Svart Box-metod som implementerar ett rutnät – search-variant av rättvis regression med algoritmen för begränsat grupp bortfall som beskrivs i [rättvis regression: kvantitativa definitioner och reduktionbaserade algoritmer](https://arxiv.org/abs/1905.12843) | Regression | Binär | [Förlust av begränsad grupp](#parity-constraints) | Minskning |
| `ThresholdOptimizer` | Postprocessing-algoritmen är baserad på pappers [likheten för affärs möjligheter i övervakad utbildning](https://arxiv.org/abs/1610.02413). Den här tekniken tar sig in i en befintlig klassificerare och den känsliga funktionen, och härleder en enkel färgs omvandling av klassificerarens förutsägelse för att genomdriva de angivna paritets begränsningarna. | Binära klassificering | Kategoriska | [Demografisk paritet](#parity-constraints), [desamma strider](#parity-constraints) | Efter bearbetning |

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder de olika komponenterna genom att kontrol lera Fairlearn- [GitHub](https://github.com/fairlearn/fairlearn/), [användar handboken](https://fairlearn.github.io/main/user_guide/index.html), [exempel](https://fairlearn.github.io/main/auto_examples/index.html)och [exempel antecknings böcker](https://github.com/fairlearn/fairlearn/tree/master/notebooks).
- Lär dig [hur du](how-to-machine-learning-fairness-aml.md) aktiverar skälighet-utvärdering av Machine Learning-modeller i Azure Machine Learning.
- Se [exempel antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) för ytterligare skälighet utvärderings scenarier i Azure Machine Learning. 
