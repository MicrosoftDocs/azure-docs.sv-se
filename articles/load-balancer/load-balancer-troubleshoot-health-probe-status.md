---
title: Felsök Azure Load Balancer status för hälso avsökning
description: Lär dig hur du felsöker kända problem med status för Azure Load Balancer hälso avsökning.
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
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029229"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>Felsök Azure Load Balancer status för hälso avsökning

Den här sidan innehåller felsöknings information vanliga Azure Load Balancer hälso avsöknings frågor.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Symptom: virtuella datorer bakom Load Balancer svarar inte på hälso avsökningar
För att backend-servrarna ska delta i belastnings Utjämnings uppsättningen måste de klara avsöknings kontrollen. Mer information om hälso avsökningar finns i [förstå Load Balancer avsökningar](load-balancer-custom-probe-overview.md). 

Load Balancer de virtuella datorerna i fjärrpoolen kanske inte svarar på avsökningarna på grund av någon av följande orsaker: 
- Load Balancer backend-poolens VM-pool är inte felfri 
- Load Balancer VM-adresspoolen lyssnar inte på avsöknings porten 
- Brand väggen eller en nätverks säkerhets grupp blockerar porten på de virtuella datorerna i Load Balancer backend-poolen 
- Andra felkonfigurationer i Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Orsak 1: den virtuella datorn för Load Balancer backend-poolen är inte felfri

**Verifiering och lösning**

Lös problemet genom att logga in på de deltagande virtuella datorerna och kontrol lera om tillståndet för den virtuella datorn är felfritt och kan svara på **PsPing** eller **TCPing** från en annan virtuell dator i poolen. Om den virtuella datorn är ohälsosam eller inte kan svara på avsökningen, måste du åtgärda problemet och återställa den till ett felfritt tillstånd innan den kan ingå i belastnings utjämningen.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Orsak 2: den virtuella datorn i Load Balancer backend-poolen lyssnar inte på avsöknings porten
Om den virtuella datorn är felfri, men inte svarar på avsökningen, kan en möjlig orsak vara att avsöknings porten inte är öppen på den deltagande virtuella datorn eller att den virtuella datorn inte lyssnar på den porten.

**Verifiering och lösning**

1. Logga in på den virtuella datorns Server del.
2. Öppna en kommando tolk och kör följande kommando för att verifiera att det finns ett program som lyssnar på avsöknings porten: netstat-a
3. Om Port statusen inte visas som **avlyssning** konfigurerar du rätt port. 
4. Du kan också välja en annan port, som visas som **lyssning** och uppdatera belastnings Utjämnings konfigurationen.

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Orsak 3: brand vägg eller en nätverks säkerhets grupp blockerar porten på de virtuella datorerna för belastningsutjämnare i backend-poolen
Om brand väggen på den virtuella datorn blockerar avsöknings porten, eller om en eller flera nätverks säkerhets grupper som kon figurer ATS i under nätet eller på den virtuella datorn, inte tillåter avsökningen att kontakta porten, kan den virtuella datorn inte svara på hälso avsökningen.

**Verifiering och lösning**

1. Om brand väggen är aktive rad kontrollerar du om den är konfigurerad för att tillåta avsöknings porten. Om inte, konfigurerar du brand väggen så att den tillåter trafik på avsöknings porten och testar igen.
2. I listan över nätverks säkerhets grupper kontrollerar du om inkommande eller utgående trafik på avsöknings porten har störningar.
3. Kontrol lera också om regeln **Neka alla** nätverks säkerhets grupper på nätverkskortet för den virtuella datorn eller under nätet som har högre prioritet än standard regeln som tillåter lb-avsökning & trafik (nätverks säkerhets grupper måste tillåta Load Balancer IP för 168.63.129.16).
4. Om någon av dessa regler blockerar avsöknings trafiken tar du bort och konfigurerar om reglerna för att tillåta avsöknings trafiken.  
5. Testa om den virtuella datorn nu har börjat svara på hälso avsökningarna.

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Orsak 4: andra fel konfigurationer i Load Balancer
Om alla föregående orsaker verkar vara verifierade och lösta korrekt, och backend-datorn fortfarande inte svarar på hälso avsökningen, kan du manuellt testa anslutningen och samla in vissa spår för att förstå anslutningen.

**Verifiering och lösning**

1. Använd **Psping** från någon av de andra virtuella datorerna i det virtuella nätverket för att testa avsöknings portens svar (exempel: .\psping.exe-t 10.0.0.4:3389) och registrera resultat. 
2. Använd **TCPing** från någon av de andra virtuella datorerna i det virtuella nätverket för att testa avsöknings portens svar (exempel: .\tcping.exe 10.0.0.4 3389) och registrera resultat. 
3. Om inget svar tas emot i dessa ping-test,
    - Kör en samtidig netsh-spårning på mål server delens virtuella dator och en annan virtuell test dator från samma VNet. Kör nu ett PsPing-test under en viss tid, samla in några nätverks spår och stoppa sedan testet. 
    - Analysera nätverks avbildningen och se om det finns både inkommande och utgående paket relaterade till ping-frågan. 
        - Om inga inkommande paket observerats på den virtuella backend-poolen, finns det potentiellt nätverks säkerhets grupper eller UDR-konfiguration som blockerar trafiken. 
        - Om inga utgående paket observeras på den virtuella backend-poolen måste den virtuella datorn kontrol leras för eventuella orelaterade problem (till exempel program som blockerar avsöknings porten). 
    - Kontrol lera att avsöknings paketen tvingas till ett annat mål (eventuellt via UDR-inställningar) innan belastningsutjämnaren når belastningsutjämnaren. Detta kan orsaka att trafiken aldrig når den virtuella backend-datorn. 
4. Ändra avsöknings typ (till exempel HTTP till TCP) och konfigurera motsvarande port i nätverks säkerhets grupper ACL: er och brand vägg för att kontrol lera om problemet beror på konfigurationen av avsöknings svaret. Mer information om konfiguration av hälso avsökning finns i [konfiguration av hälso avsöknings konfiguration för slut punkts belastning](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details).

## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet öppnar du ett [support ärende](https://azure.microsoft.com/support/options/).