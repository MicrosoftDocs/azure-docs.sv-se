---
title: Så här väljer du en Machine Learning-algoritm
titleSuffix: Azure Machine Learning
description: Så här väljer du Azure Machine Learning algoritmer för övervakad och oövervakad inlärning i klustring, klassificering eller Regressions experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: 27b18fdc2dda40f8361483e6ecce28d0ccbd0310
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93308229"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Så här väljer du algoritmer för Azure Machine Learning

En vanlig fråga är "vilken Machine Learning-algoritm ska jag använda?" Vilken algoritm du väljer beror huvudsakligen på två olika aspekter av ditt data vetenskaps scenario:

 - **Vad du vill göra med dina data?** Mer specifikt, vad är den affärs fråga som du vill besvara genom att lära dig från dina tidigare data?

 - **Vilka är kraven för ditt data vetenskaps scenario?** Mer specifikt, vad är precision, inlärnings tid, linearitet, antal parametrar och antalet funktioner som din lösning stöder?

 ![Att tänka på när du väljer algoritmer: Vad vill du veta? Vilka är scenario kraven?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Affärs scenarier och lathund-bladet för Machine Learning algorithm

I [lathund-bladet Azure Machine Learning algorithm](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri) får du den första överväganden: **vad du vill göra med dina data**? Leta upp den uppgift som du vill göra Machine Learning i lathund-och leta reda på en [Azure Machine Learning designer](./concept-designer.md?WT.mc_id=docs-article-lazzeri) -algoritm för lösningen för förutsägelse analys. 

Machine Learning designer innehåller en omfattande portfölj med algoritmer som [besluts skog](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri)i flera klasser [, rekommendations system](./algorithm-module-reference/evaluate-recommender.md?WT.mc_id=docs-article-lazzeri), [neurala Network regression](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri), [neurala nätverk](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri)för flera klasser och [K-innebär klustring](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri). Varje algoritm är utformad för att hantera en annan typ av Machine Learning-problem. Se [algoritmen för Machine Learning designer och module](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri) för en fullständig lista tillsammans med dokumentation om hur varje algoritm fungerar och hur du kan finjustera parametrarna för att optimera algoritmen.

> [!NOTE]
> Hämta lathund-bladet för Machine Learning-algoritmen genom att gå till [lathund-bladet för Azure Machine Learning-algoritmen](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri)
> 
> 

Förutom rikt linjerna i lathund-bladet Azure Machine Learning algorithm, bör du tänka på andra krav när du väljer en Machine Learning-algoritm för din lösning. Följande är ytterligare faktorer att överväga, till exempel precision, inlärnings tid, linearitet, antal parametrar och antal funktioner.

## <a name="comparison-of-machine-learning-algorithms"></a>Jämförelse av Machine Learning-algoritmer

Vissa Learning-algoritmer gör särskilda antaganden om data strukturen eller önskade resultat. Om du kan hitta en som passar dina behov kan du ge dig mer användbara resultat, mer exakta förutsägelser eller snabbare inlärnings tider.

I följande tabell sammanfattas några av de viktigaste egenskaperna för algoritmer från klassificerings-, Regressions-och kluster familjer:

| **Integritetsalgoritm** | **Noggrannhet** | **Träningstid** | **Linearitet** | **Parametrar** | **Kommentarer** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Klassificerings familj** | | | | | |
| [Logistik regression med två klasser](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |Bra  |Snabb |Ja |4 | |
| [Besluts skog med två klasser](./algorithm-module-reference/two-class-decision-forest.md?WT.mc_id=docs-article-lazzeri) |Utmärkt |Medel |Inga |5 |Visar långsamma Poäng tider. Föreslå att inte arbeta med En-mot-alla – multiklass, på grund av långsammare poängsättnings tider som orsakas av Trappstegs låsning i ackumulerade träd förutsägelser |
| [Besluts träd med två klasser](./algorithm-module-reference/two-class-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |Utmärkt |Medel |Inga |6 |Stor minnes storlek |
| [Neurala nätverk med två klasser](./algorithm-module-reference/two-class-neural-network.md?WT.mc_id=docs-article-lazzeri) |Bra |Medel |Inga |8 | |
| [Genomsnittlig Perceptron i två klasser](./algorithm-module-reference/two-class-averaged-perceptron.md?WT.mc_id=docs-article-lazzeri) |Bra |Medel |Ja |4 | |
| [Dubbelriktad Vector-dator](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) |Bra |Snabb |Ja |5 |Lämpligt för stora funktions uppsättningar |
| [Logistik regression med multiklass](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |Bra |Snabb |Ja |4 | |
| [Besluts skog med flera klasser](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri) |Utmärkt |Medel |Inga |5 |Visar långsamma Poäng tider |
| [Besluts träd med djup klass](./algorithm-module-reference/multiclass-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |Utmärkt |Medel |Inga |6 | Vi vill förbättra noggrannheten med liten risk för mindre täckning |
| [Neurala nätverk i multiklass](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri) |Bra |Medel |Inga |8 | |
| [En-vs-all multiklass](./algorithm-module-reference/one-vs-all-multiclass.md?WT.mc_id=docs-article-lazzeri) | - | - | - | - |Se egenskaperna för den två klass metoden som valts |
| **Regressions familj** | | | | | |
| [Linjär regression](./algorithm-module-reference/linear-regression.md?WT.mc_id=docs-article-lazzeri) |Bra |Snabb |Ja |4 | |
| [Besluts skogs regression](./algorithm-module-reference/decision-forest-regression.md?WT.mc_id=docs-article-lazzeri)|Utmärkt |Medel |Inga |5 | |
| [Regressions analys av besluts träd](./algorithm-module-reference/boosted-decision-tree-regression.md?WT.mc_id=docs-article-lazzeri) |Utmärkt |Medel |Inga |6 |Stor minnes storlek |
| [Neurala nätverks regression](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri) |Bra |Medel |Inga |8 | |
| **Kluster serie** | | | | | |
| [K-innebär klustring](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri) |Utmärkt |Medel |Ja |8 |En klustrad algoritm |

## <a name="requirements-for-a-data-science-scenario"></a>Krav för ett data vetenskaps scenario

När du vet vad du vill göra med dina data måste du fastställa ytterligare krav för din lösning. 

Gör alternativ och eventuellt kompromisser för följande krav:

- Noggrannhet
- Träningstid
- Linearitet
- Antal parametrar
- Antal funktioner

## <a name="accuracy"></a>Noggrannhet

Noggrannhet i Machine Learning mäter effektiviteten hos en modell som den andel av det faktiska resultatet av det totala antalet fall. I Machine Learning designer beräknar [modulen utvärdera modell](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri) en uppsättning utvärderings mått som är bransch standard. Du kan använda den här modulen för att mäta noggrannheten för en utbildad modell.

Det är inte alltid nödvändigt att få det mest exakta svaret. Ibland är en uppskattning tillräckligt, beroende på vad du vill använda det för. Om så är fallet kanske du kan minska bearbetnings tiden dramatiskt genom att hålla mer ungefärliga metoder. Ungefärliga metoder tenderar också att undvika överanpassning.

Det finns tre sätt att använda modulen utvärdera modell:

- Generera poäng över dina utbildnings data för att utvärdera modellen
- Generera poäng i modellen, men jämför dessa resultat med resultat från en reserverad test uppsättning
- Jämför poängen för två olika men relaterade modeller med samma uppsättning data

En fullständig lista över mått och metoder som du kan använda för att utvärdera maskin inlärnings modellens exakthet finns i [utvärdera modell-modulen](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Träningstid

I övervakad utbildning innebär utbildningen att använda historiska data för att bygga en maskin inlärnings modell som minimerar fel. Antalet minuter eller timmar som krävs för att träna en modell är ett stort antal olika algoritmer. Utbildnings tiden är ofta nära knuten till noggrannhet. en som vanligt vis medföljer den andra. 

Dessutom är vissa algoritmer mer känsliga för antalet data punkter än andra. Du kan välja en speciell algoritm eftersom du har en tids gräns, särskilt när data uppsättningen är stor.

I Machine Learning designer är det vanligt vis en tre stegs process att skapa och använda en Machine Learning-modell:

1.  Konfigurera en modell genom att välja en viss typ av algoritm och definiera sedan dess parametrar eller dess egenskaper. 

2.  Ange en data mängd som är märkt och har data som är kompatibla med algoritmen. Anslut både data och modellen för att [träna modulen modell](./algorithm-module-reference/train-model.md?WT.mc_id=docs-article-lazzeri).

3.  När utbildningen har slutförts använder du den tränade modellen med en av [poängsättnings-modulerna](./algorithm-module-reference/score-model.md?WT.mc_id=docs-article-lazzeri) för att göra förutsägelser på nya data.

## <a name="linearity"></a>Linearitet

Linjärt i statistik och maskin inlärning innebär att det finns en linjär relation mellan en variabel och en konstant i din data uppsättning. Algoritmer för linjär klassificering förutsätter till exempel att klasser kan avgränsas med en rak linje (eller dess högre dimensionella analoga).

Många Machine Learning-algoritmer använder linjäritet. I Azure Machine Learning designer är de: 

- [Logistik regression med multiklass](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [Logistik regression med två klasser](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [Stöd för vektor datorer](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri)  

Algoritmer för linjär regression förutsätter att data trender följer en rak linje. Detta antagande är inte skadat för vissa problem, men för andra minskar precisionen. Även om nack delarna är linjära algoritmer populära som en första strategi. De brukar vara algorithmically enkla och snabba att träna.

![Streckad klass-gränser](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

*Streckad **klass**: _Relying på en algoritm för linjär klassificering skulle resultera i låg exakthet. *

![Data med en linjär trend](./media/how-to-select-algorithms/nonlinear-trend.png)

***Data med en linjär trend**: _Using en linjär Regressions metod genererar mycket större fel än vad som behövs. *

## <a name="number-of-parameters"></a>Antal parametrar

Parametrar är de rattar som en data expert kommer att sätta igång när en algoritm konfigureras. De är tal som påverkar algoritmens beteende, t. ex. fel tolerans eller antal iterationer eller alternativ mellan varianter av hur algoritmen fungerar. Algoritmens inlärnings tid och exakthet kan ibland vara känsligt för att bara få rätt inställningar. Vanligt vis kräver algoritmer med ett stort antal parametrar den vanligaste utvärderings versionen och fel för att hitta en lämplig kombination.

Det kan också finnas i [modulen för att finjustera modells parametrar](./algorithm-module-reference/tune-model-hyperparameters.md?WT.mc_id=docs-article-lazzeri) i Machine Learning designer: målet för den här modulen är att fastställa de optimala egenskaperna för en maskin inlärnings modell. Modulen bygger och testar flera modeller genom att använda olika kombinationer av inställningar. Den jämför mått över alla modeller för att hämta kombinationer av inställningar. 

Även om det är ett bra sätt att se till att du har använt parameter utrymmet, ökar den tid som krävs för att träna en modell exponentiellt med antalet parametrar. Det finns många parametrar som vanligt vis indikerar att en algoritm har större flexibilitet. Det kan ofta uppnå mycket bra precision, förutsatt att du kan hitta rätt kombination av parameter inställningar.

## <a name="number-of-features"></a>Antal funktioner

I Machine Learning är en funktion en kvantifierbar variabel av fenomenet som du försöker analysera. För vissa typer av data kan antalet funktioner vara mycket stort jämfört med antalet data punkter. Detta är ofta fallet med genetiska data eller text data. 

Ett stort antal funktioner kan bli rörigt ned vissa Learning-algoritmer, vilket gör utbildnings tiden unfeasibly lång. [Support Vector-datorer](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) är särskilt väl lämpade för scenarier med ett stort antal funktioner. Därför har de använts i många program från informations hämtning till text och bild klassificering. Support vektor datorer kan användas för både klassificerings-och Regressions uppgifter.

Val av funktioner syftar på hur du tillämpar statistiska tester på indata, baserat på en angiven utmatning. Målet är att avgöra vilka kolumner som är mer förutsägbara för utdata. Den [filterbaserade modulen för funktions val](./algorithm-module-reference/filter-based-feature-selection.md?WT.mc_id=docs-article-lazzeri) i Machine Learning designer innehåller flera algoritmer för val av funktioner som du kan välja mellan. Modulen innehåller korrelations metoder som Pearson-korrelation och chi2-värden.

Du kan också använda [funktionen för permutations prioritet](./algorithm-module-reference/permutation-feature-importance.md?WT.mc_id=docs-article-lazzeri) för att beräkna en uppsättning funktions resultat för din data uppsättning. Du kan sedan använda dessa Poäng för att hjälpa dig att fastställa de bästa funktionerna i en modell.

## <a name="next-steps"></a>Nästa steg

 - [Läs mer om Azure Machine Learning designer](./concept-designer.md?WT.mc_id=docs-article-lazzeri)
 - Beskrivningar av alla Machine Learning-algoritmer som är tillgängliga i Azure Machine Learning Designer finns i [Machine Learning designer-algoritmen och module-referensen](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri)
 - Om du vill utforska förhållandet mellan djup inlärning, maskin inlärning och AI, se [djup inlärning mot Machine Learning](./concept-deep-learning-vs-machine-learning.md?WT.mc_id=docs-article-lazzeri)