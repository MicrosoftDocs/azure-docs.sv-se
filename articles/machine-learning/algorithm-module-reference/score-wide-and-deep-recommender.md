---
title: Använd den stora & djup rekommenderar-modulen
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder den & djup rekommenderade modulen i Azure Machine Learning för att Visa rekommendations förutsägelser för en data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/12/2020
ms.openlocfilehash: 9a1a3892e6a47aabd9b5129ca551900494616bc8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905168"
---
# <a name="score-wide-and-deep-recommender"></a>Bedöma modulen Wide and Deep Recommender

Den här artikeln beskriver hur du använder modulen **Poäng och djup rekommenderar** i Azure Machine Learning designer för att skapa förutsägelser baserade på en utbildad rekommendations modell, baserat på den breda & djup inlärningen från Google.

Den breda och djupgående rekommendationen kan generera två olika typer av förutsägelser:

- [Förutsäg betyg för en specifik användare och objekt](#predict-ratings)

- [Rekommendera objekt till en specifik användare](#recommend-items)


När du skapar en senare typ av förutsägelser kan du använda antingen *produktions läge* eller *utvärderings läge*.

- **Produktions läget** tar hänsyn till alla användare eller objekt och används vanligt vis i en webb tjänst. Du kan skapa Poäng för nya användare, inte bara användare som ses under utbildningen. 

- **Utvärderings läget** fungerar på en reducerad uppsättning användare eller objekt som kan utvärderas och används vanligt vis under experimentering.

Mer information om den bred och den djupgående rekommendationen och dess underliggande teori finns i relevant forsknings blad:  [bred & djup inlärning för rekommenderade system](https://arxiv.org/pdf/1606.07792.pdf).  

## <a name="how-to-configure-score-wide-and-deep-recommender"></a>Så här konfigurerar du poängen bred och djup rekommendation

Den här modulen stöder olika typer av rekommendationer, var och en med olika krav. Klicka på länken för den typ av data som du har och den typ av rekommendation som du vill skapa.

+ [Förutsägelse klassificering](#predict-ratings)
+ [Rekommendera objekt](#recommend-items)

### <a name="predict-ratings"></a>Förutsägelse klassificering

När du förutsäger klassificeringarna beräknar modellen hur en viss användare kommer att reagera på ett visst objekt, baserat på tränings data. Det innebär att indata för poängsättning måste ange både en användare och objektet som ska bedömas.

1. Lägg till en utbildad bred & djup rekommendations modell i experimentet och Anslut den till en **utbildad bred och djupgående rekommendations modell**.  Du måste skapa modellen med hjälp av [träna bred och djup rekommendation](train-wide-and-deep-recommender.md).

2. **Rekommendations förutsägelse typ**: Välj **bedömnings förutsägelse**. Inga ytterligare parametrar krävs.

3. Lägg till de data som du vill göra förutsägelser för och Anslut den till **data uppsättningen för att få poäng**.

    För att förutsäga klassificeringarna måste indata-datamängden innehålla användar objekts par.

    Data uppsättningen kan innehålla en valfri tredje kolumn med klassificeringar för paret User-Item i de första och andra kolumnerna, men den tredje kolumnen kommer att ignoreras under förutsägelse.

4.  (Valfritt). Om du har en data uppsättning med användar funktioner ansluter du den till **användar funktioner**.

    Data uppsättningen för användar funktioner ska innehålla användar identifieraren i den första kolumnen. De återstående kolumnerna måste innehålla värden som karaktäriserar användarna, till exempel kön, inställningar, plats osv.
  
    Funktioner för användare som har klassificerat objekt i träning-datauppsättningen ignoreras av **resultat bred och djup rekommendation**, eftersom de redan har lärts under utbildningen. Filtrera därför din data uppsättning i förväg för att endast ta med *kall start användare* eller användare som inte har klassificerat några objekt.

    > [!WARNING]
    > Om modellen har tränats utan att använda användar funktioner kan du inte införa användar funktioner under poängsättningen.

5. Om du har en data uppsättning med objekt funktioner kan du ansluta den till **objekt funktioner**.

    Data uppsättningen för objektet features måste innehålla en objekt identifierare i den första kolumnen. De återstående kolumnerna ska innehålla värden som karaktäriserar objekten.

    Funktioner för beräknade objekt i träning-datauppsättningen ignoreras av **Poäng och djup rekommenderar** allt eftersom de redan har lärts under utbildningen. Därför bör du begränsa din bedömnings data uppsättning till *kall start objekt* eller objekt som inte har klassificerats av några användare.

    > [!WARNING]
    > Om modellen har tränats utan att använda objekt funktioner kan du inte införa objekt funktioner under poängsättningen.

7. Kör experimentet.

### <a name="results-for-rating-predictions"></a>Resultat för bedömnings förutsägelser 

Data uppsättningen för utdata innehåller tre kolumner som innehåller användaren, objektet och den förväntade klassificeringen för varje indata-användare och objekt.

Dessutom tillämpas följande ändringar under poängsättningen:

-  För en kolumn med numeriska användar-eller objekt funktioner ersätts värden som saknas automatiskt med **medelvärdet** av dess värde för tränings uppsättning som inte saknas. För funktionen kategoriska ersätts värden som saknas med samma kategoriska-värde förutom de möjliga värdena för den här funktionen.
-  Ingen översättning tillämpas på funktions värden för att bevara deras gleshet.

### <a name="recommend-items"></a>Rekommendera objekt

För att rekommendera objekt för användare, ange en lista med användare och objekt som inmatade. Från dessa data använder modellen sina kunskaper om befintliga objekt och användare för att skapa en lista med objekt som kan överklaga till varje användare. Du kan anpassa antalet rekommendationer som returneras och ange ett tröskelvärde för antalet tidigare rekommendationer som krävs för att skapa en rekommendation.

1. Lägg till en utbildad bred och djupgående rekommendations modell i experimentet och Anslut den till en **utbildad bred och djupgående rekommendations modell**.  Du måste skapa modellen med hjälp av [träna bred och djup rekommendation](train-wide-and-deep-recommender.md).

2. Om du vill rekommendera objekt för en viss lista med användare ställer du in rekommendations **förutsägelse typ** till **artikel rekommendation**.

3. **Rekommenderat objekt val**: Ange om du använder bedömnings modulen i produktion eller för modell utvärdering genom att välja något av följande värden:

    - **Från beräknade objekt (för modell utvärdering)**: Välj det här alternativet om du utvecklar eller testar en modell. Med det här alternativet aktive ras **utvärderings läge** och modulen gör rekommendationer enbart från de objekt i data uppsättningen för indata som har klassificerats.
    - **Från alla objekt**: Välj det här alternativet om du vill konfigurera ett experiment som ska användas i en webb tjänst eller produktion.  Det här alternativet aktiverar **produktions läge** och modulen gör rekommendationer från alla objekt som ses under utbildningen.
    - **Från oklassificerade objekt (för att föreslå nya objekt till användare)**: Välj det här alternativet om du vill att modulen bara ska göra rekommendationer från de objekt i den utbildnings data uppsättning som inte har klassificerats. 
4. Lägg till den data uppsättning som du vill göra förutsägelser för och Anslut den till **data uppsättningen för att få poäng**.

    - Om du väljer alternativet, **från alla objekt**, ska indata-datauppsättningen bestå av en och bara en kolumn som innehåller de identifierare med användare för vilka rekommendationer ska göras.

        Data uppsättningen kan innehålla ytterligare två kolumner med objekt identifierare och klassificeringar, men dessa två kolumner ignoreras. 

    - Om du väljer alternativet **från betygsatta objekt (för modell utvärdering)** ska indata-datauppsättningen bestå av **användar objekts par**. Den första kolumnen ska innehålla **användar** -ID. Den andra kolumnen ska innehålla motsvarande **objekt** identifierare.

        Data uppsättningen kan innehålla en tredje kolumn med klassificeringar av användar objekt, men den här kolumnen ignoreras.
        
    - För **från oklassificerade objekt (för att föreslå nya objekt till användare)** ska indata-datauppsättningen bestå av användar objekts par. Den första kolumnen ska innehålla användar-ID. Den andra kolumnen ska innehålla motsvarande objekt identifierare.

        Data uppsättningen kan innehålla en tredje kolumn med klassificeringar av användar objekt, men den här kolumnen ignoreras.

5. (Valfritt). Om du har en data uppsättning med **användar funktioner** ansluter du den till **användar funktioner**.

    Den första kolumnen i data uppsättningen för användar funktioner ska innehålla användar-ID. De återstående kolumnerna måste innehålla värden som karaktäriserar användaren, t. ex. kön, inställningar, plats osv.

    Funktioner för användare som har betygsatta objekt ignoreras av **poängen bred och djup rekommenderar**, eftersom dessa funktioner redan har lärts under utbildningen. Därför kan du filtrera din data uppsättning i förväg och bara ta med *användare av kall start*, eller användare som inte har klassificerat några objekt.

    > [!WARNING]
    >  Om modellen har tränats utan att använda användar funktioner kan du inte använda tillämpa funktioner under poängsättningen.

6. Valfritt Om du har en data uppsättning med **objekt funktioner** kan du ansluta den till **objekt funktioner**.

    Den första kolumnen i data uppsättningen för objekt funktioner måste innehålla objekt-ID. De återstående kolumnerna ska innehålla värden som karaktäriserar objekten.

    Funktioner i betygsatta objekt ignoreras av **poängen bred och djup rekommenderar**, eftersom dessa funktioner redan har lärts under utbildningen. Därför kan du begränsa din bedömnings data uppsättning till *kalla start objekt* eller objekt som inte har klassificerats av några användare.

    > [!WARNING]
    >  Om modellen har tränats utan att använda objekt funktioner ska du inte använda objekt funktioner när du anger poäng.  

7. **Maximalt antal objekt att rekommendera till en användare**: Ange antalet objekt som ska returneras för varje användare. Som standard rekommenderas 5 objekt.

8. **Minsta storlek på den rekommenderade poolen per användare**: Ange ett värde som anger hur många tidigare rekommendationer som krävs.  Som standard är den här parametern angiven till 2, vilket innebär att objektet måste ha rekommenderats av minst två andra användare.

    Det här alternativet bör endast användas om du bedömer i utvärderings läge. Alternativet är inte tillgängligt om du väljer **från alla objekt** eller **från oklassificerade objekt (för att föreslå nya objekt till användare)**.

9. För **från oklassificerade objekt (för att föreslå nya objekt till användare)** använder du den tredje Indataporten, med namnet **tränings data**, för att ta bort objekt som redan har klassificerats från förutsägelse resultatet.

    Om du vill använda det här filtret ansluter du den ursprungliga träning-datauppsättningen till Indataporten.

10. Kör experimentet.
### <a name="results-of-item-recommendation"></a>Resultat av objekt rekommendation

Den returnerade data uppsättningen som returneras av **poängen bred och djup rekommendation** listar de rekommenderade objekten för varje användare.

- Den första kolumnen innehåller användar-ID: n.
- Ett antal ytterligare kolumner genereras, beroende på det värde som du anger för **maximalt antal objekt för att rekommendera en användare**. Varje kolumn innehåller ett rekommenderat objekt (efter identifierare). Rekommendationerna ordnas efter mappning mellan användare och objekt, med objektet med högsta tillhörighet i kolumnen **objekt 1**.

##  <a name="technical-notes"></a>Tekniska anteckningar

Det här avsnittet innehåller svar på några vanliga frågor om hur du använder den breda & djup rekommendationen för att skapa förutsägelser.  

###  <a name="cold-start-users-and-recommendations"></a>Användare och rekommendationer från kall start

För att skapa rekommendationer kräver till exempel modulen **Scores och djup rekommenderar** samma indata som du använde när du tränar modellen, inklusive ett användar-ID. Det beror på att algoritmen måste känna till om den har lärt sig något om den här användaren under träningen. 

För nya användare kanske du inte har ett användar-ID, endast vissa användar funktioner som ålder, kön och så vidare.

Du kan fortfarande skapa rekommendationer för användare som är nya i systemet, genom att hantera dem som *kall start användare*. För sådana användare använder inte rekommendations algoritmen tidigare historik eller tidigare klassificering, bara användar funktioner.

Vid förutsägelse definieras en kall start användare som en användare med ett ID som inte har använts för utbildning. För att säkerställa att ID: n inte matchar de ID: n som används i utbildningen kan du skapa nya identifierare. Du kan till exempel generera slumpmässiga ID: n inom ett visst intervall eller allokera en serie med ID: n i förväg för användare av kall start.

Men om du inte har några samarbetes filtrerings data, till exempel en Vector med användar funktioner, är det bättre att använda en klassificerings-eller Regressions information.

###  <a name="production-use-of-the-wide-and-deep-recommender"></a>Produktions användning av bred och djup rekommendation

Om du har experimentat med bred och djupgående rekommendation och sedan flyttar modellen till produktion bör du vara medveten om dessa viktiga skillnader när du använder rekommendationen i utvärderings läge och i produktions läge:

- Utvärdering, efter definition, kräver förutsägelser som kan kontrol leras mot *mark-sanningen* i en test uppsättning. När du utvärderar rekommendationen måste det därför bara förutsäga objekt som har klassificerats i test uppsättningen. Detta begränsar förmodligen de möjliga värden som förutsägs.

    Men när du operationalisera modellen ändrar du normalt förutsägelse läget för att göra rekommendationer baserat på alla möjliga objekt, för att få bästa förutsägelser. För många av dessa förutsägelser finns det ingen motsvarande mark-sanningen, så noggrannheten i rekommendationen kan inte verifieras på samma sätt som vid experimentering.

- Om du inte anger ett användar-ID i produktion och bara tillhandahåller en funktions vektor kan du få en lista över alla rekommendationer för alla möjliga användare. Se till att ange ett användar-ID.

    Om du vill begränsa antalet rekommendationer som returneras kan du också ange det maximala antalet objekt som returneras per användare. 



## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
