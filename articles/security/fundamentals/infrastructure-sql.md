---
title: Azure SQL Database säkerhetsfunktioner
description: Den här artikeln innehåller en allmän beskrivning av hur Azure SQL Database skyddar kund information i Azure.
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
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: e0e7089e7c674f324c2c3d293661c518b41731b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84021865"
---
# <a name="azure-sql-database-security-features"></a>Azure SQL Database säkerhetsfunktioner    
Azure SQL Database tillhandahåller en Relations databas tjänst i Azure. För att skydda kunddata och tillhandahålla starka säkerhetsfunktioner som kunderna förväntar sig av en Relations databas tjänst, SQL Database ha egna uppsättningar med säkerhetsfunktioner. Dessa funktioner bygger på de kontroller som ärvs från Azure.

## <a name="security-capabilities"></a>Säkerhetsfunktioner

### <a name="usage-of-the-tds-protocol"></a>Användning av TDS-protokollet
Azure SQL Database stöder endast tabular data stream-protokollet (TDS), som kräver att databasen är tillgänglig enbart för TCP/1433-standardporten.

### <a name="azure-sql-database-firewall"></a>Azure SQL Database brand vägg
För att skydda kund information, Azure SQL Database innehåller en brand Väggs funktion som standard förhindrar all åtkomst till SQL Database som visas nedan.

![Azure SQL Database brand vägg](./media/infrastructure-sql/sql-database-firewall.png)

Gateway-brandväggen kan begränsa adresser, vilket ger kunderna detaljerad kontroll för att ange intervall med acceptabla IP-adresser. Brand väggen beviljar åtkomst baserat på den ursprungliga IP-adressen för varje begäran.

Kunder kan få brand Väggs konfiguration genom att använda en hanterings portal eller program mässigt med hjälp av Azure SQL Database hanterings REST API. Azure SQL Database Gateway-brandväggen som standard förhindrar all kund-TDS-åtkomst till Azure SQL Database. Kunder måste konfigurera åtkomst med hjälp av åtkomst kontrol listor (ACL: er) för att tillåta Azure SQL Database anslutningar via käll-och mål-Internet adresser, protokoll och port nummer.

### <a name="dosguard"></a>DoSGuard
DoS-attacker (Denial of Service) minskas med en gateway-tjänst för SQL Database som kallas DoSGuard. DoSGuard spårar aktivt misslyckade inloggningar från IP-adresser. Om det finns flera misslyckade inloggningar från en viss IP-adress inom en viss tids period, blockeras IP-adressen från åtkomst till alla resurser i tjänsten under en fördefinierad tids period.

Dessutom utför Azure SQL Database Gateway:

- Hantera kapacitets förhandlingar för att implementera TDS FIPS 140-2-verifierade krypterade anslutningar när den ansluter till databas servrarna.
- Tillstånds känslig kontroll av TDS-paket när den accepterar anslutningar från klienter. Gatewayen verifierar anslutnings informationen och vidarebefordrar de TDS-paketen till rätt fysiska Server baserat på databas namnet som anges i anslutnings strängen.

Den övergripande principen för nätverkssäkerhet för Azure SQL Database-tjänsten är att endast tillåta anslutning och kommunikation som krävs för att tjänsten ska fungera. Alla andra portar, protokoll och anslutningar blockeras som standard. Virtuella lokala nätverk (VLAN) och ACL: er används för att begränsa nätverkskommunikation genom käll-och mål nätverk, protokoll och port nummer.

Metoder som godkänns för att implementera nätverksbaserade ACL: er inkluderar ACL: er på routrar och belastningsutjämnare. Dessa mekanismer hanteras av Azure-nätverk, brand vägg för virtuella gäst datorer och brand Väggs regler för Azure SQL Database Gateway, som har kon figurer ATS av kunden.

## <a name="data-segregation-and-customer-isolation"></a>Data uppdelning och kund isolering
Azures produktions nätverk är strukturerat så att offentligt tillgängliga system komponenter åtskiljs från interna resurser. Fysiska och logiska gränser finns mellan webb servrar som ger åtkomst till den offentliga Azure Portal och den underliggande virtuella Azure-infrastrukturen, där kund program instanser och kund information finns.

