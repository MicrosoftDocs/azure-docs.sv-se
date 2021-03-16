---
title: Vad är Azure Machine Learning Studio?
description: Studio är en webb Portal för Azure Machine Learning arbets ytor. Studio kombinerar inga kod-och kod-första upplevelser för en inklusiv data vetenskaps plattform.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
adobe-target: true
ms.openlocfilehash: 48c4b2a73628ab2105e23054d747e28acc105d01
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563200"
---
# <a name="what-is-azure-machine-learning-studio"></a>Vad är Azure Machine Learning Studio?

I den här artikeln får du lära dig mer om Azure Machine Learning Studio, webb portalen för data expert-utvecklare i [Azure Machine Learning](overview-what-is-azure-ml.md). Studio kombinerar inga kod-och kod-första upplevelser för en inklusiv data vetenskaps plattform.

I den här artikeln får du lära dig:
>[!div class="checklist"]
> - Så här [skapar du Machine Learning-projekt](#author-machine-learning-projects) i Studio.
> - [Hantera till gångar och resurser](#manage-assets-and-resources) i Studio.
> - Skillnaderna mellan [Azure Machine Learning Studio och ml Studio (klassisk)](#ml-studio-classic-vs-azure-machine-learning-studio).

Vi rekommenderar att du använder den senaste webbläsaren som är kompatibel med ditt operativsystem. Följande webbläsare stöds:
  * Microsoft Edge (den nya Microsoft Edge, senaste versionen. Inte Microsoft Edge Legacy)
  * Safari (senaste versionen, endast Mac)
  * Chrome (senaste versionen)
  * Firefox (senaste versionen)

## <a name="author-machine-learning-projects"></a>Redigera Machine Learning-projekt

Studio erbjuder flera redigerings upplevelser beroende på typ av projekt och användar upplevelse.

+ **Anteckningsböcker**

  Skriv och kör din egen kod i hanterade [Jupyter Notebook servrar](how-to-run-jupyter-notebooks.md) som är direkt integrerade i Studio. 

:::image type="content" source="media/overview-what-is-azure-ml-studio/notebooks.gif" alt-text="Skärm bild: skriva och köra kod i en bärbar dator":::

+ **Azure Machine Learning Designer**

  Använd designern för att träna och distribuera Machine Learning-modeller utan att skriva någon kod. Dra och släpp data uppsättningar och moduler för att skapa ML-pipeliner. Prova [själv studie kursen om designer](tutorial-designer-automobile-price-train-score.md).

    ![Exempel på Azure Machine Learning designer](media/concept-designer/designer-drag-and-drop.gif)

+ **Automatiserat gränssnitt för Machine Learning**

  Lär dig hur du skapar [automatiserade ml-experiment](tutorial-first-experiment-automated-ml.md) med ett lättanvänt gränssnitt. 

  [![Navigerings fönstret i Azure Machine Learning Studio](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **Data etiketter**

    Använd [Azure Machine Learning data etiketter](how-to-create-labeling-projects.md) för att effektivt koordinera data märknings projekt.

## <a name="manage-assets-and-resources"></a>Hantera till gångar och resurser

Hantera dina Machine Learning-tillgångar direkt i webbläsaren. Till gångar delas i samma arbets yta mellan SDK och Studio för en sömlös upplevelse. Använd Studio för att hantera:

- Modeller
- Datauppsättningar
- Datalager
- Beräknings resurser
- Notebooks
- Experiment
- Kör loggar
- Pipelines 
- Pipeline-slutpunkter

Även om du är en erfaren utvecklare kan Studio förenkla hanteringen av arbets ytans resurser.

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (klassisk) vs Azure Machine Learning Studio

**Ml Studio (klassisk)** släpptes i 2015 var vårt första drag-och-släpp Machine Learning Builder. 

**Ml Studio (klassisk)** är en fristående tjänst som bara erbjuder en visuell upplevelse. Studio (klassisk) fungerar inte med Azure Machine Learning.

**Azure Machine Learning** är en separat och modern tjänst som levererar en komplett data vetenskaps plattform. Det stöder både kod-till-och låg kod upplevelser.

**Azure Machine Learning Studio** är en webb portal *i* Azure Machine Learning som innehåller alternativ med låg kod och ingen kod för projekt redigering och till gångs hantering. 

Vi rekommenderar att nya användare väljer **Azure Machine Learning**, i stället för ml Studio (klassisk), för den senaste mängden data vetenskaps verktyg. Om du är en befintlig ML Studio-användare (klassisk) bör du överväga [att migrera till Azure Machine Learning](classic/migrate-overview.md).

Här är några av fördelarna med att växla till Azure Machine Learning:

- Skalbara beräknings kluster för storskalig utbildning.
- Företags säkerhet och styrning.
- Samverka med populära verktyg med öppen källkod.
- MLOps från slut punkt till slut punkt.

### <a name="feature-comparison"></a>Jämför funktioner

[!INCLUDE [aml-compare-classic](../../includes/machine-learning-compare-classic-aml.md)]

## <a name="troubleshooting"></a>Felsökning

* **Användar gränssnitts objekt saknas i Studio** Rollbaserad åtkomst kontroll i Azure kan användas för att begränsa åtgärder som du kan utföra med Azure Machine Learning. Dessa begränsningar kan förhindra att användar gränssnitts objekt visas i Azure Machine Learning Studio. Om du till exempel har tilldelats en roll som inte kan skapa en beräknings instans visas inte alternativet för att skapa en beräknings instans i Studio. Mer information finns i [Hantera användare och roller](how-to-assign-roles.md).

## <a name="next-steps"></a>Nästa steg

Besök [Studio](https://ml.azure.com)eller utforska de olika redigerings alternativen med de här självstudierna:  

- + [Kom igång i din egen utvecklings miljö](tutorial-1st-experiment-sdk-setup-local.md)
  + [Använd Jupyter notebooks på en beräknings instans för att träna & distribuera modeller](tutorial-1st-experiment-sdk-setup.md)
  + [Använd automatisk maskin inlärning för att träna & distribuera modeller](tutorial-first-experiment-automated-ml.md)  
  + [Använd designern för att träna & distribuera modeller](tutorial-designer-automobile-price-train-score.md)
  + [Använda Studio i ett säkert virtuellt nätverk](how-to-enable-studio-virtual-network.md)