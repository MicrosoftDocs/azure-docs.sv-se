---
title: Tilldela offentliga IP-adresser efter redundansväxlingen med Azure Site Recovery
description: Beskriver hur du konfigurerar offentliga IP-adresser med Azure Site Recovery och Azure-Traffic Manager för haveri beredskap och migrering
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 01c2f61dcf024e8c9dbbd5b2ee11a479b3c16305
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86130290"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Konfigurera offentliga IP-adresser efter redundans

Offentliga IP-adresser tillåter att Internet-resurser kommunicerar inkommande till Azure-resurser. Offentliga IP-adresser gör det också möjligt för Azure-resurser att kommunicera utgående till Internet och offentliga Azure-tjänster med en IP-adress som tilldelats resursen.
- Inkommande kommunikation från Internet till resursen, till exempel Azure Virtual Machines (VM), Azure Application gatewayer, Azure Load Balancer, Azure VPN-gatewayer och andra. Du kan fortfarande kommunicera med vissa resurser, t. ex. virtuella datorer, från Internet, om en virtuell dator inte har tilldelats en offentlig IP-adress, så länge den virtuella datorn är en del av en backend-pool för belastningsutjämnare, och belastningsutjämnaren tilldelas en offentlig IP-adress.
- Utgående anslutning till Internet med en förutsägbar IP-adress. En virtuell dator kan till exempel kommunicera utgående till Internet utan att en offentlig IP-adress har tilldelats till den, men dess adress är en nätverks adress som översätts av Azure till en oförutsägbar offentlig adress som standard. Genom att tilldela en offentlig IP-adress till en resurs kan du se vilken IP-adress som används för utgående anslutning. Även om det går att förutse kan adressen ändras, beroende på vilken tilldelnings metod som valts. Mer information finns i [skapa en offentlig IP-adress](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Mer information om utgående anslutningar från Azure-resurser finns i [förstå utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

I Azure Resource Manager är en offentlig IP-adress en resurs som har egna egenskaper. Följande är några av resurserna du kan associera med en offentlig IP-adressresurs:

* Nätverksgränssnitt för virtuella datorer
* Internetuppkopplade lastbalanserare
* VPN-gateways
* Programgatewayer

I den här artikeln beskrivs hur du kan använda offentliga IP-adresser med Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Tilldelning av offentliga IP-adresser med återställnings plan

Den offentliga IP-adressen för produktions programmet **kan inte behållas vid redundansväxling**. Arbets belastningar som ingår i redundansväxlingen måste tilldelas en offentlig Azure-resurs som är tillgänglig i mål regionen. Det här steget kan göras antingen manuellt eller automatiseras med återställnings planer. En återställnings plan samlar in datorer i återställnings grupper. Det hjälper dig att definiera en systematisk återställnings process. Du kan använda en återställnings plan för att införa order och automatisera de åtgärder som krävs i varje steg, använda Azure Automation runbooks för redundans till Azure eller skript.

Inställningarna är följande:
- Skapa en [återställnings plan](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) och gruppera dina arbets belastningar efter behov i planen.
- Anpassa planen genom att lägga till ett steg för att koppla en offentlig IP-adress med hjälp av [Azure Automation runbooks](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) skript till den misslyckade virtuella datorn.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Offentlig slut punkts växling med Routning av DNS-nivå

Azure Traffic Manager aktiverar vidarebefordran av DNS-nivå mellan slut punkter och kan hjälpa dig att [köra återställnings tider](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) för ett Dr-scenario. 

Läs mer om failover-scenarier med Traffic Manager:
1. [Lokal till Azure-redundans](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) med Traffic Manager 
2. [Azure till Azure-redundans](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) med Traffic Manager 

Inställningarna är följande:
- Skapa en [Traffic Manager-profil](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Använd metoden för **prioritets** cirkulation och skapa två slut punkter – **primärt** för källa och **redundans** för Azure. **Primär** tilldelas prioritet 1 och **redundans** tilldelas prioritet 2.
- Den **primära** slut punkten kan vara [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) eller [external](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) , beroende på om din käll miljö finns i eller utanför Azure.
- Slut punkten för **redundans** skapas som en **Azure** -slutpunkt. Använd en **statisk offentlig IP-adress** eftersom detta är extern slut punkt för Traffic Manager i katastrof händelsen.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Traffic Manager med Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Läs mer om Traffic Manager [routningsmetoder](../traffic-manager/traffic-manager-routing-methods.md).
- Läs mer om [återställnings planer](site-recovery-create-recovery-plans.md) för att automatisera programredundans.
