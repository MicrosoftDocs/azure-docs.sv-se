---
title: Hitta en reservations inköpare från Azure Monitor loggar
description: Den här artikeln hjälper dig att hitta en reservations inköpare med information från Azure Monitor loggar.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: eedb5e7a55b50a353fd16498500b891e289e61e5
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103477071"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Hitta en reservations inköpare från Azure-loggar

Den här artikeln hjälper dig att hitta en reservations inköpare med information från dina katalog loggar. Katalog loggarna från Azure Monitor visar e-ID: n för användare som gjort reservations inköp.

## <a name="find-the-purchaser"></a>Hitta inköparen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till **övervaka**  >  **aktivitets loggs**  >  **aktivitet**.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Skärm bild som visar navigering till aktivitets logg-aktivitet." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Välj **katalog aktivitet**. Om du ser ett meddelande som anger att *du behöver behörighet att Visa katalog nivå loggar* väljer du [länken](../../role-based-access-control/elevate-access-global-admin.md) för att lära dig hur du får behörighet.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Skärm bild som visar katalog aktivitet utan behörighet att visa loggen." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. När du har behörighet kan du filtrera **klient organisations resurs leverantören** med **Microsoft. Capacity**. Du bör se alla reservations relaterade händelser för det valda tidsintervallet. Om det behövs ändrar du tidsintervallet.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Skärm bild som visar användaren som har köpt reservationen." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Vid behov kan du behöva **Redigera kolumner** för att välja **händelse som initieras av**.
   Den användare som gjorde reservations köpet visas under **händelse som initieras av**.

## <a name="next-steps"></a>Nästa steg

- Vid behov kan fakturerings administratörer [bli ägare till en reservation](view-reservations.md#how-billing-administrators-view-or-manage-reservations).