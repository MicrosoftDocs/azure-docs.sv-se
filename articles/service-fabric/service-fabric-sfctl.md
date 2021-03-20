---
title: Azure Service Fabric CLI – sfctl
description: Lär dig mer om sfctl, Azure Service Fabric Command Line Interface. Innehåller en lista över kommandon och under grupper.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fc317345155a6807a20d342e2cefd0701b20f180
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86257061"
---
# <a name="sfctl"></a>sfctl
Kommandon för att hantera Service Fabric kluster och entiteter. Den här versionen är kompatibel med Service Fabric 7,0-körningsmiljön.

Kommandon följer mönstret Substantiv-verb. Se under grupper för mer information.

## <a name="subgroups"></a>Under grupper
|Under grupp|Beskrivning|
| --- | --- |
| [program](service-fabric-sfctl-application.md) | Skapa, ta bort och hantera program och program typer. |
| [kaos](service-fabric-sfctl-chaos.md) | Starta, stoppa och rapportera om kaos test service. |
| [flernodskluster](service-fabric-sfctl-cluster.md) | Välj, hantera och använda Service Fabric kluster. |
| [utgör](service-fabric-sfctl-compose.md) | Skapa, ta bort och hantera Docker Compose-program. |
| [fönster](service-fabric-sfctl-container.md) | Kör behållar relaterade kommandon på en klusternod. |
| [planering](service-fabric-sfctl-events.md) | Hämta händelser från händelse lagret (om EventStore-tjänsten redan är installerad). |
| [utgör](service-fabric-sfctl-is.md) | Fråga och skicka kommandon till infrastruktur tjänsten. |
| [ruta](service-fabric-sfctl-mesh.md) | Ta bort och hantera Service Fabric nät-program. |
| [node](service-fabric-sfctl-node.md) | Hantera noderna som utgör ett kluster. |
| [partitionstabellen](service-fabric-sfctl-partition.md) | Fråga och hantera partitioner för alla tjänster. |
| [immaterialrätt](service-fabric-sfctl-property.md) | Lagra och fråga efter egenskaper under Service Fabric namn. |
| [Replica](service-fabric-sfctl-replica.md) | Hantera de repliker som hör till-tjänstepartitioner. |
| [varvtal](service-fabric-sfctl-rpm.md) | Fråga och skicka kommandon till tjänsten Repair Manager. |
| [sa-kluster](service-fabric-sfctl-sa-cluster.md) | Hantera fristående Service Fabric kluster. |
| [telefonitjänstprovider](service-fabric-sfctl-service.md) | Skapa, ta bort och hantera tjänst, tjänst typer och service paket. |
| [inställningar](service-fabric-sfctl-settings.md) | Konfigurera lokala inställningar för den här instansen av sfctl. |
| [store](service-fabric-sfctl-store.md) | Utför grundläggande åtgärder på filnivå på klustrets avbildnings lager. |

## <a name="next-steps"></a>Nästa steg
- [Konfigurera](service-fabric-cli.md) Service Fabric cli.
- Lär dig hur du använder Service Fabric CLI med hjälp av [exempel skripten](./scripts/sfctl-upgrade-application.md).
