---
title: Översikt över Anslutningsprogram för hantering av IT-tjänster (ITSM)
description: Den här artikeln innehåller en översikt över Anslutningsprogram för hantering av IT-tjänster (ITSM) (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93759cf239a2e7ef79c719c83299740ea3722130
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614565"
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

1. [Anslut ITSM produkter/tjänster med Anslutningsprogram för hantering av IT-tjänster (ITSM).](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
2. [Lägg till ITSMC.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#add-it-service-management-connector)
3. [Skapa en ITSM-anslutning.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-an-itsm-connection)
4. [Använd anslutningen.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#use-itsmc)

## <a name="next-steps"></a>Nästa steg

[Lägg till ITSM produkter/tjänster i Anslutningsprogram för hantering av IT-tjänster (ITSM)](./itsmc-connections.md)
