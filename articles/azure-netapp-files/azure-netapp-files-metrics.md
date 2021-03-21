---
title: Mått för Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files tillhandahåller mått för allokerat lagrings utrymme, faktisk lagrings användning, volym-IOPS och latens. Använd dessa mått för att förstå användning och prestanda.
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
ms.date: 12/04/2020
ms.author: b-juche
ms.openlocfilehash: a17e6cc0479cf8ff2306736994a369d9e44dfdda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96745952"
---
# <a name="metrics-for-azure-netapp-files"></a>Mått för Azure NetApp Files

Azure NetApp Files tillhandahåller mått för allokerat lagrings utrymme, faktisk lagrings användning, volym-IOPS och latens. Genom att analysera dessa mått kan du få en bättre förståelse för användnings mönstret och volym prestandan för dina NetApp-konton.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Användnings statistik för kapacitets grupper

- *Allokerad storlek för pool*   
    Poolens allokerade storlek.

- *Pool allokerad till volym storlek*  
    Summan av volym kvoten (GiB) i en specifik kapacitets grupp (det vill säga summan av volymernas etablerade storlekar i kapacitets gruppen).  
    Den här storleken är den storlek som du valde när du skapade volymen.  

- *Förbrukad pool storlek*  
    Det totala logiska utrymmet (GiB) som används för volymer i en kapacitets grupp.  

- *Total ögonblicks bild storlek för poolen*    
    Summan av ögonblicks bild storleken från alla volymer i poolen.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Användnings statistik för volymer

- *Förbrukad storlek för procent volym*    
    Procent andelen av den förbrukade volymen, inklusive ögonblicks bilder.  
- *Allokerad volym storlek*   
    En volyms allokerade storlek
- *Volym kvots storlek*    
    Kvot storlek (GiB) som volymen är etablerad med.   
- *Storlek på förbrukad volym*   
    Den logiska storleken på volymen (använda byte).  
    Den här storleken inkluderar det logiska utrymmet som används av aktiva fil system och ögonblicks bilder.  
- *Storlek på volym ögonblicks bild*   
   Storleken på alla ögonblicks bilder i en volym.  

## <a name="performance-metrics-for-volumes"></a>Prestanda mått för volymer

- *Genomsnittlig Läs fördröjning*   
    Genomsnittlig tid för läsningar från volymen i millisekunder.
- *Genomsnittlig Skriv fördröjning*   
    Genomsnittlig tid för skrivningar från volymen i millisekunder.
- *Läs IOPS*   
    Antalet läsningar till volymen per sekund.
- *Skriv IOPS*   
    Antalet skrivningar till volymen per sekund.
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>Mått för volym replikering

> [!NOTE] 
> * Nätverks överförings storleken (till exempel *volym replikeringens totala överförings* mått) kan skilja sig från käll-eller mål volymerna för en replikering mellan regioner. Det här beteendet är ett resultat av en effektiv replikeringstopologi som används för att minimera nätverks överförings kostnaden.
> * Mått för volym replikering är för närvarande ifyllt för replikeringens mål volymer och inte källan för replikeringsrelationen.

- *Är Volume Replication-status felfri*   
    Villkoret för replikeringsrelationen. Ett felfritt tillstånd betecknas av `1` . Ett ohälsosamt tillstånd betecknas av `0` .

- *Överför volym replikering*    
    Anger om status för Volume Replication är ' transfer '. 
 
- *Fördröjning för Volume Replication*   
    Hur lång tid i sekunder som data på speglingen lags bakom källan. 

- *Varaktighet för senaste överföring av Volume Replication*   
    Hur lång tid i sekunder det tog för den senaste överföringen att slutföras. 

- *Senaste överförings storlek för Volume Replication*    
    Det totala antalet byte som överförs som en del av den senaste överföringen. 

- *Diskens replikerings förlopp*    
    Den totala mängden data som överförts för den aktuella överförings åtgärden. 

- *Överföring av Volume Replication totalt*   
    Ackumulerade byte som överförs för relationen. 

## <a name="next-steps"></a>Nästa steg

* [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
* [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
