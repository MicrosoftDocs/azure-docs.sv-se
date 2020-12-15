---
title: Felsök problem med kapacitets pooler för Azure NetApp Files | Microsoft Docs
description: Beskriver eventuella problem som kan uppstå när du hanterar kapacitets grupper och ger lösningar på problemen.
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
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: b-juche
ms.openlocfilehash: a07898692c8af70fab0d2634ed1513f0f3666a4d
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505468"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Felsöka problem med kapacitetspooler

I den här artikeln beskrivs lösningar på problem som du kan ha när du hanterar kapacitets grupper, inklusive ändrings åtgärden för poolen. 

## <a name="issues-managing-a-capacity-pool"></a>Problem med att hantera en pool för kapacitet 

|     Feltillstånd    |     Lösning    |
|-|-|
| Problem med att skapa en pool för kapacitet |  Kontrol lera att antalet kapacitets pooler inte överskrider gränsen. Se [resurs gränser för Azure NetApp Files](azure-netapp-files-resource-limits.md).  Om antalet är mindre än gränsen och du fortfarande stöter på problem, så kan du ange ett support ärende och ange poolens namn. |
| Problem med att ta bort en pool med kapacitet  |  Se till att du tar bort alla Azure NetApp Files volymer och ögonblicks bilder i prenumerationen där du försöker ta bort kapacitets gruppen. <br> Om du redan har tagit bort alla volymer och ögonblicks bilder och du fortfarande inte kan ta bort kapacitets gruppen kan det fortfarande finnas referenser till resurser utan att visas i portalen. I det här fallet, File a Support Ticket och ange att du har utfört ovanstående rekommenderade steg. |
| Det går inte att skapa eller ändra volym med `Requested throughput not available` fel | Tillgängligt data flöde för en volym bestäms av poolens storlek och tjänst nivå. Om du inte har tillräckligt med data flöde bör du öka Poolens storlek eller justera det befintliga volym data flödet. | 

## <a name="issues-when-changing-the-capacity-pool-of-a-volume"></a>Problem vid ändring av poolens kapacitets enhet 

> [!IMPORTANT] 
> Den [dynamiskt ändrade Service nivån för en volym offentlig för](dynamic-change-volume-service-level.md) hands versions registrering är stoppad tills vidare meddelande.

|     Feltillstånd    |     Lösning    |
|-|-|
| Det är inte tillåtet att ändra kapacitets gruppen för en volym. | Du kanske inte har behörighet att använda den här funktionen. <br> Funktionen för att flytta en volym till en annan kapacitets pool är för närvarande en för hands version. Om du använder den här funktionen för första gången måste du registrera funktionen först och ange `-FeatureName ANFTierChange` . Se registrerings stegen i [dynamisk ändring av Service nivån för en volym](dynamic-change-volume-service-level.md). |
| Storleken på kapacitets poolen är för liten för total volym storlek. |  Felet är ett resultat av att mål kapacitets gruppen inte har den tillgängliga kapaciteten för volymen som flyttas.  <br> Öka storleken på måldomänkontrollanten eller Välj en annan pool som är större.  Se [ändra storlek på en kapacitets grupp eller en volym](azure-netapp-files-resize-capacity-pools-or-volumes.md).   |
|  Det går inte att slutföra ändringen av poolen eftersom det `'{source pool name}'` redan finns en volym med namnet i mål-poolen `'{target pool name}'` | Felet beror på att det redan finns en volym med samma namn i poolen för mål kapacitet.  Välj en annan kapacitets pool som inte har en volym med samma namn.   | 

## <a name="next-steps"></a>Nästa steg  

* [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
* [Hantera en manuell QoS-kapacitetspool](manage-manual-qos-capacity-pool.md)
* [Ändra tjänstnivå för en volym dynamiskt](dynamic-change-volume-service-level.md)
* [Ändra storlek på en kapacitetspool eller en volym](azure-netapp-files-resize-capacity-pools-or-volumes.md)
