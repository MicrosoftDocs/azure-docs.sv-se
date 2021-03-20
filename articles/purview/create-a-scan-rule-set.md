---
title: Skapa en regeluppsättning för genomsökning
description: Skapa en skannings regel uppsättning i Azure avdelningens kontroll för att snabbt söka igenom data källor i din organisation.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96554742"
---
# <a name="create-a-scan-rule-set"></a>Skapa en regeluppsättning för genomsökning

I en Azure avdelningens kontroll-katalog kan du skapa skannings regel uppsättningar som gör att du snabbt kan söka igenom data källor i din organisation.

En skannings regel uppsättning är en behållare för att gruppera en uppsättning skannings regler tillsammans så att du enkelt kan koppla dem till en sökning. Du kan till exempel skapa en standard regel uppsättning för genomsökning för varje typ av data källa och sedan använda de här Sök regel uppsättningarna som standard för alla sökningar i företaget. Du kanske också vill att användare med rätt behörighet kan skapa andra skannings regel uppsättningar med olika konfigurationer baserat på affärs behov.

## <a name="steps-to-create-a-scan-rule-set"></a>Steg för att skapa en skannings regel uppsättning

Så här skapar du en skannings regel uppsättning:

1. Välj **hanterings Center** från Azure avdelningens kontroll-katalogen.

1. Välj **Sök regel uppsättningar** i den vänstra rutan och välj sedan **ny**.

1. På sidan **ny skannings regel uppsättning** väljer du de data källor som katalog skannern stöder från List rutan **Källtyp** . Du kan skapa en skannings regel uppsättning för varje typ av data källa som du vill genomsöka.

1. Ange ett **namn** för din skannings regel. Den maximala längden är 63 tecken, utan tillåtna blank steg. Du kan också ange en **Beskrivning**. Den maximala längden är 256 tecken.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="Skärm bild som visar sidan för skannings regel uppsättningen." border="true":::

1. Välj **Fortsätt**.

   Sidan **Välj filtyper** visas. Observera att fil typs alternativen på den här sidan varierar beroende på vilken typ av data källa som du valde på föregående sida. Alla filtyper är aktiverade som standard.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="Skärm bild som visar sidan Välj filtyper.":::

   Med alternativet **dokument fil typer** på den här sidan kan du ta med eller undanta följande typer av Office-filer:. doc,. docm,. docx,. dot,. ODP,. ods,. odt,. pdf,. pot,. PPS,. ppsx,. ppt,. pptm,. pptx,. xlc,. xls,. xlsb,. xlsm,. xlsx och. xlt.

1. Aktivera eller inaktivera en fil typs panel genom att markera eller avmarkera kryss rutan. Om du väljer en data källa för Data Lake typ (till exempel Azure Data Lake Storage Gen2 eller Azure Blob) aktiverar du de filtyper som du vill extrahera och klassificera schemat för.

1. För vissa typer av data källor kan du också [skapa en anpassad filtyp](#create-a-custom-file-type).

1. Välj **Fortsätt**.

   Sidan **Välj klassificerings regler** visas. På den här sidan visas de valda **system reglerna** och de **anpassade reglerna**, och det totala antalet klassificerings regler som har valts. Som standard är alla kryss rutor för **System regler** markerade

1. För de regler som du vill ta med eller undanta, kan du markera eller avmarkera kryss rutorna för klassificerings regler för **System regler** globalt efter kategori.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="Skärm bild som visar sidan Välj klassificerings regler.":::

1. Du kan expandera noden kategori och markera eller avmarkera enskilda kryss rutor. Om regeln för **Argentina. DNI-numret** till exempel har höga falska positiva identifieringar, kan du avmarkera den aktuella kryss rutan.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="Skärm bild som visar hur du väljer system regler.":::

1. Klicka på **skapa** för att slutföra skapandet av skannings regel uppsättningen.

### <a name="create-a-custom-file-type"></a>Skapa en anpassad filtyp

Azure avdelningens kontroll har stöd för att lägga till ett anpassat tillägg och definiera en anpassad kolumn avgränsare i en skannings regel uppsättning.

Så här skapar du en anpassad filtyp:

1. Följ steg 1 – 5 i [steg för att skapa en skannings regel uppsättning](#steps-to-create-a-scan-rule-set) eller redigera en befintlig skannings regel uppsättning.

1. På sidan **Välj filtyper** väljer du **ny filtyp** för att skapa en ny anpassad filtyp.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="Skärm bild som visar hur du väljer ny filtyp på sidan Välj fil typer.":::

1. Ange ett **fil namns tillägg** och en valfri **Beskrivning**.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="Skärm bild som visar sidan ny typ av anpassad fil." border="true":::

1. Gör något av följande för **fil innehåll i** för att ange typ av fil innehåll i din fil:

   - Välj **anpassad avgränsare** och ange din egen **avgränsare** (endast enskilt tecken).

   - Välj **system fil typ** och välj en system fil typ (till exempel XML) i list rutan **typ av system fil** .

1. Välj **skapa** för att spara den anpassade filen.

   Systemet återgår till sidan **Välj filtyper** och infogar den nya anpassade fil typen som en ny panel.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="Skärm bild som visar den nya anpassade fil typs panelen på sidan Välj filtyper.":::

1. Välj **Redigera** i den nya fil typs rutan om du vill ändra eller ta bort den.

1. Välj **Fortsätt** för att slutföra konfigurationen av skannings regel uppsättningen.

## <a name="system-scan-rule-sets"></a>Regel uppsättningar för system skanning

Regel uppsättningar för system genomsökning är Microsoft-definierade Sök regel uppsättningar som skapas automatiskt för varje Azure avdelningens kontroll-katalog. Varje regel uppsättning för system skanning är associerad med en speciell typ av data källa. När du skapar en genomsökning kan du associera den med en regel uppsättning för system skanning. Varje gång som Microsoft gör en uppdatering av dessa system regel uppsättningar kan du uppdatera dem i katalogen och tillämpa uppdateringen på alla tillhör ande genomsökningar.

1. Om du vill visa en lista över regel uppsättningar för system skanning väljer du **skannings regel uppsättningar** i **hanterings centret** och sedan fliken **system** .

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="Skärm bild som visar listan över regel uppsättningar för system genomsökning." lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. Varje regel uppsättning för system skanning har ett **namn**, en **Källtyp** och en **version**. Om du väljer versions numret för en skannings regel uppsättning i kolumnen **version** visas de regler som är kopplade till den aktuella versionen och tidigare versioner (om sådana finns).

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="Skärm bild som visar en regel uppsättnings sida för system skanning." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. Om det finns en uppdatering för en regel uppsättning för system skanning kan du välja **Uppdatera** i kolumnen **version** . På sidan system skannings regel väljer du från en version från List rutan **Välj en ny version som ska uppdateras** . Sidan innehåller en lista över system klassificerings regler som är associerade med den nya versionen och den aktuella versionen.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="Skärm bild som visar hur du ändrar versionen för en regel uppsättning för system skanning." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>Associera en sökning med en regel uppsättning för system skanning

När du [skapar en genomsökning](tutorial-scan-data.md#scan-data-into-the-catalog)kan du välja att koppla den till en regel uppsättning för system skanning enligt följande:

1. På sidan **Välj en regel uppsättning för skanning** väljer du regel uppsättningen för system genomsökning.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="Skärm bild som visar hur du väljer en regel uppsättning för system skanning för en sökning." lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. Välj **Fortsätt** och välj sedan **Spara och kör**.
