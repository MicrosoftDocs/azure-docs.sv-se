---
title: 'Ange data manuellt: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen ange data manuellt i Azure Machine Learning för att skapa en liten data uppsättning genom att ange värden. Data uppsättningen kan ha flera kolumner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3bff9a91f06649487560faef3ab554b2a3d56af3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90908080"
---
# <a name="enter-data-manually-module"></a>Ange modulen data manuellt

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd modulen **ange data manuellt** för att skapa en liten data uppsättning genom att ange värden. Data uppsättningen kan ha flera kolumner.
  
Den här modulen kan vara till hjälp i scenarier som:  
  
- Genererar en liten uppsättning värden för testning.  
- Skapa en kort lista med etiketter.  
- Ange en lista med kolumn namn som ska infogas i en data uppsättning.

## <a name="create-a-dataset"></a>Skapa en datauppsättning 
  
1. Lägg till modulen [ange data manuellt](./enter-data-manually.md) i din pipeline. Du hittar den här modulen i kategorin **data indata och utdata** i Azure Machine Learning. 
  
1. Välj något av följande alternativ för **dataformat**. De här alternativen avgör hur de data som du anger ska tolkas. Kraven för varje format skiljer sig kraftigt, så se till att läsa närliggande ämnen.  
  
   - **Arff**: attribut-Relations fil format som används av wekas.   
   - **CSV**: format med kommaavgränsade värden. Mer information finns i [konvertera till CSV](./convert-to-csv.md).    
   - **SVMLight**: format som används av Vowpal Wabbit och andra ramverk för maskin inlärning.    
   - **TSV**: format med Tabbavgränsade värden.

   Om du väljer ett format och inte tillhandahåller data som uppfyller format kraven uppstår ett körnings fel.
  
1. Klicka i text rutan **data** för att börja ange data. Följande format kräver särskild uppmärksamhet:  
  
   - **CSV**: om du vill skapa flera kolumner, klistra in i kommaavgränsad text eller skriva flera kolumner med kommatecken mellan fälten.
  
     Om du väljer alternativet **HasHeader** kan du använda den första raden med värden som kolumn rubrik.  
  
     Om du avmarkerar det här alternativet används kolumn namnen (Col1, Col2 och så vidare). Du kan lägga till eller ändra kolumn namn senare med hjälp av [Redigera metadata](./edit-metadata.md).  
  
   - **TSV**: om du vill skapa flera kolumner klistrar du in tabbavgränsad text eller skriver flera kolumner genom att använda flikar mellan fält.  
  
     Om du väljer alternativet **HasHeader** kan du använda den första raden med värden som kolumn rubrik.  
  
     Om du avmarkerar det här alternativet används kolumn namnen (Col1, Col2 och så vidare). Du kan lägga till eller ändra kolumn namn senare med hjälp av [Redigera metadata](./edit-metadata.md).  
  
   - **Arff**: klistra in i en befintlig arff-format fil. Om du skriver värden direkt ska du se till att lägga till de valfria rubrik fälten och de obligatoriska attributen i början av data. 

     Till exempel kan följande rader för sidhuvud och attribut läggas till i en enkel lista. Kolumn rubriken är `SampleText` . Observera att sträng typen inte stöds.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: Skriv eller klistra in värden med SVMLight-formatet.  
  
     Följande exempel representerar till exempel de första raderna i data uppsättningen blod donation i SVMLight-format:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     När du kör modulen [ange data manuellt](./enter-data-manually.md) , konverteras dessa rader till en data uppsättning kolumner och index värden enligt följande:  
  
     |Col1|Col2|Col3|Col4|Etiketter|  
     |-|-|-|-|-|  
     |0,00016|0,004|0,999961|0,00784|1|  
     |0|0,004|0,999955|0,008615|1|  
  
1. Starta en ny rad genom att välja nyckeln RETUR efter varje rad.      
     
   Om du väljer Ange flera gånger för att lägga till flera tomma efterföljande rader tas de tomma raderna bort eller rensas.  
  
   Om du skapar rader med värden som saknas kan du alltid filtrera dem senare.  
  
1. Anslut utgående port till andra moduler och kör pipelinen.  
  
   Om du vill visa data uppsättningen högerklickar du på modulen och väljer **visualisera**.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 