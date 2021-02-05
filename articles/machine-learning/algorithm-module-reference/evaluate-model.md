---
title: 'Utvärdera modell: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen utvärdera modell i Azure Machine Learning för att mäta noggrannheten i en utbildad modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/27/2020
ms.openlocfilehash: 39bdf9cb0c97e19a67b23046c6f06b60daa30147
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584948"
---
# <a name="evaluate-model-module"></a>Utvärdera modell modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att mäta noggrannheten för en utbildad modell. Du anger en data uppsättning som innehåller resultat som genererats från en modell, och modulen **utvärdera modell** beräknar en uppsättning utvärderings mått som är bransch standard.
  
 Måtten som returneras av **evaluate-modellen** beror på vilken typ av modell du utvärderar:  
  
-   **Klassificerings modeller**    
-   **Regressions modeller**  
-   **Kluster modeller**  


> [!TIP]
> Om du inte har använt modell utvärderingen rekommenderar vi video serien av Dr. Stephen Elston som en del av [Machine Learning-kursen](/archive/blogs/machinelearning/new-edx-course-data-science-machine-learning-essentials) från EDX. 


## <a name="how-to-use-evaluate-model"></a>Använda utvärdera modell
1. Anslut den returnerade **data uppsättningens** utdata från [Poäng modellen](./score-model.md) eller resultat data uppsättningens utdata från [tilldela data till kluster](./assign-data-to-clusters.md) till den vänstra Indataporten för **utvärdera modell**. 
    > [!NOTE] 
    > Om du använder moduler som "Välj kolumner i data uppsättning" för att välja en del av data uppsättningen för indata, se till att kolumnen verklig etikett (används i träning), kolumnen "beräknade sannolikheter" och "score etiketter" finns för att beräkna mått som AUC, precision för binära klassificering/avvikelse identifiering.
    > Den faktiska etikett kolumnen, kolumnen scored etiketter finns för att beräkna mått för klassificering/regression i flera klasser.
    > Kolumnen tilldelningar, kolumnerna DistancesToClusterCenter nej. X ' (X är centroid-index, sträcker sig från 0,..., antal centroids-1) för att beräkna mått för klustring.

    > [!IMPORTANT]
    > + Om du vill utvärdera resultaten ska den utgående data uppsättningen innehålla särskilda namn på resultat kolumnen som uppfyller kraven för utvärdera modell modul.
    > + `Labels`Kolumnen kommer att anses som faktiska etiketter.
    > + För Regressions aktivitet måste data uppsättningen som ska utvärderas ha en kolumn med namnet `Regression Scored Labels` , som representerar Poäng etiketter.
    > + För att den binära klassificerings aktiviteten ska kunna utvärdera måste den data uppsättning som ska utvärderas ha två kolumner, med namnet `Binary Class Scored Labels` , `Binary Class Scored Probabilities` som representerar beräknade etiketter respektive sannolikheter.
    > + För att flera klassificerings uppgifter ska kunna utvärderas måste den data uppsättning som ska utvärderas ha en kolumn med namnet `Multi Class Scored Labels` , som representerar Poäng etiketter.
    > Om utdata från den överordnade modulen inte har dessa kolumner måste du ändra enligt kraven ovan.

2. Valfritt Anslut den returnerade **data uppsättningens** utdata från [Poäng modellen](./score-model.md) eller resultat data uppsättningens utdata från tilldela data till kluster för den andra modellen till den **högra** Indataporten för **utvärdera modell**. Du kan enkelt jämföra resultat från två olika modeller på samma data. De två indatavärdena ska vara av samma typ av algoritm. Eller så kan du jämföra resultat från två olika körningar över samma data med olika parametrar.

    > [!NOTE]
    > Algoritmen refererar till klassificeringen "dubbelriktad", "klassad klassificering", "regression", "Clustering" under Machine Learning algoritmer. 

3. Skicka in pipelinen för att generera utvärderings poängen.

## <a name="results"></a>Resultat

När du har kört **utvärdera modell** väljer du modulen för att öppna navigerings panelen **utvärdera modell** till höger.  Välj sedan fliken **utdata + loggar** och på den fliken har avsnittet **data utdata** flera ikoner. **Visualiserings** ikonen har en stapeldiagram och är ett första sätt att se resultatet.

När du har klickat på **visualiserings** ikonen för binär klassificering kan du visualisera den binära förvirring i matrisen.
För flera klassificeringar kan du hitta filen för förvirring i mat ris ritningen på fliken **utdata + loggar** , till exempel följande:
> [!div class="mx-imgBorder"]
> ![Förhands granskning av Uppladdad bild](media/module/multi-class-confusion-matrix.png)

Om du ansluter data uppsättningar till båda indata för **utvärdera modell** kommer resultatet att innehålla mått för båda data uppsättningarna eller båda modellerna.
Modellen eller data som är kopplade till den vänstra porten visas först i rapporten följt av måtten för data uppsättningen eller modellen som är kopplad till rätt port.  

Följande bild visar till exempel en jämförelse av resultat från två kluster modeller som bygger på samma data, men med olika parametrar.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Eftersom det här är en kluster modell är utvärderings resultatet annorlunda än om du jämför resultat från två Regressions modeller eller jämför två klassificerings modeller. Den övergripande presentationen är dock densamma. 

