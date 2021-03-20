---
title: Azures produktions nätverk
description: Lär dig mer om Azures produktions nätverk. Se säkerhets åtkomst metoder och skydds metoder för att upprätta en anslutning till nätverket.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 724c2ee2d74ca9cab11280e92faac71c2cb2218e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87543817"
---
# <a name="the-azure-production-network"></a>Azures produktions nätverk
Användare av Azures produktions nätverk omfattar både externa kunder som har åtkomst till sina egna Azure-program och intern Azure support-personal som hanterar produktions nätverket. I den här artikeln beskrivs säkerhets åtkomst metoder och skydds metoder för att upprätta anslutningar till Azures produktions nätverk.

## <a name="internet-routing-and-fault-tolerance"></a>Internet-Routning och fel tolerans
En globalt redundant intern och extern Azure Domain Name Service-infrastruktur (DNS), kombinerat med flera primära och sekundära DNS-serverkluster, ger fel tolerans. Samtidigt används ytterligare Azure Network Security-kontroller, till exempel NetScaler, för att förhindra DDoS-attacker (distributed denial of Service) och skydda Azure DNS tjänsternas integritet.

Azure DNS-servrarna finns i flera data Center anläggningar. Azure DNS implementationen införlivar en hierarki med sekundära och primära DNS-servrar för att offentligt matcha Azures kund domän namn. Domän namn matchas vanligt vis till en CloudApp.net-adress, som omsluter den virtuella IP-adressen (VIP) för kundens tjänst. Unikt för Azure, den VIP som motsvarar den interna dedikerade IP-adressen (DIP) för klient översättningen görs av de Microsoft-belastningsutjämnare som är ansvariga för denna VIP.

Azure är värd för geografiskt distribuerade Azure-datacenter i USA, och det bygger på de plattformar som används för den grafik som implementerar robusta, skalbara arkitektur standarder. Bland de viktiga funktionerna är:

- MPLS-baserad (Multiprotocol Label Switching) trafik teknik, som ger effektiv länk användning och bättre försämring av tjänsten om det uppstår ett avbrott.
- Nätverk implementeras med "behöver plus ett" (N + 1) redundans arkitekturer eller bättre.
- Externt betjänas Data Center av dedikerade nätverks kretsar med hög bandbredd som redundant ansluter egenskaper med över 1 200 Internet leverantörer globalt på flera peering-platser. Den här anslutningen ger större än 2 000 GB per sekund (Gbit/s) av Edge-kapacitet.

Eftersom Microsoft äger sina egna nätverks kretsar mellan data Center kan de här attributen hjälpa Azure-erbjudandet att uppnå 99,9 + procent nätverks tillgänglighet utan att det behövs några traditionella Internet leverantörer från tredje part.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Anslutning till produktions nätverk och tillhör ande brand väggar
Azure Network Internet Traffic Flow-principen dirigerar trafik till Azures produktions nätverk som finns i närmaste regionala Data Center i USA. Eftersom Azures produktions Data Center upprätthåller konsekvent nätverks arkitektur och maskin vara, så gäller trafik flödes beskrivningen som följer konsekvent till alla data Center.

När Internet trafik för Azure dirigeras till närmaste Data Center, upprättas en anslutning till åtkomst routrarna. Dessa routrar för åtkomst för att isolera trafik mellan Azure-noder och kundinstansierade virtuella datorer. Nätverks infrastruktur enheter på åtkomst-och gräns platserna är de gräns punkter där inkommande och utgående filter används. Dessa routrar konfigureras via en lista över åtkomst kontrol listor (ACL) för att filtrera oönskad nätverks trafik och tillämpa trafik hastighets begränsningar, om det behövs. Trafik som tillåts av ACL dirigeras till belastningsutjämnarens belastnings utjämning. Distributions routrar är utformade för att endast tillåta Microsoft-godkända IP-adresser, tillhandahålla skydd mot förfalskning och upprätta TCP-anslutningar som använder ACL: er.

Externa enheter för belastnings utjämning finns bakom åtkomst routrarna för att utföra Network Address Translation (NAT) från Internet-dirigerade IP-adresser till interna Azure-IP-adresser. Enheterna dirigerar också paket till giltiga produktioners interna IP-adresser och portar, och de fungerar som en skydds mekanism för att begränsa exponeringen av det interna adress utrymmet för produktions nätverket.

Som standard tvingar Microsoft Hypertext Transfer Protocol Secure (HTTPS) för all trafik som överförs till kunders webbläsare, inklusive inloggning och all trafik därefter. Användningen av TLS v 1.2 gör det möjligt för en säker tunnel att passera. ACL: er för åtkomst-och kärn routrar ser till att trafik källan stämmer överens med vad som förväntas.

En viktig skillnad i den här arkitekturen, när den jämförs med traditionell säkerhets arkitektur, är att det inte finns några särskilda maskin varu brand väggar, specialiserade intrångs identifierings-eller skydds enheter eller andra säkerhets anordningar som normalt förväntas innan anslutningar görs till Azures produktions miljö. Kunderna förväntar sig vanligt vis dessa maskinvarubaserade brand Väggs enheter i Azure-nätverket. ingen är dock anställd i Azure. Dessa säkerhetsfunktioner är nästan uteslutande inbyggda i program varan som kör Azure-miljön för att tillhandahålla robusta säkerhetsmekanismer för flera lager, inklusive brand Väggs funktioner. Dessutom är räckvidden för gränserna och tillhör ande tillväxten av kritiska säkerhetsenheter enklare att hantera och inventera, som visas i föregående bild, eftersom den hanteras av den program vara som kör Azure.

