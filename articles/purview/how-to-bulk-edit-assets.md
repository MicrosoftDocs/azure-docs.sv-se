---
title: Så här massr du redigera till gångar för att tagga klassificeringar, ord listans villkor och ändra kontakter
description: Lär dig mer om Mass redigering av till gångar i Azure avdelningens kontroll.
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 149a70ea9b3fad919e771e8eb279e01d1492b3b3
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952464"
---
# <a name="how-to-bulk-edit-assets-to-tag-glossary-terms"></a>Så här massr du redigera till gångar för att tagga ord listans villkor

Den här artikeln beskriver hur du tagga flera ord listor i en lista över valda till gångar i en enda åtgärd.

### <a name="add-assets-to-view-selected-list-using-search"></a>Lägg till till gångar för att visa den valda listan med Sök

1. Sök efter den data till gång som du vill lägga till i listan för Mass redigering.

2. På sidan Sök Resultat hovrar du till den till gång som du vill lägga till i listan med massredigera **vyer** för att se en kryss ruta.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="Skärm bild av kryss rutan.":::

3. Markera kryss rutan för att lägga till den i listan över massredigera **vyer som valts** . När du har lagt till visas ikonen valda objekt längst ned på sidan.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="Skärm bild av listan.":::

4. Upprepa stegen ovan för att lägga till alla data till gångar i listan.

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>Lägg till till gångar för att visa den valda listan från sidan till gångs information

1. På sidan till gångs information markerar du kryss rutan i det övre högra hörnet för att lägga till till gången till den valda listan med massredigera **vyer** .

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="Skärm bild av till gången.":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>Massredigera till gångar i listan Visa valda om du vill lägga till, ersätta eller ta bort ord List villkor.

1. En du är färdig med identifieringen av alla data till gångar som måste vara Mass redigering, Välj Visa den **valda** listan från sidan Sök resultat eller till gångs information.

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="Skärm bild av vyn.":::

2. Granska listan och välj **ta bort** om du vill ta bort alla villkor.

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="Skärm bild av Remove.":::

3. Välj Mass redigering för att lägga till, ta bort eller Ersätt ord listans villkor för alla valda till gångar.

4. Välj åtgärd som **Lägg** till för att lägga till ord listans villkor. Markera alla ord listas villkor som du vill lägga till i det nya värdet. Välj Använd när du är klar.
    - Lägg till åtgärd lägger till ett nytt värde i listan över ord listor som redan är märkta med data till gångar.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="Skärm bild av Lägg till.":::

5. Om du vill ersätta ord listans villkor väljer du åtgärden **Ersätt med**. Markera alla ord listans villkor som du vill ersätta med det nya värdet. Välj Använd när du är klar.
    - Ersättnings åtgärden ersätter alla ord listans villkor för markerade data till gångar med de villkor som valts i nytt värde.
   
6. Om du vill ta bort ord listans villkor väljer du åtgärden **ta bort**. Välj Använd när du är klar.
    - Ta bort åtgärd tar bort alla ord listans villkor för markerade data till gångar.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="Skärm bild av borttagnings villkoren.":::

7. Upprepa ovanstående för klassificeringar, ägare och experter.

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="Skärm bild av klassificeringar och kontakter.":::

8. När du är klar stänger du redigera bladet Mass redigering genom att välja **Stäng** eller **ta bort alla och Stäng**. Om du stänger tas de valda till gångarna bort, medan ta bort alla och Stäng tas alla valda till gångar bort.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="Skärm bild av stängningen.":::

   > [!Important]
   > Det rekommenderade antalet till gångar för Mass redigering är 25. Om du väljer fler än 25 kan prestanda problem uppstå.
   > Rutan **Visa markerad** visas bara om det finns minst en till gång vald.


Följ [självstudien: skapa och importera ord listans villkor](how-to-create-import-export-glossary.md) för mer information.
