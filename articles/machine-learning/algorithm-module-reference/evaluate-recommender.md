---
title: 'Utvärdera rekommendation: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen utvärdera Rekommenderadere i Azure Machine Learning för att utvärdera noggrannheten hos rekommendationer för rekommenderade modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 0890e13acbba8dae31de28d7c78a81bd9b516853
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76312268"
---
# <a name="evaluate-recommender"></a>Utvärdera rekommenderare

I den här artikeln beskrivs hur du använder modulen utvärdera rekommenderar i Azure Machine Learning designer. Målet är att mäta noggrannheten hos förutsägelser som en rekommendations modell har gjort. Genom att använda den här modulen kan du utvärdera olika typer av rekommendationer:  
  
-   Omdömen som förutsägs för en användare och ett objekt    
-   Objekt som rekommenderas för en användare  
  
När du skapar förutsägelser med hjälp av en rekommendations modell returneras något annorlunda resultat för var och en av de förutsägelse typer som stöds. Utvärderingen av bedömnings rekommendationer härleder typen av förutsägelse från kolumn formatet för den returnerade data uppsättningen. Till exempel kan den resulterande data uppsättningen innehålla:

- Användar objekt – betygs ätter tredubbla
- Användare och deras rekommenderade objekt

Modulen tillämpar också lämpliga prestanda mått baserat på vilken typ av förutsägelse som görs. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Så här konfigurerar du utvärderings rekommendationen

I modulen utvärdera rekommenderar vi att du jämför utdata med en rekommendations modell med motsvarande "sanningen"-data. Till exempel ger den [SVD-rekommenderade](score-svd-recommender.md) -modulen Poäng som du kan analysera genom att använda utvärderings rekommendation.

### <a name="requirements"></a>Krav

Utvärderings rekommendation kräver följande data uppsättningar som indata. 
  
#### <a name="test-dataset"></a>Test data uppsättning

Test data uppsättningen innehåller data för "mark sanningen" i form av tredubbla användar objekt – omdöme.  

#### <a name="scored-dataset"></a>Resultat data mängd

Den Poäng data uppsättningen innehåller de förutsägelser som rekommendations modellen genererade.  
  
Kolumnerna i den här andra data uppsättningen beror på vilken typ av förutsägelse som du utförde under bedömnings processen. Till exempel kan den resulterande data uppsättningen innehålla något av följande:

- Användare, objekt och klassificeringar som användaren sannolikt skulle ge objektet
- En lista över användare och objekt som rekommenderas för dem 

### <a name="metrics"></a>Mått

Prestanda måtten för modellen genereras baserat på typen av Indatatyp. I följande avsnitt får du information.

## <a name="evaluate-predicted-ratings"></a>Utvärdera förväntade värderingar  

När du utvärderar förväntade betyg måste den returnerade data uppsättningen (den andra indatan för att utvärdera rekommendationen) innehålla tredubbla objekt för användar objekts klassificering som uppfyller följande krav:
  
-   Den första kolumnen i data uppsättningen innehåller användar-ID: n.    
-   Den andra kolumnen innehåller objekt identifierare.  
-   Den tredje kolumnen innehåller motsvarande användar objekts klassificeringar.  
  
> [!IMPORTANT] 
> För att utvärderingen ska lyckas måste kolumn namnen vara `User` , respektive `Item` `Rating` .  
  
Utvärdera rekommendationer jämför klassificeringarna i data uppsättningen "mark sanningen" med de förväntade klassificeringarna för den returnerade data uppsättningen. Sedan beräknar den medelvärdet av absolut fel (MAE) och RMSE (root mean error).



## <a name="evaluate-item-recommendations"></a>Utvärdera objekt rekommendationer

När du utvärderar objekt rekommendationer använder du en resultat uppsättning som innehåller de rekommenderade objekten för varje användare:
  
-   Den första kolumnen i data mängden måste innehålla användar-ID.    
-   Alla efterföljande kolumner ska innehålla motsvarande rekommenderade objekt identifierare, sorterade efter hur relevant ett objekt är för användaren. 

Innan du ansluter den här data uppsättningen rekommenderar vi att du sorterar data uppsättningen så att de mest relevanta objekten kommer först.  

> [!IMPORTANT] 
> För att utvärderings rekommendationen ska fungera måste kolumn namnen vara `User` , `Item 1` , `Item 2` `Item 3` och så vidare.  
  
Utvärdera rekommendationer beräknar den genomsnittliga normaliserade rabatten för den ackumulerade vinsten (NDCG) och returnerar den i data uppsättningen för utdata.  
  
Eftersom det är omöjligt att känna till det faktiska "mark sanningen" för rekommenderade objekt, använder utvärderings funktionen användar objekt klassificeringarna i test data uppsättningen som vinster vid beräkningen av NDCG. För att utvärdera måste du bara skapa rekommendationer för objekt med klassificeringen "sanningen" (i test data uppsättningen).  
  

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