## <a name="core-security-and-firewall-features"></a>Funktioner för kärn säkerhet och brand vägg
Azure implementerar robusta program varu säkerhets-och brand Väggs funktioner på olika nivåer för att genomdriva säkerhetsfunktioner som vanligt vis förväntas i en traditionell miljö för att skydda den grundläggande säkerhetsauktoriseringen.

### <a name="azure-security-features"></a>Säkerhetsfunktioner i Azure
Azure implementerar värdbaserade program varu brand väggar i produktions nätverket. Flera kärn funktioner för säkerhet och brand väggar finns i Azures kärn miljö. Dessa säkerhetsfunktioner visar en djupgående strategi i Azure-miljön. Kund information i Azure skyddas av följande brand väggar:

**Hypervisor-brandvägg (paket filter)**: den här brand väggen implementeras i hypervisorn och konfigureras av FC-agenten (Fabric Controller). Den här brand väggen skyddar den klient som körs inuti den virtuella datorn från obehörig åtkomst. När en virtuell dator skapas blockeras som standard all trafik och sedan lägger FC-agenten till regler och undantag i filtret för att tillåta auktoriserad trafik.

Två kategorier av regler är programmerade här:

- **Machine config eller infrastruktur regler**: som standard är all kommunikation blockerad. Det finns undantag som gör att en virtuell dator kan skicka och ta emot Dynamic Host Configuration Protocol (DHCP)-kommunikation och DNS-information och skicka trafik till "offentlig" Internet utgående till andra virtuella datorer i FC-klustret och OS-aktiverings servern. Eftersom de virtuella datorernas lista över tillåtna utgående destinationer inte omfattar Azure router-undernät och andra Microsoft-egenskaper fungerar reglerna som ett skydds lager för dem.
- **Regler för roll konfigurations fil**: definierar inkommande ACL: er baserat på klient organisationens tjänst modell. Om en klient till exempel har en front webb på port 80 på en viss virtuell dator, öppnas port 80 för alla IP-adresser. Om den virtuella datorn har en arbets roll som kör, öppnas arbets rollen bara för den virtuella datorn inom samma klient organisation.

**Inbyggd värd brand vägg**: Azure Service Fabric och Azure Storage körs på ett ursprungligt operativ system, som inte har någon hypervisor och därför konfigureras Windows-brandväggen med föregående två uppsättningar med regler.

**Värd brand vägg**: värd brand väggen skyddar diskpartitionen, som kör hypervisor-modulen. Reglerna är programmerade för att bara tillåta att FC-och hopp rutor kommunicerar med diskpartitionen på en speciell port. De andra undantagen är att tillåta DHCP-svar och DNS-svar. Azure använder en dator konfigurations fil som innehåller en mall med brand Väggs regler för diskpartitionen. Det finns också ett undantag för värd brand väggen som gör det möjligt för virtuella datorer att kommunicera med värd komponenter, tråd Server och metadatatjänst via vissa protokoll/portar.

**Gäst brand vägg**: Windows-brandväggen i gäst operativ systemet, som kan konfigureras av kunder på kundens virtuella datorer och lagring.

Ytterligare säkerhetsfunktioner som är inbyggda i Azure-funktionerna är:

- Infrastruktur komponenter som tilldelas IP-adresser från DIP. En angripare på Internet kan inte hantera trafik till dessa adresser, eftersom den inte når Microsoft. Routrar för Internet Gateway filtrerar paket som endast adresseras till interna adresser, så de kommer inte att ange produktions nätverket. De enda komponenter som accepterar trafik som dirigeras till VIP: er är belastningsutjämnare.
- Brand väggar som implementeras på alla interna noder har tre primära säkerhets arkitekturs överväganden för alla tänkbara scenarier:

   - Brand väggarna placeras bakom belastningsutjämnaren och accepterar paket överallt. Dessa paket är avsedda att exponeras externt och motsvarar de öppna portarna i en traditionell gräns brand vägg.
   - Brand väggar accepterar bara paket från en begränsad uppsättning adresser. Detta är en del av en djupgående strategi mot DDoS-attacker. Sådana anslutningar autentiseras kryptografiskt.
   - Det går bara att komma åt brand väggar från Välj interna noder. De accepterar bara paket från en uppräknad lista med käll-IP-adresser, som alla är DIP i Azure-nätverket. En attack i företags nätverket kan till exempel dirigera begär anden till dessa adresser, men angreppen blockeras om inte paketets käll adress var ett i den uppräknade listan i Azure-nätverket.
     - Fjärråtkomstroutern vid perimeter blockerar utgående paket som är adresserade till en adress som är i Azure-nätverket på grund av dess konfigurerade statiska vägar.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokal och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure information system](infrastructure-components.md)
- [Azure nätverks arkitektur](infrastructure-network.md)
- [Azure SQL Database säkerhetsfunktioner](infrastructure-sql.md)
- [Åtgärder och hantering av Azure-produktion](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Integritet för Azure-infrastruktur](infrastructure-integrity.md)
- [Data skydd för Azure-kunder](protection-customer-data.md)
