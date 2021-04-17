---
title: Vad är BareMetal-infrastruktur för Oracle?
description: Lär dig mer om funktionerna som BareMetal Infrastructure erbjuder för Oracle-arbetsbelastningar.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: efb7506a0fa98ca93390bfe7d3cb1401cdbb77e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559283"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Vad är BareMetal-infrastruktur för Oracle?

Den här artikeln ger en översikt över funktionerna som BareMetal Infrastructure erbjuder för Oracle-arbetsbelastningar.

BareMetal Infrastructure för Oracle baseras på Oracle-certifierat Unified Computing System (UCS) och FPodPod. FlexPod-plattformen levererar för validerad lagrings-, nätverks- och serverteknik. Det erbjuder NFS-lagring, vilket ger integrering med DirectNFS-protokollet. BareMetal-servrarna är dedikerade till dig, utan hypervisor-program på BareMetal-instanserna. 

Dessa instanser används för att köra verksamhetskritiska program som kräver en Oracle-arbetsbelastning. BareMetal-instanser ger korta svarstider (0,35 ms) till dina program som körs på virtuella Azure-datorer (VM). BareMetal tillhandahåller delad lagringsdisk och stöder multi-cast som krävs för nod-till-nod-kommunikation med ett dedikerat privat sammankopplingsnätverk. 

Andra funktioner i BareMetal Infrastructure for Oracle är:

- Oracle-certifierade UCS-blad – UCSB200-M5, UCSB460-M4, UCSB480-M5
- Oracle Real Application Clusters (RAC) nod-till-nod-kommunikation (multi-cast) med hjälp av privat virtuellt LAN (VLAN) -40 Gb.
- Microsoft-hanterad maskinvara
  - Redundant lagring, nätverk, strömförsörjning, hantering
  - Övervakning av infra, reparation och ersättning
  - Omfattar Azure ExpressRoute till kundens domänkontrollant
  - Säker fysisk säkerhet och nätverkssäkerhet, kan komma åt alla Azure-molntjänster

### <a name="supported-protocols"></a>Protokoll som stöds

Följande protokoll används för olika monteringspunkter i BareMetal-servrar för Oracle-arbetsbelastning.

- OS-montering – iSCSI
- Data/logg – NFSv3
- säkerhetskopiering/arkivering – NFSv4

### <a name="licensing"></a>Licensiering

- Du har egna lokala operativsystem och Oracle-licenser.

### <a name="operating-system"></a>Operativsystem

Servrar är förinstallerade med operativsystemet RHEL 7.6.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om SKU:er för Oracle BareMetal-arbetsbelastningar.

> [!div class="nextstepaction"]
> [BareMetal-SKU:er för Oracle-arbetsbelastningar](oracle-baremetal-skus.md)
