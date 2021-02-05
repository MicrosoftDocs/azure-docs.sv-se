---
title: Använda matematisk åtgärd
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Använd matematik operation i Azure Machine Learning för att tillämpa en matematisk åtgärd på kolumn värden i en pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 3b2925a532d722598ccf16c001c9e2591aed1f2b
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584998"
---
# <a name="apply-math-operation"></a>Använda matematisk åtgärd

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd funktionen Använd matematik för att skapa beräkningar som tillämpas på numeriska kolumner i data uppsättningen för indata. 

Matematiska åtgärder innehåller aritmetiska funktioner, trigonometriska funktioner, avrundnings funktioner och särskilda funktioner som används i data vetenskap, till exempel gamma-och fel funktioner.  

När du har definierat en åtgärd och kört pipelinen läggs värdena till i din data uppsättning. Beroende på hur du konfigurerar modulen kan du:

+ Lägg till resultaten i data uppsättningen (användbart när du verifierar resultatet av åtgärden).
+ Ersätt kolumn värden med de nya beräknade värdena.
+ Generera en ny kolumn för resultat och Visa inte ursprungliga data. 

Leta efter den åtgärd du behöver i följande kategorier:  

- [Basic](#basic-math-operations)  
  
     Funktionerna i kategorin **Basic** kan användas för att ändra ett enda värde eller en kolumn med värden. Du kan till exempel få det absoluta värdet för alla tal i en kolumn eller beräkna kvadratroten för varje värde i en kolumn.  
  
-   [Jämför](#comparison-operations)  
  
      Funktionerna i kategorin **Jämför** används för jämförelse: du kan göra en par jämförelse av värdena i två kolumner eller jämföra varje värde i en kolumn med en viss konstant. Du kan till exempel jämföra kolumner för att avgöra om värdena var samma i två data uppsättningar. Eller så kan du använda en konstant, till exempel ett maximalt tillåtet värde, för att hitta avvikande värden i en numerisk kolumn.  
  
-   [Åtgärder](#arithmetic-operations)  
  
     Kategorin **åtgärder** innehåller grundläggande matematiska funktioner: addition, subtraktion, multiplikation och division. Du kan arbeta med antingen kolumner eller konstanter. Du kan till exempel lägga till värdet i kolumn A till värdet i kolumn B. Eller så kan du subtrahera en konstant, till exempel ett tidigare beräknat medelvärde, från varje värde i kolumn A.  
  
-   [Avrundning](#rounding-operations)  
  
     **Avrundnings** kategorin innehåller en mängd funktioner för att utföra åtgärder, till exempel avrundning, tak, golv och trunkering till olika precisions nivåer. Du kan ange precisions nivån för både decimal tal och heltal.  
  
-   [Speciella](#special-math-functions)  
  
     Kategorin **Special** innehåller matematiska funktioner som används särskilt i data vetenskap, till exempel Elliptic integraler och funktionen Gaussisk fel.  
  
-   [Trigonometriska](#trigonometric-functions)  
  
     Den **trigonometriska** kategorin innehåller alla standard trigonometriska funktioner. Du kan till exempel konvertera radianer till grader eller beräkna funktioner som tangens i antingen radianer eller grader.
     Dessa funktioner är unära, vilket innebär att de tar en enda kolumn med värden som indatatyper, använder den trigonometriska funktionen och returnerar en kolumn med värden som resultatet. Se till att kolumnen indatamängd är av lämplig typ och innehåller rätt typ av värden för den angivna åtgärden.   

## <a name="how-to-configure-apply-math-operation"></a>Konfigurera tillämpa matematik åtgärd  

Använd modulen för **matematik körning** kräver en data uppsättning som innehåller minst en kolumn som endast innehåller siffror. Talen kan vara diskreta eller kontinuerliga men måste vara av en numerisk datatyp, inte en sträng.

Du kan tillämpa samma åtgärd på flera numeriska kolumner, men alla kolumner måste finnas i samma data uppsättning. 

Varje instans av den här modulen kan bara utföra en typ av åtgärd i taget. Om du vill utföra komplexa matematiska åtgärder kan du behöva kedja samman flera instanser av modulen **Använd matematik** .  
  
1.  Lägg till modulen **Använd matematik** i din pipeline.

1. Anslut en data uppsättning som innehåller minst en numerisk kolumn.  

1.  Välj en eller flera käll kolumner som beräkningen ska utföras på.   
  
    - Alla kolumner som du väljer måste vara en numerisk datatyp. 
    - Data området måste vara giltigt för den valda matematiska åtgärden. Annars kan ett fel eller NaN (inte ett tal) uppstå. Till exempel är ln (-1,0) en ogiltig åtgärd och resulterar i värdet `NaN` .
  
1.  Välj **kategori** för att välja vilken **typ** av matematik åtgärd som ska utföras.
    
1. Välj en viss åtgärd i listan i den kategorin.
  
1.  Ange ytterligare parametrar som krävs av varje typ av åtgärd.  
  
1.  Använd alternativet **output mode** för att ange hur du vill att matematik åtgärden ska genereras: 

    - **Lägg till**. Alla kolumner som används som indata ingår i data uppsättningen för utdata, plus ytterligare en kolumn som innehåller resultatet av matematik åtgärden.
    - **Inplats**. Värdena i de kolumner som används som indata ersätts med de nya beräknade värdena. 
    - **ResultOnly**. En enskild kolumn returneras som innehåller resultatet av matematik åtgärden.
  
1.  Skicka pipelinen.  
  
## <a name="results"></a>Resultat

Om du genererar resultaten med hjälp av **tilläggs** -eller **ResultOnly** -alternativen anger kolumn rubrikerna för den returnerade data uppsättningen den åtgärd och de kolumner som användes. Om du till exempel jämför två kolumner med operatorn **Equals** ser resultatet ut så här:  
  
-   **Lika med (Col2_Col1)**, som anger att du har testat Col2 mot Col1.  
-   **Lika med (Col2_ $10)**, vilket anger att du jämförde kolumn 2 med konstant 10.  

Även om du använder alternativet **på plats** tas inte källdata bort eller ändras. kolumnen i den ursprungliga data uppsättningen är fortfarande tillgänglig i designern. Om du vill visa ursprungliga data kan du ansluta modulen [Lägg till kolumner](add-columns.md) och koppla den till utdata från **Använd matematik-åtgärd**.  
    
## <a name="basic-math-operations"></a>Grundläggande matematik åtgärder 

Funktionerna i kategorin **Basic** tar vanligt vis ett enda värde från en kolumn, utför den fördefinierade åtgärden och returnerar ett enda värde. För vissa funktioner kan du ange en konstant eller en kolumn uppsättning som ett andra argument.  
  
 Azure Machine Learning stöder följande funktioner i kategorin **Basic** :  

### <a name="abs"></a>Abs

Returnerar det absoluta värdet för de markerade kolumnerna.  
  
### <a name="atan2"></a>Atan2

Returnerar en omvänd tangens för fyra kvadranter.  

Markera de kolumner som innehåller punkt koordinaterna. För det andra argumentet, som motsvarar x-koordinaten, kan du också ange en konstant.  

Motsvarar funktionen ATAN2 i MATLAB.  

### <a name="conj"></a>Conj

Returnerar konjugatet för värdena i den markerade kolumnen.  

### <a name="cuberoot"></a>CubeRoot

Beräknar kubens rot för värdena i den markerade kolumnen.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Beräknar den dubbla fakulteten för värden i den markerade kolumnen. Dubbel fakulteten är ett tillägg till den normala fakultets funktionen och den betecknas som `x!!` .  

### <a name="eps"></a>Per

Returnerar storleken på mellanrummet mellan det aktuella värdet och det näst högsta, dubbla precisions numret. Motsvarar EPS-funktionen i MATLAB.  
  
### <a name="exp"></a>Exp

Returnerar e upphöjt till kraften i värdet i den markerade kolumnen. Den här funktionen är samma som funktionen för EXP-funktionen i Excel.  

### <a name="exp2"></a>Exp2

Returnerar bas 2 upphöjt till argumenten, lösning för y = x * 2<sup>t</sup> där t är en kolumn med värden som innehåller exponenter.  

I  **kolumn uppsättning** väljer du den kolumn som innehåller exponent värden t.

För **Exp2** kan du ange ett andra argument x, som kan vara antingen en konstant eller en annan kolumn med värden. I den **andra argument typen** anger du om du vill ge multiplikatorn x som en konstant eller ett värde i en kolumn.  

Om du till exempel väljer en kolumn med värdena {0,1,2,3,4,5} för både multiplikatorn och exponenten returnerar funktionen {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Returnerar den negativa exponenten för värden i den markerade kolumnen.  

### <a name="factorial"></a>Fakultet
Returnerar fakulteten för värdena i den markerade kolumnen.  

### <a name="hypotenuse"></a>Hypotenuse
Beräknar Hypotenuse för en triangel där längden på en sida har angetts som en kolumn med värden, och längden på den andra sidan anges antingen som en konstant eller som två kolumner.  

### <a name="ln"></a>Ln

Returnerar den naturliga logaritmen för värdena i den markerade kolumnen.  

### <a name="lnplus1"></a>LnPlus1

Returnerar den naturliga logaritmen plus en för värdena i den markerade kolumnen.  

### <a name="log"></a>Loggas

Returnerar loggen för värdena i den markerade kolumnen, med angiven bas.  

Du kan ange basen (det andra argumentet) antingen som en konstant eller genom att välja en annan kolumn med värden.  

### <a name="log10"></a>Log10

Returnerar de 10 logaritmiska värdena för den markerade kolumnen.  

### <a name="log2"></a>Log2

Returnerar de två logaritmiska värdena för den markerade kolumnen.  

### <a name="nthroot"></a>NthRoot
Returnerar den n:te roten av värdet med en n som du anger.  

Välj de kolumner som du vill beräkna roten för, genom att använda alternativet **ColumnSet** .  

I den **andra argument typen** väljer du en annan kolumn som innehåller roten eller anger en konstant som ska användas som rot.  

Om det andra argumentet är en kolumn används varje värde i kolumnen som värdet n för motsvarande rad. Om det andra argumentet är en konstant skriver du värdet för n i text rutan **andra argument** .
### <a name="pow"></a>POW

Beräknar X upphöjt till Y för varje värde i den markerade kolumnen.  

Först väljer du de kolumner som innehåller **basen**, som ska vara ett flyttal, med alternativet **ColumnSet** .  

I den **andra argument typen** väljer du den kolumn som innehåller exponenten eller anger en konstant som ska användas som exponent.  

Om det andra argumentet är en kolumn, används varje värde i kolumnen som exponent för motsvarande rad. Om det andra argumentet är en konstant, anger du värdet för exponenten i text rutan **andra argument** .  

### <a name="sqrt"></a>Sqrt

Returnerar kvadratroten ur värdena i den markerade kolumnen.  

### <a name="sqrtpi"></a>SqrtPi

För varje värde i den markerade kolumnen multipliceras värdet med Pi och Returnerar kvadratroten ur resultatet.  

### <a name="square"></a>Square

Kvadraterar värdena i den markerade kolumnen.  

## <a name="comparison-operations"></a>Jämförelse åtgärder  

Använd jämförelse funktionerna i Azure Machine Learning designer när du behöver testa två uppsättningar värden mot varandra. I en pipeline kan du till exempel behöva utföra dessa jämförelse åtgärder:  

- Utvärdera en kolumn med sannolikhets Poäng modell mot ett tröskelvärde.
- Kontrol lera om två resultat uppsättningar är desamma. För varje rad som skiljer sig lägger du till en falsk flagga som kan användas för vidare bearbetning eller filtrering.  

### <a name="equalto"></a>EqualTo

Returnerar true om värdena är identiska.  

### <a name="greaterthan"></a>GreaterThan

Returnerar true om värdena i **kolumn uppsättningen** är större än den angivna konstanten, eller större än motsvarande värden i jämförelse kolumnen.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Returnerar true om värdena i **kolumn uppsättningen** är större än eller lika med den angivna konstanten, eller större än eller lika med motsvarande värden i jämförelse kolumnen.  

### <a name="lessthan"></a>LessThan

Returnerar true om värdena i **kolumn uppsättningen** är mindre än den angivna konstanten, eller färre än motsvarande värden i jämförelse kolumnen.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Returnerar true om värdena i **kolumn uppsättningen** är mindre än eller lika med den angivna konstanten, eller mindre än eller lika med motsvarande värden i jämförelse kolumnen.  

### <a name="notequalto"></a>NotEqualTo

Returnerar true om värdena i **kolumn uppsättningen** inte är lika med konstanten eller jämförelse kolumnen och returnerar FALSE om de är lika.  

### <a name="pairmax"></a>PairMax

Returnerar värdet som är större – värdet i **kolumn uppsättningen** eller värdet i kolumnen konstant eller jämförelse.  

### <a name="pairmin"></a>PairMin

Returnerar värdet som är mindre – värdet i **kolumn uppsättningen** eller värdet i kolumnen konstant eller jämförelse  
  
##  <a name="arithmetic-operations"></a>Aritmetiska åtgärder   

Innehåller de grundläggande aritmetiska åtgärderna: addition och subtraktion, Division och multiplikation.  Eftersom de flesta åtgärder är binära och kräver två siffror väljer du först åtgärden och väljer sedan den kolumn eller de tal som ska användas i de första och andra argumenten.

Ordningen för Division och subtraktion är följande: 
- Subtrahera (Arg1_Arg2) = Arg1-arg 2
- Dividera (Arg1_Arg2) = Arg1/arg 2

I följande tabell visas några exempel
Åtgärd|Num1|Num2|Resultat kolumn|Resultat värde|
----|----|----|----|----
|Addition|1|5|Lägg till (Num2_Num1)| 6|
|Multiplikation|1|5|Flera (Num2_Num1)|5|
|Subtraktion|5|1|Subtrahera (Num2_Num1)|4|
|Subtraktion|0|1|Subtrahera (Num2_Num1)|-1|
|Division|5|1|Dividera (Num2_Num1)|5|
|Division|1|0|Dividera (Num2_Num1)|Infinity|

### <a name="add"></a>Lägg till

Ange käll kolumnerna med hjälp av **kolumn uppsättning** och Lägg sedan till dessa värden ett tal som anges i det **andra argumentet**.  

Om du vill lägga till värden i två kolumner väljer du en kolumn eller kolumner med **kolumn uppsättning** och väljer sedan en andra kolumn med hjälp av det **andra argumentet**.  

### <a name="divide"></a>Dividera

Dividerar värdena i en **kolumn uppsättning** med en konstant eller av kolumnvärdena som definierats i det **andra argumentet**.  Med andra ord väljer du först divisorn och sedan utdelning. Värdet output är kvoten.

### <a name="multiply"></a>Multiplicera

Multiplicerar värdena i **kolumn uppsättningen** med de angivna konstant-eller kolumn värden.  

### <a name="subtract"></a>Subtrahera

Ange den kolumn med värden som ska användas ( *MINUEND*) genom att välja en annan kolumn med hjälp av alternativet **kolumn uppsättning** . Ange sedan det tal som ska subtraheras ( *SUBTRAHEND*) med hjälp av den **andra argument** List rutan. Du kan välja antingen en konstant eller en kolumn med värden.

##  <a name="rounding-operations"></a>Avrundnings åtgärder 

Azure Machine Learning Designer stöder flera olika avrundnings åtgärder. För många åtgärder måste du ange hur mycket precision som ska användas vid avrundning. Du kan antingen använda en statisk precisions nivå, anges som en konstant, eller så kan du använda ett dynamiskt precisions värde som hämtats från en kolumn med värden.  

- Om du använder en konstant anger du **precisions typen** **konstant** och skriver sedan antalet siffror som ett heltal i text rutan **konstant precision** . Om du anger ett icke-heltal, genererar modulen inget fel, men det kan vara oväntade resultat.  

- Om du vill använda ett annat precisions värde för varje rad i data uppsättningen anger du **precisions typen** till **ColumnSet** och väljer sedan den kolumn som innehåller lämpliga precisions värden.  

### <a name="ceiling"></a>Ceiling

Returnerar taket för värdena i **kolumn uppsättningen**.  

### <a name="ceilingpower2"></a>CeilingPower2

Returnerar taket i fyrkant för värdena i **kolumn uppsättningen**.  

### <a name="floor"></a>Floor

Returnerar golvet för värdena i **kolumn uppsättningen** till den angivna precisionen.  

### <a name="mod"></a>Mod

Returnerar decimal delen av värdena i **kolumn uppsättningen** till den angivna precisionen.  

### <a name="quotient"></a>Kvot

Returnerar decimal delen av värdena i **kolumn uppsättningen** till den angivna precisionen.  

### <a name="remainder"></a>Rest

Returnerar resten av värdena i **kolumn uppsättningen**.  

### <a name="rounddigits"></a>RoundDigits

Returnerar värdena i **kolumn uppsättningen**, avrundade av regeln 4/5 till det angivna antalet siffror.  

### <a name="rounddown"></a>RoundDown

Returnerar värdena i **kolumn uppsättningen**, avrundade nedåt till det angivna antalet siffror.  

### <a name="roundup"></a>RoundUp

Returnerar värdena i **kolumn uppsättningen**, avrundade uppåt till det angivna antalet siffror.  

### <a name="toeven"></a>ToEven

Returnerar värdena i **kolumn uppsättningen**, avrundat till närmaste heltal, jämna tal.  

### <a name="toodd"></a>ToOdd

Returnerar värdena i **kolumn uppsättningen**, avrundade till närmaste hela, udda tal.  

### <a name="truncate"></a>Truncate

Trunkerar värdena i **kolumn uppsättningen** genom att ta bort alla siffror som inte tillåts av den angivna precisionen.  
  
## <a name="special-math-functions"></a>Särskilda matematiska funktioner

Den här kategorin innehåller specialiserade matematiska funktioner som ofta används i data vetenskap. Om inget annat anges är funktionen enställig och returnerar den angivna beräkningen för varje värde i den markerade kolumnen eller kolumnerna.  

### <a name="beta"></a>Beta

Returnerar värdet för Eulers beta funktion.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
Returnerar värdet för den ofullständiga Elliptic-integralen.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Returnerar värdet för den fullständiga Elliptic-integralen (K).  

### <a name="erf"></a>FELF

Returnerar värdet för fel funktionen.  

Fel funktionen (som även kallas Gauss-felfunktionen) är en särskild funktion i sigmoid-formen som används i sannolikheten för att beskriva diffusionen.  

### <a name="erfc"></a>Erfc

Returnerar värdet för funktionen kompletterande fel.  

`Erfc` definieras som 1 – FELF (x).  

### <a name="erfscaled"></a>ErfScaled

Returnerar värdet för funktionen scaleed error.  

Den skalade versionen av error-funktionen kan användas för att undvika aritmetiskt underskott.  

### <a name="erfinverse"></a>ErfInverse

Returnerar värdet för den inversna `erf` funktionen.  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

Returnerar värdet för den exponentiella integral-ei.  

### <a name="gamma"></a>Gamma

Returnerar värdet för gamma funktionen.  

### <a name="gammaln"></a>Gammal

Returnerar den naturliga logaritmen för gamma funktionen.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Returnerar värdet för den vanliga ofullständiga gamma funktionen.  

Den här funktionen tar ett andra argument, som kan anges antingen som en konstant eller en kolumn med värden.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Returnerar värdet för den inverterade reguljära gamma funktionen.  

Den här funktionen tar ett andra argument, som kan anges antingen som en konstant eller en kolumn med värden.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Returnerar värdet för den vanliga ofullständiga gamma funktionen.  

Den här funktionen tar ett andra argument, som kan anges antingen som en konstant eller en kolumn med värden.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Returnerar värdet för den inverterade generaliserade, normala gamma funktionen.

Den här funktionen tar ett andra argument, som kan anges antingen som en konstant eller en kolumn med värden.  

### <a name="polygamma"></a>Polygamma

Returnerar värdet för polygamma-funktionen.  

Den här funktionen tar ett andra argument, som kan anges antingen som en konstant eller en kolumn med värden.

##  <a name="trigonometric-functions"></a>Trigonometriska funktioner 

Den här kategorin iIncludes de flesta av de viktiga trigonometriska och inversna trigonometriska funktionerna. Alla trigonometriska funktioner är unära och kräver inga ytterligare argument.  

### <a name="acos"></a>Acos

Beräknar arcus cosinus för kolumnvärdena.  

### <a name="acosdegree"></a>AcosDegree

Beräknar arcus cosinus för kolumnvärdena i grader.  

### <a name="acosh"></a>Acosh

Beräknar hyperboliskt arcus cosinus för kolumn värden.  

### <a name="acot"></a>Acot

Beräknar cotangens för kolumn värden.  

### <a name="acotdegrees"></a>AcotDegrees

Beräknar cotangens för kolumnvärdena i grader.  

### <a name="acoth"></a>Acoth

Beräknar de hyperboliska cotangens för kolumn värden.  

### <a name="acsc"></a>ACSC

Beräknar arccosecant för kolumn värden.  

### <a name="acscdegrees"></a>AcscDegrees

Beräknar arccosecant för kolumnvärdena i grader.  
### <a name="asec"></a>Asec

Beräknar arcsecant för kolumn värden.  

### <a name="asecdegrees"></a>AsecDegrees

Beräknar arcsecant för kolumnvärdena i grader.  

### <a name="asech"></a>Asech

Beräknar de hyperboliska arcsecant för kolumn värden.  

### <a name="asin"></a>Asin

Beräknar arcus sinus för kolumn värden.  

### <a name="asindegrees"></a>AsinDegrees

Beräknar arcus sinus för kolumnvärdena i grader.  

### <a name="asinh"></a>Asinh

Beräknar hyperbolisk arcus sinus för kolumnvärdena.  

### <a name="atan"></a>Atan

Beräknar arcus tangens för kolumnvärdena.  

### <a name="atandegrees"></a>AtanDegrees

Beräknar arcus tangens för kolumnvärdena i grader.  

### <a name="atanh"></a>Atanh

Beräknar kolumn värdets hyperboliska arcus tangens.  

### <a name="cos"></a>Cos

Beräknar cosinus för kolumn värden.  

### <a name="cosdegrees"></a>CosDegrees

Beräknar cosinus för kolumnvärdena i grader.  

### <a name="cosh"></a>Cosh

Beräknar hyperbolisk cosinus för kolumnvärdena.  

### <a name="cot"></a>Cot

Beräknar cotangens för kolumnvärdena.  

### <a name="cotdegrees"></a>CotDegrees

Beräknar cotangens för kolumnvärdena i grader.  

### <a name="coth"></a>Coth
Beräknar hyperbolisk cotangens för kolumn värden.  

### <a name="csc"></a>CSC

Beräknar cosekant för kolumnvärdena.  

### <a name="cscdegrees"></a>CscDegrees

Beräknar cosekant för kolumnvärdena i grader.  

### <a name="csch"></a>Csch

Beräknar hyperbolisk cosekant för kolumnvärdena.  

### <a name="degreestoradians"></a>DegreesToRadians

Konverterar grader till radianer.  

### <a name="sec"></a>SEC

Beräknar sekant för kolumn värden.  

### <a name="asecdegrees"></a>aSecDegrees

Beräknar sekant för kolumnvärdena i grader.  

### <a name="asech"></a>aSech

Beräknar hyperbolisk sekant för kolumn värden.  

### <a name="sign"></a>Tecken

Returnerar tecknet för kolumn värden.  

### <a name="sin"></a>Sin

Beräknar sinus för kolumn värden.  

### <a name="sinc"></a>Sinc

Beräknar sinus-cosinus-värdet för kolumn värden.  

### <a name="sindegrees"></a>SinDegrees

Beräknar sinus för kolumnvärdena i grader.  

### <a name="sinh"></a>Sinh

Beräknar kolumn värdets hyperboliska sinus.  

### <a name="tan"></a>Tan

Beräknar tangens för kolumnvärdena.  

### <a name="tandegrees"></a>TanDegrees

Beräknar tangens för argumentet i grader.  

### <a name="tanh"></a>Tanh

Beräknar kolumn värdens hyperboliska tangens.  
  
## <a name="technical-notes"></a>Tekniska anteckningar

Var försiktig när du väljer mer än en kolumn som den andra operatorn. Resultaten är lätta att förstå om åtgärden är enkel, till exempel att lägga till en konstant i alla kolumner. 

Anta att din data uppsättning har flera kolumner och att du lägger till data uppsättningen till sig själv. I resultaten läggs varje kolumn till i sig själv enligt följande:  
  
|Num1|Num2|Num3|Lägg till (Num1_Num1)|Lägg till (Num2_Num2)|Lägg till (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Om du behöver utföra mer komplexa beräkningar kan du kedja flera instanser av **Använd matematik-åtgärd**. Du kan till exempel lägga till två kolumner genom att använda en instans av **Använd matematik-åtgärd** och sedan använda en annan instans av **Använd matematik-åtgärd** för att dividera summan med en konstant för att få medelvärdet.  
  
Du kan också använda en av följande moduler för att utföra alla beräkningar samtidigt, med SQL, R eller Python-skript:
 
+ [Köra R-skript](execute-r-script.md)
+ [Köra Python-skript](execute-python-script.md)
+ [Använda SQL-transformering](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
