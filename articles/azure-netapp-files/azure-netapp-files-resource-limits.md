---
title: Resursgränser för Azure NetApp Files | Microsoft Docs
description: Beskriver gränser för Azure NetApp Files och hur du begär ökning av resursgräns.
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
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: f023bfa2b3941f7d667f4be34a8ee8dc1ed9a9c3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750202"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Resursbegränsningar för Azure NetApp Files

Att förstå resursgränser för Azure NetApp Files hjälper dig att hantera dina volymer.

## <a name="resource-limits"></a>Resursgränser

I följande tabell beskrivs resursgränser för Azure NetApp Files:

|  Resurs  |  Standardgräns  |  Justerbar via supportbegäran  |
|----------------|---------------------|--------------------------------------|
|  Antal NetApp-konton per Azure-region och prenumeration  |  10    |  Yes   |
|  Antal kapacitetspooler per NetApp-konto   |    25     |   Yes   |
|  Antal volymer per prenumeration   |    500     |   Yes   |
|  Antal volymer per kapacitetspool     |    500   |    Yes     |
|  Antal ögonblicksbilder per volym       |    255     |    No        |
|  Antal undernät som har delegerats till Azure NetApp Files (Microsoft.NetApp/volumes) per Azure-Virtual Network    |   1   |    No    |
|  Antal ip-adresser som används i ett VNet (inklusive direkt peer-peerade virtuella nätverk) med Azure NetApp Files   |    1000   |    No   |
|  Minsta storlek på en enskild kapacitetspool   |  4 TiB     |    No  |
|  Maximal storlek för en enskild kapacitetspool    |  500 TiB   |   No   |
|  Minsta storlek på en enskild volym    |    100 GiB    |    No    |
|  Maximal storlek på en enskild volym     |    100 TiB    |    No    |
|  Maximal storlek på en enskild fil     |    16 TiB    |    No    |    
|  Maximal storlek på katalogmetadata i en enda katalog      |    320 MB    |    No    |    
|  Maximalt antal filer ([maxfiles](#maxfiles)) per volym     |    100 miljoner    |    Yes    |    
|  Maximalt antal exportprincipregler per volym     |    5  |    No    | 
|  Minsta tilldelade dataflöde för en manuell QoS-volym     |    1 MiB/s   |    No    |    
|  Maximalt tilldelat dataflöde för en manuell QoS-volym     |    4 500 MiB/s    |    No    |    
|  Antal replikeringsdataskyddsvolymer mellan regioner (målvolymer)     |    5    |    Yes    |     

Om du vill se om en katalog närmar sig den maximala storleksgränsen för katalogmetadata (320 MB) kan du Hur gör jag för att avgöra om en katalog närmar sig [gränsen .](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size)   

Mer information finns i Vanliga [frågor och svar om kapacitetshantering.](azure-netapp-files-faqs.md#capacity-management-faqs)

## <a name="maxfiles-limits"></a>Maxfiles-gränser <a name="maxfiles"></a> 

Azure NetApp Files har en gräns som kallas *maxfiles*. Maxfiles-gränsen är antalet filer som en volym kan innehålla. Linux-filsystem refererar till gränsen som *i-odes*. Maxfiles-gränsen för en Azure NetApp Files-volym indexeras baserat på volymens storlek (kvot). Maxfiles-gränsen för en volym ökar eller minskar med en hastighet på 20 miljoner filer per TiB av den etablerade volymens storlek. 

Tjänsten justerar dynamiskt maxfiles-gränsen för en volym baserat på dess etablerade storlek. Till exempel skulle en volym som konfigurerats från början med en storlek på 1 TiB ha en maxfilgräns på 20 miljoner. Efterföljande ändringar av volymens storlek skulle resultera i en automatisk justering av maxfiles-gränsen baserat på följande regler: 

|    Volymstorlek (kvot)     |  Automatisk justering av maxfiles-gränsen    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20 miljoner     |
|    > 1 TiB men <= 2 TiB    |    40 miljoner     |
|    > 2 TiB men <= 3 TiB    |    60 miljoner     |
|    > 3 TiB men <= 4 TiB    |    80 miljoner     |
|    > 4 TiB                 |    100 miljoner    |

Om du redan har allokerat minst 4 TiB av kvoten för en volym kan du initiera en [supportbegäran](#limit_increase) för att öka maxfiles-gränsen (i-odes) till över 100 miljoner. För varje 100 miljoner filer som du ökar (eller en bråkdel av dem) måste du öka motsvarande volymkvot med 4 TiB.  Om du till exempel ökar maxfiles-gränsen från 100 miljoner filer till 200 miljoner filer (eller ett tal däremellan) måste du öka volymkvoten från 4 TiB till 8 TiB.

Du kan öka maxfiles-gränsen till 500 miljoner om din volymkvot är minst 20 TiB. <!-- ANF-11854 --> 

## <a name="request-limit-increase"></a>Ökning av begärandegräns <a name="limit_increase"></a> 

Du kan skapa en Azure-supportbegäran för att öka de justerbara gränserna från tabellen ovan. 

Från Azure Portal navigeringsplanet: 

1. Klicka **på Hjälp + support.**
2. Klicka **på + Ny supportbegäran.**
3. På fliken Grundläggande anger du följande information: 
    1. Typ av problem: **Välj Tjänst- och prenumerationsgränser (kvoter).**
    2. Prenumerationer: Välj prenumerationen för den resurs som du behöver öka kvoten för.
    3. Kvottyp: Välj **Lagring: Azure NetApp Files gränser.**
    4. Klicka **på Nästa: Lösningar**.
4. På fliken Information:
    1. I rutan Beskrivning anger du följande information för motsvarande resurstyp:

        |  Resurs  |    Överordnade resurser      |    Begärda nya gränser     |    Orsak till kvotökning       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Prenumerations-ID*   |  *Begärt nytt **maximalt kontonummer***    |  *Vilket scenario eller användningsfall efterfrågade begäran?*  |
        |  Pool    |  *Prenumerations-ID, NetApp-konto-URI*  |  *Begärd nytt högsta **poolnummer***   |  *Vilket scenario eller användningsfall efterfrågade begäran?*  |
        |  Volym  |  *Prenumerations-ID, NetApp-konto-URI, kapacitetspool-URI*   |  *Begärt nytt **maximalt volymnummer***     |  *Vilket scenario eller användningsfall efterfrågade begäran?*  |
        |  Maxfiles  |  *Prenumerations-ID, NetApp-konto-URI, kapacitetspool-URI, volym-URI*   |  *Begärt nytt **maxantal för maximalt** antal filer*     |  *Vilket scenario eller användningsfall efterfrågade begäran?*  |    
        |  Replikering av dataskyddsvolymer mellan regioner  |  *Prenumerations-ID, NetApp-målkontots URI, målkapacitetspoolens URI, netApp-källkontots URI, källkapacitetspoolens URI, källvolym-URI*   |  *Begärt nytt maximalt antal **replikeringsdataskyddsvolymer mellan regioner (målvolymer)** _     |  _What scenariot eller användningsfallet efterfrågade begäran?*  |    

    2. Ange lämplig supportmetod och ange din kontraktinformation.

    3. Klicka **på Nästa: Granska + skapa** för att skapa begäran. 


## <a name="next-steps"></a>Nästa steg  

- [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Kostnadsmodell för Azure NetApp Files](azure-netapp-files-cost-model.md)
