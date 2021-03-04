---
title: Felsöka Azure reservations överföringar mellan klienter
description: Den här artikeln hjälper reservations ägare att överföra en reservations order från en Azure Active Directory klient (katalog) till en annan.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.openlocfilehash: 79473d57cc7504e7e6ef4ef68ba0cee74203f62b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055843"
---
# <a name="troubleshoot-reservation-transfers-between-tenants"></a>Felsöka reservations överföringar mellan klienter

Den här artikeln hjälper reservations ägare att överföra en reservations order från en Azure Active Directory klient (katalog) till en annan. När du ändrar en reservations beställnings katalog, tar den bort alla Azure RBAC-åtkomst till reservations ordningen och beroende reservationer. Endast du kommer att ha åtkomst efter ändringen. Om du ändrar katalogen ändras inte fakturerings ägarskapet för reservations ordern. Katalogen har ändrats för den överordnade reservations ordningen och beroende reservationer.

Reservations utbyte och uppsägning behöver inte överföras mellan klienter.

När du har överfört en reservation till en annan klient kanske du också vill lägga till ytterligare ägare i reservationen. Mer information finns i [som kan hantera en reservation som standard](view-reservations.md#who-can-manage-a-reservation-by-default).

När du överför en reservations order överförs alla reservationer i ordern till den.

## <a name="transfer-a-reservation"></a>Överför en reservation

Använd följande steg för att överföra en reservations order och den är beroende av reservationer till en annan klient.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Om du inte är fakturerings administratör men du är reservations ägare, navigerar du till **reservationer** och går sedan vidare till steg 6.
1. Gå till **Kostnadshantering + fakturering**.
    - Om du är en EA-administratör väljer du **fakturerings omfång** i den vänstra menyn och väljer sedan en i listan över fakturerings omfång.
    - Om du är Microsoft kund avtal ägare av fakturerings profil väljer du **fakturerings profiler** på den vänstra menyn. I listan över fakturerings profiler väljer du ett.
1. På den vänstra menyn väljer du **reservations transaktioner**. Listan över reservations transaktioner visas.
1. En banderoll överst på sidan läser *nu fakturerings administratörerna kan hantera reservationer. Klicka här för att hantera reservationer.* Välj banderollen.
1. Den fullständiga listan över reservationer för din EA-registrering eller fakturerings profil visas.
1. Välj den reservation som du vill överföra.
1. I reservations informationen väljer du reservations orderns ID.
1. I reservations ordningen väljer du **ändra katalog**.
1. I fönstret Ändra katalog väljer du den Azure AD-katalog som du vill överföra reservationen till och väljer sedan **Bekräfta**.

## <a name="next-steps"></a>Nästa steg

- Du kan läsa mer om reservationer i [Vad är Azure-reservationer?](save-compute-costs-reservations.md)