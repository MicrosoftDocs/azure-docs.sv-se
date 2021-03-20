---
title: 'ML Studio (klassisk): tolka modell resultat – Azure'
description: Hur du väljer den optimala parameter uppsättningen för en algoritm med hjälp av och visualisering av Poäng modellens utdata.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 11/29/2017
ms.openlocfilehash: f824cf6e30ed795ef0fe4468542ae16c801bd61b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516132"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Tolka modell resultat i Azure Machine Learning Studio (klassisk)

**gäller för:** ![ Detta är en bock markering, vilket innebär att den här artikeln gäller Machine Learning Studio (klassisk). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ Detta är ett X, vilket innebär att den här artikeln gäller för Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

I det här avsnittet beskrivs hur du visualiserar och tolkar förutsägelse resultat i Azure Machine Learning Studio (klassisk). När du har tränat en modell och gjort förutsägelser ovanpå den ("score modellen"), måste du förstå och tolka resultatet av förutsägelsen.

Det finns fyra viktiga typer av maskin inlärnings modeller i Azure Machine Learning Studio (klassisk):

* Klassificering
* Klustring
* Regression
* Rekommenderade system

Modulerna som används för förutsägelser ovanpå dessa modeller är:

* [Score modell][score-model] -modul för klassificering och regression
* [Tilldela till kluster][assign-to-clusters] -modul för klustring
* [Poäng matchbox rekommenderar][score-matchbox-recommender] för rekommendations system

Lär dig hur du [väljer parametrar för att optimera algoritmerna i ml Studio (klassisk)](algorithm-parameters-optimize.md).

Information om hur du utvärderar modeller finns i [så här utvärderar du modell prestanda](evaluate-model-performance.md).

Om du är nybörjare på ML Studio (klassisk) [lär du dig hur du skapar ett enkelt experiment](create-experiment.md).

## <a name="classification"></a>Klassificering
Det finns två under Kategorier med klassificerings problem:

* Problem med endast två klasser (klassificering med två klasser eller binära)
* Problem med fler än två klasser (klassificering i flera klasser)

Azure Machine Learning Studio (klassisk) har olika moduler för att hantera var och en av dessa typer av klassificering, men metoderna för att tolka deras förutsägelse resultat liknar varandra.

### <a name="two-class-classification"></a>Klassificering i två klasser
**Exempelexperiment**

Ett exempel på ett klassificerings problem med två klasser är klassificeringen av Iris blommor. Uppgiften är att klassificera irisnas blommor baserat på deras funktioner. Iris-datauppsättningen som tillhandahålls i Azure Machine Learning Studio (klassisk) är en del av den populära [Iris-datauppsättningen](https://en.wikipedia.org/wiki/Iris_flower_data_set) som innehåller instanser av två blomma arter (klasserna 0 och 1). Det finns fyra funktioner för varje blomma (sepal längd, sepal bredd, blad längd och blad bredd).

![Skärm bild av Iris-experiment](./media/interpret-model-results/1.png)

Bild 1. Iris i två klassers klassificerings problem

Ett experiment har utförts för att lösa det här problemet, som visas i bild 1. En utökat besluts träd modell med två klasser har tränats och betyg ATS. Nu kan du visualisera förutsägelse resultatet från modulen [Poäng modell][score-model] genom att klicka på utdataporten för modulen [Poäng modell][score-model] och sedan klicka på **visualisera**.

![Score modell-modul](./media/interpret-model-results/1_1.png)

Resultatet visas i bild 2.

![Resultat av iris, två klassens klassificerings experiment](./media/interpret-model-results/2.png)

Figur 2. Visualisera en resultat modell i en klassificering i två klasser

**Resultat tolkning**

Det finns sex kolumner i resultat tabellen. De fyra vänstra kolumnerna är de fyra funktionerna. De två högra kolumnerna, resultat etiketter och resultat sannolikheter är förutsägelse resultatet. Kolumnen resultat av sannolikhet visar sannolikheten att en blomma tillhör klassen positiv (klass 1). Det första talet i kolumnen (0,028571) innebär till exempel att det finns 0,028571 sannolikhet för att den första blomma tillhör klass 1. Kolumnen Poäng etiketter visar den förväntade klassen för varje blomma. Detta baseras på kolumnen Scoret sannolikhets värde. Om den förväntade sannolikheten för en blomma är större än 0,5, förutsägs den som klass 1. Annars förutsägs det som klass 0.

**Webb tjänst publicering**

När förutsägelse resultaten har förstått och bedömt ljud kan experimentet publiceras som en webb tjänst så att du kan distribuera den i olika program och anropa den för att få klass förutsägelser på alla nya Iris. Information om hur du ändrar ett utbildnings experiment till ett bedömnings experiment och publicerar det som en webb tjänst finns i [självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md). Den här proceduren ger dig ett Poäng experiment som visas i bild 3.

![Skärm bild av poängsättnings experiment](./media/interpret-model-results/3.png)

Bild 3. Bedömning av Iriss problem med klassificerings problem i två klass

Nu måste du ange indata och utdata för webb tjänsten. Indatamängden är den högra Indataporten i [Poäng modellen][score-model], vilket är den inmatade Iris-funktionen. Valet av utdata beror på om du är intresse rad av den förväntade klassen (resultat etikett), den resulterande sannolikheten eller både och. I det här exemplet förutsätts att du är intresse rad av båda. Om du vill välja önskade utdatakolumner använder du en [Välj kolumner i modulen data uppsättning][select-columns] . Klicka på [Välj kolumner i data uppsättning][select-columns], klicka på **Starta kolumn väljaren** och välj **resultat etiketter** och **betyg betygs** ätt. När du har angett utdataporten för [Välj kolumner i data uppsättningen][select-columns] och kör den igen, bör du vara redo att publicera bedömnings experimentet som en webb tjänst genom att klicka på **publicera webb tjänst**. Det slutliga experimentet ser ut som figur 4.

![Iris i två Klasss experiment](./media/interpret-model-results/4.png)

Bild 4. Slut på att experimentera med en Iriss klassificerings problem i två klasser

När du har kört webb tjänsten och angett några funktions värden för en test instans returnerar resultatet två siffror. Det första talet är den nya etiketten och den andra är sannolikheten. Den här blomman förutsägs som klass 1 med 0,9655 sannolikhet.

![Test för att tolka Poäng modell](./media/interpret-model-results/4_1.png)

![Bedömnings test resultat](./media/interpret-model-results/5.png)

Bild 5. Webb tjänst resultat av Iris i två Klasss klassificering

### <a name="multi-class-classification"></a>Klassificering i flera klasser
**Exempelexperiment**

I det här experimentet utför du en igenkännings uppgift som ett exempel på klassificering av multiklasser. Klassificeraren försöker förutsäga en viss bokstav% 28class %29 baserat på vissa manuellt skrivna attributvärden som extraherats från de manuella avbildningarna.

![Exempel på brev igenkänning](./media/interpret-model-results/5_1.png)

I tränings data finns det 16 funktioner som har extraherats från handskrivna brev bilder. 26 bokstäver utgör våra 26 klasser. Bild 6 visar ett experiment som tränar en klassificerings modell i multiklass för brev igenkänning och förutsäger samma funktions uppsättning i en test data uppsättning.

![Skrift igenkänning av klassificerings experiment i multiklass](./media/interpret-model-results/6.png)

Bild 6. Brev igenkänning multiklass klassificerings problem experiment

Visualisera resultaten från modulen [Poäng modell][score-model] genom att klicka på utdataporten för [Poäng modell][score-model] och klicka på **visualisera**. du bör se innehållet som visas på bild 7.

![Resultat modell resultat](./media/interpret-model-results/7.png)

Bild 7. Visualisera Poäng modell resulterar i en klassificering i flera klasser

**Resultat tolkning**

De vänstra 16 kolumnerna representerar test uppsättningens funktions värden. Kolumnerna med namn som betygs ätt för klass "XX" är precis som kolumnen med resultatet av sannolikheten i två klass fall. De visar sannolikheten för att motsvarande post hamnar i en viss klass. För den första posten är det till exempel 0,003571 sannolikt att det är en "A" 0,000451 sannolikhet att det är en "B" och så vidare. Den sista kolumnen (Poäng etiketter) är samma som de resultat etiketter som visas i det två klass fallet. Den väljer klassen med störst sannolikhet som den förväntade klassen för motsvarande post. För den första posten är till exempel etiketten "F" eftersom den har störst sannolikhet att vara en "F" (0,916995).

**Webb tjänst publicering**

Du kan också hämta den resultat etikett som visas för varje post och sannolikheten för den resultat etikett som visas. Den grundläggande logiken är att hitta den största sannolikheten bland alla sannolika sannolikheter. Om du vill göra detta måste du använda modulen [Kör R-skript][execute-r-script] . R-koden visas i bild 8, och resultatet av experimentet visas i bild 9.

![Exempel på R-kod](./media/interpret-model-results/8.png)

Figur 8. R-kod för att extrahera Poäng etiketter och de associerade sannolikheterna för etiketterna

![Experiment resultat](./media/interpret-model-results/9.png)

Bild 9. Slutliga bedömnings experiment om klassificerings problem med multiklass i brev igenkänning

När du har publicerat och kört webb tjänsten och angett några värden för inmatade funktioner ser det returnerade resultatet ut som figur 10. Detta handskrivna brev, med extraherade 16 funktioner, förväntas vara "T" med 0,9715 sannolikhet.

![Testa score-modulen](./media/interpret-model-results/9_1.png)

![Testresultat](./media/interpret-model-results/10.png)

Bild 10. Webb tjänst resultat för klassificering av multiklass

## <a name="regression"></a>Regression
Regressions problem skiljer sig från klassificerings problem. I ett klassificerings problem försöker du förutse diskreta klasser, till exempel vilken klass en Iris blomma tillhör. Men som du kan se i följande exempel på ett Regressions problem försöker du förutsäga en kontinuerlig variabel, till exempel priset på en bil.

**Exempelexperiment**

Använd pris förutsägelser för bilar som exempel för regression. Du försöker förutsäga priset på en bil baserat på dess funktioner, inklusive fabrikat, bränsle typ, typ av brödtext och enhets hjul. Experimentet visas i bild 11.

![Test av bil pris regression](./media/interpret-model-results/11.png)

Bild 11. Problem experiment med Automobil pris regression

Visualisering av modulen [Poäng modell][score-model] , resultatet ser ut som figur 12.

![Resultat för problem med förutsägande av mobil pris](./media/interpret-model-results/12.png)

Figur 12. Resultat för problem med att förutsäga det mobila priset

**Resultat tolkning**

Poäng etiketter är resultat kolumnen i det här resultat resultatet. Siffrorna är det förväntade priset för varje bil.

**Webb tjänst publicering**

Du kan publicera Regressions experimentet i en webb tjänst och anropa det för biluppskattning av mobila enheter på samma sätt som i ett klassificerings scenario med två klasser.

![Poäng experiment för automobilt pris Regressions problem](./media/interpret-model-results/13.png)

Figur 13. Poäng experiment med ett analys problem med bil-priset

När webb tjänsten körs ser det returnerade resultatet ut som bild 14. Det förväntade priset för den här bilen är $15 085,52.

![Testa bedömnings bedömnings modul](./media/interpret-model-results/13_1.png)

![Resultat för bedömnings modul](./media/interpret-model-results/14.png)

Bild 14. Webb tjänst resultat av ett analys problem med bil-priset

## <a name="clustering"></a>Klustring
**Exempelexperiment**

Vi använder Iris-datauppsättningen igen för att skapa ett kluster experiment. Här kan du filtrera ut klass etiketterna i data uppsättningen så att de bara har funktioner och kan användas för klustring. I detta fall anger du hur många kluster som ska vara två under inlärnings processen, vilket innebär att du kan klustra blommorna i två klasser. Experimentet visas i bild 15.

![Problem experiment för Iris-kluster](./media/interpret-model-results/15.png)

Figur 15. Problem experiment för Iris-kluster

Klustring skiljer sig från klassificering i att tränings data uppsättningen inte har sanningen etiketter. Klustring grupperar data uppsättnings instanserna för utbildning i distinkta kluster. Under övnings processen namnger modellen posterna genom att lära sig skillnaderna mellan sina funktioner. Sedan kan den tränade modellen användas för att ytterligare klassificera framtida poster. Det finns två delar av resultatet som vi är intresserade av i ett kluster problem. Den första delen märks med tränings data uppsättningen och den andra klassificerar en ny data uppsättning med den tränade modellen.

Den första delen av resultatet kan visualiseras genom att klicka på den vänstra utdataporten för [träna kluster modell][train-clustering-model] och klicka på **visualisera**. Visualiseringen visas i bild 16.

![Kluster resultat](./media/interpret-model-results/16.png)

Bild 16. Visualisera kluster resultat för tränings data uppsättningen

Resultatet av den andra delen, kluster nya poster med den tränade kluster modellen, visas i bild 17.

![Visualisera kluster resultat](./media/interpret-model-results/17.png)

Figur 17. Visualisera kluster resultat på en ny data uppsättning

**Resultat tolkning**

Även om resultatet av de två delarna härrör från olika experiment faser ser de likadana ut och tolkas på samma sätt. De första fyra kolumnerna är funktioner. Den sista kolumnen, tilldelningarna är resultatet av förutsägelsen. De poster som har tilldelats samma nummer förväntas finnas i samma kluster, det vill säga att de delar liknande på något sätt (det här experimentet använder standardvärdet för Euclidean avstånd). Eftersom du angav antalet kluster som ska vara 2, är posterna i tilldelningarna märkta med antingen 0 eller 1.

**Webb tjänst publicering**

Du kan publicera kluster experimentet i en webb tjänst och anropa det för kluster förutsägelser på samma sätt som i ett klassificerings scenario med två klasser.

![Poäng experiment för problem med Iris-kluster](./media/interpret-model-results/18.png)

Bild 18. Poäng experiment av ett problem med Iris-kluster

När du har kört webb tjänsten ser det returnerade resultatet ut som bild 19. Denna blomma förutsägs vara i kluster 0.

![Testa modulen för att tolka Poängsättning](./media/interpret-model-results/18_1.png)

![Resultat av bedömnings modul](./media/interpret-model-results/19.png)

Bild 19. Webb tjänst resultat av Iris i två Klasss klassificering

## <a name="recommender-system"></a>Rekommenderat system
**Exempelexperiment**

För rekommenderade system kan du använda problemet med restaurang rekommendationer som exempel: du kan rekommendera restauranger för kunder baserat på deras klassificerings historik. Indata består av tre delar:

* Restaurang betyg från kunder
* Kund funktions data
* Restaurang funktions data

Det finns flera saker som vi kan göra med modulen [träna matchbox-rekommendationer][train-matchbox-recommender] i Azure Machine Learning Studio (klassisk):

* Förutsäg betyg för en specifik användare och objekt
* Rekommendera objekt till en specifik användare
* Hitta användare som är relaterade till en specifik användare
* Hitta objekt som rör ett angivet objekt

Du kan välja vad du vill göra genom att välja bland de fyra alternativen på menyn **rekommenderat förutsägelse typ** . Här kan du gå igenom alla fyra scenarier.

![Matchbox-rekommendation](./media/interpret-model-results/19_1.png)

Ett vanligt Azure Machine Learning Studio (klassiskt) experiment för ett rekommenderat system ser ut som figur 20. Information om hur du använder dessa rekommenderade system moduler finns i [träna matchbox rekommenderar][train-matchbox-recommender] and [Scores matchbox rekommenderar][score-matchbox-recommender].

![Rekommenderat system experiment](./media/interpret-model-results/20.png)

Bild 20. Rekommenderat system experiment

**Resultat tolkning**

**Förutsäg betyg för en specifik användare och objekt**

Genom att välja **betygs** förutsägelse under **rekommendations förutsägelse typ** ber du det rekommenderade systemet att förutsäga omdömet för en viss användare och objekt. Visualiseringen av [poängen matchbox rekommenderar][score-matchbox-recommender] att utdata ser ut som figur 21.

![Resultat resultat för rekommendations systemet – betygs förutsägelse](./media/interpret-model-results/21.png)

Bild 21. Visualisera resultatet från rekommendations systemet – betygs förutsägelse

De första två kolumnerna är de användar objekts par som tillhandahålls av indata. Den tredje kolumnen är den förväntade omdömet för en användare för ett visst objekt. I den första raden förutsägs till exempel kund U1048 för att ge pris på restaurang 135026 som 2.

**Rekommendera objekt till en specifik användare**

Genom att välja **artikel rekommendation** under **rekommenderat förutsägelse typ** ber du det rekommenderade systemet att rekommendera objekt till en viss användare. Den sista parametern som du väljer i det här scenariot *rekommenderas val av objekt*. Alternativet **från betygsatta objekt (för modell utvärdering)** är i första hand för modell utvärdering under inlärnings processen. För det här förutsägelse skedet väljer vi **från alla objekt**. Visualiseringen av [poängen matchbox rekommenderar][score-matchbox-recommender] att utdata ser ut som figur 22.

![Resultat från rekommendations system – artikel rekommendation](./media/interpret-model-results/22.png)

Bild 22. Visualisera Poäng resultatet av rekommendations systemet – artikel rekommendation

Den första av de sex kolumnerna representerar de angivna användar-ID: na för att rekommendera objekt för, enligt uppgifterna i indata. De andra fem kolumnerna representerar de objekt som rekommenderas för användaren i fallande ordning efter relevans. Till exempel, på den första raden, är den mest rekommenderade restaurangen för kund U1048 134986, följt av 135018, 134975, 135021 och 132862.

**Hitta användare som är relaterade till en specifik användare**

Genom att välja **relaterade användare** under **rekommendations förutsägelse typ** ber du det rekommenderade systemet att hitta relaterade användare till en viss användare. Relaterade användare är de användare som har liknande inställningar. Den sista parametern som du väljer i det här scenariot är *relaterad användar val*. Alternativet **från användare som betygsattade objekt (för modell utvärdering)** är i första hand för modell utvärdering under inlärnings processen. Välj **bland alla användare** för den här förutsägelse fasen. Visualiseringen av [poängen matchbox rekommenderar][score-matchbox-recommender] att utdata ser ut som Figur 23.

![Resultat från rekommenderade system-relaterade användare](./media/interpret-model-results/23.png)

Bild 23. Visualisera Poäng resultat för de rekommenderade användarens system-relaterade användare

Den första av de sex kolumnerna visar de angivna användar-ID: n som krävs för att hitta relaterade användare, som tillhandahålls av indata. De andra fem kolumnerna lagrar de förväntade relaterade användarna av användaren i fallande ordning efter relevans. Till exempel, på den första raden, är den mest relevanta kunden för kund U1048 U1051, följt av U1066, U1044, U1017 och U1072.

**Hitta objekt som rör ett angivet objekt**

Genom att välja **relaterade objekt** under **rekommendations förutsägelse typ** ber du det rekommenderade systemet att hitta relaterade objekt till ett givet objekt. Relaterade objekt är de objekt som troligt vis kommer att Gillas av samma användare. Den sista parametern som du väljer i det här scenariot är *relaterat objekt val*. Alternativet **från betygsatta objekt (för modell utvärdering)** är i första hand för modell utvärdering under inlärnings processen. Vi väljer **bland alla objekt** för den här förutsägelse fasen. Visualiseringen av [poängen matchbox rekommenderar][score-matchbox-recommender] att utdata ser ut som figur 24.

![Resultat från rekommenderade system-relaterade objekt](./media/interpret-model-results/24.png)

Bild 24. Visualisera Poäng resultat för de rekommenderade system-relaterade objekten

Den första av de sex kolumnerna representerar de angivna objekt-ID: n som krävs för att hitta relaterade objekt, som tillhandahålls av indata. De andra fem kolumnerna lagrar de förväntade relaterade objekten för objektet i fallande ordning i förhållande till relevansen. Till exempel, på den första raden, är det mest relevanta objektet för artikel 135026 135074, följt av 135035, 132875, 135055 och 134992.

**Webb tjänst publicering**

Processen att publicera dessa experiment som webb tjänster för att få förutsägelser liknar varandra för var och en av de fyra scenarierna. Här tar vi det andra scenariot (rekommendera objekt till en specifik användare) som exempel. Du kan följa samma procedur med de andra tre.

Om du sparar det utbildade rekommenderade systemet som en utbildad modell och filtrerar indata till en kolumn för användar-ID som begärs, kan du ansluta experimentet som i bild 25 och publicera det som en webb tjänst.

![Poäng experiment med problemet med restaurang rekommendation](./media/interpret-model-results/25.png)

Bild 25. Poäng experiment med problemet med restaurang rekommendation

När webb tjänsten körs ser det returnerade resultatet ut som bild 26. De fem rekommenderade restauranger för User U1048 är 134986, 135018, 134975, 135021 och 132862.

![Exempel på tjänsten rekommenderar system](./media/interpret-model-results/25_1.png)

![Exempel på experiment resultat](./media/interpret-model-results/26.png)

Bild 26. Webb tjänst resultat av problem med restaurang rekommendationer

<!-- Module References -->
[assign-to-clusters]: /azure/machine-learning/studio-module-reference/assign-data-to-clusters
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[score-matchbox-recommender]: /azure/machine-learning/studio-module-reference/score-matchbox-recommender
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-clustering-model]: /azure/machine-learning/studio-module-reference/train-clustering-model
[train-matchbox-recommender]: /azure/machine-learning/studio-module-reference/train-matchbox-recommender