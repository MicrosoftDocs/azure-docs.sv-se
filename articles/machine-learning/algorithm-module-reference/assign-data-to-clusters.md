---
title: 'Tilldela data till kluster: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen tilldela data till kluster i Azure Machine Learning till Poäng kluster modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: e618395a2a4a11b4afb311d612cf5d0e27503dc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898866"
---
# <a name="module-assign-data-to-clusters"></a>Modul: tilldela data till kluster

Den här artikeln beskriver hur du använder modulen *tilldela data till kluster* i Azure Machine Learning designer. Modulen genererar förutsägelser genom en kluster modell som har tränats med algoritmen *K-metod* .

Modulen tilldela data till kluster returnerar en data uppsättning som innehåller de sannolika tilldelningarna för varje ny data punkt. 

## <a name="how-to-use-assign-data-to-clusters"></a>Så här använder du tilldela data till kluster
  
1. Leta upp en tidigare tränad kluster modell i Azure Machine Learning designer. Du kan skapa och träna en kluster modell genom att använda någon av följande metoder:  
  
    - Konfigurera den N:te klustrade algoritmen med hjälp av modulen [K-betyder-kluster](k-means-clustering.md) och träna modellen med hjälp av en data uppsättning och modulen träna kluster modell (den här artikeln).  
  
    - Du kan också lägga till en befintlig tränad kluster modell från gruppen för **sparade modeller** i din arbets yta.

2. Koppla den tränade modellen till den vänstra Indataporten för att **tilldela data till kluster**.  

3. Bifoga en ny data uppsättning som indata. 

   I den här data uppsättningen är etiketter valfria. I allmänhet är klustring en oövervakad inlärnings metod. Du förväntas inte känna till kategorierna i förväg. Inmatade kolumner måste dock vara samma som de kolumner som användes i träna kluster modellen, eller ett fel uppstår.

    > [!TIP]
    > Om du vill minska antalet kolumner som skrivs till designern från kluster förutsägelserna använder du [Välj kolumner i data uppsättningen](select-columns-in-dataset.md)och väljer en delmängd av kolumnerna. 
    
4. Lämna kryss rutan **Markera för Lägg till eller ta bort kontroll för endast resultat** markerad om du vill att resultatet ska innehålla den fullständiga data uppsättningen, inklusive en kolumn som visar resultatet (kluster tilldelningar).
  
    Om du avmarkerar den här kryss rutan returneras bara resultatet. Det här alternativet kan vara användbart när du skapar förutsägelser som en del av en webb tjänst.
  
5.  Skicka pipelinen.  
  
### <a name="results"></a>Resultat

+  Om du vill visa värdena i data uppsättningen högerklickar du på modulen och väljer **visualisera**. Eller Välj modulen och växla till fliken **utdata** i den högra panelen, klicka på histogrammets ikon i **portens utdata** för att visualisera resultatet.

