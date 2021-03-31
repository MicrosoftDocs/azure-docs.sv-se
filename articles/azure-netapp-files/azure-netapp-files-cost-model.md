---
title: Kostnads modell för Azure NetApp Files | Microsoft Docs
description: Beskriver kostnads modellen för Azure NetApp Files för att hantera utgifter från tjänsten.
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
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 9c4eebae6909c9ef0969bc85bcb9a985db2a7c02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325614"
---
# <a name="cost-model-for-azure-netapp-files"></a>Kostnadsmodell för Azure NetApp Files 

Att förstå kostnads modellen för Azure NetApp Files hjälper dig att hantera dina utgifter från tjänsten. 

För kostnads modell som är speciell för replikering över flera regioner, se [kostnads modell för replikering över flera regioner](cross-region-replication-introduction.md#cost-model-for-cross-region-replication).

## <a name="calculation-of-capacity-consumption"></a>Beräkning av kapacitets förbrukning

Azure NetApp Files debiteras för allokerad lagrings kapacitet.  Etablerad kapacitet tilldelas genom att kapacitets grupper skapas.  Kapacitets grupper faktureras baserat på $/provisioned-GiB/month i timmar. Den minsta storleken för en pool med enskild kapacitet är 4 TiB och kapacitets grupper kan sedan utökas i steg om 1-TiB. Volymer skapas i kapacitets grupper.  Varje volym tilldelas en kvot som avgränsar från de pooler som har allokerats. Kvoten som kan tilldelas till volym intervall från minst 100 GiB till högst 100 TiB.  

För en aktiv volym baseras kapacitets förbrukning mot kvoten på den logiska (effektiv) kapaciteten.

Om den faktiska kapacitets förbrukningen för en volym överskrider lagrings kvoten kan volymen fortsätta att växa. Skrivningar kommer fortfarande att tillåtas så länge den faktiska volym storleken är mindre än system gränsen (100 TiB).  

Den totala använda kapaciteten i en kapacitetsutnyttjande mot dess etablerade belopp är summan av antingen den tilldelade kvoten eller den faktiska förbrukningen av alla volymer i poolen: 

   ![Total Använd kapacitets beräkning](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Diagrammet nedan illustrerar dessa begrepp.  
* Vi har en pool med 4 TiB med etablerad kapacitet.  Poolen innehåller tre volymer.  
    * Volym 1 tilldelas en kvot på 2 TiB och har 800 GiB förbrukning.  
    * Volym 2 har tilldelats en kvot på 1 TiB och har 100 GiB förbrukning.  
    * Volym 3 har tilldelats en kvot på 500 GiB men har 800 GiB (överförbrukning).  
* Kapacitets gruppen mäts för 4 TiB (det etablerade beloppet).  
    3,8 TiB-kapacitet förbrukas (2 TiB och 1 TiB kvot från volymer 1 och 2, och 800 GiB av faktisk konsumtion för volym 3). Och 200 GiB-kapacitet är kvar.

   ![Kapacitets grupp med tre volymer](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Överförbrukning i kapacitets förbrukning  

När den totala använda kapaciteten för en pool överskrider den etablerade kapaciteten tillåts data skrivningar fortfarande.  Efter respitperioden (en timme) ökar Poolens storlek automatiskt i steg om 1 TiB tills den etablerade kapaciteten är större än den totala använda kapaciteten, om den använda kapaciteten för poolen fortfarande överskrider sin etablerade kapacitet.  Till exempel, i bilden ovan, om volym 3 fortsätter att växa och den faktiska förbrukningen når 1,2 TiB, kommer poolen automatiskt att ändra storlek till 5 TiB efter Grace-perioden.  Resultatet är att den etablerade poolens kapacitet (5 TiB) överskrider den använda kapaciteten (4,2 TiB).  

Även om storleken på kapacitets gruppen växer automatiskt för att möta volymens behov, minskas den automatiskt när volym storleken minskar. Om du vill stänga av kapacitets gruppen efter en volym storleks minskning (till exempel efter att data har rensats) måste du minska storleken på kapacitets gruppen _manuellt_ .

## <a name="manual-changes-of-the-pool-size"></a>Manuella ändringar av poolens storlek  

Du kan öka eller minska storleken på poolen manuellt. Följande begränsningar gäller dock:
* Lägsta och högsta gräns för tjänsten  
    Se artikeln om [resurs begränsningar](azure-netapp-files-resource-limits.md).
* En 1-TiB-ökning efter det första inköpet av 4-TiB minimalt
* En minsta fakturerings ökning på en timme
* Den etablerade poolen kan inte minskas till mindre än den totala använda kapaciteten i poolen.
* För kapacitets grupper med manuell QoS kan pool storleken bara minskas om storleken och tjänst nivån ger mer data flöde än det faktiska tilldelade data flödet för alla volymer.

## <a name="behavior-of-maximum-size-pool-overage"></a>Beteende för maximal storlek för poolen   

Den maximala storleken för en kapacitets pool som du kan skapa eller ändra storlek till är 500 TiB.  När den totala använda kapaciteten i en kapacitets pool överskrider 500 TiB inträffar följande situationer:
* Data skrivningar tillåts fortfarande (om volymen är lägre än systemets max 100 TiB).
* Efter Grace-perioden för en timme ändras poolen automatiskt i steg om 1-TiB, tills den allokerade kapaciteten för poolen överskrider den totala använda kapaciteten.
* Den ytterligare etablerade och debiterbara pool kapaciteten som överstiger 500 TiB kan inte användas för att tilldela volym kvot. Den kan också inte användas för att expandera QoS-gränser för prestanda.  
    Se [service nivåer](azure-netapp-files-service-levels.md) om prestanda begränsningar och QoS-storlek.

Diagrammet nedan illustrerar dessa begrepp:
* Vi har en kapacitets uppsättning med en Premium Storage-nivå och en 500-TiB kapacitet. Poolen innehåller nio volymer.
    * Volymerna 1 till 8 tilldelas en kvot på 60 TiB.  Den totala använda kapaciteten är 480 TiB.  
        Varje volym har en QoS-gräns på 3,75 GiB/s med data flöde (60 TiB * 64 MiB/s).  
    * Volym 9 har tilldelats en kvot på 20 TiB.  
        Volym 9 har en QoS-gräns på 1,25 GiB/s med data flöde (20 TiB * 64 MiB/s).
* Volume 9 är ett överutnyttjande scenario. Den har 25 TiB faktisk konsumtion.  
    * Efter Grace-perioden för en timme ändras kapacitets gruppen till 505 TiB.  
        Det vill säga total använt kapacitet = 8 * 60-TiB kvot för volymerna 1 till 8 och 25 TiB faktisk konsumtion för volym 9.
    * Den fakturerade kapaciteten är 505 TiB.
    * Volym kvoten för volym 9 kan inte ökas (eftersom den totala tilldelade kvoten för poolen inte får överstiga 500 TiB).
    * Ytterligare QoS-dataflöde kan inte tilldelas (eftersom det totala QoS-värdet för poolen fortfarande baseras på 500 TiB).

   ![Kapacitets grupp med nio volymer](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Kapacitets förbrukning för ögonblicks bilder 

Kapacitets förbrukningen för ögonblicks bilder i Azure NetApp Files debiteras mot kvoten för den överordnade volymen.  Därför delar den samma fakturerings takt som den kapacitets uppsättning som volymen tillhör.  Till skillnad från den aktiva volymen mäts dock förbrukningen av ögonblicks bilder baserat på den stegvisa kapaciteten som används.  Azure NetApp Files ögonblicks bilder är differentiella. Beroende på data ändrings takten använder ögonblicks bilderna ofta mycket mindre kapacitet än den logiska kapaciteten hos den aktiva volymen. Anta till exempel att du har en ögonblicks bild av en 500-GiB-volym som bara innehåller 10 GiB differentiella data. Kapaciteten som debiteras mot volym kvoten för ögonblicks bilden skulle vara 10 GiB, inte 500 GiB. 

## <a name="next-steps"></a>Nästa steg

* [Sidan Azure NetApp Files prissättning](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Kostnads modell för replikering över flera regioner](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
