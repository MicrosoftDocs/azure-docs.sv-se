---
title: Algoritm- och modulreferens
description: Lär dig mer om de Azure Machine Learning designer-moduler som du kan använda för att skapa dina egna Machine Learning-projekt.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/09/2020
ms.openlocfilehash: 89ad9aae7c0d01971bbcfc7e392cb9d455ef85cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94376850"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Algoritmen & module-referens för Azure Machine Learning designer

Detta referens innehåll ger teknisk bakgrund på var och en av Machine Learning-algoritmer och moduler som är tillgängliga i Azure Machine Learning designer.

Varje modul representerar en uppsättning kod som kan köras separat och utföra en maskin inlärnings uppgift, med de nödvändiga inmatningarna. En modul kan innehålla en viss algoritm eller utföra en uppgift som är viktig i Machine Learning, till exempel värde utbyte som saknas eller statistisk analys.

Hjälp med att välja algoritmer finns i 
* [Så här väljer du algoritmer](../how-to-select-algorithms.md)
* [Lathund-blad för Azure Machine Learning algorithm](../algorithm-cheat-sheet.md)

> [!TIP]
> I en pipeline i designern kan du hämta information om en speciell modul. Välj länken **Läs mer** i modulen modul vid hovring i modulen i modulen modul eller i den högra rutan i modulen.

## <a name="data-preparation-modules"></a>Moduler för data förberedelse


