---
title: 'Rensa saknade data: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen rensa data som saknas i Azure Machine Learning för att ta bort, ersätta eller härleda saknade värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 5b7943b2026d640ae7e5d119e165bd752ae2fe7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898824"
---
# <a name="clean-missing-data-module"></a>Rensa data modul som saknas

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att ta bort, ersätta eller härleda saknade värden. 

Data forskare kontrollerar ofta data för saknade värden och utför sedan olika åtgärder för att åtgärda data eller infoga nya värden. Målet med sådana rengörings åtgärder är att förhindra problem som orsakas av saknade data som kan uppstå när du tränar en modell. 

Den här modulen stöder flera typer av åtgärder för "Rensa"-saknade värden, inklusive:

+ Ersätta saknade värden med en plats hållare, ett medelvärde eller något annat värde
+ Ta bort rader och kolumner som saknar värden fullständigt
+ Härleda värden baserat på statistiska metoder


Om du använder den här modulen ändras inte käll data uppsättningen. I stället skapas en ny data uppsättning i arbets ytan som du kan använda i det efterföljande arbets flödet. Du kan också spara den nya, rensade data uppsättningen för åter användning.

I den här modulen visas också en definition av den omvandling som används för att rensa de värden som saknas. Du kan använda den här omvandlingen igen på andra data uppsättningar som har samma schema, med hjälp av modulen [Använd omvandling](./apply-transformation.md) .  

## <a name="how-to-use-clean-missing-data"></a>Använda rensade data som saknas

Med den här modulen kan du definiera en rensnings åtgärd. Du kan också spara rensnings åtgärden så att du kan använda den senare på nya data. I följande avsnitt beskrivs hur du skapar och sparar en rensnings process: 
 
