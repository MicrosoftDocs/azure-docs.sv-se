---
title: Nätverks säkerhets grupper med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du använder nätverks säkerhets grupper med Azure Site Recovery för haveri beredskap och migrering
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 367aba09f84da1e227c08721077aa1b2132a62bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92367982"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Nätverkssäkerhetsgrupper med Azure Site Recovery

Nätverks säkerhets grupper används för att begränsa nätverks trafiken till resurser i ett virtuellt nätverk. En [nätverks säkerhets grupp (NSG)](../virtual-network/network-security-groups-overview.md#network-security-groups) innehåller en lista över säkerhets regler som tillåter eller nekar inkommande eller utgående nätverks trafik baserat på källans eller MÅLETs IP-adress, port och protokoll.

Under distributions modellen Resource Manager kan NSG: er associeras med undernät eller enskilda nätverks gränssnitt. När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet. Trafiken kan begränsas ytterligare genom att även associera en NSG till enskilda nätverks gränssnitt i ett undernät som redan har en associerad NSG.

I den här artikeln beskrivs hur du kan använda nätverks säkerhets grupper med Azure Site Recovery.

## <a name="using-network-security-groups"></a>Använda nätverks säkerhets grupper

Ett enskilt undernät kan ha noll, eller ett, associerat NSG. Ett enskilt nätverks gränssnitt kan också ha noll, eller ett, associerat NSG. Därför kan du effektivt ha dubbla trafik begränsningar för en virtuell dator genom att associera en NSG till ett undernät och sedan en annan NSG till den virtuella datorns nätverks gränssnitt. Tillämpning av NSG-regler i det här fallet beror på trafikens riktning och prioritetsordningen för tillämpade säkerhets regler.

Överväg ett enkelt exempel med en virtuell dator på följande sätt:
-    Den virtuella datorn placeras i Contoso- **undernätet**.
-    **Contoso-undernätet** är kopplat till **Subnet NSG**.
-    Nätverks gränssnittet för virtuella datorer är dessutom associerat med **VM-NSG**.

![NSG med Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

I det här exemplet utvärderas NSG för inkommande trafik först. All trafik som tillåts via undernät NSG utvärderas sedan av VM-NSG. Återföringen gäller för utgående trafik, med VM-NSG som utvärderas först. All trafik som tillåts via VM-NSG utvärderas sedan av under nätets NSG.

Detta möjliggör detaljerade säkerhets regel program. Du kanske till exempel vill tillåta inkommande Internet åtkomst till några program-VM: ar (t. ex. virtuella klient datorer) under ett undernät, men begränsa inkommande Internet åtkomst till andra virtuella datorer (till exempel databas och andra VM-datorer). I det här fallet kan du ha en mer flexibel-regel på under nätet NSG, tillåta Internet trafik och begränsa åtkomsten till vissa virtuella datorer genom att neka åtkomst på VM-NSG. Samma kan användas för utgående trafik.

När du konfigurerar sådana NSG-konfigurationer bör du kontrol lera att rätt prioriteter tillämpas på [säkerhets reglerna](../virtual-network/network-security-groups-overview.md#security-rules). Regler bearbetas i prioritetsordning. Låga tal bearbetas före höga tal eftersom låga tal har högre prioritet. När trafiken matchar en regel avbryts bearbetningen. Det innebär att regler som har lägre prioritet (högre tal) och samma attribut som regler med högre prioritet inte bearbetas.

Du kanske inte alltid är medveten om när nätverkssäkerhetsgrupper tillämpas för både ett nätverksgränssnitt och ett undernät. Du kan kontrol lera de sammanställda regler som tillämpas på ett nätverks gränssnitt genom att visa de [effektiva säkerhets reglerna](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) för ett nätverks gränssnitt. Du kan också använda funktionen [verifiera IP-flöde](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) i [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) för att avgöra om kommunikation tillåts till eller från ett nätverks gränssnitt. Verktyget visar om kommunikation tillåts, och om nätverkssäkerhetsregeln tillåter eller nekar trafik.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Lokal till Azure-replikering med NSG

Azure Site Recovery möjliggör haveri beredskap och migrering till Azure för lokala [virtuella Hyper-V-datorer](hyper-v-azure-architecture.md), [virtuella VMware-datorer](vmware-azure-architecture.md)och [fysiska servrar](physical-azure-architecture.md). För alla lokala Azure-scenarier skickas replikeringsdata till och lagras i ett Azure Storage-konto. Under replikeringen betalar du inga avgifter för virtuella datorer. När du kör en redundansväxling till Azure skapar Site Recovery automatiskt virtuella Azure IaaS-datorer.

När virtuella datorer har skapats efter en redundansväxling till Azure kan NSG: er användas för att begränsa nätverks trafiken till det virtuella nätverket och virtuella datorer. Site Recovery skapar inte NSG: er som en del av redundansväxlingen. Vi rekommenderar att du skapar nödvändiga Azure-NSG: er innan du initierar redundans. Du kan sedan koppla NSG: er till misslyckade virtuella datorer automatiskt under redundansväxling, med hjälp av Automation-skript med Site Recovery kraftfulla [återställnings planer](site-recovery-create-recovery-plans.md).

Till exempel, om konfigurationen för VM-redundansen liknar det [exempel scenario](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) som beskrivs ovan:
-    Du kan skapa **contoso VNet** och **contoso-undernät** som en del av Dr-planeringen på Azure-regionen för målet.
-    Du kan också skapa och konfigurera både **NSG för undernät** och **VM-NSG** som en del av samma Dr-planering.
-    **Under nätets NSG** kan sedan omedelbart associeras med **contoso-undernätet**, eftersom både NSG och under nätet redan är tillgängliga.
-    **VM-NSG** kan associeras med virtuella datorer under redundans med återställnings planer.

När NSG: er har skapats och kon figurer ATS rekommenderar vi att du kör ett [redundanstest](site-recovery-test-failover-to-azure.md) för att verifiera SKRIPTade NSG-associationer och VM-anslutning efter redundans.

## <a name="azure-to-azure-replication-with-nsg"></a>Azure till Azure-replikering med NSG

Azure Site Recovery möjliggör haveri beredskap för [virtuella Azure-datorer](azure-to-azure-architecture.md). När du aktiverar replikering för virtuella Azure-datorer kan Site Recovery skapa de virtuella replik nätverken (inklusive undernät och gateway-undernät) i mål regionen och skapa nödvändiga mappningar mellan käll-och mål nätverk. Du kan också skapa mål sidans nätverk och undernät i förväg och använda samma när du aktiverar replikering. Site Recovery skapar inga virtuella datorer i Azure-regionen före [redundansväxlingen](azure-to-azure-tutorial-failover-failback.md).

För Azure VM-replikering kontrollerar du att NSG-reglerna i Azure-regionen för Azure tillåter [utgående anslutning](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) för replikeringstrafik. Du kan också testa och verifiera de nödvändiga reglerna genom den här [exempel NSG-konfigurationen](azure-to-azure-about-networking.md#example-nsg-configuration).

Site Recovery skapar eller replikerar inte NSG: er som en del av redundansväxlingen. Vi rekommenderar att du skapar nödvändiga NSG: er på Azure-regionen innan du påbörjar redundans. Du kan sedan koppla NSG: er till misslyckade virtuella datorer automatiskt under redundansväxling, med hjälp av Automation-skript med Site Recovery kraftfulla [återställnings planer](site-recovery-create-recovery-plans.md).

Som beaktar [exempel scenariot](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) som beskrivits tidigare:
-    Site Recovery kan skapa repliker av **contoso VNet** och **contoso-undernätet** i Azure-regionen där replikering är aktiverat för den virtuella datorn.
-    Du kan skapa önskade repliker av **UNDERNÄT NSG** och **VM-NSG** (t. ex. **mål under nät NSG** och **mål-VM NSG**) i Azure-regionen för Azure, vilket gör det möjligt för eventuella ytterligare regler som krävs i mål regionen.
-    **Mål under nätets NSG** kan sedan omedelbart associeras med mål regions under nätet, eftersom både NSG och under nätet redan är tillgängliga.
-    **Virtuella NSG** kan associeras med virtuella datorer under redundans med återställnings planer.

När NSG: er har skapats och kon figurer ATS rekommenderar vi att du kör ett [redundanstest](azure-to-azure-tutorial-dr-drill.md) för att verifiera SKRIPTade NSG-associationer och VM-anslutning efter redundans.

## <a name="next-steps"></a>Nästa steg
-    Läs mer om [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md#network-security-groups).
-    Läs mer om [säkerhets regler](../virtual-network/network-security-groups-overview.md#security-rules)för NSG.
-    Läs mer om [gällande säkerhets regler](../virtual-network/diagnose-network-traffic-filter-problem.md) för en NSG.
-    Läs mer om [återställnings planer](site-recovery-create-recovery-plans.md) för att automatisera programredundans.