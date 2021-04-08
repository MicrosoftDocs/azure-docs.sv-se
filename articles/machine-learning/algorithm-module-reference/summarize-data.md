---
title: Sammanfatta data
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen sammanfatta data i Azure Machine Learning för att skapa en grundläggande beskrivande statistik rapport för kolumnerna i en data uppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: 5206565b85d1551e5e551f1dfe75d28c93bc53f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898218"
---
# <a name="summarize-data"></a>Sammanfatta data

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd modulen sammanfatta data för att skapa en uppsättning standard statistiska mått som beskriver varje kolumn i inmatnings tabellen.

Sammanfattnings statistik är användbar när du vill förstå egenskaperna för hela data uppsättningen. Du kan till exempel behöva känna till:

- Hur många saknade värden finns det i varje kolumn?
- Hur många unika värden finns i en funktions kolumn?
- Vad är medelvärdet och standard avvikelsen för varje kolumn?

Modulen beräknar de viktiga poängen för varje kolumn och returnerar en rad med sammanfattnings statistik för varje variabel (data kolumn) som angetts som indata.

## <a name="how-to-configure-summarize-data"></a>Konfigurera sammanfattande data  

1. Lägg till modulen **sammanfatta data** i din pipeline. Du hittar den här modulen i kategorin **statistiska funktioner** i designern.

1. Anslut den data uppsättning som du vill skapa en rapport för.

    Om du bara vill rapportera om vissa kolumner använder du modulen [Välj kolumner i data uppsättning](select-columns-in-dataset.md) för att projicera en delmängd med kolumner att arbeta med.

1. Inga ytterligare parametrar krävs. Som standard analyserar modulen alla kolumner som tillhandahålls som indata, och beroende på vilken typ av värden som finns i kolumnerna, matas ut en relevant uppsättning statistik enligt beskrivningen i avsnittet [resultat](#results) .

1. Skicka pipelinen.

## <a name="results"></a>Resultat

Rapporten från modulen kan innehålla följande statistik. 

|Kolumnnamn|Description|
|------|------|  
|**Funktion**|Kolumnens namn|
|**Reparationer**|Antal rader|
|**Antal unika värden**|Antal unika värden i kolumnen|
|**Antal saknade värden**|Antal unika värden i kolumnen|
|**Minimum**|Lägsta värdet i kolumnen|  
|**Bekräftat**|Högsta värdet i kolumnen|
|**Anses**|Medelvärde för alla kolumn värden|
|**Genomsnittlig avvikelse**|Medelvärde för kolumn värden|
|**1: a kvartilen**|Värde vid första kvartilen|
|**Säte**|Kolumn värde för median|
|**tredje kvartilen**|Värde vid tredje kvartilen|
|**Läge**|Läge för kolumn värden|
|**Intervall**|Ett heltal som representerar antalet värden mellan högsta och lägsta värden|
|**Exempel avvikelse**|Varians för kolumn; Se Obs!|
|**Exempel på standard avvikelse**|Standard avvikelse för kolumn; Se Obs!|
|**Exempel skevning**|Skevning för kolumnen; Se Obs!|
|**Exempel-och-upplösning**|För kolumn; Se Obs!|
|**P 0,5**|0,5% percentil|
|**P1**|1% percentil|
|**P5**|5% percentil|
|**P95**|95% percentil|
|**P 99,5**|99,5% percentil |

## <a name="technical-notes"></a>Tekniska anteckningar

- För icke-numeriska kolumner beräknas endast värdena för antal, unika värden och antalet saknade värden. För annan statistik returneras ett null-värde.

- Kolumner som innehåller booleska värden bearbetas med följande regler:

    - Vid beräkningen av min används ett logiskt och används.
    
    - Vid beräkning av Max, ett logiskt eller används
    
    - Vid beräknings intervallet kontrollerar modulen först om antalet unika värden i kolumnen är lika med 2.
    
    - När du beräknar statistik som kräver flytt ALS beräkningar behandlas värdena true som 1,0 och värdet false behandlas som 0,0.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.  
