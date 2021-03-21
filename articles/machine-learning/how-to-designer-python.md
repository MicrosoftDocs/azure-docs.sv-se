---
title: Kör Python-skript i designern
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder skript modellen EXECUTE python i Azure Machine Learning designer för att köra anpassade åtgärder som skrivits i python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.topic: conceptual
ms.custom: how-to, designer, devx-track-python
ms.openlocfilehash: dcc28d98efbc82079586de8cfbecd35effc93d6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94556241"
---
# <a name="run-python-code-in-azure-machine-learning-designer"></a>Kör python-kod i Azure Machine Learning designer

I den här artikeln får du lära dig hur du använder modulen [Kör Python-skript](algorithm-module-reference/execute-python-script.md) för att lägga till anpassad logik i Azure Machine Learning designer. I följande anvisningar, använder du Pandas-biblioteket för att utföra enkel funktions teknik.

Du kan använda den inbyggda kod redigeraren för att snabbt lägga till enkel python-logik. Om du vill lägga till mer komplex kod eller ladda upp ytterligare python-bibliotek bör du använda metoden zip-fil.

Standard miljön för körning använder Anacondas-distributionen av python. En fullständig lista över förinstallerade paket finns i referens sidan [Kör python-skriptfil](algorithm-module-reference/execute-python-script.md) .

![Köra python-indatamängds karta](media/how-to-designer-python/execute-python-map.png)

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="execute-python-written-in-the-designer"></a>Köra python som skrivits i designern

### <a name="add-the-execute-python-script-module"></a>Lägg till modulen kör Python-skript

1. Hitta modulen **Kör Python-skript** i designer-paletten. Den finns i avsnittet python- **språk** .

1. Dra och släpp modulen på pipeline-arbetsytan.

### <a name="connect-input-datasets"></a>Anslut indata-datauppsättningar

Den här artikeln använder exempel data uppsättningen, **bil pris data (RAW)**. 

1. Dra och släpp din data uppsättning till pipeline-arbetsytan.

1. Anslut data uppsättningens utgående port till den översta vänstra Indataporten för modulen **Kör Python-skript** . Designern visar inmatningen som en parameter för start punkt skriptet.
    
    Den högra Indataporten är reserverad för zippade python-bibliotek.

    ![Anslut data uppsättningar](media/how-to-designer-python/connect-dataset.png)
        

1. Anteckna vilken indataport du använder. Designern tilldelar den vänstra Indataporten till variabeln `dataset1` och den mittersta Indataporten till `dataset2` . 

Inmatnings moduler är valfria eftersom du kan generera eller importera data direkt i **execute Python-skript** .

### <a name="write-your-python-code"></a>Skriv din python-kod

Designern innehåller ett start punkt skript som du kan använda för att redigera och ange din egen python-kod. 

I det här exemplet använder du Pandas för att kombinera två kolumner som finns i den mobila data uppsättningen, **priset** och **hästen**, för att skapa en ny kolumn, **dollar per häst**. Den här kolumnen visar hur mycket du betalar för varje häst kraft, vilket kan vara en användbar funktion för att avgöra om en bil är ett bra erbjudande för pengarna. 

1. Välj modulen **Kör Python-skript** .

1. I fönstret som visas till höger om arbets ytan, väljer du text rutan **Python-skript** .

1. Kopiera och klistra in följande kod i text rutan.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    Din pipeline bör se ut på följande bild:
    
    ![Kör python-pipeline](media/how-to-designer-python/execute-python-pipeline.png)

    Start punkts skriptet måste innehålla funktionen `azureml_main` . Det finns två funktions parametrar som mappar till de två portarna för att **köra python** -modulen.

    Returvärdet måste vara ett Pandas-Dataframe. Du kan returnera upp till två dataframes som utdata i modulen.
    
1. Skicka pipelinen.

Nu har du en data uppsättning med den nya funktionen **kronor/HP**, som kan vara användbart i träna en bil rekommendation. Detta är ett exempel på funktions extrahering och en minskning av dimensionalitet. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [importerar dina egna data](how-to-designer-import-data.md) i Azure Machine Learning designer.
