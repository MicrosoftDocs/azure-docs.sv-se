---
title: Använd Azure Monitor arbets böcker för att ta med din egen lagring
description: Lär dig hur du skyddar din arbets bok genom att spara arbets bokens innehåll på lagrings platsen
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 16dbd7f7cd178a76b34b58f4bc6f9a0bc00fac73
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624622"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>Ta med din egen lagrings plats för att spara arbets böcker

Det finns tillfällen när du kan ha en fråga eller en affärs logik som du vill skydda. Arbets böcker är ett alternativ för att skydda arbets böckerna genom att spara arbets bokens innehåll till lagringen. Lagrings kontot kan sedan krypteras med Microsoft-hanterade nycklar eller så kan du hantera krypteringen genom att tillhandahålla dina egna nycklar. [Se Azure-dokumentationen på Kryptering för lagringstjänst.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Spara arbets boken med hanterade identiteter

1. Innan du kan spara arbets boken på lagrings platsen måste du skapa en hanterad identitet (alla tjänster-> hanterade identiteter) och ge den `Storage Blob Data Contributor` åtkomst till ditt lagrings konto. [Se Azure-dokumentation om hanterade identiteter.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Skärm bild som visar hur du lägger till en roll tilldelning](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Skapa en ny arbets bok.
3. Välj knappen **Spara** för att spara arbets boken.
4. Det finns ett alternativ för `Save content to an Azure Storage Account` , markera kryss rutan för att spara till ett Azure Storage-konto.

    ![Skärm bild som visar en sparad dialog ruta](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. Välj det lagrings konto och den lagrings behållare som vill ha. Lagrings konto listan är från den prenumeration som valts ovan.

    ![Skärm bild som visar dialog rutan Spara med lagrings alternativ](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. Välj sedan **ändra** för att välja en hanterad identitet som skapats tidigare.

    [![Skärm bild som visar dialog rutan Ändra identitet](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. När du har valt dina lagrings alternativ trycker du på **Spara** för att spara arbets boken.

## <a name="limitations"></a>Begränsningar

- När du sparar till anpassad lagring kan du inte fästa enskilda delar av arbets boken på en instrument panel eftersom de enskilda PIN-koderna innehåller skyddad information i själva instrument panelen. När du använder anpassad lagring kan du bara fästa länkar till själva arbets boken på instrument paneler.
- När en arbets bok har sparats i en anpassad lagrings plats sparas den alltid i den anpassade lagringen och kan inte stängas av. Om du vill spara någon annan stans kan du använda "Spara som" och välja att inte spara kopian till den anpassade lagrings platsen.
- Arbets böcker i Application Insights resurs är "äldre" arbets böcker och stöder inte anpassad lagring. De senaste arbets böckerna i Application Insights resurs är "... Fler val. Äldre arbets böcker har inga prenumerations alternativ när du sparar.

   ![Skärm bild som visar äldre arbets bok](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du skapar en [kart](workbooks-map-visualizations.md) visualisering i arbets böcker.
- Lär dig hur du använder [grupper i arbets böcker](../visualize/workbooks-groups.md).