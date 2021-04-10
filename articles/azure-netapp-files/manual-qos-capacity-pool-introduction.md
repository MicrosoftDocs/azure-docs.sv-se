---
title: Pool för manuell QoS-kapacitet för Azure NetApp Files | Microsoft Docs
description: Innehåller en introduktion till poolen för manuella QoS-kapaciteter och referenser för ytterligare information.
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
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 13acee8b21adf946192544afcea17b4a8d9b9ec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581133"
---
# <a name="manual-qos-capacity-pool"></a>Manuell QoS-kapacitetspool

Den här artikeln innehåller en introduktion till funktionerna för manuell QoS-kapacitet (Quality of Service).

## <a name="how-manual-qos-differs-from-auto-qos"></a>Hur manuell QoS skiljer sig från automatisk QoS

[QoS-typen](azure-netapp-files-understand-storage-hierarchy.md#qos_types) är ett attribut för en pool för kapacitet. Azure NetApp Files tillhandahåller två QoS-typer av kapacitets grupper – automatisk (standard) och manuell.  

I en *manuell* pool för QoS-kapacitet kan du tilldela kapaciteten och data flödet för en volym oberoende av varandra. För lägsta och högsta data flödes nivåer, se [resurs gränser för Azure NetApp Files](azure-netapp-files-resource-limits.md#resource-limits). Det totala data flödet för alla volymer som skapats med en manuell QoS-kapacitet begränsas av poolens totala genomflöde. Det bestäms av kombinationen av poolens storlek och data flödet på tjänst nivå. 

I en pool med *Automatisk* QoS-kapacitet tilldelas data flöde automatiskt till volymerna i poolen, proportionella mot storleks kvoten som tilldelats volymerna.  

Se [Storage-hierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) och [prestanda överväganden för Azure NetApp Files](azure-netapp-files-performance-considerations.md) för överväganden om QoS-typer.

## <a name="example-of-using-manual-qos"></a>Exempel på användning av manuell QoS

När du använder en manuell pool för QoS-kapacitet med, till exempel ett SAP HANA system, en Oracle-databas eller andra arbets belastningar som kräver flera volymer, kan kapacitets gruppen användas för att skapa dessa program volymer.  Varje volym kan ge en individuell storlek och genom strömning för att uppfylla program kraven.  Se [exempel på data flödes gränser för volymer i en manuell pool för QoS-kapacitet](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool) för mer information om fördelarna.  

## <a name="how-to-specify-the-manual-qos-type"></a>Ange den manuella QoS-typen

När du [skapar en pool för kapacitet](azure-netapp-files-set-up-capacity-pool.md)kan du ange för kapacitets poolen att använda den manuella QoS-typen.  Du kan också [ändra en befintlig kapacitets uppsättning](manage-manual-qos-capacity-pool.md#change-to-qos) så att den använder den manuella QoS-typen. 

Att ställa in kapacitets typen till manuell QoS är en permanent ändring. Du kan inte konvertera ett manuellt kapacitets verktyg för QoS-typ till en pool med automatisk QoS-kapacitet. 

Om du använder den manuella QoS-typen måste du [Registrera funktionen](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Nästa steg

* [Hantera en manuell QoS-kapacitetspool](manage-manual-qos-capacity-pool.md)
* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
* [Lagringspool](azure-netapp-files-understand-storage-hierarchy.md) 
* [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Saker att tänka på gällande prestanda för Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Kostnadsmodell för Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Skapa en NFS-volym](azure-netapp-files-create-volumes.md)
* [Skapa en SMB-volym](azure-netapp-files-create-volumes-smb.md)
* [Skapa en volym med dubbla protokoll](create-volumes-dual-protocol.md)
* [Mått för Azure NetApp Files](azure-netapp-files-metrics.md)
* [Felsöka problem med kapacitetspooler](troubleshoot-capacity-pools.md)
