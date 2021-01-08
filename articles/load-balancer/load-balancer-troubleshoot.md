---
title: Felsök vanliga problem Azure Load Balancer
description: Lär dig hur du felsöker vanliga problem med Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028819"
---
# <a name="troubleshoot-azure-load-balancer"></a>Felsök Azure Load Balancer

Den här sidan innehåller felsöknings information för grundläggande och vanliga Azure Load Balancer frågor. Mer information om Standard Load Balancer finns i [standard Load Balancer översikt](load-balancer-standard-diagnostics.md).

När Load Balancer anslutningen inte är tillgänglig är de vanligaste symptomen följande:

- Virtuella datorer bakom Load Balancer svarar inte på hälso avsökningar 
- Virtuella datorer bakom Load Balancer svarar inte på trafiken på den konfigurerade porten

När de externa klienterna till de virtuella server dels datorerna går via belastningsutjämnaren, används IP-adressen för klienterna för kommunikationen. Kontrol lera att IP-adressen för klienterna läggs till i listan över tillåtna NSG.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Ingen utgående anslutning från standard intern belastningsutjämnare (ILB)

**Verifiering och lösning**

Standard-ILB är **säkra som standard**. Basic-ILB tillåts ansluta till Internet via en *dold* offentlig IP-adress. Detta rekommenderas inte för produktions arbets belastningar eftersom IP-adressen varken är statisk eller låst via NSG: er som du äger. Om du nyligen har flyttat från en grundläggande ILB till en standard-ILB bör du skapa en offentlig IP-adress direkt via en [utgående](egress-only.md) konfiguration, som låser ner IP-adressen via NSG: er. Du kan också använda en [NAT-gateway](../virtual-network/nat-overview.md) på ditt undernät.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>Det går inte att ändra backend-porten för den befintliga LB-regeln för en belastningsutjämnare som har distribuerats i backend-poolen.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Orsak: Server dels porten kan inte ändras för en belastnings Utjämnings regel som används av en hälso avsökning för belastningsutjämnaren som refereras av den virtuella datorns skal uppsättning

**Lösning** För att kunna ändra porten kan du ta bort hälso avsökningen genom att uppdatera den virtuella datorns skalnings uppsättning, uppdatera porten och sedan konfigurera hälso avsökningen igen.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Liten trafik går fortfarande igenom belastningsutjämnaren när de virtuella datorerna har tagits bort från belastningsutjämnaren.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Orsak: virtuella datorer som har tagits bort från backend-poolen bör inte längre ta emot trafik. Den lilla mängden nätverks trafik kan vara relaterad till lagring, DNS och andra funktioner i Azure.

För att verifiera kan du utföra en nätverks spårning. FQDN som används för Blob Storage-konton visas i egenskaperna för varje lagrings konto.  Från en virtuell dator i din Azure-prenumeration kan du utföra nslookup för att avgöra vilken Azure-IP-adress som tilldelats det lagrings kontot.

## <a name="additional-network-captures"></a>Ytterligare nätverks avbildningar

Om du väljer att öppna ett support ärende samlar du in följande information för en snabbare lösning. Välj en enskild virtuell dator för att utföra följande tester:

- Använd PS ping från en av de virtuella datorerna i det virtuella nätverket för att testa avsöknings portens svar (exempel: PS ping 10.0.0.4:3389) och registrera resultat. 
- Om inget svar tas emot i de här ping-testerna kör du en samtidig netsh-spårning på den virtuella datorns VM och VNet-testets VM medan du kör PsPing och stoppar sedan netsh-spårningen.

## <a name="load-balancer-in-failed-state"></a>Load Balancer i felaktigt tillstånd

**Lösning**

- När du har identifierat den resurs som är i fel tillstånd går du till [Azure Resource Explorer](https://resources.azure.com/) och identifierar resursen i det här läget.
- Uppdatera växlingen till det högra övre hörnet för att läsa/skriva.
- Klicka på Redigera för resursen i felaktigt tillstånd.
- Klicka på Lägg till, följt av GET för att se till att etablerings statusen har uppdaterats till slutfört.
- Sedan kan du fortsätta med andra åtgärder eftersom resursen inte är i felläge.

## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet öppnar du ett [support ärende](https://azure.microsoft.com/support/options/).
