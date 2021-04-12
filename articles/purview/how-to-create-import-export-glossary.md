---
title: Skapa, importera och exportera ord listans villkor
description: Lär dig hur du skapar, importerar och exporterar ord listans villkor i Azure avdelningens kontroll.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/02/2021
ms.openlocfilehash: 6796a53ac05e8db606b29447a8792fa509c03d7e
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284111"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>Skapa, importera och exportera ord listans villkor

Den här artikeln beskriver hur du skapar en ord Frass term i Azure avdelningens kontroll Data Catalog och importerar och exporterar ord listans villkor med CSV-filer.

## <a name="create-a-new-term"></a>Skapa en ny term

Gör så här för att skapa en ny ord lista:

1. Välj ord List ikonen i det vänstra navigerings fönstret på Start sidan för att gå till term List sidan.

2. På sidan **ord listans villkor** väljer du **+ ny term**. En sida **öppnas med systemdefinierad** mall vald. Välj den mall som du vill skapa en ord lista med och välj **Fortsätt**.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="Skärm bild av den nya term skapandet." border="true":::

3. Ge din nya term ett namn som måste vara unikt i katalogen. Term namnet är Skift läges känsligt, vilket innebär att du kan ha en term som kallas **exempel** och **exempel** i katalogen.

4. Lägg till en **definition**.

5. Ange **status** för termen. Nya villkor som standard för **utkast** status.

   :::image type="content" source="media/how-to-create-import-export-glossary/overview-tab.png" alt-text="Skärm bild av status alternativen.":::

   Dessa status markörer är metadata som associeras med termen. För närvarande kan du ange följande status för varje term:

   - **Utkast**: den här termen är inte officiellt implementerad än.
   - **Godkänd**: den här termen är officiell/standard/godkänd.
   - **Utgånget**: den här termen bör inte längre användas.
   - **Varning**: den här termen behöver åtgärdas.

6. Lägg till **resurser** och **akronym**. Om termen är en del av hierarkin kan du lägga till överordnade villkor till **överordnad** på fliken Översikt.

7. Lägg till **synonymer** och **relaterade villkor** på fliken relaterad.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="Skärm bild av ny term > relaterad flik." border="true":::

8. Du kan också välja fliken **kontakter** för att lägga till experter och vård till din period.

9. Välj **skapa** för att skapa din term.

## <a name="import-terms-into-the-glossary"></a>Importera villkor till ord listan

Azure avdelningens kontroll-Data Catalog innehåller en mall. csv-fil som du kan använda för att importera dina villkor till ord listan.

Du kan importera villkor i katalogen. De dubbla villkoren i filen kommer att skrivas över.

Observera att term namn är Skift läges känsliga. Till exempel `Sample` och `saMple` kan båda finnas i samma ord lista.

### <a name="to-import-terms-follow-these-steps"></a>Följ dessa steg om du vill importera villkor

1. När du är på sidan **ord för ord** listan väljer du **import villkor**.

2. Sidan term mall öppnas. Matcha term mal len till typen av. CSV som du vill importera.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="Skärm bild av sidan ord listans villkor, knappen Importera villkor.":::

3. Hämta CSV-mallen och Använd den för att ange dina villkor som du vill lägga till.

   > [!Important]
   > Systemet stöder bara import av kolumner som är tillgängliga i mallen. Mallen "system standard" kommer att ha alla standardattribut.
   > Anpassade term mallar kommer dock att ha inaktuella attribut och ytterligare anpassade attribut som definierats i mallen. Därför. CSV-filen skiljer sig åt både från det totala antalet kolumner och kolumn namn beroende på vilken term mal len som valts. Du kan också granska filen om det finns problem efter överföringen.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="Skärm bild av sidan ord listans villkor väljer du fil för import.":::

4. När du har fyllt i CSV-filen väljer du den fil som ska importeras och väljer sedan **OK**.

5. Systemet kommer att ladda upp filen och lägga till alla villkor i katalogen.
 
   > [!Important]
   > E-postadressen för administratörer och experter bör vara den primära adressen för användaren från AAD-gruppen. Alternativa e-postmeddelanden, User Principal Name och e-postmeddelanden som inte är AAD-e-post stöds inte ännu. 

## <a name="export-terms-from-glossary-with-custom-attributes"></a>Exportera termer från ord lista med anpassade attribut

Du bör kunna exportera termer från ord lista så länge de valda villkoren tillhör samma term-mall.

1. När du är i ord listan är **export** knappen som standard inaktive rad. När du har valt de villkor som du vill exportera aktive ras knappen **Exportera** om de valda villkoren tillhör samma mall.

2. Välj **Exportera** för att ladda ned de valda villkoren.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-export.png" lightbox="media/how-to-create-import-export-glossary/select-term-template-for-export.png" alt-text="Skärm bild av sidan ord listans villkor väljer du fil för export.":::

   > [!Important]
   > Om villkoren i en hierarki tillhör olika term mallar måste du dela upp dem i olika. CSV-filer för import. Det går för närvarande inte att uppdatera en överordnad av en term med hjälp av import processen.

## <a name="next-steps"></a>Nästa steg

Följ [självstudien: skapa och importera ord listans villkor](tutorial-import-create-glossary-terms.md) för mer information.
