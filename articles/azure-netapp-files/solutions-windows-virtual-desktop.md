---
title: Använda Windows Virtual Desktop med Azure NetApp Files | Microsoft Docs
description: Innehåller tips om bästa praxis och exempel på hur du distribuerar virtuella Windows-datorer med Azure NetApp Files.
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
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: a003090fd610f2ac75895cccbf97750adbd4cfcd
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258323"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>Fördelar med att använda Azure NetApp Files med Windows Virtual Desktop 

Den här artikeln innehåller råd om hur du distribuerar Windows Virtual Desktop (WVD) med Azure NetApp Files.

Azure NetApp Files är en fil lagrings tjänst med hög prestanda från Azure. Det kan ge upp till 450 000 IOPS-och under millisekunder-svars tid, vilket kan ge stöd för extremt stor skala av Windows-distributioner för virtuella datorer. Du kan justera bandbredden och ändra service nivån för dina Azure NetApp Files volymer på begäran nästan omedelbart utan att pausa IO samtidigt som data Plans åtkomsten behålls. Med den här funktionen kan du enkelt optimera din WVD distributions skala för kostnad. Du kan också skapa utrymmes effektiva ögonblicks bilder av en viss tidpunkt utan att påverka volymens prestanda. Den här funktionen gör det möjligt för dig att återställa enskilda [FSLogix användar profil behållare](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile) via en kopia från `~snapshot` katalogen eller att omedelbart återställa hela volymen på en gång via volym återställnings funktionen.  Med upp till 255 (rotations) ögonblicks bilder på plats för att skydda en volym från data förlust eller skada, har administratörer många chans att ångra vad som har gjorts.

## <a name="sample-blueprints"></a>Exempel ritningar

Följande exempel ritningar visar integrationen av virtuella Windows-datorer med Azure NetApp Files. I ett sammanslaget Skriv bords scenario dirigeras användare till den bästa tillgängliga sessionen ( [Bredd-första läge](https://docs.microsoft.com/azure/virtual-desktop/host-pool-load-balancing#breadth-first-load-balancing-method)) i poolen med hjälp av [virtuella datorer med flera sessioner](https://docs.microsoft.com/azure/virtual-desktop/windows-10-multisession-faq#what-is-windows-10-enterprise-multi-session). Å andra sidan är personliga datorer reserverade för scenarier där varje användare har sin egen virtuella dator.

### <a name="pooled-desktop-scenario"></a>Scenario med poolen skriv bord

I det poolbaserade scenariot [rekommenderar](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) Windows Virtual Desktop-teamet följande vägledning genom antal användare till vCPU. Observera att ingen virtuell dator storlek anges i den här rekommendationen.

|     Arbets belastnings typ     |     Ljus    |     Medium    |     Tung    |
|-----------------------|--------------|---------------|--------------|
|     Användare per vCPU    |     6        |     4         |     2        |


Den här rekommendationen bekräftas av ett 500-användare LoginVSI-test, som loggar cirka 62 "kunskap/medium-användare" på varje D16as_V4 virtuell dator. 

Som exempel, vid 62 användare per D16as_V4 virtuell dator, kan Azure NetApp Files enkelt stödja 60 000-användare per miljö. Testning för att utvärdera den övre gränsen för den D32as_v4 virtuella datorn pågår. Om WVD-användaren per vCPU-rekommendation är sann för D32as_v4, skulle fler än 120 000 användare få plats inom 1 000 Virtual Machines innan [1 000](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)broaching, vilket visas i följande bild.  

![Scenario för pool med virtuella Windows-datorer i virtuella datorer](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>Scenario för personliga datorer 

I ett eget Skriv bords scenario visar följande bild den generella arkitektur rekommendationen. Användare mappas till vissa Skriv bords poddar och varje Pod har precis under 1 000 virtuella datorer, vilket lämnar plats för IP-adresser som sprids från det virtuella hanterings nätverket. Azure NetApp Files enkelt kan hantera 900 + personliga skriv bord per VNet-VNet med en session och det faktiska antalet virtuella datorer som är lika med 1 000 minus antalet hanterings värdar som finns i hubbens VNet. Om fler personliga skriv bord behövs är det enkelt att lägga till fler poddar (värdar och virtuella nätverk), som du ser i följande bild. 

![Det personliga Skriv bords scenariot för Windows Virtual Desktop](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

När du skapar en POD-baserad arkitektur som detta, är det viktigt att användarna alltid hittar sina användar profiler när de tilldelar användare till rätt Pod vid inloggningen. 

## <a name="next-steps"></a>Nästa steg

- [Lösningsarkitekturer med Azure NetApp Files](azure-netapp-files-solution-architectures.md)
