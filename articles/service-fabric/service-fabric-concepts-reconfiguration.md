---
title: Omkonfiguration i Azure Service Fabric
description: Lär dig mer om konfigurationer för tillstånds känsliga tjänste repliker och processen för omkonfiguration Service Fabric använder för att upprätthålla konsekvens och tillgänglighet under ändringen.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75610003"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Omkonfiguration i Azure Service Fabric
En *konfiguration* definieras som repliker och deras roller för en partition i en tillstånds känslig tjänst.

En *omkonfiguration* är processen att flytta en konfiguration till en annan konfiguration. Den gör en ändring i replik uppsättningen för en partition av en tillstånds känslig tjänst. Den gamla konfigurationen kallas för den *tidigare konfigurationen (PC)* och den nya konfigurationen kallas för den *aktuella konfigurationen (CC)*. Protokollet för omkonfiguration i Azure Service Fabric bevarar konsekvens och upprätthåller tillgängligheten vid ändringar i replik uppsättningen.

Redundanshanteraren initierar omkonfigurationer som svar på olika händelser i systemet. Till exempel, om den primära Miss lyckas, initieras en omkonfiguration för att befordra en aktiv sekundär till en primär. Ett annat exempel är svar på program uppgraderingar när det kan vara nödvändigt att flytta den primära noden till en annan nod för att kunna uppgradera noden.

## <a name="reconfiguration-types"></a>Omkonfigurations typer
Omkonfigurationer kan delas in i två typer:

- Omkonfigurationer där den primära ändras:
    - **Redundans**: redundans är omkonfigurationer som svar på en primär körnings orsak.
    - **SwapPrimary**: växlingar är omkonfigurationer där Service Fabric måste flytta en löpande primär från en nod till en annan, vanligt vis som svar på belastnings utjämning eller en uppgradering.

- Omkonfigurationer där primär inte ändras.

## <a name="reconfiguration-phases"></a>Omkonfigurations faser
En omkonfiguration fortsätter i flera faser:

- **Phase0**: den här fasen sker i byte-primär omkonfigurationer där den aktuella primära överföringen har sitt tillstånd till den nya primära och över gångar till aktiv sekundär.

- **Phase1**: den här fasen sker under omkonfigurationer där den primära ändras. Under den här fasen identifierar Service Fabric rätt primär mellan de aktuella replikerna. Den här fasen behövs inte under växling – primär omkonfigurationer eftersom den nya primära servern redan har valts. 

- **Phase2**: under den här fasen ser Service Fabric till att alla data är tillgängliga i en majoritet av replikerna av den aktuella konfigurationen.

Det finns flera andra faser som endast används för internt bruk.

## <a name="stuck-reconfigurations"></a>Låsta omkonfigurationer
Omkonfigurationer kan *fastna* av olika orsaker. Några av de vanligaste orsakerna är:

- **Ned-repliker**: vissa omkonfigurations faser kräver att en majoritet av replikerna i konfigurationen är upp.
- **Nätverks-eller kommunikations problem**: omkonfigurationer kräver nätverks anslutning mellan olika noder.
- **API-problem**: omkonfigurations protokollet kräver att tjänst implementeringar Slutför vissa API: er. Om du till exempel inte följer den token för att inte ta emot token i en tillförlitlig tjänst så kommer SwapPrimary omkonfigurationer att bli fast.

Använd hälso rapporter från system komponenter, till exempel System.FM, system. RA och system. RAP, för att diagnosticera var en omkonfiguration fastnar. På [sidan system hälso rapport](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) beskrivs dessa hälso rapporter.

## <a name="next-steps"></a>Nästa steg
Mer information om Service Fabric-begrepp finns i följande artiklar:

- [Reliable Services-livscykel – C#](service-fabric-reliable-services-lifecycle.md)
- [System hälso rapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Repliker och instanser](service-fabric-concepts-replica-lifecycle.md)
