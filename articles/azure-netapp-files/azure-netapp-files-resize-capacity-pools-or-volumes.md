---
title: Ändra storlek på kapacitets gruppen eller en volym för Azure NetApp Files | Microsoft Docs
description: Lär dig hur du ändrar storleken på en kapacitets uppsättning eller en volym. Om du ändrar storlek på kapacitetsutnyttjandet ändras den köpta Azure NetApp Files kapaciteten.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594486"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Ändra storlek på en kapacitetspool eller en volym
Du kan ändra storleken på en kapacitets uppsättning eller en volym om det behövs. 

## <a name="resize-the-capacity-pool"></a>Ändra storlek på kapacitets gruppen 

Du kan ändra storleken på TiB ökningar eller minskningar i en pool. Kapacitets bassängens storlek kan dock inte vara mindre än 4 TiB. Om du ändrar storlek på kapacitetsutnyttjandet ändras den köpta Azure NetApp Files kapaciteten.

1. Från bladet hantera NetApp-konton klickar du på den kapacitets uppsättning som du vill ändra storlek på. 
2. Högerklicka på namnet på mediepoolen eller klicka på "..." i slutet av poolens rad för att Visa snabb menyn. 
3. Använd snabb meny alternativen för att ändra storlek på eller ta bort kapacitets gruppen.

## <a name="resize-a-volume"></a>Ändra storlek på en volym

Du kan ändra storlek på en volym om det behövs. En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.

1. Från bladet hantera NetApp-konton klickar du på **volymer**. 
2. Högerklicka på namnet på den volym som du vill ändra storlek på eller klicka på "..." ikonen i slutet av volymens rad för att Visa snabb menyn.
3. Använd snabb meny alternativen för att ändra storlek på eller ta bort volymen.

## <a name="resize-a-cross-region-replication-destination-volume"></a>Ändra storlek på en mål volym för replikering mellan regioner 

I en replikeringsrelation [mellan regioner](cross-region-replication-introduction.md) ändras storleken på mål volymen automatiskt baserat på käll volymens storlek. Därför behöver du inte ändra storlek på mål volymen separat. Det här beteendet för automatisk storleks ändring är tillämpligt när volymerna finns i en aktiv replikeringsrelation, eller när replikeringen är bruten vid [omsynkroniseringen](cross-region-replication-manage-disaster-recovery.md#resync-replication). 

I följande tabell beskrivs hur du ändrar storlek på mål volymen baserat på [speglings status](cross-region-replication-display-health-status.md):

|  Speglings tillstånd  | Storleks ändrings beteende för mål volym |
|-|-|
| *Speglad* | När mål volymen har initierats och är redo att ta emot speglings uppdateringar ändrar storleken på käll volymen automatiskt storlek på mål volymerna. |
| *Brutit* | När du ändrar storlek på käll volymen och speglings statusen är *bruten* ändras storleken på mål volymen automatiskt med [omsynkroniseringen](cross-region-replication-manage-disaster-recovery.md#resync-replication).  |
| *Avinitierad* | När du ändrar storlek på käll volymen och speglings statusen fortfarande är *avinitierad* måste du göra en ändring av mål volymens storlek manuellt. Därför rekommenderar vi att du väntar tills initieringen har slutförts (det vill säga när speglings statusen blir *speglad*) för att ändra storlek på käll volymen. | 

> [!IMPORTANT]
> Se till att du har tillräckligt med utrymme i poolerna för kapacitet för både käll-och mål volymerna för replikering över flera regioner. När du ändrar storlek på käll volymen ändras storleken på mål volymen automatiskt. Men om den kapacitets pool som är värd för mål volymen inte har tillräckligt med utrymme, går det inte att ändra storlek på både käll-och mål volymerna.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
- [Hantera en manuell QoS-kapacitetspool](manage-manual-qos-capacity-pool.md)
- [Ändra tjänstnivå för en volym dynamiskt](dynamic-change-volume-service-level.md) 