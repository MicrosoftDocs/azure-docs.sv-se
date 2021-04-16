---
title: Arkitektur för BareMetal-infrastruktur för Oracle
description: Lär dig mer om arkitekturen i flera konfigurationer av BareMetal Infrastructure for Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 1bdc32c14cfc8986c52a4ea916a6130ee29e6028
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559296"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>Arkitektur för BareMetal-infrastruktur för Oracle

I den här artikeln tittar vi på arkitekturalternativen för BareMetal Infrastructure for Oracle och de funktioner som var och en stöder.

## <a name="single-instance"></a>Enstaka instans

Den här topologin stöder en enda instans av Oracle Database med Oracle Data Guard för migrering till BareMetal-infrastrukturen. Den stöder användning av väntelägesnod för arbete med hög tillgänglighet och underhåll.

[![Diagram som visar arkitekturen för en enskild instans av Oracle Database med Oracle Data Guard.](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Oracle Real Application Clusters (RAC) en nod

Den här topologin stöder en RAC-konfiguration med delad lagring och GRID-kluster. Databasinstanser körs bara på en nod (aktiv-passiv konfiguration).

Funktionerna omfattar:

- Aktiv-passiv med Oracle RAC One Node

    - Automatisk redundans

    - Snabb omstart på den andra noden

- Redundans i realtid och skalbarhet med Oracle RAC

- Inget löpande underhåll vid driftstopp

[![Diagram som visar arkitekturen för en aktiv-passiv Oracle RAC One Node-konfiguration.](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>Rac

Den här topologin stöder en Oracle RAC-konfiguration med delat lagrings- och rutnätskluster medan flera instanser per databas körs samtidigt (aktiv-aktiv-konfiguration).

- Prestandan är enkel att skala genom onlineetablering av tillagda servrar. 
-  Användare är aktiva på alla servrar och alla servrar delar åtkomst till samma Oracle Database. 
-  Alla typer av databasunderhåll kan utföras antingen online eller på rullande sätt för minimal eller noll avbrottstid. 
- Oracle Active Data Guard-väntesystem (ADG) kan enkelt användas med dubbla syften som testsystem. 

Med den här konfigurationen kan du testa alla ändringar på en exakt kopia av produktionsdatabasen innan de tillämpas på produktionsmiljön.

> [!NOTE]
> Om du tänker använda Active Data Guard Far Sync (synkront läge) måste du överväga de regionala zoner där den här funktionen stöds. För endast geografiska distribuerade regioner rekommenderar vi att du använder Data Guard med asynkront läge.

[![Diagram som visar arkitekturen för en aktiv-aktiv Oracle RAC-konfiguration.](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om etablering av BareMetal-instanser för Oracle-arbetsbelastningar.

> [!div class="nextstepaction"]
> [Etablera BareMetal-infrastruktur för Oracle](oracle-baremetal-provision.md)

