---
title: Hantera redundant zon med hög tillgänglighet – Azure Portal-Azure Database for MySQL flexibel Server
description: I den här artikeln beskrivs hur du aktiverar eller inaktiverar zonens redundanta hög tillgänglighet i Azure Database for MySQL flexibel Server genom Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: d65b074385311e74444929ef74901e402e29ec03
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93241743"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Hantera redundanta zoner med hög tillgänglighet i Azure Database for MySQL flexibel Server (för hands version)

Den här artikeln beskriver hur du kan aktivera eller inaktivera zonens redundanta konfiguration för hög tillgänglighet på din flexibla Server.

Funktionen för hög tillgänglighet etablerar fysiskt separat primär och vänte replik i olika zoner. Mer information finns i [dokumentation om begrepp för hög tillgänglighet](./concepts/../concepts-high-availability.md). 

> [!IMPORTANT]
> Du kan bara aktivera en redundant hög tillgänglighet för zonen när du skapar en flexibel Server.

Den här sidan innehåller rikt linjer för hur du kan aktivera eller inaktivera hög tillgänglighet. Den här åtgärden ändrar inte dina andra inställningar, inklusive VNET-konfiguration, brand Väggs inställningar och kvarhållning av säkerhets kopior. På samma sätt är inaktive ring av hög tillgänglighet en online-åtgärd och påverkar inte din program anslutning och dina åtgärder.

> [!IMPORTANT]
> Zon redundant hög tillgänglighet är tillgänglig i en begränsad uppsättning regioner: Sydostasien, väst 2, Västeuropa och östra USA.  

## <a name="enable-high-availability-during-server-creation"></a>Aktivera hög tillgänglighet när servern skapas

Det här avsnittet innehåller information som är specifik för de fält som är relaterade till. Du kan följa de här stegen för att distribuera hög tillgänglighet när du skapar en flexibel Server.

1.  I [Azure Portal](https://portal.azure.com/)väljer du flexibel Server och klickar på **skapa**.  Mer information om hur du fyller i information som **prenumeration**, **resurs grupp**, **Server namn**, **region** och andra fält finns i instruktions dokumentation för att skapa servern.

2.  Klicka på kryss rutan för **zonen redundant hög tillgänglighet** i tillgänglighets alternativet.

3.  Klicka på  **Konfigurera Server** om du vill ändra standard beräkningen och lagringen.

4.  Om alternativet för hög tillgänglighet är markerat är den Burstable nivån inte tillgänglig för att välja. Du kan välja antingen **generell användning** eller **minnesoptimerade** beräknings nivåer.

    > [!IMPORTANT]
    > Vi stöder bara Zone-redundant hög tillgänglighet för det ***generella syftet** _ och _ *_Memory_* Optimized * pris nivå.

5.  Välj **beräknings storlek** för ditt val i list rutan.

6.  Välj **lagrings storlek** i GIB med hjälp av glidande stapel och välj **tids perioden för kvarhållning av säkerhets kopior** mellan 7 dagar och 35 dagar.   

## <a name="disable-high-availability"></a>Inaktivera hög tillgänglighet

Följ dessa steg om du vill inaktivera hög tillgänglighet för din flexibla server som redan har kon figurer ATS med zon redundans.

1.  I [Azure Portal](https://portal.azure.com/)väljer du den befintliga Azure Database for MySQL flexibla servern.

2.  På sidan flexibel Server klickar du på **hög tillgänglighet** på Front panelen för att öppna sidan med hög tillgänglighet.

3.  Klicka på kryss rutan **zon redundant hög tillgänglighet** om du vill inaktivera alternativet och klicka på **Spara** för att spara ändringen.

4.  En bekräftelse dialog ruta visas där du kan bekräfta att inaktive ras.

5.  Klicka på **inaktivera ha** om du vill inaktivera hög tillgänglighet.

6.  Ett meddelande visas som tar upp inaktive ring av distributionen av hög tillgänglighet.

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om [verksamhets kontinuitet](./concepts-business-continuity.md)
-   Lär dig mer om [Zone-redundant hög tillgänglighet](./concepts-high-availability.md)
