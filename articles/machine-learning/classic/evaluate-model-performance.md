---
title: 'ML Studio (klassisk): utvärdera & kors validerings modeller – Azure'
description: Lär dig mer om de mått som du kan använda för att övervaka modell prestanda i Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: b2ca78d30659fce6e4246c81216cae94b404955e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520025"
---
# <a name="evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Utvärdera modell prestanda i Azure Machine Learning Studio (klassisk)

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


I den här artikeln får du lära dig mer om de mått som du kan använda för att övervaka modell prestanda i Azure Machine Learning Studio (klassisk).  Utvärdering av prestanda för en modell är en av kärn stegen i data vetenskaps processen. Det visar hur framgångs poängen (förutsägelserna) av en data uppsättning har varit av en utbildad modell. Azure Machine Learning Studio (klassisk) stöder utvärdering av modeller genom två av dess huvudsakliga Machine Learning-moduler: 
+ [Utvärdera modell][evaluate-model] 
+ [Kors validerings modell][cross-validate-model]

Med dessa moduler kan du se hur din modell presterar i termer av ett antal mått som ofta används i maskin inlärning och statistik.

Utvärdering av modeller bör beaktas tillsammans med:
+ [Parameter optimering för algoritmer](algorithm-parameters-optimize.md)
+ [Modelltolkning](interpret-model-results.md)

Tre vanliga övervakade inlärnings scenarier presenteras: 
* regression
* binära klassificering 
* klassificering av multiklass


## <a name="evaluation-vs-cross-validation"></a>Utvärdering kontra kors validering
Utvärdering och kors validering är standard sätt att mäta modellens prestanda. Båda genererar utvärderings mått som du kan kontrol lera eller jämföra med andra modeller.

[Utvärdera modell][evaluate-model] förväntar sig en returnerad data uppsättning som indata (eller två om du vill jämföra prestanda i två olika modeller). Därför måste du träna din modell med modulen [träna modell][train-model] och göra förutsägelser på viss data uppsättning med modulen [Poäng modell][score-model] innan du kan utvärdera resultatet. Utvärderingen baseras på etiketter och sannolikheter tillsammans med de sanna etiketterna, som alla matas ut av modulen [Poäng modell][score-model] .

Du kan också använda kors validering för att utföra ett antal åtgärder för bedömning av träna Poäng (10 vikning) automatiskt på olika del mängder av indata. Indata delas upp i 10 delar, där en är reserverad för testning och den andra 9 for Training. Den här processen upprepas 10 gånger och utvärderings måtten är genomsnitts. Detta hjälper till att fastställa hur väl en modell skulle generaliseras till nya data uppsättningar. Modulen för modul för att [kontrol lera modell][cross-validate-model] tar i en modell som inte är tränad och vissa märkta data uppsättningar och utvärderar utvärderings resultaten för var och en av de 10 vikningarna, utöver de genomsnittliga resultaten.

I följande avsnitt kommer vi att bygga enkla Regressions-och klassificerings modeller och utvärdera deras prestanda med hjälp av modulerna [utvärdera modell][evaluate-model] och [kors validering][cross-validate-model] .

## <a name="evaluating-a-regression-model"></a>Utvärdera en Regressions modell
Anta att vi vill förutsäga priset på en bil med hjälp av funktioner som dimensioner, häst krafter, motor specifikationer och så vidare. Detta är ett typiskt Regressions problem, där mål variabeln (*pris*) är ett kontinuerligt numeriskt värde. Vi kan få en linjär Regressions modell som, med tanke på funktions värden för en viss bil, kan förutsäga priset på den bilen. Denna Regressions modell kan användas för att räkna med samma data uppsättning som vi tränade på. När vi har de förutsagda bil priserna kan vi utvärdera modell prestandan genom att titta på hur mycket förutsägelserna avviker från de faktiska priserna i genomsnitt. För att illustrera detta använder vi *data uppsättningen för Automobile-pris (RAW)* som är tillgänglig i avsnittet **sparade data uppsättningar** i Machine Learning Studio (klassisk).

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler till din arbets yta i Azure Machine Learning Studio (klassisk):

