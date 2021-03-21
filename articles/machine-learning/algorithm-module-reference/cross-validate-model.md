---
title: 'Kors validerings modell: modulreferens'
titleSuffix: Azure Machine Learning
description: Använd modulen för att kontrol lera modell i Azure Machine Learning designer för att korsa beräkning av parameter uppskattningar för klassificerings-eller Regressions modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: d4099ecf6e6bcc6654391e54292878393fb22914
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421353"
---
# <a name="cross-validate-model"></a>Korsvalidera modell

Den här artikeln beskriver hur du använder modulen för att validera modeller i Azure Machine Learning designer. *Kors validering* är en teknik som ofta används i Machine Learning för att utvärdera både variabiliteten hos en data uppsättning och tillförlitligheten för en modell som har tränas genom dessa data.  

Modulen för att validera modell tar sig in som indata för en etikettad data uppsättning, tillsammans med en klass som inte är tränad eller Regressions modell. Den delar in data uppsättningen i ett antal del mängder (*vikning*), skapar en modell på varje vikning och returnerar sedan en uppsättning noggrannhets statistik för varje vikning. Genom att jämföra noggrannhets statistiken för alla vik objekt kan du tolka data uppsättningens kvalitet. Du kan sedan förstå om modellen är mottaglig för variationer i data.  

Med kors validerings modellen returneras även förväntade resultat och sannolikheter för data uppsättningen, så att du kan utvärdera förutsägelsens tillförlitlighet.  

### <a name="how-cross-validation-works"></a>Så här fungerar kors validering

1. Kors valideringen delar slumpmässigt upp utbildnings data i vikningar. 

   Algoritmen är standardvärdet 10 vikning om du inte tidigare har partitionerat data uppsättningen. Om du vill dela upp data uppsättningen i ett annat antal vikningar kan du använda [partition och exempel](partition-and-sample.md) -modulen och ange hur många vikningar som ska användas.  

2.  Modulen sätter undan data i vikt 1 som ska användas för verifiering. (Detta kallas ibland för *Hold-vikningen*.) Modulen använder de återstående viken för att träna en modell. 

    Om du till exempel skapar fem vikningar genererar modulen fem modeller under kors valideringen. Modulen tågen varje modell genom att använda fyra femtedel av data. Den testar varje modell på den återstående en-femte.  

3.  Vid testning av modellen för varje vikning utvärderar modulen flera noggrannhets statistik. Vilken statistik som modulen använder beror på vilken typ av modell du utvärderar. Olika statistik används för att utvärdera klassificerings modeller jämfört med Regressions modeller.  

4.  När utvecklings-och utvärderings processen är klar för alla veck genererar kors validerings modellen en uppsättning prestanda mått och resultat resultat för alla data. Granska dessa mått om du vill se om en enskild vikning har hög eller låg exakthet. 

### <a name="advantages-of-cross-validation"></a>Fördelar med kors validering

Ett annat och vanligt sätt att utvärdera en modell är att dela upp data i en utbildning och test uppsättning med hjälp av [dela data](split-data.md)och sedan validera modellen på tränings data. Men kors valideringen erbjuder vissa fördelar:  

-   Kors valideringen använder fler test data.

    Kors validering mäter modellens prestanda med de angivna parametrarna i ett större data utrymme. Det vill säga kors valideringen använder hela inlärnings data uppsättningen för både utbildning och utvärdering, i stället för en del. Om du däremot validerar en modell genom att använda data som genereras från en slumpmässig delning, utvärderar du vanligt vis modellen på högst 30 procent eller mindre av tillgängliga data.  

    Men eftersom kors validerings tåg och validerar modellen flera gånger över en större data uppsättning, är det mycket mer beräknings intensiva. Det tar mycket längre tid än att verifiera vid en slumpmässig delning.  

-   Kors valideringen utvärderar både data uppsättningen och modellen.

    Kors valideringen mäter inte bara noggrannheten i en modell. Det ger dig också en uppfattning om hur representativ data uppsättningen är och hur känslig modell det kan vara för variationer i data.  

## <a name="how-to-use-cross-validate-model"></a>Använda kors validerings modell

Kors validering kan ta lång tid att köra om data uppsättningen är stor.  Så du kan använda kors validerings modell i den inledande fasen av att skapa och testa din modell. I den fasen kan du utvärdera modell parametrarnas lämplighet (förutsatt att beräknings tiden är nöjd). Du kan sedan träna och utvärdera din modell genom att använda de etablerade parametrarna i [träna modell](train-model.md) och [utvärdera modell](evaluate-model.md) moduler.

