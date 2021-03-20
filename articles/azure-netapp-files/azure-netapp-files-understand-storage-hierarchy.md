---
title: Azure NetApp Files för lagringspool | Microsoft Docs
description: Beskriver lagringshierarkin, inklusive Azure NetApp Files-konton, kapacitetspooler och volymer.
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
ms.topic: overview
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 435d74e771a9d887c87c9d10e6b525ac77cf97e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91278336"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Lagringshierarkin för Azure NetApp Files

Innan du skapar en volym i Azure NetApp Files måste du köpa och konfigurera en pool för etablerad kapacitet.  Du måste ha ett NetApp-konto för att kunna konfigurera en kapacitetspool. När du förstår lagringshierarkin är det lättare att konfigurera och hantera Azure NetApp Files-resurser.

> [!IMPORTANT] 
> Azure NetApp Files stöder för närvarande inte migrering mellan prenumerationer.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>NetApp-konton

- Ett NetApp-konto fungerar som en administrativ gruppering av de ingående kapacitetspoolerna.  
- NetApp-kontot är inte detsamma som ditt allmänna Azure-lagringskonto. 
- Ett NetApp-konto är regionalt i omfång.   
- Du kan ha flera NetApp-konton i en region, men varje NetApp-konto är endast kopplat till en enda region.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Kapacitetspooler

Förstå hur kapacitets grupper fungerar genom att välja rätt kapacitets pool för dina lagrings behov. 

### <a name="general-rules-of-capacity-pools"></a>Allmänna regler för kapacitets grupper

- En kapacitetspool mäts genom dess etablerade kapacitet.   
    Mer information finns i [QoS-typer](#qos_types) .  
- Kapaciteten etableras via de fasta SKU:er som du köpte (exempelvis en kapacitet på 4 TiB).
- Kapacitetspoolen kan endast ha en servicenivå.  
- Varje kapacitetspool kan endast tillhör ett NetApp-konto. Du kan dock ha flera kapacitetspooler inom ett NetApp-konto.  
- Det går inte att flytta kapacitetspooler mellan NetApp-konton.   
  I [Konceptdiagram över lagringshierarki](#conceptual_diagram_of_storage_hierarchy) nedan kan exempelvis Capacity Pool 1 inte flyttas från NetApp-kontot USA, östra till NetApp-kontot USA, västra 2.  
- Det går inte att ta bort en kapacitets pool förrän alla volymer i kapacitets gruppen har tagits bort.

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><a name="qos_types"></a>QoS-typer (Quality of Service) för kapacitets grupper

QoS-typen är ett attribut för en pool för kapacitet. Azure NetApp Files innehåller två QoS-typer för kapacitets grupper. 

- *Automatiskt (eller automatiskt)* QoS-typ  

    När du skapar en pool för kapacitet är standard typen för QoS automatisk.

    I en pool med automatisk QoS-kapacitet tilldelas data flöde automatiskt till volymerna i poolen, proportionella mot storleks kvoten som tilldelats volymerna. 

    Det maximala data flöde som tilldelas en volym beror på tjänst nivå för kapacitets gruppen och volymens storleks kvot. Se [service nivåer för Azure NetApp Files](azure-netapp-files-service-levels.md) till exempel beräkning.

- <a name="manual_qos_type"></a>*Manuell* QoS-typ  

     > [!IMPORTANT] 
     > Att använda den manuella QoS-typen för en kapacitets pool kräver registrering.  Se [hantera en manuell pool för QoS-kapacitet](manage-manual-qos-capacity-pool.md).  

    Du kan välja att använda den manuella QoS-typen för en kapacitets grupp.

    I en manuell pool för QoS-kapacitet kan du tilldela kapaciteten och data flödet för en volym oberoende av varandra. Det totala data flödet för alla volymer som skapats med en manuell QoS-kapacitet begränsas av poolens totala genomflöde.  Det bestäms av kombinationen av poolens storlek och data flödet på tjänst nivå. 

    Till exempel har en 4-TiB kapacitets pool med Ultra Service Level en total data flödes kapacitet på 512 MiB/s (4 TiB x 128 MiB/s/TiB) tillgängliga för volymerna.


## <a name="volumes"></a><a name="volumes"></a>Volymer

- En volym mäts efter logisk kapacitetsförbrukning och är skalbar. 
- En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.
- En volyms data flödes förbrukning räknas mot poolens tillgängliga data flöde. Se [manuell QoS-typ](#manual_qos_type).
- Varje volym hör till endast en pool, men en pool kan innehålla flera volymer. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Konceptdiagram över lagringshierarki 
I följande exempel visar vi sambanden mellan Azure-prenumeration, NetApp-konton, kapacitetspooler och volymer.   

![Konceptdiagram över lagringshierarki](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Nästa steg

- [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Registrera dig för Azure NetApp Files](azure-netapp-files-register.md)
- [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Saker att tänka på gällande prestanda för Azure NetApp Files](azure-netapp-files-performance-considerations.md)
- [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
- [Hantera en manuell QoS-kapacitetspool](manage-manual-qos-capacity-pool.md)
