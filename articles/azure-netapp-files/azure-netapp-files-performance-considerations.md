---
title: Prestanda överväganden för Azure NetApp Files | Microsoft Docs
description: Lär dig mer om prestanda för Azure NetApp Files, inklusive relationen mellan kvot-och data flödes gränsen och hur du dynamiskt ökar/minskar volym kvoten.
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
ms.date: 02/19/2021
ms.author: b-juche
ms.openlocfilehash: f963c87148c08a4855befc5afb79d9c5ea0f4481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713396"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Saker att tänka på gällande prestanda för Azure NetApp Files

[Data flödes gränsen](azure-netapp-files-service-levels.md) för en volym med automatisk QoS bestäms av en kombination av den kvot som tilldelats volymen och den valda Service nivån. För volymer med manuell QoS kan data flödes gränsen definieras individuellt. När du gör prestanda planer om Azure NetApp Files måste du förstå flera saker att tänka på. 

## <a name="quota-and-throughput"></a>Kvot och data flöde  

Data flödes gränsen är bara en Determinant av de faktiska prestanda som kommer att realiseras.  

Vanliga överväganden för lagrings prestanda, inklusive Läs-och skriv mixning, överförings storlek, slumpmässiga eller sekventiella mönster, och många andra faktorer bidrar till den totala prestanda som levereras.  

Det maximala empiriska data flödet som har observerats vid testning är 4 500 MiB/s.  På Premium Storage-nivån tillhandahåller en automatisk QoS-volym på 70,31 TiB en data flödes gräns som är tillräckligt hög för att uppnå den här prestanda nivån.  

Om du överväger att tilldela volym kvots belopp utöver 70,31 TiB, kan ytterligare kvot tilldelas till en volym för lagring av ytterligare data. Den tillagda kvoten kommer dock inte att resultera i en ytterligare ökning av det faktiska data flödet.  

Samma tak för empiriska data flöden gäller för volymer med manuella QoS. Maximalt data flöde kan tilldelas till en volym är 4 500 MiB/s.

## <a name="automatic-qos-volume-quota-and-throughput"></a>Automatisk volym kvot och data flöde för QoS

I det här avsnittet beskrivs kvot hantering och data flöde för volymer med den automatiska QoS-typen.

### <a name="overprovisioning-the-volume-quota"></a>Överetablera volym kvoten

Om arbets Belastningens prestanda är data flödes gräns, är det möjligt att överetablera den automatiska QoS-volymens kvot för att ange en högre data flödes nivå och uppnå högre prestanda.  

Om till exempel en automatisk QoS-volym på Premium Storage-nivån bara har 500 GiB av data men kräver 128 MiB/s av data flöde, kan du ange kvoten till 2 TiB så att data flödes nivån anges enligt detta (64 MiB/s per TB * 2 TiB = 128 MiB/s).  

Om du konsekvent överetablerar en volym för att uppnå ett högre data flöde bör du överväga att använda de manuella QoS-volymerna eller att använda en högre service nivå i stället.  I exemplet ovan kan du uppnå samma data flödes gräns med hälften av den automatiska QoS-volymens kvot genom att använda Ultra Storage-nivå i stället (128 MiB/s per TiB * 1 TiB = 128 MiB/s).

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Öka eller minska volym kvoten dynamiskt

Om dina prestanda krav är temporära i natur, eller om du har ökat prestanda behov under en viss tids period, kan du dynamiskt öka eller minska volym kvoten för att omedelbart justera data flödes gränsen.  Tänk på följande: 

* Volym kvoten kan ökas eller minskas utan att du behöver pausa IO och åtkomst till volymen avbryts eller påverkas inte.  

    Du kan justera kvoten under en aktiv I/O-transaktion mot en volym.  Observera att volym kvoten aldrig kan minskas under mängden logiska data som lagras i volymen.

* När volym kvoten ändras är motsvarande förändring i data flödes gränsen nästan momentant. 

    Ändringen avbryter eller påverkar inte volym åtkomsten eller I/O.  

* Anpassning av volym kvoten kan kräva en ändring i storleken på kapacitets gruppen.  

    Storleken på kapacitets poolen kan justeras dynamiskt och utan att volymens tillgänglighet eller I/O påverkas.

## <a name="manual-qos-volume-quota-and-throughput"></a>Manuell volym kvot och data flöde för QoS 

Om du använder manuella QoS-volymer behöver du inte överetablera volym kvoten för att uppnå ett högre data flöde eftersom data flödet kan tilldelas till varje volym oberoende av varandra. Du måste dock fortfarande se till att kapacitets gruppen är företablerad med tillräckligt med data flöde för dina prestanda behov. Data flödet i en pool för kapacitet tillhandahålls enligt dess storlek och service nivå. Mer information finns i [tjänste nivåer för Azure NetApp Files](azure-netapp-files-service-levels.md) .


## <a name="next-steps"></a>Nästa steg

- [Azure NetApp Files prestanda kalkylatorn](https://cloud.netapp.com/azure-netapp-files/tco?hs_preview=tIKQbfoF-41214739590)
- [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestandamått för Linux](performance-benchmarks-linux.md)
