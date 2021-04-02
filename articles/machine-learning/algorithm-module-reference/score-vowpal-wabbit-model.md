---
title: Bedöma Vowpal Wabbit-modell
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen score Vowpal Wabbit Model för att generera poäng för en uppsättning indata med hjälp av en befintlig utbildad Vowpal Wabbit-modell.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 23dfee7b78f2606c54525391e1260af69a9b0779
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898403"
---
# <a name="score-vowpal-wabbit-model"></a>Bedöma Vowpal Wabbit-modell
Den här artikeln beskriver hur du använder modulen **score Vowpal Wabbit modell** i Azure Machine Learning designer för att generera poäng för en uppsättning indata med hjälp av en befintlig utbildad Vowpal Wabbit-modell.  

Den här modulen innehåller den senaste versionen av Vowpal Wabbit Framework, version 8.8.1. Använd den här modulen för att räkna data med hjälp av en tränad modell som sparas i VW version 8-format.  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>Så här konfigurerar du score Vowpal Wabbit-modellen

1.  Lägg till **score Vowpal Wabbit modell** -modulen i experimentet.  
  
2.  Lägg till en utbildad Vowpal Wabbit-modell och Anslut den till den vänstra porten för indataport. Du kan använda en utbildad modell som skapats i samma experiment eller leta upp en sparad modell i kategorin **data uppsättningar** i designerns vänstra navigerings fönster. Modellen måste dock vara tillgänglig i Azure Machine Learning designer.  
  
    > [!NOTE]
    > Endast Vowpal Wabbit 8.8.1-modeller stöds. Du kan inte ansluta sparade modeller som har tränats med andra algoritmer.
  
3.  Lägg till test data uppsättningen och Anslut den till den högra porten för indata. Om test data uppsättning är en katalog som innehåller test data filen anger du namnet på test data filen med **namnet på test data filen**. Om test data uppsättningen är en enskild fil lämnar du **namnet på test data filen** tomt.

4. I text rutan **VW-argument** anger du en uppsättning giltiga kommando rads argument till den körbara filen Vowpal Wabbit.  

    Information om vilka Vowpal Wabbit-argument som stöds och som inte stöds i Azure Machine Learning finns i avsnittet [tekniska anteckningar](#technical-notes) .  

5.  **Namn på test data filen**: Skriv namnet på filen som innehåller indata. Det här argumentet används bara när test data uppsättningen är en katalog.

6. **Ange filtyp**: Ange vilket format dina utbildnings data använder. Vowpal Wabbit stöder dessa två indatafiler:  

   - **VW** representerar det interna formatet som används av Vowpal-Wabbit. Mer information finns på [Vowpal Wabbit wiki-sidan](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
   - **SVMLight** är ett format som används av vissa andra Machine Learning-verktyg. 

7. Välj alternativet, **Lägg till en extra kolumn som innehåller etiketter**, om du vill skriva ut etiketter tillsammans med poängen.  

   Vid hantering av text data kräver Vowpal Wabbit inte etiketter, och returnerar bara poängen för varje datarad.  

8. Välj alternativet, **Lägg till en extra kolumn som innehåller rå Scores** om du vill skriva ut rå data tillsammans med resultaten.  

9. Skicka pipelinen.

## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill visualisera resultaten högerklickar du på utdata från modulen [score Vowpal Wabbit modell](score-vowpal-wabbit-model.md) . Utdata anger att ett förutsägelse resultat normaliseras från 0 till 1. 

+ Om du vill utvärdera resultaten ska den utgående data uppsättningen innehålla särskilda namn på resultat kolumnen som uppfyller kraven för utvärdera modell modul.

  + För Regressions aktivitet måste data uppsättningen som ska utvärderas ha en kolumn med namnet `Regression Scored Labels` , som representerar Poäng etiketter.
  + För att den binära klassificerings aktiviteten ska kunna utvärdera måste den data uppsättning som ska utvärderas ha två kolumner, med namnet `Binary Class Scored Labels` , `Binary Class Scored Probabilities` som representerar beräknade etiketter respektive sannolikheter.
  + För att flera klassificerings uppgifter ska kunna utvärderas måste den data uppsättning som ska utvärderas ha en kolumn med namnet `Multi Class Scored Labels` , som representerar Poäng etiketter.

  Observera att resultaten av Vowpal Wabbit Model-modulen inte kan utvärderas direkt. Innan du bedömer bör data uppsättningen ändras enligt kraven ovan.

##  <a name="technical-notes"></a>Tekniska anteckningar

Det här avsnittet innehåller implementerings information, tips och svar på vanliga frågor.

### <a name="parameters"></a>Parametrar

Vowpal Wabbit har många kommando rads alternativ för att välja och justera algoritmer. Det går inte att utföra en fullständig diskussion av de här alternativen här. Vi rekommenderar att du visar [wiki-sidan för Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).  

Följande parametrar stöds inte i Azure Machine Learning Studio (klassisk).  

-   De indata/utdata-alternativ som anges i [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     De här egenskaperna har redan kon figurer ATS automatiskt av modulen.  
  
-   Dessutom tillåts alla alternativ som genererar flera utmatningar eller tar flera indata. Dessa inkluderar *`--cbt`* , *`--lda`* och *`--wap`* .  
  
-   Endast övervakade Learning-algoritmer stöds. Detta tillåter inte följande alternativ: *`–active`* , `--rank` *`--search`* osv.  

Alla argument förutom de som beskrivs ovan är tillåtna.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 