---
title: Djup inlärning eller Machine Learning
titleSuffix: Azure Machine Learning
description: Lär dig hur djup inlärningen är relaterad till Machine Learning och AI. I Azure Machine Learning använder du djup inlärnings modeller för bedrägeri identifiering, objekt identifiering och mycket annat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 01/14/2020
ms.custom: contperf-fy21q1,contperfq1
ms.openlocfilehash: 48de06d28442b4d05cd3a7ab287732c0999e434c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659706"
---
# <a name="deep-learning-vs-machine-learning-in-azure-machine-learning"></a>Djup inlärning jämfört med Machine Learning i Azure Machine Learning

Den här artikeln beskriver djup inlärning och maskin inlärning och hur de passar in i den bredare kategorin av artificiell intelligens. Lär dig mer om djup inlärnings lösningar som du kan bygga på Azure Machine Learning, till exempel bedrägeri identifiering, röst-och ansikts igenkänning, sentiment analys och tids serie prognoser.

Vägledning om hur du väljer algoritmer för dina lösningar finns i [lathund-bladet Machine Learning algorithm](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-machine-learning-and-ai"></a>Djup inlärning, maskin inlärning och AI

![Relations diagram: AI kontra Machine Learning jämfört med djup inlärning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Överväg följande definitioner för att förstå djup inlärningen jämfört med Machine Learning vs. AI:

- **Djup inlärning** är en del av maskin inlärning som baseras på artificiella neurala nätverk. _Inlärnings processen_ är _djupgående_ eftersom strukturen hos artificiellt neurala-nätverk består av flera indata, utdata och dolda lager. Varje lager innehåller enheter som omvandlar indata till information som nästa lager kan använda för en viss förutsägelseuppgift. Tack vare den här strukturen kan en dator lära sig genom sin egen data bearbetning.

- **Machine Learning** är en del av artificiell intelligens som använder tekniker (till exempel djup inlärning) som gör att datorer kan förbättra sina uppgifter. _Inlärnings processen_ baseras på följande steg:

   1. Mata in data i en algoritm. (I det här steget kan du ange ytterligare information till modellen, till exempel genom att utföra funktions extrahering.)
   1. Använd dessa data för att träna en modell.
   1. Testa och distribuera modellen.
   1. Använd den distribuerade modellen för att göra en automatiserad förutsägande uppgift. (Du kan med andra ord anropa och använda den distribuerade modellen för att ta emot förutsägelserna som returneras av modellen.)

- **Artificiell intelligens (AI)** är en teknik som gör det möjligt för datorer att efterlikna mänsklig intelligens. Den innehåller Machine Learning. 
 
Genom att använda maskin inlärnings-och djup inlärnings tekniker kan du skapa dator system och program som utför uppgifter som ofta är associerade med mänsklig intelligens. Dessa uppgifter omfattar bild igenkänning, tal igenkänning och språk översättning.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Tekniker för djup inlärning jämfört med Machine Learning 

Nu när du har en översikt över Machine Learning jämfört med djup inlärning ska vi jämföra de två teknikerna. I Machine Learning måste algoritmen uppmanas att göra en korrekt förutsägelse genom att använda mer information (till exempel genom att utföra funktions extrahering). I djup inlärning kan algoritmen lära sig att göra en korrekt förutsägelse genom sin egen data bearbetning, tack vare den artificiella neurala nätverks strukturen.

I följande tabell jämförs de två metoderna i detalj:

| |Alla Machine Learning |Endast djup inlärning|
|---|---|---|
|  **Antal data punkter** | Kan använda små mängder data för att göra förutsägelser. | Behöver använda stora mängder tränings data för att göra förutsägelser. |
|  **Maskin varu beroenden** | Kan arbeta på datorer med låg slut. Det behöver inte vara en stor mängd beräknings kraft. | Är beroende av avancerade datorer. Det finns ett stort antal åtgärder för mat ris multiplikation. En GPU kan effektivt optimera dessa åtgärder. |
|  **Funktionalisering process** | Kräver att funktioner identifieras korrekt och skapas av användare. | Lär dig mer om hög nivå funktioner från data och skapar nya funktioner själva. |
|  **Inlärnings metod** | Delar upp inlärnings processen i mindre steg. Den kombinerar sedan resultaten från varje steg till ett resultat. | Går igenom inlärnings processen genom att lösa problemet från slut punkt till slut punkt. |
|  **Körningstid** | Tar relativt lite tid att träna, från några sekunder till några timmar. | Det tar vanligt vis lång tid att träna eftersom en djup inlärnings algoritm omfattar många lager. |
|  **Resultat** | Utdata är vanligt vis ett numeriskt värde, till exempel en poäng eller en klassificering. | Utdata kan ha flera format, t. ex. en text, en poäng eller ett ljud. |

## <a name="what-is-transfer-learning"></a>Vad är transfer Learning

Inlärnings djup inlärnings modeller kräver ofta stora mängder utbildnings data, avancerade beräknings resurser (GPU, TPU) och en längre utbildnings tid. I scenarier där du inte har någon av dessa tillgängliga kan du genvägen till inlärnings processen med hjälp av en teknik som kallas *överförings inlärning.*

Överförings inlärning är en teknik som används för att lösa ett problem med ett annat men relaterat problem.

På grund av strukturen i neurala-nätverk innehåller den första uppsättningen lager vanligt vis lågnivå funktioner, medan den slutliga uppsättningen lager innehåller en funktion på högre nivå som är närmare den aktuella domänen. Genom att syftes ändring de sista lagren för användning i en ny domän eller problem kan du avsevärt minska mängden tid, data och beräknings resurser som behövs för att träna den nya modellen. Om du till exempel redan har en modell som identifierar bilar kan du använda den för att använda transfer Learning för att även identifiera truckar, motorcyklar och andra typer av fordon.

Lär dig hur du tillämpar överförings inlärning för bild klassificering med ett ramverk med öppen källkod i Azure Machine Learning: [träna en djup inlärnings PyTorch modell med hjälp av transfer Learning](./how-to-train-pytorch.md?WT.mc_id=docs-article-lazzeri).

## <a name="deep-learning-use-cases"></a>Användnings fall för djup inlärning

På grund av den artificiella neurala nätverks strukturen, är djup inlärningen perfekt vid identifiering av mönster i ostrukturerade data som bilder, ljud, video och text. Av den anledningen kan djup inlärningen snabbt omvandla många branscher, inklusive hälso vård, energi, ekonomi och transport. Dessa branscher håller nu på att se till att traditionella affärs processer bevaras. 

Några av de vanligaste programmen för djup inlärning beskrivs i följande stycken. I Azure Machine Learning kan du använda en modell från du skapar från ett ramverk med öppen källkod eller skapa modellen med hjälp av de verktyg som tillhandahålls.

### <a name="named-entity-recognition"></a>Identifiering av namngivna enheter

Identifiering av namngivna enheter är en djup inlärnings metod som tar en del av texten som inmatad och omvandlar den till en fördefinierad klass. Den nya informationen kan vara ett post nummer, ett datum, ett produkt-ID. Informationen kan sedan lagras i ett strukturerat schema för att bygga en lista över adresser eller fungera som ett riktmärke för en identitets validerings motor.

### <a name="object-detection"></a>Objektidentifiering

Djup inlärning har tillämpats i många olika användnings fall för objekt identifiering. Objekt identifiering består av två delar: bild klassificering och avbildnings lokalisering. Bild _klassificeringen_ identifierar avbildningens objekt, till exempel bilar eller personer. Avbildnings _lokalisering_ tillhandahåller den speciella platsen för dessa objekt. 

Objekt identifiering används redan i branscher som spel, detalj handel, turism och självdrivande bilar.

### <a name="image-caption-generation"></a>Generering av bild text

Som bild igenkänning, i bild texter, för en bild måste systemet generera en beskrivning som beskriver innehållet i bilden. När du kan identifiera och märka objekt i fotografier är nästa steg att omvandla dessa etiketter till beskrivande meningar. 

Normalt används (convolutional neurala-nätverk för att identifiera objekt i en avbildning och sedan använda ett återkommande neurala-nätverk för att omvandla etiketterna till konsekventa meningar.

### <a name="machine-translation"></a>Maskin Översättning

Maskin översättningen tar ord eller meningar från ett språk och översätter automatiskt dem till ett annat språk. Maskin översättning har varit runt under en längre tid, men djup inlärningen uppnår imponerande resultat i två olika områden: automatisk översättning av text (och översättning av tal till text) och automatisk översättning av bilder.

Med rätt data omvandling kan ett neurala-nätverk förstå text-, ljud-och visuella signaler. Maskin översättning kan användas för att identifiera kodfragment av ljud i större ljudfiler och skriva över det talade ordet eller bilden som text.

### <a name="text-analytics"></a>Textanalys

Text analys som bygger på djup inlärnings metoder innebär analys av stora mängder text data (till exempel medicinska dokument eller utgifts kvitton), igenkänning av mönster och skapande av strukturerad och kortfattad information från IT.

Företag använder djup inlärning för att utföra text analyser för att identifiera insider handel och efterlevnad med myndighets bestämmelser. Ett annat vanligt exempel är försäkrings bedrägerier: text analys har ofta använts för att analysera stora mängder dokument för att identifiera sannolikheten för ett försäkrings anspråk som bedrägerier. 

## <a name="artificial-neural-networks"></a>Artificiell neurala-nätverk

Artificiell neurala-nätverk bildas av lager av anslutna noder. I djup inlärnings modeller används neurala-nätverk som har ett stort antal lager. 

I följande avsnitt lär du de flesta populära artificiell neurala-nätverkstopologier.

### <a name="feedforward-neural-network"></a>Feedforward neurala-nätverk

Feedforward neurala Network är den enklaste typen av artificiellt neurala-nätverk. I ett feedforward nätverk flyttas informationen endast i en riktning från indatanivå till output-lagret. Feedforward neurala Networks transformerar inmatningar genom att placera det genom en serie med dolda lager. Varje lager består av en uppsättning neurons och varje lager är fullständigt anslutet till alla neurons i lagret. Det senast fullständigt anslutna lagret (utmatnings lagret) representerar de genererade förutsägelserna.

### <a name="recurrent-neural-network"></a>Aktuellt neurala-nätverk

Befintliga neurala-nätverk är ett mycket vanligt artificiellt neurala nätverk. Dessa nätverk sparar utdata för ett lager och matar tillbaka det till inmatnings lagret för att förutsäga lagrets resultat. Befintliga neurala-nätverk har fantastiska inlärnings möjligheter. De används ofta för komplexa uppgifter som tids serie prognoser, inlärnings hand skrift och tolknings språk.

### <a name="convolutional-neural-network"></a>(Convolutional neurala-nätverk

Ett (convolutional neurala-nätverk är ett särskilt effektivt artificiellt neurala-nätverk och det presenterar en unik arkitektur. Lager ordnas i tre dimensioner: bredd, höjd och djup. Neurons i ett lager ansluter inte till alla neurons i nästa skikt, men endast till en liten region i lagrets neurons. Den slutliga utmatningen reduceras till en enda kombination av sannolikhets poäng, ordnat längs djup dimensionen. 

(Convolutional neurala-nätverk har använts i områden som video igenkänning, bild igenkänning och rekommenderade system.

## <a name="next-steps"></a>Nästa steg

I följande artiklar visas fler alternativ för att använda djup inlärnings modeller med öppen källkod i [Azure Machine Learning](./index.yml?WT.mc_id=docs-article-lazzeri):


- [Klassificera handskrivna siffror med hjälp av en TensorFlow-modell](./how-to-train-tensorflow.md?WT.mc_id=docs-article-lazzeri) 

- [Klassificera handskrivna siffror med en TensorFlow-uppskattning och keras](./how-to-train-keras.md?WT.mc_id=docs-article-lazzeri)

- [Klassificera handskrivna siffror med hjälp av en kedje modell](./how-to-set-up-training-targets.md?WT.mc_id=docs-article-lazzeri)
