---
title: Hantera haveri beredskap med Azure NetApp Files replikering över flera regioner | Microsoft Docs
description: Beskriver hur du hanterar haveri beredskap med Azure NetApp Files replikering över flera regioner.
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
ms.openlocfilehash: 5b1c1a5216b7a1ad5b23167e776f2b0bbb0a578f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591001"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Hantera haveri beredskap med replikering över flera regioner 

En kontinuerlig replikering mellan käll-och mål volymerna (se [skapa databasreplikering](cross-region-replication-create-peering.md)) förbereder dig för en katastrof återställnings händelse. 

När en sådan händelse inträffar kan du [redundansväxla till mål volymen](#fail-over-to-destination-volume), vilket gör att klienten kan läsa och skriva till mål volymen. 

Efter haveri beredskap kan du utföra en [omsynkronisering](#resync-replication) för att växla tillbaka till käll volymen. Sedan [återupprättar du replikeringen från källa till mål](#reestablish-source-to-destination-replication) och monterar käll volymen för klienten på åtkomst. 

Detaljerna beskrivs nedan. 

## <a name="fail-over-to-destination-volume"></a>Redundansväxla till mål volymen

När du behöver aktivera mål volymen (till exempel när du vill redundansväxla till mål regionen) måste du bryta peering-replikeringen och sedan montera mål volymen.  

1. Om du vill avbryta replikerings-peering väljer du mål volymen. Klicka på **replikering** under lagrings tjänst.  

2.  Kontrol lera följande fält innan du fortsätter:  
    * Kontrol lera att speglings statusen visas som ***speglad***.   
        Försök inte att avbryta replikerings-peering om speglings status visar *oinitierad*.
    * Se till att Relations status visar ***inaktiv***.   
        Försök inte att avbryta replikerings-peering om Relations status visar *överföring*.   

    Se [Visa hälso status för replikeringsrelation](cross-region-replication-display-health-status.md). 

3.  Klicka på **Bryt peering**.  

4.  Skriv **Ja** när du uppmanas till det och klicka på knappen **Avbryt** . 

    ![Bryt peering-replikering](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  Montera mål volymen genom att följa stegen i [montera eller demontera en volym för virtuella Windows-eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).   
    Det här steget gör det möjligt för en klient att komma åt mål volymen.

## <a name="resync-volumes-after-disaster-recovery"></a><a name="resync-replication"></a>Synkronisera om volymer efter haveri beredskap

Efter haveri beredskap kan du återaktivera käll volymen genom att utföra en omsynkronisering.  Omsynkroniseringen återställer replikeringen och synkroniserar data från mål volymen till käll volymen.  

> [!IMPORTANT] 
> Omsynkroniseringen skriver över käll volym data med mål volym data.  Användar gränssnittet varnar dig om risken för data förlust. Du uppmanas att bekräfta omsynkroniseringen innan åtgärden startar.

1. Välj *käll* volymen om du vill synkronisera om replikeringen. Klicka på **replikering** under lagrings tjänst. Klicka sedan på **Synkronisera** igen.  

2. Skriv **Ja** när du uppmanas till det och klicka på **Synkronisera** igen. 
 
    ![Synkronisera om replikering](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. Övervaka käll volymens hälso status genom att följa stegen i [Visa hälso status för replikeringsrelation](cross-region-replication-display-health-status.md).   
    När käll volymens hälso status visar följande värden slutförs omsynkroniseringen och ändringar som görs på mål volymen samlas nu in på käll volymen:   

    * Speglad status: *speglad*  
    * Överförings tillstånd: *inaktiv*  

## <a name="reestablish-source-to-destination-replication"></a>Återupprätta replikering från källa till mål

När omsynkroniseringen har slutförts från målet till källan måste du bryta peering-replikeringen igen för att återupprätta replikeringen från källa till mål. Du bör också montera om käll volymen så att klienten kan komma åt den.  

1. Bryt peering-replikeringen:  
    a. Välj *mål* volym. Klicka på **replikering** under lagrings tjänst.  
    b. Kontrol lera följande fält innan du fortsätter:   
    * Kontrol lera att speglings statusen visas som ***speglad***.   
    Försök inte att avbryta replikerings-peering om speglings status visar *oinitierad*.  
    * Se till att Relations status visar ***inaktiv***.   
    Försök inte att avbryta replikerings-peering om Relations status visar *överföring*.    

        Se [Visa hälso status för replikeringsrelation](cross-region-replication-display-health-status.md). 

    c. Klicka på **Bryt peering**.   
    d. Skriv **Ja** när du uppmanas till det och klicka på knappen **Avbryt** .  

2. Synkronisera om käll volymen med mål volymen:  
    a. Välj *mål* volym. Klicka på **replikering** under lagrings tjänst. Klicka sedan på **Synkronisera** igen.   
    b. Skriv **Ja** när du uppmanas till det och klicka på knappen **Synkronisera** igen.

3. Montera om käll volymen genom att följa stegen i [montera eller demontera en volym för virtuella Windows-eller Linux-datorer](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  
    Det här steget gör det möjligt för en klient att komma åt käll volymen.

## <a name="next-steps"></a>Nästa steg  

* [Replikering mellan regioner](cross-region-replication-introduction.md)
* [Krav och överväganden för att använda replikering över flera regioner](cross-region-replication-requirements-considerations.md)
* [Visa hälsostatus för replikeringsrelation](cross-region-replication-display-health-status.md)
* [Ändra storlek på en mål volym för replikering mellan regioner](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Mått för volym replikering](azure-netapp-files-metrics.md#replication)
* [Ta bort volymreplikeringar eller volymer](cross-region-replication-delete.md)
* [Felsöka replikering mellan regioner](troubleshoot-cross-region-replication.md)

