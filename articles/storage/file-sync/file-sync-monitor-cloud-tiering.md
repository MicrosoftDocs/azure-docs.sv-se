---
title: Övervaka Azure File Sync av molnnivåindelad | Microsoft Docs
description: Information om mått som ska användas för att övervaka dina principer för molnnivåindelad lagring.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cbdde16ff214077d7fe4814c8841d805fe05c37c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797118"
---
# <a name="monitor-cloud-tiering"></a>Övervaka molnnivåindelad
Det finns två sätt att övervaka din princip för molnnivåindelad lagring: bladet egenskaper för serverslutpunkt och Azure Monitor.

## <a name="monitoring-via-server-endpoint"></a>Övervakning via serverslutpunkt

Logga in på [Azure Portal](https://portal.azure.com/)och gå sedan till bladet egenskaper för **serverslutpunkt** för den serverslutpunkt som du vill övervaka och rulla ned till avsnittet för molnnivåindelad lagring. 

![En skärmbild av avsnittet om molnnivåindelad lagring i egenskaperna för serverslutpunkten.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**Utrymmesbesparingar** är mängden utrymme som sparas genom att aktivera molnnivåindelad lagring. Om din lokala volymstorlek är tillräckligt stor för att innehålla alla dina data har du 0 % utrymmesbesparingar. Om du har 0 % eller en låg procentandel utrymmesbesparingar kan det tyda på att molnnivåindelad lagring inte är fördelaktig med din aktuella lokala cachestorlek. 

**Träfffrekvens för cache** är procentandelen filer som du öppnar i din lokala cache. Träfffrekvensen för cache beräknas av filer som öppnas direkt från cacheminnet/det totala antalet öppna filer. En cacheträff på 100 % innebär att alla filer som du öppnade under den senaste timmen redan fanns i din lokala cache. Om målet är att minska utgående, skulle detta indikera att din aktuella princip fungerar bra.

> [!NOTE]
> Arbetsbelastningar med slumpmässiga åtkomstmönster har vanligtvis lägre träfffrekvens för cache. 

**Total storlek (moln)** är storleken på dina filer som synkroniserades till molnet. 

**Cachelagrad storlek (server)** är den totala storleken på filerna på servern, både nedladdade och nivåindelade. Ibland kan den cachelagrade storleken vara större än den totala storleken på dina filer i molnet. Variabler som klusterstorleken på volymen på servern kan orsaka detta. Om den cachelagrade storleken är större än du vill kan du överväga att öka volymprincipen för ledigt utrymme. 

**Gällande volymprincip** används av Azure File Sync för att avgöra hur mycket ledigt utrymme som ska vara kvar på volymen som serverslutpunkten är på. När det finns flera serverslutpunkter på samma volym blir principen för högsta mängd ledigt utrymme mellan serverslutpunkterna den gällande volymprincipen för alla serverslutpunkter på volymen. Om det till exempel finns två serverslutpunkter på samma volym, en med 30 % ledigt utrymme och en annan med 50 % ledigt utrymme, blir den effektiva principen för ledigt volymutrymme för båda serverslutpunkterna 50 %.

**Aktuellt ledigt utrymme på volymen** är det lediga volymutrymme som för närvarande är tillgängligt på den lokala servern. Om du har ett stort utgående utrymme men mer ledigt utrymme innan principen för ledigt utrymme på volymen börjar användas bör du överväga att inaktivera datumprincipen. Ett annat problem kan vara att de filer som för närvarande är nivåindelade är större än mängden ledigt utrymme som återstår innan principen börjar användas. I det här fallet bör du överväga att öka din lokala volymstorlek. 

## <a name="monitoring-via-azure-monitor"></a>Övervakning via Azure Monitor

Gå till tjänsten **för synkronisering av lagring** och välj Mått **i** sidonavigeringen. 

Det finns tre olika mått som du kan använda för att övervaka utgående data specifikt från molnnivåindelad lagring:

- Storlek för återkallande av molnnivåinkallning
    - Det här är den totala storleken på de data som återkallas i byte och kan användas för att identifiera hur mycket data som återkallas.
- Storlek på återkallande av molnnivå efter program
    - Det här är den totala storleken på data som återkallas i byte av ett program och kan användas för att identifiera vad som återkallar data.
- Dataflöde för återkallande av molnnivåinkallning
    - Detta mäter hur snabbt data återkallas i byte och bör användas om du har problem med prestanda. 

Om du vill veta mer om vad du vill att graferna ska visa kan du använda **Lägg till filter** och Tillämpa **delning.**
 
Mer information om de olika typerna av mått för Azure File Sync och hur du använder dem finns i [Övervaka Azure File Sync](file-sync-monitoring.md).

Mer information om hur du använder mått finns i [Komma igång med Azure Metrics Explorer.](../../azure-monitor/essentials/metrics-getting-started.md).

Om du vill ändra din princip för molnnivåindelad kan du se [Välj principer för molnnivåindelad lagring.](file-sync-choose-cloud-tiering-policies.md)

## <a name="next-steps"></a>Nästa steg

* [Planera för distribution av Azure File Sync](file-sync-planning.md)