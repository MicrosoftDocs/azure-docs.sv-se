---
title: Hitta en reservationsköpare från Azure Monitor loggar
description: Den här artikeln hjälper dig att hitta en reservationsköpare med information från Azure Monitor loggar.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: 965e90eed0690d57b6ad3cf3a1543b1329c0fe74
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773380"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Hitta en reservationsköpare från Azure-loggar

Den här artikeln hjälper dig att hitta en reservationsköpare med information från dina katalogloggar. Katalogloggarna från Azure Monitor visar e-post-ID:erna för användare som har gjort reservationsköp.

## <a name="find-the-purchaser"></a>Hitta inköparen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till **Övervaka**  >  **aktivitetsloggaktivitet**  >  .  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Skärmbild som visar navigering till aktivitetslogg – aktivitet." lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Välj **Katalogaktivitet**. Om du ser ett meddelande om *att du behöver behörighet att visa loggar på katalognivå* väljer du [länken](../../role-based-access-control/elevate-access-global-admin.md) för att lära dig hur du får behörigheter.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Skärmbild som visar Katalogaktivitet utan behörighet att visa loggen." lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. När du har behörighet filtrerar du **Klientorganisationsresursprovider** **med Microsoft.Capacity**. Du bör se alla reservationsrelaterade händelser för det valda tidsintervallet. Ändra tidsintervallet om det behövs.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Skärmbild som visar användaren som köpte reservationen." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    Om det behövs kan du behöva Redigera kolumner **för att välja** Händelse **initierad av**.
   Användaren som gjorde reservationsköpet visas under Händelse **initierad av**.

## <a name="next-steps"></a>Nästa steg

- Vid behov kan faktureringsadministratörer [bli ägare till en reservation](view-reservations.md#how-billing-administrators-can-view-or-manage-reservations).
