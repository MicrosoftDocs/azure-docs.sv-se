---
title: 'Poängen i SVD-rekommendation: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder den SVD-rekommenderade modulen i Azure Machine Learning till Poäng rekommendations förutsägelser för en data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/10/2020
ms.openlocfilehash: bf62fa995724b8e1fff757e89945cc39db3d9842
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90893707"
---
# <a name="score-svd-recommender"></a>Poäng för SVD-rekommenderare

Den här artikeln beskriver hur du använder modulen Scores i SVD-rekommendation i Azure Machine Learning designer. Använd den här modulen för att skapa förutsägelser med hjälp av en utbildad rekommendations modell som baseras på en algoritm för enkel värdes avsammansättning (SVD).

SVD-rekommendationen kan generera två olika typer av förutsägelser:

- [Förutsäg betyg för en specifik användare och objekt](#prediction-of-ratings)
- [Rekommendera objekt till en användare](#recommendations-for-users)

När du skapar den andra typen av förutsägelser kan du använda i något av följande lägen:

- **Produktions läget** tar hänsyn till alla användare eller objekt. Den används vanligt vis i en webb tjänst.

  Du kan skapa Poäng för nya användare, inte bara användare som ses under utbildningen. Mer information finns i [teknisk](#technical-notes)information. 

- **Utvärderings läget** fungerar på en reducerad uppsättning användare eller objekt som kan utvärderas. Den används vanligt vis under pipeline-åtgärder.

Mer information om den SVD-rekommenderade algoritmen finns i [factorization-tekniker för forsknings papper för rekommenderade system](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).

## <a name="how-to-configure-score-svd-recommender"></a>Konfigurera poängen i SVD-rekommenderat

Den här modulen stöder två typer av förutsägelser, var och en med olika krav. 

###  <a name="prediction-of-ratings"></a>Förutsägelse av klassificeringar

När du förutsäger klassificeringarna beräknar modellen hur en användare kommer att reagera på ett visst objekt, baserat på tränings data. Indata för poängsättning måste ge både en användare och objektet att kunna bedöma.

1. Lägg till en utbildad rekommendations modell i din pipeline och Anslut den till **tränad SVD-rekommendation**. Du måste skapa modellen med hjälp av modulen [träna SVD-rekommendation](train-SVD-recommender.md) .

2. För **rekommendations förutsägelse typ** väljer du **bedömnings förutsägelse**. Inga andra parametrar krävs.

3. Lägg till de data som du vill göra förutsägelser för och Anslut den till **data uppsättningen för att få poäng**.

   För att modellen ska förutse klassificeringar måste indata-datauppsättningen innehålla par av användar objekt.

   Data uppsättningen kan innehålla en valfri tredje kolumn med klassificeringar för paret User-Item i de första och andra kolumnerna. Men den tredje kolumnen kommer att ignoreras under förutsägelse.

4. Skicka pipelinen.

### <a name="results-for-rating-predictions"></a>Resultat för bedömnings förutsägelser 

Data uppsättningen för utdata innehåller tre kolumner: användare, objekt och den förväntade klassificeringen för varje indata-användare och objekt.

###  <a name="recommendations-for-users"></a>Rekommendationer för användare 

För att rekommendera objekt för användare, ange en lista med användare och objekt som inmatade. Från dessa data använder modellen sina kunskaper om befintliga objekt och användare för att skapa en lista med objekt som kan överklaga till varje användare. Du kan anpassa antalet rekommendationer som returneras. Och du kan ange ett tröskelvärde för antalet tidigare rekommendationer som krävs för att skapa en rekommendation.

1. Lägg till en utbildad rekommendations modell i din pipeline och Anslut den till **tränad SVD-rekommendation**.  Du måste skapa modellen med hjälp av modulen [träna SVD-rekommendation](train-svd-recommender.md) .

2. För att rekommendera objekt för en lista med användare anger du rekommendationer för **rekommendations förutsägelse** till **artikel rekommendation**.

3. Ange om du vill använda bedömnings modulen i produktion eller för modell utvärdering för **Val av rekommenderat objekt**. Välj något av följande värden:

    - **Från alla objekt**: Välj det här alternativet om du konfigurerar en pipeline som ska användas i en webb tjänst eller i produktion.  Det här alternativet aktiverar *produktions läge*. Modulen gör rekommendationer från alla objekt som ses under utbildningen.

    - **Från beräknade objekt (för modell utvärdering)**: Välj det här alternativet om du utvecklar eller testar en modell. Det här alternativet aktiverar *utvärderings läge*. Modulen gör rekommendationer enbart från de objekt i data uppsättningen för indata som har klassificerats.
    
    - **Från oklassificerade objekt (för att föreslå nya objekt till användare)**: Välj det här alternativet om du vill att modulen bara ska göra rekommendationer från de objekt i den utbildnings data uppsättning som inte har klassificerats. 

4. Lägg till den data uppsättning som du vill göra förutsägelser för och Anslut den till **data uppsättningen för att få poäng**.

    - För **från alla objekt** ska indata-datauppsättningen bestå av en kolumn. Det innehåller identifierarna för de användare som du vill ha rekommendationer för.

      Data uppsättningen kan innehålla ytterligare två kolumner med objekt identifierare och klassificeringar, men dessa två kolumner ignoreras. 

    - För **från beräknade objekt (för modell utvärdering)** ska indata-datauppsättningen bestå av användar objekts par. Den första kolumnen ska innehålla användar-ID. Den andra kolumnen ska innehålla motsvarande objekt identifierare.

      Data uppsättningen kan innehålla en tredje kolumn med klassificeringar av användar objekt, men den här kolumnen ignoreras.

    - För **från oklassificerade objekt (för att föreslå nya objekt till användare)** ska indata-datauppsättningen bestå av användar objekts par. Den första kolumnen ska innehålla användar-ID. Den andra kolumnen ska innehålla motsvarande objekt identifierare.

     Data uppsättningen kan innehålla en tredje kolumn med klassificeringar av användar objekt, men den här kolumnen ignoreras.

5. **Maximalt antal objekt att rekommendera till en användare**: Ange antalet objekt som ska returneras för varje användare. Som standard rekommenderar modulen fem objekt.

6. **Minsta storlek på den rekommenderade poolen per användare**: Ange ett värde som anger hur många tidigare rekommendationer som krävs. Som standard är den här parametern inställd på 2, vilket innebär att minst två andra användare har rekommenderat objektet.

   Använd bara det här alternativet om du är i bedömnings läge. Alternativet är inte tillgängligt om du väljer **från alla objekt** eller **från oklassificerade objekt (för att föreslå nya objekt till användare)**.

7.  För **från oklassificerade objekt (för att föreslå nya objekt till användare)** använder du den tredje Indataporten, med namnet **tränings data**, för att ta bort objekt som redan har klassificerats från förutsägelse resultatet.

    Om du vill använda det här filtret ansluter du den ursprungliga träning-datauppsättningen till Indataporten.

8. Skicka pipelinen.

### <a name="results-of-item-recommendation"></a>Resultat av objekt rekommendation

Den returnerade data uppsättningen som returneras av poängen SVD-rekommendation listar de rekommenderade objekten för varje användare:

- Den första kolumnen innehåller användar-ID: n.
- Ett antal ytterligare kolumner genereras, beroende på vilket värde som du har angett för **maximalt antal objekt som ska rekommenderas till en användare**. Varje kolumn innehåller ett rekommenderat objekt (efter identifierare). Rekommendationerna sorteras efter mappning mellan användare och objekt. Objektet med högsta tillhörighet placeras i kolumn **objekt 1**.


##  <a name="technical-notes"></a>Tekniska anteckningar

Om du har en pipeline med SVD-rekommendation och du flyttar modellen till produktion bör du vara medveten om att det finns viktiga skillnader mellan att använda rekommendationen i utvärderings läge och att använda den i produktions läge.

Utvärdering, efter definition, kräver förutsägelser som kan kontrol leras mot *mark-sanningen* i en test uppsättning. När du utvärderar rekommendationen får den bara förutsäga objekt som har klassificerats i test uppsättningen. Detta begränsar de möjliga värden som förutsägs.

När du operationalisera modellen ändrar du normalt förutsägelse läget för att göra rekommendationer baserat på alla möjliga objekt, för att få bästa förutsägelser. För många av dessa förutsägelser finns det ingen motsvarande mark-sanningen. Det går därför inte att verifiera att rekommendationen är korrekt på samma sätt som vid pipeline-åtgärder.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
