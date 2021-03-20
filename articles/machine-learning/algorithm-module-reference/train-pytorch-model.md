---
title: Träna Pytorch-modell
titleSuffix: Azure Machine Learning
description: Använd modulen träna Pytorch models i Azure Machine Learning designer för att träna modeller från grunden eller finjustera befintliga modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2d88069f33995bdbe9dd479afe9a4e72ab9939b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420673"
---
# <a name="train-pytorch-model"></a>Träna Pytorch-modell

Den här artikeln beskriver hur du använder modulen **träna Pytorch-modell** i Azure Machine Learning designer för att träna Pytorch modeller som DenseNet. Träningen sker efter att du har definierat en modell och angett dess parametrar och kräver etiketterade data. 

## <a name="how-to-use-train-pytorch-model"></a>Använda träna Pytorch-modellen 

1. Lägg till [DenseNet](densenet.md) -modul eller [ResNet](resnet.md) i ditt pipeline-utkast i designern.

2. Lägg till modulen **träna Pytorch modell** i pipelinen. Du hittar den här modulen under kategorin **modell inlärning** . Expandera **träna** och dra modulen **träna Pytorch modell** till din pipeline.

   > [!NOTE]
   > **Träna Pytorch modell** module körs bättre på **GPU** -typ Compute för stor data uppsättning, annars Miss förloppet. Du kan välja Compute för en speciell modul i den högra rutan i modulen genom att ställa in **Använd annat beräknings mål**.

3.  Koppla en modell som inte är tränad till vänster. Koppla data uppsättningen för träning och validering till den mittersta och den högra indatan för **träna Pytorch-modellen**.

    För en modell som inte är tränad måste den vara en pytorch modell som DenseNet; annars kommer en ' InvalidModelDirectoryError ' att genereras.

    Träning-datauppsättningen måste vara en etikettad avbildnings katalog för data uppsättningen. Se **konvertera till avbildnings katalog** för att få en etikettad avbildnings katalog. Om den inte är märkt kommer en ' NotLabeledDatasetError ' att genereras.

    Data uppsättningen för träning och verifiering har samma etikett kategorier, annars kommer en InvalidDatasetError att genereras.

4.  För **epoker** anger du hur många epoker du vill träna. Hela data uppsättningen kommer att upprepas i varje epok, som standard 5.

5.  För **batchstorlek** anger du hur många instanser som ska tränas i en batch, som standard 16.

6.  För **inlärnings frekvens** anger du ett värde för *inlärnings takten*. Värdena för inlärning styr storleken på det steg som används i optimering som SGD varje gång modellen testas och korrigeras.

    Genom att göra hastigheten mindre testar du modellen oftare, med den risk att du kan fastna i en lokal platå. Genom att göra steget större kan du konvergera snabbare, vid risken för att den sanna minimi överskrids. som standard är 0,001.

7.  För **slumpmässigt utsäde** kan du ange ett heltals värde som ska användas som dirigering. Du rekommenderas att använda ett start värde om du vill säkerställa reproducerbarheten för experimentet i flera körningar.

8.  För **tålamod** anger du hur många epoker som tidigt upphör att fungera om verifierings förlusten inte minskar i följd. som standard är 3.

9.  Skicka pipelinen. Om din data uppsättning har större storlek, tar det en stund och en GPU-beräkning rekommenderas.

## <a name="results"></a>Resultat

När pipeline-körningen har slutförts ska du använda modellen för att skapa en modell för [Pytorch-modellen](train-pytorch-model.md) för att skapa en [bild modell](score-image-model.md)för att förutsäga värden för nya exempel på indata.

## <a name="technical-notes"></a>Tekniska anteckningar
###  <a name="expected-inputs"></a>Förväntade indata  

| Namn               | Typ                    | Beskrivning                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| Modell som inte är tränad    | UntrainedModelDirectory | Modell som inte är tränad, kräver pytorch         |
| Data uppsättning för utbildning   | ImageDirect          | Data uppsättning för utbildning                         |
| Verifierings data uppsättning | ImageDirect          | Validerings data uppsättning för utvärdering varje epok |

###  <a name="module-parameters"></a>Parametrar för modul  

| Name          | Intervall            | Typ    | Standardvärde | Beskrivning                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Epoker        | > 0               | Integer | 5       | Markera den kolumn som innehåller etiketten eller resultat kolumnen |
| Batchstorlek    | > 0               | Integer | 16      | Hur många instanser som ska tränas i en batch   |
| Inlärningstakt | >= Double. Epsilon | Float   | 0,001   | Den inledande inlärnings takten för Stochastic gradient brantaste-optimering. |
| Slumpmässigt utsäde   | Valfri              | Integer | 1       | Startvärdet för den slump tals generator som används av modellen. |
| Tålamod      | > 0               | Integer | 3       | Hur många epoker till tidig stängning av utbildning   |

###  <a name="outputs"></a>Utdata  

| Namn          | Typ           | Beskrivning   |
| ------------- | -------------- | ------------- |
| Tränad modell | ModelDirectory | Tränad modell |

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 



