---
title: Konvertera till indikatorvärden
titleSuffix: Azure Machine Learning
description: Använd modulen konvertera till indikator värden i Azure Machine Learning designer för att konvertera kategoriska-kolumner till en serie med binära indikator kolumner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 81b3c113f46428327842c1555fdd1934e9ae8762
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420860"
---
# <a name="convert-to-indicator-values"></a>Konvertera till indikatorvärden
I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd modulen **konvertera till indikator värden** i Azure Machine Learning designer för att konvertera kolumner som innehåller kategoriska-värden till en serie binära indikator kolumner.  

I den här modulen visas också en definition av den omvandling som används för att konvertera till indikator värden. Du kan återanvända den här omvandlingen på andra data uppsättningar som har samma schema, med hjälp av modulen [Använd omvandling](apply-transformation.md) .

## <a name="how-to-configure-convert-to-indicator-values"></a>Så här konfigurerar du konvertera till indikator värden

1.  Hitta **värdena Convert to Indicator** och dra dem till ditt pipeline-utkast. Du hittar den här modulen under kategorin **data omvandling** .
    > [!NOTE]
    > Du kan använda modulen [Redigera metadata](edit-metadata.md) innan modulen **konvertera till Indiciator-värden** markerar mål kolumnerna som kategoriska.

1. Anslut modulen **konvertera till indikator värden** till den data uppsättning som innehåller de kolumner som du vill konvertera. 

1. Välj **Redigera kolumn** för att välja en eller flera kategoriska-kolumner.

1. Välj alternativet **Skriv över kategoriska-kolumner** om du **bara** vill mata ut de nya booleska kolumnerna. Som standard är det här alternativet inaktiverat.
    

    > [!TIP]
    >  Om du väljer alternativet att skriva över, tas inte käll kolumnen bort eller ändras. I stället genereras de nya kolumnerna och visas i data uppsättningen för utdata och käll kolumnen förblir tillgänglig i arbets ytan. Om du behöver se ursprungliga data kan du använda modulen [Lägg till kolumner](add-columns.md) när som helst för att lägga till käll kolumnen i igen.

1. Skicka pipelinen.

## <a name="results"></a>Resultat

Anta att du har en kolumn med poäng som anger om en server har hög, medel eller låg sannolikhet för ett haveri.  

| Server-ID | Felaktiga Poäng |
| --------- | ------------- |
| 10301     | Låg           |
| 10302     | Medel        |
| 10303     | Högt          |

När du använder **konvertera till indikator värden**, konverterar designern en enda kolumn med etiketter till flera kolumner som innehåller booleska värden:  

| Server-ID | Felpoäng – låg | Felpoäng – medels Tor | Felaktiga resultat – hög |
| --------- | ------------------- | ---------------------- | -------------------- |
| 10301     | 1                   | 0                      | 0                    |
| 10302     | 0                   | 1                      | 0                    |
| 10303     | 0                   | 0                      | 1                    |

Så här fungerar konverteringen:  

-   I kolumnen med **felaktiga resultat** som beskriver risker finns det bara tre möjliga värden (hög, medel och låg) och inga värden saknas. Därför skapas exakt tre nya kolumner.  

-   De nya indikator kolumnerna namnges baserat på kolumn rubrikerna och värdena i käll kolumnen med det här mönstret: *\<source column>- \<data value>* .  

-   Det ska finnas en 1 i exakt en indikator kolumn och 0 i alla andra indikator kolumner eftersom varje server bara kan ha en risk klassificering.  

Du kan nu använda de tre indikator kolumnerna som funktioner i en Machine Learning-modell.

Modulen returnerar två utdata:

- **Resultat data uppsättning**: en data uppsättning med konverterade indikator värden kolumner. Kolumner som inte har valts för rengöring är också "passerade".
- **Omvandling av indikator värden**: en datatransformering som används för att konvertera till indikator värden, som kan sparas i din arbets yta och tillämpas på nya data senare.

## <a name="apply-a-saved-indicator-values-operation-to-new-data"></a>Använd en sparad indikator värdes åtgärd för nya data

Om du behöver upprepa indikator värden ofta kan du spara dina data behandlings steg som en *transformering* för att återanvända den med samma data uppsättning. Detta är användbart om du ofta måste importera på nytt och sedan Rensa data som har samma schema.

1. Lägg till modulen [Använd omvandling](apply-transformation.md) i din pipeline.

1. Lägg till den data uppsättning som du vill rensa och Anslut data uppsättningen till den högra porten för indata.

1. Expandera **data omvandlings** gruppen i det vänstra fönstret i designern. Leta upp den sparade omvandlingen och dra den till pipelinen.

1. Anslut den sparade omvandlingen till den vänstra Indataporten för [Apply Transformation](apply-transformation.md).

   När du använder en sparad omvandling kan du inte välja vilka kolumner som ska transformeras. Detta beror på att transformationen har definierats och tillämpas automatiskt på de data typer som anges i den ursprungliga åtgärden.

1. Skicka pipelinen.
 
## <a name="technical-notes"></a>Tekniska anteckningar  

Det här avsnittet innehåller implementerings information, tips och svar på vanliga frågor.

### <a name="usage-tips"></a>Användnings tips

-   Endast kolumner som marker ATS som kategoriska kan konverteras till indikator kolumner. Om du ser följande fel, är det troligt att en av de markerade kolumnerna inte är kategoriska:  

     Fel 0056: kolumnen med namnet  \<column name> är inte i en tillåten kategori.  

     Som standard hanteras de flesta sträng kolumner som sträng funktioner, så du måste uttryckligen markera dem som kategoriska med [Redigera metadata](edit-metadata.md).  

-   Det finns ingen gräns för antalet kolumner som kan konverteras till indikator kolumner. Men eftersom varje kolumn med värden kan ge flera indikator kolumner kanske du vill konvertera och granska bara några kolumner i taget.  

-   Om kolumnen innehåller värden som saknas skapas en separat indikator kolumn för kategorin som saknas, med följande namn: *\<source column> -saknas*  

-   Om kolumnen som du konverterar till indikator värden innehåller siffror måste de markeras som kategoriska som andra funktions kolumner. När du har gjort det behandlas talen som diskreta värden. Om du till exempel har en numerisk kolumn med MPG-värden som sträcker sig från 25 till 30 skapas en ny indikator kolumn för varje diskret värde:  

    | Modell       | Motorväg MPG-25 | Motorväg MPG-26 | Motorväg MPG-27 | Motorväg MPG-28 | Motorväg MPG-29 | Motorväg MPG-30 |
    | ---------- | --------------- | --------------- | --------------- | --------------- | --------------- | --------------- |
    | Contoso bilar | 0               | 0               | 0               | 0               | 0               | 1               |

- För att undvika att lägga till för många dimensioner i data uppsättningen. Vi rekommenderar att du först kontrollerar antalet värden i kolumnen, och bin eller kvantifierar data på rätt sätt.  


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
