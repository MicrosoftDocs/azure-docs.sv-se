---
title: Resurs gränser för Azure NetApp Files | Microsoft Docs
description: Beskriver gränser för Azure NetApp Files resurser och hur du begär ökning av resurs gränsen.
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
ms.date: 01/29/2021
ms.author: b-juche
ms.openlocfilehash: c82e834c0af3737c1e5ef19c7aa789b94d87f6d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99095399"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Resursbegränsningar för Azure NetApp Files

Att förstå resurs gränser för Azure NetApp Files hjälper dig att hantera dina volymer.

## <a name="resource-limits"></a>Resursgränser

I följande tabell beskrivs resurs gränser för Azure NetApp Files:

|  Resurs  |  Standardgräns  |  Justerbar via supportbegäran  |
|----------------|---------------------|--------------------------------------|
|  Antal NetApp-konton per Azure-region per prenumeration  |  10    |  Ja   |
|  Antal kapacitets pooler per NetApp-konto   |    25     |   Ja   |
|  Antal volymer per prenumeration   |    500     |   Ja   |
|  Antal volymer per kapacitets grupp     |    500   |    Ja     |
|  Antal ögonblicks bilder per volym       |    255     |    Inga        |
|  Antal undernät som har delegerats till Azure NetApp Files (Microsoft. NetApp/Volumes) per Azure-Virtual Network    |   1   |    Inga    |
|  Antal använda IP-adresser i ett VNet (inklusive direkt peer-virtuella nätverk) med Azure NetApp Files   |    1000   |    Inga   |
|  Minsta storlek på en pool med enskild kapacitet   |  4 TiB     |    Inga  |
|  Maximal storlek för en pool med enskild kapacitet    |  500 TiB   |   Inga   |
|  Minsta storlek på en enskild volym    |    100 GiB    |    Inga    |
|  Maximal storlek på en enskild volym     |    100 TiB    |    Inga    |
|  Maximal storlek för en enskild fil     |    16 TiB    |    Inga    |    
|  Maximal storlek på katalogens metadata i en enskild katalog      |    320 MB    |    Inga    |    
|  Maximalt antal filer ([maxfiles](#maxfiles)) per volym     |    100 000 000    |    Ja    |    
|  Lägsta tilldelade data flöde för en manuell QoS-volym     |    1 MiB/s   |    Inga    |    
|  Maximalt kopplat data flöde för en manuell QoS-volym     |    4 500 MiB/s    |    Inga    |    
|  Antal data skydds volymer för replikering mellan regioner (mål volymer)     |    5    |    Ja    |     

Om du vill se om en katalog närmar sig den maximala storleks gränsen för katalogens metadata (320 MB), se [Hur gör jag för att ta reda på om en katalog närmar sig gräns storleken](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size).   

Mer information finns i [vanliga frågor och svar om kapacitets hantering](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="maxfiles-limits"></a>Maxfiles-gränser <a name="maxfiles"></a> 

Azure NetApp Files volymer har en gräns som kallas *maxfiles*. Gränsen för maxfiles är antalet filer som en volym kan innehålla. Maxfiles-gränsen för en Azure NetApp Files volym indexeras baserat på volymens storlek (kvot). Maxfiles-gränsen för en volym ökar eller minskar med hastigheten på 20 000 000 filer per TiB med etablerad volym storlek. 

Tjänsten justerar dynamiskt maxfiles-gränsen för en volym baserat på dess etablerade storlek. Till exempel skulle en volym som har kon figurer ATS ursprungligen med en storlek på 1 TiB ha en maxfiles gräns på 20 000 000. Efterföljande ändringar av volymens storlek skulle leda till automatisk omanpassning av maxfiles-gränsen baserat på följande regler: 

|    Volym storlek (kvot)     |  Automatisk justering av maxfiles-gränsen    |
|----------------------------|-------------------|
|    <= 1 TiB                |    20 000 000     |
|    > 1 TiB men <= 2 TiB    |    40 000 000     |
|    > 2 TiB men <= 3 TiB    |    60 000 000     |
|    > 3 TiB men <= 4 TiB    |    80 000 000     |
|    > 4 TiB                 |    100 000 000    |

Om du redan har tilldelat minst 4 TiB av kvoten för en volym kan du initiera en [support förfrågan](#limit_increase) för att öka maxfiles-gränsen bortom 100 000 000. För varje 100 000 000-filer som du ökar (eller en del av) måste du öka den motsvarande volym kvoten med 4 TiB.  Om du till exempel ökar maxfiles-gränsen från 100 000 000-filer till 200 000 000-filer (eller ett värde mellan) måste du öka volym kvoten från 4 TiB till 8 TiB.

## <a name="request-limit-increase"></a>Begär gräns ökning <a name="limit_increase"></a> 

Du kan skapa en support förfrågan för Azure för att öka de justerbara gränserna från tabellen ovan. 

Från Azure Portal navigerings plan: 

1. Klicka på **Hjälp + Support**.
2. Klicka på **+ ny supportbegäran**.
3. Ange följande information på fliken grundläggande: 
    1. Typ av problem: Välj **begränsningar för tjänsten och prenumerationen (kvoter)**.
    2. Prenumerationer: Välj prenumerationen för den resurs du behöver för att öka kvoten.
    3. Kvot typ: Välj **lagring: Azure NetApp Files gränser**.
    4. Klicka på **Nästa: lösningar**.
4. På fliken information:
    1. I rutan Beskrivning anger du följande information för motsvarande resurs typ:

        |  Resurs  |    Överordnade resurser      |    Begärda nya gränser     |    Orsak till kvotökning       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Prenumerations-ID*   |  *Begärt nytt maximalt **konto** nummer*    |  *Vilket scenario eller vilka användnings fall ber begäran?*  |
        |  Pool    |  *Prenumerations-ID, NetApp-konto-URI*  |  *Begärt nytt nummer för högsta **pool***   |  *Vilket scenario eller vilka användnings fall ber begäran?*  |
        |  Volym  |  *Prenumerations-ID, NetApp-konto-URI, URI för kapacitets pool*   |  *Begärt nytt maximalt **volym** nummer*     |  *Vilket scenario eller vilka användnings fall ber begäran?*  |
        |  Maxfiles  |  *Prenumerations-ID, NetApp-konto-URI, URI för kapacitets pool, volym-URI*   |  *Begärt nytt maximalt **maxfiles** -nummer*     |  *Vilket scenario eller vilka användnings fall ber begäran?*  |    
        |  Replikering av data skydds volymer över flera regioner  |  *Prenumerations-ID, mål NetApp konto-URI, URI för mål kapacitets grupp, käll NetApp konto-URI, URI för käll kapacitets pool, käll volym-URI*   |  * Begärt nytt maximalt antal **data skydds volymer för replikering mellan regioner (mål volymer)** _     |  _What scenariot eller användnings fallet uppmanas förfrågan? *  |    

    2. Ange lämplig support metod och ange din kontrakts information.

    3. Klicka på **Nästa: granska + skapa** för att skapa begäran. 


## <a name="next-steps"></a>Nästa steg  

- [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Kostnadsmodell för Azure NetApp Files](azure-netapp-files-cost-model.md)
