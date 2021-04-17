---
title: Korrigeringsöverväganden för BareMetal för Oracle
description: Lär dig mer om överväganden för operativsystem/kernelkorrigering för BareMetal Infrastructure for Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 5af713c776def8af558531c84013b221a8d30087
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559223"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Korrigeringsöverväganden för BareMetal för Oracle

I den här artikeln tittar vi på viktiga överväganden för operativsystem/kernelkorrigering för BareMetal Infrastructure för Oracle.

För korrekt nätverksprestanda och systemstabilitet installerar du den OS-specifika versionen av eNIC- och fNIC-drivrutiner enligt följande kompatibilitetstabell. 

Servrar levereras till kunder med kompatibla versioner. Under operativsystem (OS)/kernel-korrigering kan drivrutiner återställas till standarddrivrutinsversionerna. Se därför till att bekräfta att rätt drivrutinsversion kör följande os-/kernelkorrigeringsåtgärder.

| Os-leverantör | Os-paketversion | Version av inbyggd programvara | eNIC-drivrutin | fNIC-drivrutin |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om Ethernet-konfigurationen av BareMetal för Oracle.

> [!div class="nextstepaction"]
> [Ethernet-konfiguration av BareMetal för Oracle](oracle-baremetal-ethernet.md)

