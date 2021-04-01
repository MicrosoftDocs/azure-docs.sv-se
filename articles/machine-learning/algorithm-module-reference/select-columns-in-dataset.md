---
title: 'Välj kolumner i data uppsättning: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Välj kolumner i data uppsättning i Azure Machine Learning för att välja en delmängd av kolumner som ska användas i underordnade åtgärder.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 7926d5dd33df5538713f8de152dbbace2bc29864
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90890287"
---
# <a name="select-columns-in-dataset-module"></a>Välj kolumner i data uppsättnings modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att välja en delmängd av kolumner som ska användas i underordnade åtgärder. Modulen tar inte bort kolumnerna från käll data uppsättningen fysiskt. i stället skapas en delmängd av kolumner, ungefär som en databas *vy* eller *projektion*.

Den här modulen är användbar när du behöver begränsa de kolumner som är tillgängliga för en underordnad åtgärd, eller om du vill minska storleken på data uppsättningen genom att ta bort kolumner som inte behövs.

Kolumnerna i data uppsättningen matas ut i samma ordning som i de ursprungliga data, även om du anger dem i en annan ordning.

## <a name="how-to-use"></a>Använd så här

Den här modulen har inga parametrar. Du använder kolumn väljaren för att välja vilka kolumner som ska tas med eller undantas.

### <a name="choose-columns-by-name"></a>Välj kolumner efter namn

Det finns flera alternativ i modulen för att välja kolumner efter namn: 

+ Filtrera och söka

    Klicka på alternativet **efter namn** .

    Om du har anslutit en data mängd som redan är ifylld, ska en lista över tillgängliga kolumner visas. Om inga kolumner visas kan du behöva köra överordnade moduler för att Visa kolumn listan.

    Filtrera listan genom att skriva i sökrutan. Om du till exempel skriver bokstaven `w` i sökrutan filtreras listan för att visa de kolumn namn som innehåller bokstaven `w` .

    Markera kolumner och klicka på högerpilen för att flytta de markerade kolumnerna till listan i den högra rutan.

    + Om du vill välja ett kontinuerligt intervall med kolumn namn trycker du på **Skift + klicka**.
    + Om du vill lägga till enskilda kolumner i markeringen trycker du på **CTRL + klicka**.

    Klicka på bock knappen för att spara och stänga.

+ Använd namn i kombination med andra regler

    Klicka på alternativet **med regler** .
    
    Välj en regel, till exempel Visa kolumner av en speciell datatyp.

    Klicka sedan på enskilda kolumner av den typen efter namn för att lägga till dem i urvals listan.

+ Skriv eller klistra in en kommaavgränsad lista med kolumn namn

    Om data uppsättningen är bred kan det vara lättare att använda index eller genererade listor med namn, i stället för att välja kolumner separat. Förutsatt att du har för berett listan i förväg:

    1. Klicka på alternativet **med regler** . 
    2. Välj **inga kolumner**, Välj  **Inkludera** och klicka sedan i text rutan med det röda utrops tecknet. 
    3. Klistra in eller Skriv en kommaavgränsad lista över tidigare validerade kolumn namn. Du kan inte spara modulen om en kolumn har ett ogiltigt namn, så se till att du kontrollerar namnen på förhand.
    
    Du kan också använda den här metoden för att ange en lista med kolumner som använder sina index värden. 

### <a name="choose-by-type"></a>Välj efter typ

Om du använder alternativet **med regler** kan du tillämpa flera villkor på kolumn valen. Du kan till exempel behöva bara hämta funktions kolumner av en numerisk datatyp.

Alternativet **börja med** bestämmer start punkten och är viktigt för att förstå resultaten. 

+ Om du väljer alternativet **alla kolumner** läggs alla kolumner till i listan. Sedan måste du använda alternativet **exkludera** för att *ta bort* kolumner som uppfyller vissa villkor. 

    Du kan till exempel börja med alla kolumner och sedan ta bort kolumner efter namn eller typ.

+ Om du väljer alternativet **inga kolumner** börjar listan över kolumner vara tom. Ange sedan villkor för att *lägga till* kolumner i listan. 

    Om du använder flera regler, är varje villkor **additiv**. Anta till exempel att du börjar med inga kolumner och sedan lägger till en regel för att hämta alla numeriska kolumner. Det resulterar i 16 kolumner i data uppsättningen för Automobile-priset. Klicka sedan på **+** signera för att lägga till ett nytt villkor och välj **Inkludera alla funktioner**. Den resulterande data uppsättningen innehåller alla numeriska kolumner, plus alla funktions kolumner, inklusive vissa sträng funktions kolumner.

### <a name="choose-by-column-index"></a>Välj efter kolumn index

Kolumn indexet refererar till kolumnens ordning i den ursprungliga data uppsättningen.

+ Kolumner numreras sekventiellt från 1.  
+ Om du vill hämta ett kolumn intervall använder du ett bindestreck. 
+ Open-avslutade specifikationer som `1-` eller `-3` är inte tillåtna.
+ Dubbla index värden (eller kolumn namn) tillåts inte och kan resultera i ett fel.

Om din data uppsättning till exempel har minst åtta kolumner kan du klistra in följande exempel för att returnera flera icke-sammanhängande kolumner: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

det sista exemplet resulterar inte i ett fel. den returnerar dock en enda instans av en kolumn `4` .



### <a name="change-order-of-columns"></a>Ändra ordning på kolumner

Alternativet **Tillåt dubbletter och bevara kolumn ordning i markeringen** börjar med en tom lista och lägger till kolumner som du anger efter namn eller index. Till skillnad från andra alternativ, som alltid returnerar kolumner i deras "naturliga ordning", visar det här alternativet kolumnerna i den ordning som du namnger eller listar dem. 

I en data uppsättning med kolumnerna Col1, Col2, Col3 och Col4 kan du till exempel ändra ordningen på kolumnerna och lämna kolumn 2 genom att ange någon av följande listor:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 