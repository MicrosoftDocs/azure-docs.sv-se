---
title: Använda modell modulen för Poäng avbildning
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen för Poäng avbildnings modell i Azure Machine Learning för att generera förutsägelser med hjälp av en utbildad avbildnings modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fe57a9e8ce9b14f7d1346d819965576770afef3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324882"
---
# <a name="score-image-model"></a>Bildpoängmodell

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att generera förutsägelser med hjälp av en utbildad avbildnings modell på indata från indata.

## <a name="how-to-configure-score-image-model"></a>Så här konfigurerar du en modell för Poäng avbildning

1. Lägg till **modell modulen för Poäng avbildning** i din pipeline.

2. Koppla en utbildad avbildnings modell och en data uppsättning som innehåller data för indata. 

    Data ska vara av typen ImageDirect. Mer information om hur du hämtar en avbildnings katalog hittar du i avsnittet [konvertera till en avbildnings katalog](convert-to-image-directory.md) . Schemat för data uppsättningen för indata ska också ofta matcha schemat för de data som används för att träna modellen.

3. Skicka pipelinen.

## <a name="results"></a>Resultat

När du har genererat en uppsättning med poäng med hjälp av en [bild modell](score-image-model.md)för att generera en uppsättning mått som används för att utvärdera modellens exakthet (prestanda), kan du ansluta den här modulen och den returnerade data uppsättningen för att [utvärdera modellen](evaluate-model.md). 

### <a name="publish-scores-as-a-web-service"></a>Publicera resultat som en webb tjänst

En vanlig användning av poängsättning är att returnera utdata som en del av en förutsägbar webb tjänst. Mer information finns i [den här självstudien](../tutorial-designer-automobile-price-deploy.md) om hur du distribuerar en slut punkt i real tid baserat på en pipeline i Azure Machine Learning designer.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.