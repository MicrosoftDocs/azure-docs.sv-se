---
title: Så här hanterar du term mallar för företags ord lista
description: Lär dig hur du hanterar term mallar för företags ord lista i en Azure avdelningens kontroll Data Catalog.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555339"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>Så här hanterar du term mallar för företags ord lista

Med Azure avdelningens kontroll kan du skapa en ord lista med termer som är viktiga för att utöka dina data. Varje ny term som läggs till i din avdelningens kontroll Data Catalog ord lista baseras på en term mall som avgör fälten för termen. Den här artikeln beskriver hur du skapar en terms-mall och anpassade attribut som kan kopplas till ord listans villkor.

## <a name="manage-term-templates-and-custom-attributes"></a>Hantera term mallar och anpassade attribut

Med hjälp av term mallar kan du skapa anpassade attribut, gruppera dem tillsammans och tillämpa en mall när du skapar villkor. När en term har skapats går det inte att ändra mallen som är associerad med termen.

1. På sidan **ord listans villkor** väljer du **Hantera term mallar**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="Skärm bild av ord lista > knappen Hantera term mallar.":::

2. Sidan visar både system och anpassade attribut. Välj fliken **anpassad** om du vill skapa eller redigera term mallar.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="Skärm bild av ord listans termer > hantera term mallar sidan.":::

3. Välj **+ ny term mall** och ange ett Mallnamn och en beskrivning.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="Skärm bild av ord termer > hantera term mallar > nya term mallar":::

4. Välj **+ nytt attribut** för att skapa ett nytt anpassat attribut för term mal len. Ange ett attributnamn, en beskrivning. Namnet på det anpassade attributet måste vara unikt inom en term mall, men kan ha samma namn som kan återanvändas mellan mallar.

   Välj fält typ i listan med alternativ **text**, **ett alternativ**, **flera** alternativ eller  **datum**. Du kan också ange en standardvärdes sträng för text fält typer.  Attributet kan också markeras som **obligatoriskt**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="Skärm bild av ord listans villkor > sidan nytt attribut.":::

5. När alla anpassade attribut har skapats väljer du för **hands version** för att ordna ordningen på anpassade attribut. Du kan dra och släppa anpassade attribut i önskad ordning.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="Skärm bild av ord listans termer > term mal len för förhands granskning.":::

6. När alla anpassade attribut har definierats väljer du **skapa** för att skapa en term-mall med anpassade attribut.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="Skärm bild av ord listans termer > ny term mall – knappen Skapa.":::

7. Befintliga anpassade attribut kan markeras som upphört att gälla om **kryss rutan markeras som upphört**. När det har gått ut går det inte att återaktivera attributet. Det utgångna attributet blir grå för befintliga villkor. Framtida nya termer som skapats med den här term mal len kommer inte längre att Visa attributet som har marker ATS som förfallet.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="Skärm bild av ord termer > Edit attribut för att markera det som upphört.":::

## <a name="next-steps"></a>Nästa steg

Följ [självstudien: skapa och importera ord listans villkor](tutorial-import-create-glossary-terms.md) för mer information.