* Bil pris data (RAW)
* [Linjär regression][linear-regression]
* [Träningsmodell][train-model]
* [Poängmodell][score-model]
* [Utvärdera modell][evaluate-model]

Anslut portarna enligt beskrivningen nedan i bild 1 och Ställ in kolumnen etikett i modulen [träna modell][train-model] på *pris*.

![Utvärdera en Regressions modell](./media/evaluate-model-performance/1.png)

Bild 1. Utvärdera en Regressions modell.

### <a name="inspecting-the-evaluation-results"></a>Inspektera utvärderings resultaten
När du har kört experimentet kan du klicka på utdataporten för modulen [utvärdera modell][evaluate-model] och välja *visualisera* för att se utvärderings resultatet. De utvärderings mått som är tillgängliga för Regressions modeller är: *medels absolut fel*, *rot medelvärde absolut fel*, *relativt absolut fel*, *relativt kvadratvärde* och *koefficienten för bestämning*.

Termen "fel" här representerar skillnaden mellan det förväntade värdet och det sanna värdet. Det absoluta värdet eller kvadraten av denna skillnad beräknas vanligt vis för att fånga den totala storleken på fel i alla instanser, eftersom skillnaden mellan det förväntade och sanna värdet kan vara negativ i vissa fall. Fel måtten mäter förutsägelse prestanda för en Regressions modell i termer av genomsnitts avvikelsen för dess förutsägelser från de sanna värdena. Lägre fel värden innebär att modellen är mer exakt för att göra förutsägelser. En övergripande felvärdet noll innebär att modellen passar data perfekt.

Koefficienten för bestämning, som även kallas R-kvadrat, är också ett standard sätt att mäta hur väl modellen passar data. Den kan tolkas som den andel av variationen som förklaras av modellen. En högre proportion är bättre i det här fallet, där 1 är perfekt anpassad.

![Mät värden för linjär Regressions utvärdering](./media/evaluate-model-performance/2.png)

Figur 2. Mät värden för linjär Regressions utvärdering.

### <a name="using-cross-validation"></a>Använda kors validering
Som tidigare nämnts kan du utföra upprepad utbildning, bedömning och utvärderingar automatiskt med modulen för [kors validerings modell][cross-validate-model] . Allt du behöver i det här fallet är en data uppsättning, en modell som inte är tränad och en modul för [kors validering][cross-validate-model] (se bilden nedan). Du måste ange Label-kolumnen till *pris* i egenskaperna för [Cross-validate Model-][cross-validate-model] modulen.

![Kors validering av en Regressions modell](./media/evaluate-model-performance/3.png)

Bild 3. Kors validering av en Regressions modell.

När du har kört experimentet kan du kontrol lera utvärderings resultatet genom att klicka på den högra utdataporten för modulen för [kors validerings modell][cross-validate-model] . Detta ger en detaljerad vy över måtten för varje iteration (vikning) och de genomsnittliga resultaten av vart och ett av måtten (bild 4).

![Kors validerings resultat för en Regressions modell](./media/evaluate-model-performance/4.png)

Bild 4. Kors validerings resultat för en Regressions modell.

