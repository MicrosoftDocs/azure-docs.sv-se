---
title: Hantera zonredundant hög tillgänglighet – Azure Portal – Azure Database for MySQL flexibel server
description: Den här artikeln beskriver hur du aktiverar eller inaktiverar zonredundant hög tillgänglighet i Azure Database for MySQL flexibel server via Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: e217dcaeafd553803f5c9699ab6d7779ed755b67
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818302"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Hantera zonredundant hög tillgänglighet i Azure Database for MySQL flexibel server (förhandsversion)

I den här artikeln beskrivs hur du kan aktivera eller inaktivera zonredundant konfiguration med hög tillgänglighet på din flexibla server.

Funktionen för hög tillgänglighet tillhandsar fysiskt separata primära repliker och väntelägesrepliker i olika zoner. Mer information finns i dokumentationen [om begrepp med hög tillgänglighet.](./concepts/../concepts-high-availability.md) 

> [!IMPORTANT]
> Du kan bara aktivera zonredundant hög tillgänglighet när du skapar en flexibel server.

Den här sidan innehåller riktlinjer för hur du kan aktivera eller inaktivera hög tillgänglighet. Den här åtgärden ändrar inte dina andra inställningar, inklusive VNET-konfiguration, brandväggsinställningar och kvarhållning av säkerhetskopior. På samma sätt är inaktivering av hög tillgänglighet en onlineåtgärd som inte påverkar programmets anslutning och åtgärder.

> [!IMPORTANT]
> Zonredundant hög tillgänglighet är tillgängligt i en begränsad uppsättning regioner: Sydostasien, USA, västra 2, Europa, västra och USA, östra.  

## <a name="enable-high-availability-during-server-creation"></a>Aktivera hög tillgänglighet när servern skapas

Det här avsnittet innehåller information specifikt för HA-relaterade fält. Du kan följa de här stegen för att distribuera hög tillgänglighet när du skapar din flexibla server.

1.  I den [Azure Portal](https://portal.azure.com/)väljer du flexibel server och klickar på **Skapa.**  Mer information om hur du fyller i information som **Prenumeration,** **Resursgrupp,** **Servernamn,** **Region** och andra fält finns i dokumentationen för serverskapande.

2.  Klicka på kryssrutan för **Zonredundant hög** tillgänglighet i alternativet Tillgänglighet.

3.  Om du vill ändra standardbearbetningen och lagringen klickar du på  **Konfigurera server**.

4.  Om alternativet för hög tillgänglighet är markerat kan du inte välja nivå med hög tillgänglighet. Du kan välja **beräkningsnivåer för generell** **användning eller minnesoptimerad.**

    > [!IMPORTANT]
    > Vi stöder endast zonredundant hög tillgänglighet för prisnivån ***Generell** användning _ och _ *_Minnesoptimerad_** .

5.  Välj **Beräkningsstorlek** i listrutan.

6.  Välj **Lagringsstorlek** i GiB med skjutfältet och välj **kvarhållningsperioden för säkerhetskopiering** mellan 7 dagar och 35 dagar.   

## <a name="disable-high-availability"></a>Inaktivera hög tillgänglighet

Följ dessa steg om du vill inaktivera hög tillgänglighet för din flexibla server som redan har konfigurerats med zonredundans.

1.  I [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL flexibel server.

2.  På sidan flexibel server klickar du på **Hög tillgänglighet på** frontpanelen för att öppna sidan med hög tillgänglighet.

3.  Klicka på kryssrutan **zonredundant hög** tillgänglighet för att inaktivera alternativet och klicka **på Spara** för att spara ändringen.

4.  En bekräftelsedialogruta visas där du kan bekräfta att ha inaktiverats.

5.  Klicka **på knappen Inaktivera** hög tillgänglighet för att inaktivera hög tillgänglighet.

6.  Ett meddelande visas som visar att inaktiveringen av distributionen med hög tillgänglighet pågår.


## <a name="forced-failover"></a>Framtvingade redundans

Följ dessa steg om du vill framtända redundans från den primära till den flexibla servern i vänteläge

1.  I den [Azure Portal](https://portal.azure.com/)väljer du din befintliga Azure Database for MySQL flexibel server som har funktionen för hög tillgänglighet aktiverad.

2.  På sidan flexibel server klickar du på **Hög tillgänglighet på** frontpanelen för att öppna sidan med hög tillgänglighet.

3.  Kontrollera den **primära tillgänglighetszonen och** **tillgänglighetszonen för vänteläge**

4.  Klicka på **Framtvinga redundans** för att starta den manuella redundansproceduren. Ett popup-fönster informerar dig om den förväntade tiden för redundans beroende på den primära arbetsbelastningen och den senaste kontrollpunktens rekändhet, läser meddelandet och klickar på OK.
 
5. Ett meddelande visas som nämner att redundans pågår.

6. När redundansen till standby-servern lyckas visas ett meddelande.

7. Kontrollera den nya **primära tillgänglighetszonen** och **tillgänglighetszonen för vänteläge.**

![Framtvinga redundans](media/how-to-configure-high-availability/how-to-forced-failover.png) 

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om [affärskontinuhet](./concepts-business-continuity.md)
-   Läs mer om [zonredundant hög tillgänglighet](./concepts-high-availability.md)