I det här scenariot tränar du och testar modellen med hjälp av kors validerings modellen.

1. Lägg till modulen för att validera modell i din pipeline. Du hittar den i Azure Machine Learning designer i **utvärderings kategorin modell bedömnings &** . 

2. Anslut utdata från en klassificerings-eller Regressions modell. 

    Om du till exempel använder **två klass förstärknings besluts träd** för klassificering konfigurerar du modellen med de parametrar som du vill använda. Dra sedan en koppling från klassificerarens **modell** port till den matchande porten för kors validerings modellen. 

    > [!TIP] 
    > Du behöver inte träna modellen eftersom en kors validerings modell automatiskt tränar modellen som en del av utvärderingen.  
3.  På **data uppsättnings** porten för kors validerings modellen ansluter du till en etikettad utbildnings data uppsättning.  

4.  Klicka på **Redigera kolumn** i den högra panelen av kors validerings modellen. Välj den enstaka kolumn som innehåller klass etiketten eller det förutsägbara värdet. 

5. Ange ett värde för den **slumpmässiga start** parametern om du vill upprepa resultatet av kors valideringen mellan efterföljande körningar på samma data.  

6. Skicka pipelinen.

7. Se avsnittet [Results](#results) för en beskrivning av rapporterna.

## <a name="results"></a>Resultat

När alla iterationer har slutförts skapar kors validerings modellen Poäng för hela data uppsättningen. Det skapar också prestanda mått som du kan använda för att utvärdera modellens kvalitet.

### <a name="scored-results"></a>Resultat

Den första utdata i modulen tillhandahåller källdata för varje rad, tillsammans med vissa förväntade värden och relaterade sannolikheter. 

Om du vill visa resultaten går du till pipelinen och högerklickar på modulen för att validera modell. Välj **visualisera Poäng resultat**.

| Nytt kolumnnamn      | Beskrivning                              |
| -------------------- | ---------------------------------------- |
| Poängsatta etiketter        | Den här kolumnen läggs till i slutet av data uppsättningen. Det innehåller det förväntade värdet för varje rad. |
| Resultat sannolikhet | Den här kolumnen läggs till i slutet av data uppsättningen. Det anger den uppskattade sannolikheten för värdet i **Poäng etiketter**. |
| Vikt nummer          | Anger det nollbaserade indexet för den vikning som varje datarad har tilldelats vid kors validering. |

 ### <a name="evaluation-results"></a>Utvärderingsresultat

Den andra rapporten är grupperad efter vikning. Kom ihåg att vid körning delar kors validerings modellen slumpmässigt utbildnings data i *n* -vikning (som standard 10). I varje iteration över data uppsättningen använder kors validerings modellen en vikning som en verifierings data uppsättning. Den använder de återstående *n-1-* vikningarna för att träna en modell. Var och en av de *n* modellerna testas mot data i alla andra vik.

I den här rapporten visas vikningarna efter index värde i stigande ordning.  Om du vill sortera efter andra kolumner kan du spara resultaten som en data uppsättning.

Om du vill visa resultaten går du till pipelinen och högerklickar på modulen för att validera modell. Välj **visualisera utvärderings resultat per vikning**.


|Kolumnnamn| Description|
|----|----|
|Vikt nummer| En identifierare för varje vikning. Om du har skapat fem vikningar skulle det finnas fem del mängder av data, numrerade 0 till 4.
|Antal exempel i vikning|Antalet rader tilldelade till varje vikning. De bör vara ungefär lika. |


Modulen innehåller också följande mått för varje vikning, beroende på vilken typ av modell du utvärderar: 

+ **Klassificerings modeller**: precision, återkallande, F-score, AUC, precision  

+ **Regressions modeller**: Genomsnittligt absolut fel, rot genomsnitts fel, relativt absolut fel, relativt kvadratvärde och koefficient för bestämning


## <a name="technical-notes"></a>Tekniska anteckningar  

+ Det är en bra idé att normalisera data uppsättningar innan du använder dem för kors validering. 

+ En kors validerings modell är mycket mer beräknings intensiv och tar längre tid än om du validerade modellen med hjälp av en slumpvis delad data uppsättning. Orsaken är att modellen verifierar modell tåg och validerar modellen flera gånger.

+ Du behöver inte dela upp data uppsättningen i inlärnings-och test uppsättningar när du använder kors validering för att mäta modellens noggrannhet. 


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

