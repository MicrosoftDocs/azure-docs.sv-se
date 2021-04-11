---
title: Övervaka Azure File Sync moln nivåer | Microsoft Docs
description: Information om mått som ska användas för att övervaka dina principer för moln nivåer.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c27916afb0d199bcb32db9d43202e552a4a04f53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593143"
---
# <a name="monitor-cloud-tiering"></a>Övervaka moln nivåer
Det finns två sätt som du kan använda för att övervaka din princip för moln nivåer: bladet egenskaper för Server slut punkt och Azure Monitor.

## <a name="monitoring-via-server-endpoint"></a>Övervakning via server slut punkt

Logga in på [Azure Portal](https://portal.azure.com/)och navigera sedan till bladet **Server slut punkts egenskaper** för den server slut punkt som du vill övervaka och rulla ned till avsnittet om moln nivåer. 

![En skärm bild av avsnittet moln nivå i egenskaper för Server slut punkt.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**Utrymmes besparingar** är mängden utrymme som sparas genom att aktivera moln nivåer. Om din lokala volym storlek är tillräckligt stor för att rymma alla dina data kommer du att ha 0% utrymmes besparingar. Om du har 0% eller en låg procent andel av utrymmes besparingarna kan det tyda på att moln nivån inte är fördelaktig med din nuvarande lokala cachestorlek. 

**Antal cacheträffar** är den procent andel av filer som du öppnar i det lokala cacheminnet. Antalet träffar i cacheminnet beräknas av filer som öppnats direkt från cache/totalt antal öppna filer. En cacheträffar på 100% innebär att alla filer som du öppnade under den senaste timmen redan fanns i din lokala cache. Om målet är att minska utgången, indikerar detta att den aktuella principen fungerar bra.

> [!NOTE]
> Arbets belastningar med slumpmässiga åtkomst mönster har vanligt vis lägre träffar i cacheträffar. 

**Total storlek (Cloud)** är storleken på dina filer som synkroniserats till molnet. 

**Cachelagrad storlek (Server)** är den totala storleken på filer på servern, både hämtade och nivåer. Ibland kan den cachelagrade storleken vara större än den totala storleken på dina filer i molnet. Variabler som kluster storleken på volymen på servern kan orsaka detta. Om den cachelagrade storleken är större än du vill, bör du överväga att öka mängden ledigt utrymme på volymen. 

En **effektiv volym princip** används av Azure File Sync för att avgöra hur mycket ledigt utrymme som finns på volymen som server slut punkten är på. När det finns flera Server slut punkter på samma volym blir den mest lediga disk utrymmes principen bland Server slut punkterna den effektiva volym principen för alla Server slut punkter på volymen. Om det till exempel finns två server slut punkter på samma volym, en med 30% ledigt utrymme på volymen och en annan med 50% ledigt utrymme på volymen blir den effektiva mängden ledigt utrymme-princip för båda Server slut punkterna 50%.

Det lediga **utrymmet** på volymen är mängden ledigt utrymme som är tillgängligt på den lokala servern. Om du har högt utgående men mer ledigt utrymme på volymen innan du använder principen för ledigt utrymme på volymen kan du inaktivera din datum princip. Ett annat problem kan bero på att de filer som för närvarande är på nivå, den senast öppnade filen är större än volymens lediga utrymme kvar innan principen körs i. I det här fallet bör du överväga att öka din lokala volym storlek. 

## <a name="monitoring-via-azure-monitor"></a>Övervakning via Azure Monitor

Gå till **tjänsten Storage Sync** och välj **mått** i navigerings sidan. 

Det finns tre olika mått som du kan använda för att övervaka ditt utgångs skikt specifikt från moln nivåer:

- Återställnings storlek för moln nivå
    - Detta är den totala storleken på data som har återkallats i byte och kan användas för att identifiera hur mycket data som återkallas.
- Återställnings storlek för moln skikt per program
    - Detta är den totala storleken på data som återkallas i byte av ett program och kan användas för att identifiera vad som återkallar data.
- Återkalla data flöde för moln nivå
    - Detta mäter hur snabbt data återkallas i byte och bör användas om du har problem med prestanda. 

Om du vill ha mer information om vad du vill att diagrammen ska visa, kan du överväga att använda **Lägg till filter** och **tillämpa delning**.
 
Mer information om olika typer av mått för Azure File Sync och hur du använder dem finns i [övervaka Azure File Sync](storage-sync-files-monitoring.md).

Mer information om hur du använder mått finns i [komma igång med Azure Metrics Explorer.](../../azure-monitor/essentials/metrics-getting-started.md).

Om du vill ändra din princip för moln nivåer, se [Välj principer för moln nivåer](storage-sync-choose-cloud-tiering-policies.md).

## <a name="next-steps"></a>Nästa steg
* [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)