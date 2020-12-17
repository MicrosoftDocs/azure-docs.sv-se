---
title: Översikt över Anslutningsprogram för hantering av IT-tjänster (ITSM)
description: Den här artikeln innehåller en översikt över Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: aaaeb23ef24f0d8a0fa4b38139fed57cda6fa63d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657047"
---
# <a name="it-service-management-connector-overview"></a>Översikt över Anslutningsprogram för hantering av IT-tjänster (ITSM)

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC) gör det möjligt att ansluta Azure till en ITSM-produkt eller-tjänst (supported IT Service Management).

Azure-tjänster som Azure Log Analytics och Azure Monitor innehåller verktyg för att identifiera, analysera och felsöka problem med dina Azure-och icke-Azure-resurser. Men arbets objekt som rör ett problem finns vanligt vis i en ITSM-produkt eller-tjänst. ITSMC tillhandahåller en dubbelriktad anslutning mellan Azure-och ITSM-verktyg som hjälper dig att lösa problem snabbare.

## <a name="configuration-steps"></a>Konfigurationssteg

ITSMC stöder anslutningar med följande ITSM-verktyg:

-   ServiceNow
-   System Center Service Manager
-   Styrka
-   Cherwell

   >[!NOTE]
> Vi föreslår våra Cherwell och förstyrker kunder att använda [webhook-åtgärder](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) för att Cherwell och bevisa slut punkt som en annan lösning på integrationen.

Med ITSMC kan du:

-  Skapa arbets objekt i ITSM-verktyget baserat på dina Azure-aviseringar (mått aviseringar, aktivitets logg aviseringar och Log Analytics aviseringar).
-  Du kan också synkronisera dina incidenter och ändra begär ande data från ITSM-verktyget till en Azure Log Analytics-arbetsyta.

Information om juridiska villkor och sekretess policyn finns i [Microsofts sekretess policy](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Du kan börja använda ITSMC genom att utföra följande steg:

1. [Anslut ITSM produkter/tjänster med Anslutningsprogram för hantering av IT-tjänster (ITSM).](./itsmc-connections.md)
1. [Lägg till ITSMC.](/.itsmc-definition.md#add-it-service-management-connector)
1. [Skapa en ITSM-anslutning.](./itsmc-definition.md#create-an-itsm-connection)
1. [Använd anslutningen.](./itsmc-definition.md#use-itsmc)

## <a name="next-steps"></a>Nästa steg

[Lägg till ITSM produkter/tjänster i anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-connections.md) 
 [Lägg till ITSM-anslutningsprogram](./itsmc-definition.md) 
 [Felsöka problem i ITSM-anslutningsprogram](./itsmc-resync-servicenow.md)