## <a name="metrics"></a>Mått

I det här avsnittet beskrivs de mått som returneras för de olika typerna av modeller som stöds för användning med **utvärdera modell**:

+ [klassificerings modeller](#metrics-for-classification-models)
+ [Regressions modeller](#metrics-for-regression-models)
+ [kluster modeller](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Mått för klassificerings modeller


Följande mått rapporteras när du utvärderar binära klassificerings modeller.
  
-   **Noggrannhet** mäter en klassificerings modells lämplighet som andel sanna resultat av totalt antal fall.  
  
-   **Precision** är förhållandet mellan faktiska resultat och alla positiva resultat. Precision = TP/(TP + RP)  
  
-   **Återkallande** är den totala mängden av relevanta instanser som faktiskt hämtades. Återkallande = TP/(TP + FN)  
  
-   **F1-poängen** beräknas som viktat medelvärde för precision och återkallande mellan 0 och 1, där det idealiska värdet för F1 är 1.  
  
-   **AUC** mäter ytan under kurvan som ritas med sant positiva resultat på y-axeln och falska positiva identifieringar på x-axeln. Det här måttet är användbart eftersom det innehåller ett enda nummer som gör det möjligt att jämföra modeller av olika typer. AUC är klassificerings tröskel-invariant. Den mäter kvaliteten på modellens förutsägelser oavsett vilket klassificerings tröskel som valts.


### <a name="metrics-for-regression-models"></a>Mät värden för Regressions modeller
 
Måtten som returneras för Regressions modeller är utformade för att uppskatta mängden fel.  En modell anses vara bra anpassad om skillnaden mellan observerade och förväntade värden är liten. Men om du tittar på resten av resten (skillnaden mellan en förutsägande punkt och dess motsvarande faktiska värde) kan du få mycket information om potentiell förskjutning i modellen.  
  
 Följande mått rapporteras för utvärdering av Regressions modeller.
  
- **Medelvärde för absolut fel (Mae)** hur nära förutsägelserna är till de faktiska resultatet; Därför är en lägre poäng bättre.  
  
- **Rot genomsnitts fel (rmse)** skapar ett enskilt värde som sammanfattar felet i modellen. Genom att squaring skillnaden ignorerar måttet skillnaden mellan överförutsägelse och under förutsägelse.  
  
- **Relativ absolut fel (RAE)** är den relativa absoluta skillnaden mellan förväntade och faktiska värden. relativ eftersom genomsnitts skillnaden divideras med det aritmetiska medelvärdet.  
  
- **Ett relativt kvadratvärde (RSE)** normaliserar på samma sätt det totala antalet fel i det förväntade värdet genom att dividera med det totala antalet fel i det faktiska värdet.  
  

  
- **Koefficienten för bestämning**, som ofta kallas R <sup>2</sup>, representerar modellens förutsägande effekt som ett värde mellan 0 och 1. Noll betyder att modellen är slumpmässig (förklarar ingenting). 1 betyder en perfekt anpassning. Vi bör dock använda försiktighet i tolkningen av R<sup>2</sup> -värden, eftersom låga värden kan vara helt normala och höga värden kan vara misstänkta.

###  <a name="metrics-for-clustering-models"></a>Mått för kluster modeller

Eftersom kluster modeller skiljer sig avsevärt från klassificerings-och Regressions modeller i många avseenden, returnerar [utvärdera modellen](evaluate-model.md) även en annan uppsättning statistik för kluster modeller.  
  
 Statistiken som returneras för en kluster modell beskriver hur många data punkter som har tilldelats varje kluster, mängden separering mellan kluster och hur nära data punkterna är sammanslagna i varje kluster.  
  
 Statistiken för kluster modellen beräknas över hela data uppsättningen, med ytterligare rader som innehåller statistik per kluster.  
  
Följande mått rapporteras för utvärdering av kluster modeller.
    
-   Poängen i kolumnen, det **genomsnittliga avståndet till andra centret**, visar hur nära, i genomsnitt, varje punkt i klustret är till centroids för alla andra kluster.   

-   Poängen i kolumnen, **Genomsnittligt avstånd till kluster Center**, motsvarar stängningen av alla punkter i ett kluster till centroid i klustret.  
  
-   I kolumnen **antal punkter** visas hur många data punkter som har tilldelats varje kluster, tillsammans med det totala antalet data punkter i alla kluster.  
  
     Om antalet data punkter som tilldelas till kluster är mindre än det totala antalet tillgängliga data punkter, innebär det att data punkterna inte kunde tilldelas till ett kluster.  
  
-   Poängen i kolumnen, **Maximalt avstånd till kluster Center**, representerar Max avståndet mellan varje punkt och centroid för den aktuella punktens kluster.  
  
     Om det här värdet är högt, kan det betyda att klustret är allmänt utspridd. Du bör granska statistiken tillsammans med det **genomsnittliga avståndet till kluster Center** för att fastställa klustrets uppslag.   

-   Den **kombinerade utvärderings** poängen längst ned i varje del av resultaten visar genomsnitts poängen för de kluster som skapats i just den modellen.  
  

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.