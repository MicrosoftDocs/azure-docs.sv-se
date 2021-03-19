---
title: Felsöka replikering över flera regioner för Azure NetApp Files | Microsoft Docs
description: Beskriver fel meddelanden och lösningar som kan hjälpa dig att felsöka replikeringar mellan regioner för Azure NetApp Files.
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
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: d3d944646689e9e6189b0343e8bf67c8fb0abcbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590933"
---
# <a name="troubleshoot-cross-region-replication"></a>Felsöka replikering mellan regioner

I den här artikeln beskrivs fel meddelanden och lösningar som kan hjälpa dig att felsöka replikeringar mellan regioner för Azure NetApp Files. 

## <a name="errors-creating-replication"></a>Fel när replikering skapades  

|     Felmeddelande    |     Lösning    |
|-|-|
|     `Volume {0} cannot   be used as source because it is already in replication`    |     Du kan inte skapa en replikering med en käll volym som redan finns i en datareplikerings relation.    |
|     `Peered region   '{0}' is not accepted`    |     Du försöker skapa en replikering mellan icke-peer-peer-regioner.    |
|     `RemoteVolumeResource   '{0}' of wrong type '{1}'`    |     Kontrol lera att fjär resurs-ID: t är volym resurs-ID.    |

## <a name="errors-authorizing-volume"></a>Fel vid auktorisering av volym  

|     Felmeddelande    |     Lösning    |
|-|-|
|     `Missing value   for 'AuthorizeSourceReplication'`    |     `RemoteResourceID`Saknas eller är ogiltigt från användar gränssnittet eller API-begäran (korrigera fel meddelande).    |
|     `Missing value   for 'RemoteVolumeResourceId'`    |     `RemoteResourceID`Saknas eller är ogiltigt från användar gränssnittet eller API-begäran.    |
|     `Data   Protection volume not found for RemoteVolumeResourceId: {remoteResourceId}`    |     Verifiera om   `RemoteResourceID` är korrekt eller finns för användaren.    |
|     `Remote volume   '{0}' is not configured for replication`    |     Mål volymen är inte en data skydds volym.    |
|     `Remote volume   '{0}' does not have source volume '{1}' as RemoteVolumeResourceId`    |     Data skydds volymen har inte denna käll volym i sitt fjärrresurs-ID (fel käll-ID har angetts).    |
|     `The   destination volume replication creation failed (message: {0})`    |     Det här felet indikerar ett Server fel. Kontakta supporten.    |

## <a name="errors-deleting-replication"></a>Fel vid borttagning av replikering

|     Felmeddelande    |     Lösning    |
|-|-|
|     `Replication   cannot be deleted, mirror state needs to be in status: Broken before deleting`    |     Kontrol lera att replikeringen har brutits eller inte är initierad och inaktiv (det gick inte att initiera).    |
|     `Cannot delete   source replication`    |     Det är inte tillåtet att ta bort replikeringen från käll sidan. Se till att du tar bort replikeringen från mål sidan.    |

## <a name="errors-deleting-volume"></a>Fel vid borttagning av volym

|     Felmeddelande    |     Lösning    |
|-|-|
| `Volume is a member of an active volume replication relationship`  |  Ta bort replikeringen innan du tar bort volymen. Se [ta bort replikeringar](cross-region-replication-delete.md). Den här åtgärden kräver att du bryter peering innan du tar bort replikeringen för volymen. |
| `Volume with replication cannot be deleted`  |  Ta bort replikeringen innan du tar bort volymen. Se [ta bort replikeringar](cross-region-replication-delete.md). Den här åtgärden kräver att du bryter peering innan du tar bort replikeringen för volymen. 

## <a name="errors-resyncing-volume"></a>Fel vid omsynkronisering av volym

|     Felmeddelande    |     Lösning    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     Verifiera att replikeringstrafiken har statusen "bruten".    |

## <a name="errors-deleting-snapshot"></a>Fel vid borttagning av ögonblicks bild 

|     Felmeddelande    |     Lösning    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     Kontrol lera att du har förstört volymens replikering om du vill ta bort den här ögonblicks bilden.    |
|     `Cannot delete   volume replication generated snapshot`    |     Det är inte tillåtet att ta bort ögonblicks bilder av Replikerings bas linjer.    |

## <a name="errors-resizing-volumes"></a>Fel vid storleks ändring av volymer

|     Felmeddelande    |     Lösning    |
|-|-|
|   Försöket att ändra storlek på en käll volym misslyckades med felet `"PoolSizeTooSmall","message":"Pool size too small for total volume size."`  |  Se till att du har tillräckligt med utrymme i poolerna för kapacitet för både käll-och mål volymerna för replikering över flera regioner. När du ändrar storlek på käll volymen ändras storleken på mål volymen automatiskt. Men om den kapacitets pool som är värd för mål volymen inte har tillräckligt med utrymme, går det inte att ändra storlek på både käll-och mål volymerna. Mer information finns i [ändra storlek på en mål volym för replikering mellan regioner](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume) .   |

## <a name="next-steps"></a>Nästa steg  

* [Replikering mellan regioner](cross-region-replication-introduction.md)
* [Krav och överväganden för att använda replikering över flera regioner](cross-region-replication-requirements-considerations.md)
* [Skapa volymreplikering](cross-region-replication-create-peering.md)
* [Visa hälsostatus för replikeringsrelation](cross-region-replication-display-health-status.md)
* [Hantera haveriberedskap](cross-region-replication-manage-disaster-recovery.md)
* [Ändra storlek på en mål volym för replikering mellan regioner](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Felsöka replikering mellan regioner](troubleshoot-cross-region-replication.md)