## <a name="evaluating-a-binary-classification-model"></a>Utvärdera en binär klassificerings modell
I ett binära klassificerings scenario har mål variabeln bara två möjliga resultat, till exempel: {0, 1} eller {false, true}, {negativ, positiv}. Anta att du får en data uppsättning vuxna anställda med några demografiska och anställnings variabler och att du uppmanas att förutsäga inkomst nivån, en binär variabel med värdena {"<= 50 K", ">50 K"}. Med andra ord representerar den negativa klassen de anställda som gör mindre än eller lika med 50 000 per år och den positiva klassen representerar alla andra anställda. Som i Regressions scenariot skulle vi träna en modell, Poäng för några data och utvärdera resultatet. Den huvudsakliga skillnaden här är valet av mått Azure Machine Learning Studio (klassiska) beräkningar och utdata. För att illustrera förutsägelse scenariot för uppskattning av inkomstnivå använder vi den [vuxen](https://archive.ics.uci.edu/ml/datasets/Adult) data uppsättningen för att skapa ett Studio-experiment (klassiskt) och utvärdera prestanda för en logistik Regressions modell med två klasser, en ofta använd binär klassificerare.

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler till din arbets yta i Azure Machine Learning Studio (klassisk):

* Data uppsättning för binära sammanräknings inkomst
* [Logistic Regression med två klasser][two-class-logistic-regression]
* [Träningsmodell][train-model]
* [Poängmodell][score-model]
* [Utvärdera modell][evaluate-model]

Anslut portarna enligt vad som visas nedan i bild 5 och Ställ in kolumnen etikett i modulen [träna modell][train-model] till *inkomst*.

![Utvärdera en binär klassificerings modell](./media/evaluate-model-performance/5.png)

Bild 5. Utvärdera en binär klassificerings modell.

### <a name="inspecting-the-evaluation-results"></a>Inspektera utvärderings resultaten
När du har kört experimentet kan du klicka på utdataporten för modulen [utvärdera modell][evaluate-model] och välja *visualisera* för att se utvärderings resultaten (bild 7). De utvärderings mått som är tillgängliga för binära klassificerings modeller är: *precision*, *precision*, *återkallande*, *F1-Poäng* och *AUC*. Dessutom visar modulen en Förväxlings mat ris som visar antalet sanna positiva identifieringar, falska negativa tal, falska positiva identifieringar och sanna negativ, samt *Roc*, *precision/återkallande* och *lyft* kurvor.

Noggrannhet är bara den andel av korrekt klassificerade instanser. Det är vanligt vis det första mått du tittar på när du utvärderar en klassificerare. Men om test data inte är balanserade (där de flesta instanserna tillhör en av klasserna), eller om du är mer intresserade av prestandan i någon av klasserna, så är noggrannheten inte riktigt att en klassificerare är effektiv. I klassificerings scenariot för inkomst nivån förutsätter du att du testar på vissa data där 99% av instanserna representerar personer som har mindre än eller lika med 50 000 per år. Det är möjligt att uppnå en 0,99 precision genom att förutsäga klassen "<= 50 000" för alla instanser. Klassificeraren i det här fallet verkar vara en effektiv uppgift, men i verkligheten går det inte att klassificera någon av de enskilda inkomst personerna (1%) Bra.

Av den anledningen är det bra att beräkna ytterligare mått som fångar upp mer detaljerade aspekter av utvärderingen. Innan du går igenom informationen om dessa mått är det viktigt att förstå Förväxlings mat ris för en binära klassificerings utvärdering. Klass etiketterna i inlärnings uppsättningen kan bara ha två möjliga värden, som vi vanligt vis refererar till som positivt eller negativt. De positiva och negativa instanserna som en klassificerare förutsäger korrekt kallas för sant positiv (TP) och sant negativ (TN). På samma sätt kallas de felaktigt klassificerade instanserna falskt positiva (RP) och falskt negativ (FN). Förväxlings mat ris är bara en tabell som visar antalet instanser som faller under var och en av dessa fyra kategorier. Azure Machine Learning Studio (klassisk) bestämmer automatiskt vilken av de två klasserna i data uppsättningen som är den positiva klassen. Om klass etiketter är booleska eller heltal, tilldelas de märkta instanserna "true" eller "1" den positiva klassen. Om etiketterna är strängar, t. ex. med inkomst data uppsättningen, sorteras etiketterna alfabetiskt och den första nivån väljs som den negativa klassen medan den andra nivån är den positiva klassen.

![Förvirring mat ris i binära klassifikation](./media/evaluate-model-performance/6a.png)

Bild 6. Visualiserings mat ris för binära klassificering.

Om du går tillbaka till intäkts klassificeringen skulle vi vilja ställa flera utvärderings frågor som hjälper oss att förstå prestandan för den klassificerare som används. En naturlig fråga: "av de personer som modellen förutsäger för att tjäna >50 K (TP + RP), hur många klassificerades korrekt (TP)?" Den här frågan kan besvaras genom att titta på modellens **precision** , som är den andel av positiva resultat som klassificeras korrekt: TP/(TP + RP). En annan vanlig fråga är "av alla anställda med inkomst >50 000 (TP + FN), hur många klassificerar klassificeraren korrekt (TP)". Detta är i själva verket **återställnings** punkt eller den verkliga positiva HASTIGHETEN: TP/(TP + FN) för klassificeraren. Du kanske märker att det finns en uppenbar kompromiss mellan precisionen och återställningen. Till exempel med en relativt bal anse rad data uppsättning skulle en klassificerare som förutser mest positiva instanser ha en hög återkallning, men en ganska låg precision då många av de negativa instanserna skulle bli felklassificerade som resulterar i ett stort antal falska positiva identifieringar. Om du vill se ett diagram över hur dessa två mått varierar kan du klicka på kurvan **precision/återkalla** på sidan för resultat av utvärderings resultat (övre vänstra delen av figur 7).

![Resultat av binära klassificerings utvärdering](./media/evaluate-model-performance/7.png)

Bild 7. Resultat av binära klassificerings utvärdering.

Ett annat relaterat mått som ofta används är **F1-poängen**, som tar både precision och åter användning. Det är det harmoniska medelvärdet av dessa två mått och beräknas som sådant: F1 = 2 (precision x återkalla)/(precision + återkalla). F1-poängen är ett bra sätt att sammanfatta utvärderingen i ett enda tal, men det är alltid en bra idé att titta på både precisionen och återkalla tillsammans för att bättre förstå hur en klassificerare fungerar.

Dessutom kan en granska den sanna positiva nivån jämfört med den falska positiva positiva frekvensen i **Roc-kurvan (receiver Operational)** och motsvarande **del under värdet kurva (AUC)** . Den närmare kurvan är i det övre vänstra hörnet, desto bättre klassificerarens prestanda (som maximerar den sanna positiva hastigheten och minimerar den falska positiva positiva frekvensen). Kurvor som ligger nära ritytans Diagonal, är resultatet av klassificerare som tenderar att göra förutsägelser som är nära att gissa sig.

### <a name="using-cross-validation"></a>Använda kors validering
Som i Regressions exemplet kan vi utföra kors validering för att flera gånger träna, score och utvärdera olika del mängder av data automatiskt. På samma sätt kan vi använda modulen för [kors validering][cross-validate-model] , en modell med en ej tränad logistik Regressions modell och en data uppsättning. Etikett kolumnen måste anges till *intäkt* i egenskaperna för den [kors validerings modellens][cross-validate-model] modul. När du har kört experimentet och klickat på den högra utdataporten för modulen för [kors validerings modell][cross-validate-model] kan vi se de binära klassificerings måtten för varje vikning, utöver medelvärdet och standard avvikelsen för var och en. 

![Kors validering av en binär klassificerings modell](./media/evaluate-model-performance/8.png)

Figur 8. Kors validering av en binär klassificerings modell.

![Kors validerings resultat för en binär klassificerare](./media/evaluate-model-performance/9.png)

Bild 9. Kors validerings resultat för en binär klassificerare.

## <a name="evaluating-a-multiclass-classification-model"></a>Utvärdera en klassificerings modell med multiklass
I det här experimentet kommer vi att använda den populära [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") -datauppsättningen, som innehåller instanser av tre olika typer (klasser) av Iris-anläggningen. Det finns fyra funktions värden (sepal längd/bredd och blad längd/bredd) för varje instans. I föregående experiment tränade vi och testade modellerna med samma data uppsättningar. Här ska vi använda modulen [dela data][split] för att skapa två del mängder av data, träna på den första och poäng och utvärdera den andra. Iris-datauppsättningen är offentligt tillgänglig på den [Machine Learning lagrings platsen](https://archive.ics.uci.edu/ml/index.html)och kan laddas ned med en modul för att [Importera data][import-data] .

### <a name="creating-the-experiment"></a>Skapa experimentet
Lägg till följande moduler till din arbets yta i Azure Machine Learning Studio (klassisk):

* [Importera data][import-data]
* [Beslutsskog med två klasser][multiclass-decision-forest]
* [Dela data][split]
* [Träningsmodell][train-model]
* [Poängmodell][score-model]
* [Utvärdera modell][evaluate-model]

Anslut portarna enligt bilden i bild 10.

Ställ in etikett kolumn index för modulen [träna modell][train-model] på 5. Data uppsättningen har ingen rubrik rad men vi vet att klass etiketterna är i den femte kolumnen.

Klicka på modulen [Importera data][import-data] och ange egenskapen *data källa* till webb- *URL via http* och *URL: en* till http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data .

Ange del av instanser som ska användas för utbildning i modulen [dela data][split] (till exempel 0,7).

![Utvärdera en klassificering av en multiklass](./media/evaluate-model-performance/10.png)

Bild 10. Utvärdera en klassificering av en multiklass

### <a name="inspecting-the-evaluation-results"></a>Inspektera utvärderings resultaten
Kör experimentet och klicka på utdataporten för [utvärdera modell][evaluate-model]. Utvärderings resultaten presenteras i form av en förvirring mat ris, i det här fallet. I matrisen visas de faktiska respektive förväntade instanserna för alla tre klasserna.

![Utvärderings resultat för klassificering av multiklass](./media/evaluate-model-performance/11.png)

Bild 11. Utvärderings resultat för klassificering av multiklass.

### <a name="using-cross-validation"></a>Använda kors validering
Som tidigare nämnts kan du utföra upprepad utbildning, bedömning och utvärderingar automatiskt med modulen för [kors validerings modell][cross-validate-model] . Du behöver en data uppsättning, en ej tränad modell och en modul för [kors validering][cross-validate-model] (se bilden nedan). Återigen måste du ange kolumnen etikett för modulen för [kors validerings modell][cross-validate-model] (kolumn index 5 i det här fallet). När du har kört experimentet och klickat på den högra utdataporten för [kors validerings modellen][cross-validate-model], kan du kontrol lera värdena för varje vikning samt medelvärdet och standard avvikelsen. De mått som visas här liknar de som beskrivs i det binära klassificerings fallet. I klassificering med multiklass är det dock möjligt att beräkna de sanna positiva och negativa positiva och negativa positiva och negativa heltalen genom att inventera per klass, eftersom det inte finns någon övergripande positiv eller negativ klass. När du till exempel beräknar precisionen eller återkallar klassen "Iris-Setosa" förutsätts att detta är den positiva klassen och alla andra som negativa.

![Kors validering av en klassificerings modell med multiklass](./media/evaluate-model-performance/12.png)

Figur 12. Kors validering av en klassificerings modell med multiklass.

![Kors validerings resultat för en klassificerings modell med multiklass](./media/evaluate-model-performance/13.png)

Figur 13. Kors validerings resultat för en klassificerings modell i multiklass.

<!-- Module References -->
[cross-validate-model]: /azure/machine-learning/studio-module-reference/cross-validate-model
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[multiclass-decision-forest]: /azure/machine-learning/studio-module-reference/multiclass-decision-forest
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-logistic-regression]: /azure/machine-learning/studio-module-reference/two-class-logistic-regression