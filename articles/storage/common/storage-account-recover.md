---
title: Återställa ett borttaget lagringskonto
titleSuffix: Azure Storage
description: Lär dig hur du återställer ett borttaget lagrings konto inom Azure Portal.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c37e4aeb9b9af1c4f792d0827fec39750a1b1c2a
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096171"
---
# <a name="recover-a-deleted-storage-account"></a>Återställa ett borttaget lagringskonto

Ett borttaget lagrings konto kan återställas i vissa fall inom Azure Portal. Om du vill återställa ett lagrings konto måste följande villkor vara uppfyllda:

- Lagrings kontot har tagits bort under de senaste 14 dagarna.
- Lagrings kontot har skapats med Azure Resource Manager distributions modell.
- Ett nytt lagrings konto med samma namn har inte skapats sedan det ursprungliga kontot togs bort.

Innan du försöker återställa ett borttaget lagrings konto kontrollerar du att resurs gruppen för det kontot finns. Om resurs gruppen har tagits bort måste du återskapa den. Det går inte att återställa en resurs grupp. Mer information finns i [Hantera resurs grupper](../../azure-resource-manager/management/manage-resource-groups-portal.md).

Om det borttagna lagrings kontot använder Kundhanterade nycklar med Azure Key Vault och nyckel valvet också har tagits bort, måste du återställa nyckel valvet innan du återställer lagrings kontot. Mer information finns i [Översikt över Azure Key Vault återställning](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> Det går inte att återställa ett borttaget lagrings konto. Återställning är ett försök till bästa möjliga försök. Microsoft rekommenderar att du låser resurser för att förhindra borttagning av oavsiktliga konton. Mer information om resurs lås finns i [låsa resurser för att förhindra ändringar](../../azure-resource-manager/management/lock-resources.md).
>
> En annan metod rekommenderas för att undvika att ta bort oavsiktligt konto är att begränsa antalet användare som har behörighet att ta bort ett konto via rollbaserad åtkomst kontroll (Azure RBAC). Mer information finns i [metod tips för Azure RBAC](../../role-based-access-control/best-practices.md).

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Återställa ett borttaget konto från Azure Portal

Följ dessa steg om du vill återställa ett borttaget lagrings konto inifrån ett annat lagrings konto:

1. Gå till översikts sidan för ett befintligt lagrings konto i Azure Portal.
1. I avsnittet **support och fel sökning** väljer du **Återställ borttaget konto**.
1. I list rutan väljer du det konto som ska återställas, som du ser i följande bild. Om det lagrings konto som du vill återställa inte finns i list rutan, kan det inte återställas.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Skärm bild som visar hur du återställer lagrings kontot i Azure Portal":::

1. Klicka på knappen **Återställ** för att återställa kontot. Portalen visar ett meddelande om att återställningen pågår.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>Återställa ett borttaget konto via ett support ärende

1. I Azure Portal navigerar du till **Hjälp + Support**.
1. Välj **Ny supportbegäran**.
1. På fliken **grundläggande** i fältet typ av **ärende** väljer du **teknisk**.
1. I fältet **prenumeration** väljer du den prenumeration som innehöll det borttagna lagrings kontot.
1. I fältet **tjänst** väljer du **hantering av lagrings konto**.
1. I fältet **resurs** väljer du valfri lagrings konto resurs. Det borttagna lagrings kontot visas inte i listan.
1. Lägg till en kort sammanfattning av problemet.
1. I fältet **problem typ** väljer du **borttagning och återställning**.
1. I fältet **problem under typ** väljer du **Återställ borttaget lagrings konto**. Följande bild visar ett exempel på fliken **grundläggande** som fylls i:

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="Skärm bild som visar hur du återställer ett lagrings konto via fliken grundläggande support":::

1. Gå sedan till fliken **lösningar** och välj **Kundkontrollerad lagrings konto återställning**, som du ser i följande bild:

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="Skärm bild som visar hur du återställer ett lagrings konto via fliken Support Ticket – lösningar":::

1. I list rutan väljer du det konto som ska återställas, som du ser i följande bild. Om det lagrings konto som du vill återställa inte finns i list rutan, kan det inte återställas.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="Skärm bild som visar hur du återställer ett lagrings konto via support ärende":::

1. Klicka på knappen **Återställ** för att återställa kontot. Portalen visar ett meddelande om att återställningen pågår.

## <a name="next-steps"></a>Nästa steg

- [Översikt över lagringskonto](storage-account-overview.md)
- [Skapa ett lagringskonto](storage-account-create.md)