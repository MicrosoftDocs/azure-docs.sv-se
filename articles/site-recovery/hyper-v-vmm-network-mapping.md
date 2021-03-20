---
title: Om Hyper-V (med VMM) nätverks mappning med Site Recovery
description: Beskriver hur du konfigurerar nätverks mappning för haveri beredskap för virtuella Hyper-V-datorer (hanteras i VMM-moln) till Azure, med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74082566"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Förbereda nätverks mappning för haveri beredskap för virtuella Hyper-V-datorer till Azure


Den här artikeln hjälper dig att förstå och förbereda för nätverks mappning när du replikerar virtuella Hyper-V-datorer i System Center Virtual Machine Manager-moln (VMM) till Azure eller till en sekundär plats med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Förbereda nätverks mappning för replikering till Azure

När du replikerar till Azure mappar nätverks mappningen mellan virtuella dator nätverk på en VMM-webbserver och använder virtuella Azure-nätverk. Mappning utför följande:
-  **Nätverks anslutning**– säkerställer att replikerade virtuella Azure-datorer är anslutna till det mappade nätverket. Alla datorer som växlar över i samma nätverk kan ansluta till varandra, även om de har redundansväxlats i olika återställnings planer.
- **Nätverksgateway**– om en nätverksgateway har kon figurer ATS på Azure-nätverket kan virtuella datorer ansluta till andra lokala virtuella datorer.

Nätverks mappningen fungerar på följande sätt:

- Du mappar ett virtuellt dator nätverk i VMM till ett virtuellt Azure-nätverk.
- När redundansväxling av virtuella Azure-datorer i käll nätverket ansluts till det mappade mål nätverket.
- Nya virtuella datorer som läggs till i det virtuella käll dator nätverket ansluts till det mappade Azure-nätverket när replikeringen sker.
- Om målnätverket har flera undernät och ett av dessa undernät har samma namn som undernätet där den virtuella källdatorn finns så ansluts den virtuella replikdatorn till det målundernätverket efter en redundansväxling.
- Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Förbereda nätverks mappning för replikering till en sekundär plats

När du replikerar till en sekundär plats, mappas nätverks mappningen mellan virtuella dator nätverk på en VMM-webbserver och virtuella dator nätverk på en VMM-måldator. Mappning utför följande:

- **Nätverks anslutning**– ansluter virtuella datorer till lämpliga nätverk efter redundansväxlingen. Den virtuella replik datorn kommer att anslutas till mål nätverket som är mappat till käll nätverket.
- **Optimal placering av virtuella datorer**– optimalt placerar de replikerade virtuella datorerna på Hyper-V-värd servrar. Virtuella repliker placeras på värdar som har åtkomst till mappade VM-nätverk.
- **Ingen nätverks mappning**– om du inte konfigurerar nätverks mappning ansluts virtuella replik datorer inte till något virtuellt dator nätverk efter redundansväxlingen.

Nätverks mappningen fungerar på följande sätt:

- Nätverks mappning kan konfigureras mellan virtuella dator nätverk på två VMM-servrar, eller på en enda VMM-Server om två platser hanteras av samma server.
- När mappningen är korrekt konfigurerad och replikering är aktive rad kommer en virtuell dator på den primära platsen att anslutas till ett nätverk och dess replik på mål platsen kommer att anslutas till dess mappade nätverk.
- När du väljer ett virtuellt dator nätverk under nätverks mappning i Site Recovery visas de VMM-källdokument som använder det virtuella käll nätverket, tillsammans med de tillgängliga virtuella mål dator nätverken på de mål moln som används för skydd.
- Om mål nätverket har flera undernät och ett av dessa undernät har samma namn som under nätet där den virtuella käll datorn finns, kommer den virtuella replik datorn att anslutas till det mål under nätet efter redundansväxlingen. Om det inte finns något mål under nät med ett matchande namn ansluts den virtuella datorn till det första under nätet i nätverket.

## <a name="example"></a>Exempel

Här är ett exempel på hur du kan illustrera den här mekanismen. Låt oss ta en organisation med två platser i New York och Chicago.

**Plats** | **VMM-server** | **VM-nätverk** | **Mappad till**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Mappad till VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Inte mappad
Chicago | VMM-Chicago| VMNetwork1-Chicago | Mappad till VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Inte mappad

I det här exemplet:

- När en virtuell replik dator skapas för en virtuell dator som är ansluten till VMNetwork1-NewYork kommer den att anslutas till VMNetwork1-Chicago.
- När en virtuell replik dator skapas för VMNetwork2-NewYork eller VMNetwork2-Chicago ansluts den inte till något nätverk.

Så här konfigureras VMM-moln i vår exempel organisation och de logiska nätverk som är kopplade till molnen.

### <a name="cloud-protection-settings"></a>Skydds inställningar för moln

**Skyddat moln** | **Skyddar molnet** | **Logiskt nätverk (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Inställningar för logiska och virtuella dator nätverk

**Plats** | **Logiskt nätverk** | **Associerat VM-nätverk**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Mål nätverks inställningar

I följande tabell visas de alternativ som är tillgängliga när du väljer det virtuella mål nätverket, baserat på dessa inställningar.

**Välj** | **Skyddat moln** | **Skyddar molnet** | **Tillgängligt mål nätverk**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Tillgänglig
 | GoldCloud1 | GoldCloud2 | Tillgänglig
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Inte tillgängligt
 | GoldCloud1 | GoldCloud2 | Tillgänglig


Om mål nätverket har flera undernät och ett av dessa undernät har samma namn som under nätet där den virtuella käll datorn finns, ansluts den virtuella replik datorn till det mål under nätet efter redundansväxlingen. Om det inte finns något målundernät med ett matchande namn ansluts den virtuella datorn till det första undernätet i nätverket.


### <a name="failback-behavior"></a>Failback-beteende

Om du vill se vad som händer vid återställning efter fel (omvänd replikering) ska vi anta att VMNetwork1-NewYork mappas till VMNetwork1-Chicago, med följande inställningar.


**Virtuell dator** | **Ansluten till virtuellt dator nätverk**
---|---
VM1 | VMNetwork1-Network
VM2 (replik av VM1) | VMNetwork1-Chicago

Med de här inställningarna kan vi se vad som händer i ett par möjliga scenarier.

**Scenario** | **Resultatet**
---|---
Ingen ändring i nätverks egenskaperna för VM-2 efter redundans. | VM-1 är fortfarande ansluten till käll nätverket.
Nätverks egenskaperna för VM-2 ändras efter redundansväxlingen och är frånkopplad. | VM-1 är frånkopplad.
Nätverks egenskaperna för VM-2 ändras efter redundansväxlingen och är ansluten till VMNetwork2-Chicago. | Om VMNetwork2-Chicago inte har mappats kommer VM-1 att kopplas från.
Nätverks mappningen för VMNetwork1-Chicago har ändrats. | VM-1 kommer att anslutas till nätverket nu mappat till VMNetwork1-Chicago.



## <a name="next-steps"></a>Nästa steg

- [Läs mer om](hyper-v-vmm-networking.md) IP-adresser efter redundansväxling till en sekundär VMM-plats.
- [Läs mer om](concepts-on-premises-to-azure-networking.md) IP-adressering efter redundans till Azure.
