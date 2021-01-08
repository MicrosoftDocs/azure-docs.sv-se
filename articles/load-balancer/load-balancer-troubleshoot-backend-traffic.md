---
title: Felsök Azure Load Balancer
description: Lär dig hur du felsöker kända problem med Azure Load Balancer.
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
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029223"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Felsöka trafik svar för Azure Load Balancer

Den här sidan innehåller felsöknings information för Azure Load Balancer frågor.

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Virtuella datorer bakom Load Balancer svarar inte på trafik på den konfigurerade data porten

Om en virtuell dator i en virtuell dator i listan visas som felfri och svarar på hälso avsökningarna, men fortfarande inte ingår i belastnings utjämningen eller inte svarar på data trafiken, kan det bero på någon av följande orsaker:
* Load Balancer VM-adresspoolen lyssnar inte på data porten
* Nätverks säkerhets gruppen blockerar porten på den virtuella datorn i Load Balancer backend-poolen  
* Åtkomst till Load Balancer från samma virtuella dator och NIC
* Åtkomst till Internet Load Balancer-frontend från den deltagande Load Balancer backend-poolen VM

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Orsak 1: den virtuella datorn i Load Balancer backend-poolen lyssnar inte på data porten

Om en virtuell dator inte svarar på data trafiken kan det bero på att mål porten inte är öppen på den deltagande virtuella datorn eller att den virtuella datorn inte lyssnar på den porten. 

**Verifiering och lösning**

1. Logga in på den virtuella datorns Server del. 
2. Öppna en kommando tolk och kör följande kommando för att verifiera att det finns ett program som lyssnar på data porten:  
            Netstat-a 
3. Om porten inte finns med i status "lyssning" konfigurerar du rätt lyssnar port 
4. Om porten är markerad som avlyssning kontrollerar du mål programmet på den porten för eventuella problem.

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Orsak 2: nätverks säkerhets gruppen blockerar porten på den virtuella datorn Load Balancer backend-poolen  

Om en eller flera nätverks säkerhets grupper som kon figurer ATS i under nätet eller på den virtuella datorn blockerar käll-IP eller port, kommer den virtuella datorn inte att svara.

För den offentliga belastningsutjämnaren kommer IP-adressen för Internet-klienter att användas för kommunikation mellan klienterna och de virtuella datorerna för belastningsutjämnare. Kontrol lera att klientens IP-adress är tillåtna i den virtuella datorns nätverks säkerhets grupp.

1. Lista de nätverks säkerhets grupper som kon figurer ATS på den virtuella datorn. Mer information finns i [Hantera nätverks säkerhets grupper](../virtual-network/manage-network-security-group.md)
1. I listan över nätverks säkerhets grupper kontrollerar du om:
    - inkommande eller utgående trafik på data porten har störningar. 
    - en regel för att **Neka alla** nätverks säkerhets grupper på nätverkskortet för den virtuella datorn eller under nätet som har en högre prioritet som standard regeln som tillåter Load Balancer avsökningar och trafik (nätverks säkerhets grupper måste tillåta Load Balancer IP-adress för 168.63.129.16, som är avsöknings port)
1. Om någon av reglerna blockerar trafiken tar du bort och konfigurerar om reglerna för att tillåta data trafiken.  
1. Testa om den virtuella datorn nu har börjat svara på hälso avsökningarna.

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Orsak 3: komma åt Load Balancer från samma virtuella dator och nätverks gränssnitt 

Om programmet som finns på den virtuella server delen av en Load Balancer försöker få åtkomst till ett annat program som finns i samma server dels dator i samma nätverks gränssnitt, så är det ett scenario som inte stöds och inte fungerar. 

**Lösning** Du kan lösa det här problemet via någon av följande metoder:
* Konfigurera separata VM-pooler per program. 
* Konfigurera programmet i virtuella datorer med dubbla nätverkskort så att varje program använder sitt eget nätverks gränssnitt och IP-adress. 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Orsak 4: komma åt den interna Load Balancer-frontend-filen från den deltagande Load Balancer backend-poolen VM

Om ett internt Load Balancer har kon figurer ATS i ett virtuellt nätverk och en av de virtuella datorerna för en deltagar Server försöker komma åt den interna Load Balancer-frontend, kan fel uppstå när flödet mappas till den ursprungliga virtuella datorn. Det här scenariot stöds inte.

**Lösning** Det finns flera sätt att avblockera det här scenariot, inklusive att använda en proxy. Utvärdera Application Gateway eller andra tredjeparts-proxyservrar (till exempel nginx eller haproxy). Mer information om Application Gateway finns i [Översikt över Application Gateway](../application-gateway/overview.md)

**Information** Interna belastningsutjämnare översätter inte utgående, ursprungliga anslutningar till klient delen av en intern Load Balancer eftersom båda finns i privata IP-adressutrymme. Publika belastningsutjämnare tillhandahåller [utgående anslutningar](load-balancer-outbound-connections.md) från privata IP-adresser i det virtuella nätverket till offentliga IP-adresser. För interna belastningsutjämnare gör den här metoden att du undviker möjliga SNAT-portar i ett unikt internt IP-adressutrymme, där översättning inte krävs.

En sido effekt är att om ett utgående flöde från en virtuell dator i backend-poolen försöker använda ett flöde till klient delen av den interna Load Balancer i poolen _och_ som mappas tillbaka till sig själv, matchar inte de två benen i flödet. Eftersom de inte matchar, Miss lyckas flödet. Flödet lyckas om flödet inte mappades tillbaka till samma virtuella dator i backend-poolen som skapade flödet till klient delen.

När flödet mappar tillbaka till sig själv, visas det utgående flödet som härstammar från den virtuella datorn till klient delen och motsvarande inkommande flöde visas som härstammar från den virtuella datorn till sig själv. Från gäst operativ systemets visnings punkt matchar de inkommande och utgående delarna av samma flöde inte i den virtuella datorn. TCP-stacken känner inte igen de hälften av samma flöde som en del av samma flöde. Källan och målet matchar inte. När flödet mappar till en annan virtuell dator i backend-poolen matchar hälften av flödet och den virtuella datorn kan svara på flödet.

Symptomet för det här scenariot är tillfälligt anslutnings-timeout när flödet återgår till samma server del som initierade flödet. Vanliga lösningar är infogning av ett proxy-lager bakom den interna Load Balancer och användnings regler för direkt Server retur (DSR). Mer information finns i [flera klient delar för Azure Load Balancer](load-balancer-multivip-overview.md).

Du kan kombinera ett internt Load Balancer med valfri tredjeparts-proxy eller använda interna [Application Gateway](../application-gateway/overview.md) för proxy-scenarier med http/https. Även om du kan använda en offentlig Load Balancer för att undvika det här problemet, är det här scenariot känsligt för [SNAT-belastning](load-balancer-outbound-connections.md). Undvik den andra metoden om du inte noggrant hanterar den.

## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet öppnar du ett [support ärende](https://azure.microsoft.com/support/options/).