All offentligt tillgänglig information hanteras i Azures produktions nätverk. Produktions nätverket omfattas av mekanismer för tvåfaktorautentisering och gränser, använder brand Väggs-och säkerhets funktionerna som beskrivs i föregående avsnitt och använder data isolerings funktioner som anges i nästa avsnitt.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Obehöriga system och isolering av FC
Eftersom infrastruktur styrenheten (FC) är den centrala Orchestrator-infrastrukturen i Azure-infrastrukturen finns det betydande kontroller för att minimera hot mot den, särskilt från potentiellt komprometterad FAs i kund program. FC känner inte igen maskin vara vars enhets information (till exempel MAC-adress) inte är förinstallerad i FC. DHCP-servrarna i FC har konfigurerat listor över MAC-adresser för de noder som de är villiga att starta. Även om obehöriga system är anslutna, är de inte införlivade i infrastruktur resurser och är därför inte anslutna eller auktoriserade att kommunicera med något system i infrastruktur resurs lagret. Detta minskar risken för obehöriga system som kommunicerar med FC och får åtkomst till VLAN och Azure.

### <a name="vlan-isolation"></a>VLAN-isolering
Azures produktions nätverk är logiskt åtskilda i tre primära VLAN:

- Huvud-VLAN: en samman koppling av ej betrodda kundnoder.
- FC VLAN: innehåller betrodda FCs-och support system.
- Enhetens VLAN: innehåller betrodda nätverks-och andra infrastruktur enheter.

### <a name="packet-filtering"></a>Paket filtrering
IPFilter och program varu brand väggarna som implementeras på rot operativ systemet och gäst operativ systemet på noderna tillämpar anslutnings begränsningar och förhindrar obehörig trafik mellan virtuella datorer.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor-, rot-OS och virtuella gäst datorer
Isolering av rot operativ systemet från de virtuella gäst datorerna och de virtuella gäst datorerna från varandra hanteras av hypervisorn och rot operativ systemet.

### <a name="types-of-rules-on-firewalls"></a>Typer av regler för brand väggar
En regel definieras som:

{Src IP, källport, mål-IP, målport, mål protokoll, in/ut, tillstånds känslig/tillstånds lös, timeout för tillstånds känsligt flöde}.

Tillståndet för synkrona inaktiva Character (SYN) tillåts endast i eller ut om någon av reglerna tillåter det. För TCP använder Azure tillstånds lösa regler där principen är att endast tillåta alla icke-SYN-paket till eller från den virtuella datorn. Säkerhets lokal är att alla värdars stackar är elastiska att ignorera en icke-SYN om den inte har sett ett SYN paket tidigare. Själva TCP-protokollet är tillstånds känsligt, och i kombination med tillstånds lösa SYNbaserade regler uppnås ett övergripande beteende för en tillstånds känslig implementering.

För User Datagram Protocol (UDP) använder Azure en tillstånds känslig regel. Varje gång ett UDP-paket matchar en regel skapas ett omvänt flöde i den andra riktningen. Det här flödet har en inbyggd tids gräns.

Kunderna ansvarar för att skapa egna brand väggar ovanpå vad Azure tillhandahåller. Här kan kunder definiera regler för inkommande och utgående trafik.

### <a name="production-configuration-management"></a>Hantering av produktions konfiguration
Standard säkra konfigurationer underhålls av respektive drift team i Azure och Azure SQL Database. Alla konfigurations ändringar i produktions systemen dokumenteras och spåras via ett centralt spårnings system. Ändringar av program-och maskin vara spåras via det centrala spårnings systemet. Nätverks ändringar som relaterar till ACL spåras med hjälp av en ACL Management-tjänst.

Alla konfigurations ändringar i Azure utvecklas och testas i mellanlagringsdatabasen, och de distribueras därefter i produktions miljön. Program versioner granskas som en del av testningen. Säkerhets-och sekretess kontroller granskas som en del av villkoren för post check lista. Ändringarna distribueras enligt de schemalagda intervallen av respektive distributions team. Versioner granskas och signeras av av respektive distributions grupp personal innan de distribueras till produktion.

Ändringarna övervakas för att lyckas. Vid ett haveri-scenario återställs ändringen till sitt tidigare tillstånd eller också distribueras en snabb korrigering för att åtgärda problemet med godkännande av den avsedda personalen. Käll depå, git, TFS, Master Data Services (MDS), löpare, Azure Security Monitoring, FC och WinFabric-plattformen används för att centralt hantera, tillämpa och kontrol lera konfigurations inställningarna i den virtuella Azure-miljön.

På samma sätt har ändringar av maskin vara och nätverk upprättat validerings steg för att utvärdera hur de uppfyller versions kraven. Versionerna granskas och auktoriseras via en koordinerad ändrings nämnd (CAB) för respektive grupp i stacken.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokal och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure information system](infrastructure-components.md)
- [Azure nätverks arkitektur](infrastructure-network.md)
- [Azures produktions nätverk](production-network.md)
- [Åtgärder och hantering av Azure-produktion](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Integritet för Azure-infrastruktur](infrastructure-integrity.md)
- [Data skydd för Azure-kunder](protection-customer-data.md)
