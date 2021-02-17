---
title: Krav och överväganden för att använda Azure NetApp Files volym replikering mellan regioner | Microsoft Docs
description: Beskriver kraven och övervägandena för att använda replikeringen av volymer över flera regioner i Azure NetApp Files.
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
ms.date: 01/20/2021
ms.author: b-juche
ms.openlocfilehash: ed03e20f7a1a24d1a38e023b958959fdc6fdc326
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579337"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Krav och överväganden för att använda replikering över flera regioner 

Observera följande krav och överväganden om hur [du använder replikeringen över flera regioner](cross-region-replication-create-peering.md) i Azure NetApp Files:  

## <a name="requirements-and-considerations"></a>Krav och överväganden 

* Funktionen för replikering över flera regioner finns för närvarande i offentlig för hands version. Du måste skicka in en Waitlist-begäran för att få åtkomst till funktionen via [Azure NetApp Files över flera regioner Waitlist sändnings sida](https://aka.ms/anfcrrpreviewsignup). Vänta tills en officiell bekräftelse skickas från Azure NetApp Files-teamet innan du använder funktionen för replikering över flera regioner.
* Azure NetApp Files replikering är endast tillgänglig i vissa fasta region par. Se de [region par som stöds](cross-region-replication-introduction.md#supported-region-pairs). 
* SMB-volymer stöds tillsammans med NFS-volymer. Replikering av SMB-volymer kräver en Active Directory anslutning i käll-och mål NetApp-kontona. Målets AD-anslutning måste ha åtkomst till DNS-servrarna eller lägga till domänkontrollanter som kan nås från det delegerade under nätet i mål regionen. Mer information finns i [krav för Active Directory anslutningar](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Mål kontot måste finnas i en annan region än käll volymens region. Du kan också välja ett befintligt NetApp-konto i en annan region.  
* Mål volymen för replikeringen är skrivskyddad tills du [växlar över till mål regionen](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume) för att aktivera mål volymen för läsning och skrivning. 
* Azure NetApp Files replikering har för närvarande inte stöd för flera prenumerationer. alla replikeringar måste utföras under en enda prenumeration.
* Du kan ställa in högst fem volymer för replikering inom en enskild prenumeration per region. Du kan öppna ett support ärende för att begära en ökning av standard kvoten för fem mål volymer för replikering (per prenumeration i en region). 
* Det kan finnas en fördröjning på upp till fem minuter för gränssnittet för att avspegla en nyligen tillagd ögonblicks bild på käll volymen.  
* Det finns inte stöd för överlappande och fläkt i/ut-topologier.
* Det går inte att konfigurera volym replikering för käll volymer som skapats från ögonblicks bilder för tillfället.
* När du har konfigurerat replikering mellan regioner skapar *SnapMirror ögonblicks bilder* för att tillhandahålla referenser mellan käll volymen och mål volymen. SnapMirror-ögonblicksbilder förflyttas automatiskt när en ny skapas för varje stegvis överföring. Det går inte att ta bort SnapMirror-ögonblicksbilder förrän replikeringsrelationen och volymen har tagits bort. 
* Du kan ta bort manuella ögonblicks bilder på käll volymen för en replikeringsrelation när replikeringsrelationen är aktiv eller bruten, och även efter att replikeringsrelationen har tagits bort. Du kan inte ta bort manuella ögonblicks bilder för mål volymen förrän replikeringsrelationen är bruten.
* Det går inte att återgå till en ögonblicks bild som togs innan mål volymen för replikering skapades.

## <a name="next-steps"></a>Nästa steg
* [Skapa volymreplikering](cross-region-replication-create-peering.md)
* [Visa hälsostatus för replikeringsrelation](cross-region-replication-display-health-status.md)
* [Hantera haveriberedskap](cross-region-replication-manage-disaster-recovery.md)
* [Mått för volym replikering](azure-netapp-files-metrics.md#replication)
* [Ta bort volymreplikeringar eller volymer](cross-region-replication-delete.md)
* [Felsöka replikering mellan regioner](troubleshoot-cross-region-replication.md)