+ [Ersätta saknade värden](#replace-missing-values)
  
+ [Tillämpa en rensnings omvandling för nya data](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> Den rengörings metod som du använder för att hantera saknade värden kan dramatiskt påverka resultatet. Vi rekommenderar att du experimenterar med olika metoder. Överväg både motiveringen för användning av en viss metod och kvaliteten på resultaten.

### <a name="replace-missing-values"></a>Ersätt saknade värden  

Varje gången du använder modulen  [Rensa data som saknas](./clean-missing-data.md) i en uppsättning data tillämpas samma rengörings åtgärd på alla kolumner som du väljer. Om du behöver rensa olika kolumner med olika metoder kan du därför använda separata instanser av modulen.

1.  Lägg till modulen [Rensa data som saknas](./clean-missing-data.md) i pipelinen och Anslut den data uppsättning som saknar värden.  
  
2.  För **kolumner som ska rensas** väljer du de kolumner som innehåller de saknade värden som du vill ändra. Du kan välja flera kolumner, men du måste använda samma ersättnings metod i alla valda kolumner. Därför behöver du vanligt vis rensa sträng kolumner och numeriska kolumner separat.

    Om du till exempel vill söka efter saknade värden i alla numeriska kolumner:

    1. Välj modulen **Rensa data som saknas** och klicka på **Redigera kolumn** i den högra panelen i modulen.

    3. För **Inkludera** väljer du **kolumn typer** i list rutan och väljer sedan **numerisk**. 
  
    Eventuella rensnings-eller ersättnings metoder som du väljer måste vara tillämpliga på **alla** kolumner i markeringen. Om data i en kolumn inte är kompatibla med den angivna åtgärden returnerar modulen ett fel och stoppar pipelinen.
  
3.  Ange det minsta antalet saknade värden som krävs för att åtgärden ska utföras för **minsta värde för saknad värde**.  
  
    Du kan använda det här alternativet i kombination med **maximalt värde för saknat värde** för att definiera de villkor under vilka en rengörings åtgärd utförs i data uppsättningen. Åtgärden kan inte utföras om det finns för många eller för få rader som saknar värden. 
  
    Talet som du anger representerar **förhållandet mellan** saknade värden och alla värden i kolumnen. Som standard är den **minsta värdes kvot** egenskapen som anges till 0. Det innebär att saknade värden rensas även om det bara finns ett värde som saknas. 

    > [!WARNING]
    > Detta villkor måste uppfyllas av varje kolumn för att den angivna åtgärden ska kunna tillämpas. Anta till exempel att du har valt tre kolumner och sedan anger minimi förhållandet för saknade värden till 2 (20%), men bara en kolumn har i själva verket 20% värden som saknas. I det här fallet gäller rensnings åtgärden endast för kolumnen med fler än 20% saknade värden. De andra kolumnerna skulle därför vara oförändrade.
    > 
    > Om du är osäker på om de värden som saknas har ändrats väljer du alternativet och **genererar kolumnen indikator för saknat värde**. En kolumn läggs till i data uppsättningen för att ange om varje kolumn uppfyller de angivna villkoren för minsta och högsta intervall.  
  
4. Ange det maximala antalet saknade värden som kan finnas för den åtgärd som ska utföras för **maximal värdes kvot som saknas**.   
  
    Till exempel kanske du vill utföra värde ersättning som saknas om 30% eller färre rader innehåller värden som saknas, men lämna värdena som-är om fler än 30% av raderna innehåller värden som saknas.  
  
    Du definierar talet som förhållandet mellan saknade värden och alla värden i kolumnen. Som standard är det **maximala värdet för saknad värde** inställt på 1. Det innebär att saknade värden rensas även om 100% av värdena i kolumnen saknas.  
  
   
  
5. För **rengörings läge** väljer du något av följande alternativ för att ersätta eller ta bort värden som saknas:  
  
  
    + **Anpassat ersättnings värde**: Använd det här alternativet för att ange ett plats hållarens värde (t. ex. 0 eller na) som gäller för alla saknade värden. Det värde som du anger som ersättning måste vara kompatibelt med data typen för kolumnen.
  
    + **Ersätt med medelvärde**: beräknar kolumn medelvärdet och använder medelvärdet som ersättnings värde för varje saknat värde i kolumnen.  
  
        Gäller endast för kolumner som har data typerna Integer, Double eller Boolean.  
  
    + **Ersätt med median**: beräknar kolumnens median värde och använder median värdet som ersättning för ett saknat värde i kolumnen.  
  
        Gäller endast för kolumner som har heltals-eller dubbla data typer. 
  
    + **Ersätt med läge**: beräknar läget för kolumnen och använder läget som ersättnings värde för alla saknade värden i kolumnen.  
  
        Gäller för kolumner som har data typerna Integer, Double, Boolean eller kategoriska. 
  
    + **Ta bort hel rad**: tar bort alla rader i data uppsättningen som har ett eller flera värden som saknas. Detta är användbart om det saknade värdet kan anses slumpmässigt saknas.  
  
    + **Ta bort hel kolumn**: tar bort alla kolumner i data uppsättningen som har ett eller flera värden som saknas.  
  
    
  
6. Alternativet **ersättnings värde** är tillgängligt om du har valt alternativet, **anpassat ersättnings värde**. Ange ett nytt värde som ska användas som ersättnings värde för alla saknade värden i kolumnen.  
  
    Observera att du bara kan använda det här alternativet i kolumner som har heltal, dubbla, booleska eller strängar.
  
7. **Generera indikator kolumn för saknad värde**: Välj det här alternativet om du vill visa en indikation om värdena i kolumnen uppfyllde villkoren för rengöring av värde som saknas. Det här alternativet är särskilt användbart när du konfigurerar en ny rengörings åtgärd och vill se till att den fungerar som den ska.
  
8. Skicka pipelinen.

### <a name="results"></a>Resultat

Modulen returnerar två utdata:  

-   **Rensad data mängd**: en data uppsättning som består av de markerade kolumnerna, där värden som saknas hanteras som angivna, tillsammans med en indikator kolumn, om du har valt det alternativet.  

    Kolumner som inte har valts för rengöring är också "passerade".  
  
-  **Rensning av omvandling**: en datatransformering som används för rengöring, som kan sparas på din arbets yta och tillämpas på nya data senare.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Använd en sparad Rensnings åtgärd för nya data  

Om du ofta behöver upprepa rengörings åtgärder rekommenderar vi att du sparar ditt recept för data rengöring som en *transformering*, för att återanvända med samma data uppsättning. Att spara en rengörings omvandling är särskilt användbart om du ofta måste importera på nytt och sedan Rensa data som har samma schema.  
      
1.  Lägg till modulen [Använd omvandling](./apply-transformation.md) i din pipeline.  
  
2.  Lägg till den data uppsättning som du vill rensa och Anslut data uppsättningen till den högra porten för indata.  
  
3.  Expandera gruppen **Transforms** i den vänstra rutan i designern. Leta upp den sparade omvandlingen och dra den till pipelinen.  

4.  Anslut den sparade omvandlingen till den vänstra Indataporten för [Apply Transformation](./apply-transformation.md). 

    När du använder en sparad omvandling kan du inte välja de kolumner som transformationen tillämpas på. Det beror på att omvandlingen redan har definierats och tillämpas automatiskt på de kolumner som anges i den ursprungliga åtgärden.

    Anta dock att du har skapat en omvandling på en delmängd av numeriska kolumner. Du kan använda den här omvandlingen till en data uppsättning med blandade kolumn typer utan att ett fel uppstår, eftersom de saknade värdena bara ändras i de matchande numeriska kolumnerna.

6.  Skicka pipelinen.  

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 