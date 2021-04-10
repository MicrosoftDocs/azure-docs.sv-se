---
title: 'Azure-peering-tjänst: så här mäter du telemetri för anslutningar '
description: I den här självstudien lär du dig att mäta anslutnings telemetri.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 6bd00b314c94e1edf094599bdb43c70376a18550
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065386"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>Självstudie: mäta peering-tjänstens anslutnings telemetri

 I den här självstudien får du lära dig hur du mäter telemetri för anslutningar till peering-tjänsten.
 
 Telemetri för anslutning ger insikter som samlats in för anslutningen mellan kundens plats och Microsoft-nätverket. I den här artikeln får du lära dig hur du visar svars tids rapporten för en speciell anslutning till Azure peering-tjänsten. 

Om du vill mäta peering-tjänstens anslutnings telemetri måste du registrera en peering service-anslutning i Azure Portal. Information om hur du registrerar en anslutning finns i [Registrera en peering-tjänst anslutning-Azure Portal](azure-portal.md).


## <a name="view-a-latency-report"></a>Visa en latens-rapport

Följ dessa steg om du vill visa en latens-rapport för en speciell peering service-anslutning.

1. Välj **alla resurser** i den vänstra rutan och välj peering service-anslutningen. Välj **Öppna** under **prefix**. 

   ![Välj peering service-anslutning](./media/peering-service-measure/peering-service-measure-menu.png)

2. En rapport sida med svars tid för alla prefix som är kopplade till peering service-anslutningen visas. 

      ![Rapport sida för svars tid](./media/peering-service-measure/peering-service-latency-report.png)

3. Som standard uppdateras rapporten för varje timme som visas på den här sidan. Om du vill visa rapporten för olika tids linjer väljer du lämpligt alternativ från **Visa data för senaste**. 

4. Om du vill visa händelser för ett särskilt prefix väljer du prefixets namn och väljer **prefix-händelser** i det vänstra fönstret. De händelser som samlas in visas.


   ![Prefix-händelser](./media/peering-service-measure/peering-service-prefix-event.png)

 Några av de möjliga händelser som samlas in i listan **prefixet prefix** visas här.

| **Prefix-händelser** | **Händelse typ**|**REA soning**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |Information|Ett prefix mottogs|
| PrefixWithdrawalEvent|Varning| Avtagning av prefix togs emot |
| PrefixBackupRouteAnnouncementEvent |Information|Prefix för säkerhets kopiering av prefix mottogs |
| PrefixBackupRouteWithdrawalEvent|Varning|Det gick inte att återkalla prefixet för säkerhets kopierings flödet |
| PrefixActivePath |Information| Aktuellt prefix aktiv väg   |
| PrefixBackupPath | Information|Aktuell säkerhets kopierings väg för prefix   |
| PrefixOriginAsChangeEvent|Kritiskt| Det exakta prefixet togs emot med ett annat ursprung autonomt system nummer (för aktiv väg)| 
| PrefixBackupRouteOriginAsChangeEvent  | Fel|Prefix som tagits emot med ett annat ursprung autonomt system nummer (för säkerhets kopierings väg)  |

## <a name="next-steps"></a>Nästa steg

- Mer information om peering service-anslutning finns i [peering service-anslutning](connection.md).
- Om du vill veta mer om telemetri för peering service-anslutning läser du [telemetri för peering service-anslutning](connection-telemetry.md).