| Funktioner | Beskrivning | Modul |
| --- |--- | --- |
| Data indata och utdata | Flytta data från moln källor till din pipeline. Skriv resultaten eller mellanliggande data till Azure Storage, SQL Database eller Hive, medan du kör en pipeline eller Använd moln lagring för att utbyta data mellan pipeliner.  | [Ange data manuellt](enter-data-manually.md) <br/> [Exportera data](export-data.md) <br/> [Importera data](import-data.md) |
| Data Transformation | Åtgärder för data som är unika för maskin inlärning, till exempel normaliserande eller diskretisering data, Dimensional reducering och konvertering av data mellan olika fil format.| [Lägg till kolumner](add-columns.md) <br/> [Lägg till rader](add-rows.md) <br/> [Använda matematisk åtgärd](apply-math-operation.md) <br/> [Använda SQL-transformering](apply-sql-transformation.md) <br/> [Rensa data som saknas](clean-missing-data.md) <br/> [Beskärningsvärden](clip-values.md) <br/> [Konvertera till CSV](convert-to-csv.md) <br/> [Konvertera till datamängd](convert-to-dataset.md) <br/> [Konvertera till indikatorvärden](convert-to-indicator-values.md) <br/> [Redigera metadata](edit-metadata.md) <br/> [Gruppera data till lagerplatser](group-data-into-bins.md) <br/> [Koppla data](join-data.md) <br/> [Normalisera Data](normalize-data.md) <br/> [Partitionera och prova](partition-and-sample.md)  <br/> [Ta bort dubblettrader](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Välja kolumntranformering](select-columns-transform.md) <br/> [Välja kolumner i datauppsättning](select-columns-in-dataset.md) <br/> [Dela data](split-data.md) |
| Val av funktion | Välj en delmängd av relevanta, användbara funktioner som du kan använda när du skapar en analys modell. | [Filterbaserat funktionsval](filter-based-feature-selection.md) <br/> [Permutationfunktionsprioritet](permutation-feature-importance.md) |
| Statistiska funktioner | Tillhandahålla en mängd olika statistiska metoder för data vetenskap. | [Sammanfatta data](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Maskininlärningsalgoritmer

| Funktioner | Beskrivning | Modul |
| --- |--- | --- |
| Regression | Förutsäg ett värde. | [Förbättrad regression för beslutsträd](boosted-decision-tree-regression.md) <br/> [Regression för beslutsskog](decision-forest-regression.md) <br/> [Fast Forest Quantile Regression](fast-forest-quantile-regression.md)  <br/> [Linjär regression](linear-regression.md)  <br/> [Regression för Neural Network](neural-network-regression.md)  <br/> [Poisson-regression](poisson-regression.md)  <br/>|
| Klustring | Gruppera data tillsammans.| [K-means-klustring](k-means-clustering.md)
| Klassificering | Förutsäg en klass.  Välj mellan binära (två klasser) eller algoritmer för multiklasser.| [Förbättrat beslutsträd med flera klasser](multiclass-boosted-decision-tree.md) <br/> [Beslutsskog med två klasser](multiclass-decision-forest.md) <br/> [Logistic Regression med flera klasser](multiclass-logistic-regression.md)  <br/> [Neural Network med flera klasser](multiclass-neural-network.md) <br/> [En-mot- alla-multiklass](one-vs-all-multiclass.md) <br/> [En-mot- En multiklass](one-vs-one-multiclass.md) <br/>[Genomsnittsperceptron med två klasser](two-class-averaged-perceptron.md) <br/>  [Tvåklassigt förbättrat beslutsträd](two-class-boosted-decision-tree.md)  <br/> [Beslutsskog med två klasser](two-class-decision-forest.md) <br/>  [Logistic Regression med två klasser](two-class-logistic-regression.md) <br/> [Neural Network med två klasser](two-class-neural-network.md) <br/> [Tvåklassig dator för vektorstöd](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduler för att skapa och utvärdera modeller

| Funktioner | Beskrivning | Modul |
| --- |--- | --- |
| Modell träning | Kör data via algoritmen. |  [Träna klustringsmodellen](train-clustering-model.md) <br/> [Träningsmodell](train-model.md) <br/> [Träna Pytorch-modell](train-pytorch-model.md) <br/> [Finjustera hyperparametrar för modell](tune-model-hyperparameters.md) |
| Bedömnings-och utvärderings modell | Mät noggrannheten för den tränade modellen. | [Använda transformering](apply-transformation.md) <br/> [Tilldela data till kluster](assign-data-to-clusters.md) <br/> [Korsvalidera modell](cross-validate-model.md) <br/> [Utvärdera modell](evaluate-model.md) <br/> [Bildpoängmodell](score-image-model.md) <br/> [Poängmodell](score-model.md) |
| Python-språk | Skriv kod och bädda in den i en modul för att integrera python med din pipeline. | [Skapa Python-modell](create-python-model.md) <br/> [Köra Python-skript](execute-python-script.md) |
| R-språk | Skriv kod och bädda in den i en modul för att integrera R med din pipeline. | [Köra R-skript](execute-r-script.md) |
| Textanalys | Tillhandahåll specialiserade beräknings verktyg för att arbeta med både strukturerad och ostrukturerad text. |  [Konvertera Word till vektor](convert-word-to-vector.md) <br/> [Extrahera N-Gram-funktioner från text](extract-n-gram-features-from-text.md) <br/> [Funktions-hash](feature-hashing.md) <br/> [Förbearbeta text](preprocess-text.md) <br/> [Latent Dirichlet-allokering](latent-dirichlet-allocation.md) <br/> [Bedöma Vowpal Wabbit-modell](score-vowpal-wabbit-model.md) <br/> [Träna Vowpal Wabbit-modell](train-vowpal-wabbit-model.md)|
| Visuellt innehåll | Moduler för för bearbetning av bild data och bild igenkänning. |  [Använda bildtransformering](apply-image-transformation.md) <br/> [Konvertera till bildkatalog](convert-to-image-directory.md) <br/> [Initiera bildtransformering](init-image-transformation.md) <br/> [Dela bildkatalog](split-image-directory.md) <br/> [DenseNet](densenet.md) <br/> [ResNet](resnet.md) |
| Rekommendation | Bygg rekommendationers modeller. | [Utvärdera rekommenderare](evaluate-recommender.md) <br/> [Poäng för SVD-rekommenderare](score-svd-recommender.md) <br/> [Bedöma modulen Wide and Deep Recommender](score-wide-and-deep-recommender.md)<br/> [Träna SVD-rekommenderare](train-SVD-recommender.md) <br/> [Träna modulen Wide and Deep Recommender](train-wide-and-deep-recommender.md)|
| Avvikelseidentifiering | Bygg modeller för avvikelse identifiering. | [PCA-baserad avvikelseidentifiering](pca-based-anomaly-detection.md) <br/> [Träna avvikelseidentifieringsmodell](train-anomaly-detection-model.md) |


## <a name="web-service"></a>Webbtjänst

Lär dig mer om [webbtjänst-modulerna](web-service-input-output.md) som krävs för real tids härledning i Azure Machine Learning designer.

## <a name="error-messages"></a>Felmeddelanden

Lär dig mer om [fel meddelanden och undantags koder](designer-error-codes.md) som du kan stöta på med moduler i Azure Machine Learning designer.

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Bygg en modell i designern för att förutsäga automatiska priser](../tutorial-designer-automobile-price-train-score.md)
