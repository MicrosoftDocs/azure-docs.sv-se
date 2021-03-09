---
title: SAP Business One på Azure Virtual Machines | Microsoft Docs
description: SAP Business One på Azure.
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 07/15/2018
ms.author: juergent
ms.openlocfilehash: e17739c65c0b80beb1f6fdd09f31897b317d7858
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506896"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One på Azure Virtual Machines
Det här dokumentet innehåller rikt linjer för att distribuera SAP Business One på Azure Virtual Machines. Dokumentationen ersätter inte installations dokumentationen för Business One för SAP. Dokumentationen bör avse grundläggande planerings-och distributions rikt linjer för Azure-infrastrukturen för att köra företag ett program på.

Företag One har stöd för två olika databaser:
- SQL Server – se [SAP Note #928839-release Planning for Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA – för exakta SAP Business One support-matris för SAP HANA, checka ut [matrisen SAP Product Availability](https://support.sap.com/pam)

Vad gäller SQL Server gäller grundläggande distribution av distributioner som dokumenteras i [Azure Virtual Machines DBMS-distribution för SAP NetWeaver](./dbms_guide_general.md) . för SAP HANA beskrivs överväganden i det här dokumentet.

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda den här guiden behöver du grundläggande kunskaper om följande Azure-komponenter:

- [Virtuella Azure-datorer i Windows](../../windows/tutorial-manage-vm.md)
- [Virtuella Azure-datorer i Linux](../../linux/tutorial-manage-vm.md)
- [Hantering av Azure-nätverk och virtuella nätverk med PowerShell](../../windows/tutorial-virtual-network.md)
- [Azure-nätverk och virtuella nätverk med CLI](../../linux/tutorial-virtual-network.md)
- [Hantera Azure-diskar med Azure CLI](../../linux/tutorial-manage-disks.md)

Även om du bara är intresse rad av ett företag, kan dokumentet [Azure Virtual Machines planering och implementering för SAP NetWeaver](./planning-guide.md) vara en korrekt informations källa.

Antagandet är att du är den instans som distribuerar SAP Business One:

- Bekant med att installera SAP HANA i en specifik infrastruktur som en virtuell dator
- Välbekant installation av SAP Business One-programmet i en infrastruktur som virtuella Azure-datorer
- Bekant med drifts-SAP Business One och det DBMS-system som valts
- Bekant med distribution av infrastruktur i Azure

Alla dessa områden kommer inte att omfattas av det här dokumentet.

Förutom Azure-dokumentationen bör du vara medveten om huvud-SAP-anteckningar som hänvisar till företag en eller som är centrala anteckningar från SAP för företag One:

- [528296 – allmän översikts anteckning för SAP Business One-versioner och relaterade produkter](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 – versions uppdateringar anmärkning för SAP Business One 9,2, version för SAP HANA](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 – Central anmärkning för SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 – versions uppdateringar anmärkning för SAP Business One 9,3](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 – samlad anmärkning för SAP Business One 9,3 allmänna problem](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 – kollektiv rådgivning om SAP HANA-Related avsnitt av SAP Business One, version för SAP HANA](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Företag One-arkitektur
Företag ett är ett program som har två nivåer:

- En klient nivå med en "fat"-klient
- En databas nivå som innehåller databasschemat för en klient

En bättre översikt över vilka komponenter som körs i klient delen och vilka delar som körs i Server delen finns dokumenterade i [SAP Business One administratörs guide](https://help.sap.com/doc/601fbd9113be4240b81d74626439cfa9/10.0/en-US/AdministratorGuide_SQL.pdf) 

Eftersom det finns en kritisk svars tid av hög latens mellan klient nivån och DBMS-nivån måste båda nivåerna finnas i Azure när de distribueras i Azure. Det är vanligt att användarna sedan får ett FJÄRRan vändare till en eller flera virtuella datorer som kör en RDS-tjänst för det företag en klient komponenter.

### <a name="sizing-vms-for-sap-business-one"></a>Ändra storlek på virtuella datorer för SAP Business One

När det gäller storleken på klientens virtuella datorer, dokumenteras resurs kraven av SAP i [hand boken för SAP Business One-maskinvara](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). För Azure måste du fokusera och beräkna med de krav som anges i kapitel 2,4 i dokumentet.

Som Azure Virtual Machines för att vara värd för företaget med en klient och DBMS-värden, tillåts endast virtuella datorer som är SAP-NetWeaver som stöds. Om du vill hitta en lista över SAP-NetWeaver som stöds av virtuella Azure-datorer kan du läsa [SAP obs #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Kör SAP HANA som DBMS-backend för företag One, endast virtuella datorer som listas för företag på HANA i den [Hana-certifierade IaaS plattforms listan](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One) stöds för Hana. Företaget en klient komponenter påverkas inte av den här starkare begränsningen för det SAP HANA som DBMS-systemet.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Operativ system versioner som ska användas för SAP Business One

I princip är det alltid bäst att använda de senaste versionerna av operativ systemet. Särskilt i Linux-utrymmet introducerades nya Azure-funktioner med andra nyare versioner av SUSE och Red Hat. På Windows-sidan rekommenderas att du använder Windows Server 2016.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Distribuera infrastruktur i Azure för SAP Business One
I de kommande kapitlen är infrastrukturen det som är viktigt för att distribuera SAP.

### <a name="azure-network-infrastructure"></a>Azure nätverks infrastruktur
Vilken nätverks infrastruktur du behöver distribuera i Azure beror på om du distribuerar ett enda företag ett system för dig själv. Eller om du är en värd som är värd för ett dussin tals affärs system för kunder. Det kan också finnas små ändringar i designen om hur du ansluter till Azure. Gå igenom olika möjligheter, en design där du har en VPN-anslutning till Azure och där du utökar din Active Directory via [VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../../../expressroute/expressroute-introduction.md) till Azure.

![Enkel nätverks konfiguration med företag ett](./media/business-one-azure/simple-network-with-VPN.PNG)

Den förenklade konfigurationen presenterar flera säkerhets instanser som tillåter kontroll och begränsning av routning. Den börjar med 

- Routern/brand väggen på den lokala kund sidan.
- Nästa instans är den [Azure-nätverks säkerhets grupp](../../../virtual-network/network-security-groups-overview.md) som du kan använda för att införa regler för Routning och säkerhet för det virtuella Azure-nätverket som du kör din SAP Business en konfiguration i.
- För att undvika att användare av företag en klient kan se den server som kör företaget en server, som kör-databasen, bör du separera den virtuella datorn som är värd för företaget en klient och det företag en server i två olika undernät i VNet.
- Du skulle använda Azure-NSG som tilldelats de två olika under näten igen för att begränsa åtkomsten till det företag en server.

En mer avancerad version av en Azure-nätverks konfiguration baseras på de Azure- [dokumenterade metod tipsen för hubb och eker-arkitektur](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Arkitektur mönstret för hubb och eker skulle ändra den första förenklade konfigurationen till en liknande:


![NAV-och eker-konfiguration med företag ett](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

I de fall där användarna ansluter via Internet utan någon privat anslutning till Azure, bör designen av nätverket i Azure justeras med principerna som dokumenteras i Azures referens arkitektur för [DMZ mellan Azure och Internet](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Företag en databas server
SQL Server och SAP HANA för databas typen är tillgängliga. Oberoende av DBMS bör du läsa dokument [överväganden för Azure Virtual Machines DBMS-distribution för SAP-arbetsbelastningar](./dbms_guide_general.md) för att få en allmän förståelse för DBMS-distributioner i virtuella Azure-datorer och relaterade nätverks-och lagrings ämnen.

Även om den har framhävts i de aktuella och allmänna databas dokumenten, bör du bekanta dig med:

- [Hantera tillgängligheten för virtuella Windows-datorer i Azure](../../availability.md) och [Hantera tillgängligheten för virtuella Linux-datorer i Azure](../../availability.md)
- [SLA för Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

De här dokumenten bör hjälpa dig att bestämma urvalet av lagrings typer och konfiguration med hög tillgänglighet.

I princip bör du:

- Använd Premium-SSD över standard hård diskar. Mer information om tillgängliga disk typer finns i vår artikel [Välj en disktyp](../../disks-types.md)
- Använd Azure Managed disks över ohanterade diskar
- Kontrol lera att du har tillräckligt med IOPS och I/O-genomflöde som kon figurer ATS med disk konfigurationen
- Kombinera/Hana/data-och/Hana/log-volymer för att få en kostnads effektiv lagrings konfiguration


#### <a name="sql-server-as-dbms"></a>SQL Server som DBMS
Om du vill distribuera SQL Server som DBMS för företag en, går du vidare till dokumentet [SQL Server Azure Virtual Machines DBMS-distribution för SAP NetWeaver](./dbms_guide_sqlserver.md). 

Beräkningar av grov storlek för SQL Server är:

| Antal användare | Virtuella processorer | Minne | Exempel på VM-typer |
| --- | --- | --- | --- |
| upp till 20 | 4 | 16 GB | D4s_v3 E4s_v3 |
| upp till 40 | 8 | 32 GB | D8s_v3 E8s_v3 |
| upp till 80 | 16 | 64 GB | D16s_v3 E16s_v3 |
| upp till 150 | 32 | 128 GB | D32s_v3 E32s_v3 |

Den storlek som anges ovan bör ge en idé att börja med. Det kan vara att du behöver mindre eller flera resurser, vilket innebär att det är enkelt att göra en anpassning i Azure. En ändring mellan VM-typer är möjlig med bara en omstart av den virtuella datorn.

#### <a name="sap-hana-as-dbms"></a>SAP HANA som DBMS
Med SAP HANA som DBMS i följande avsnitt bör du följa de överväganden som du bör tänka på när det gäller dokument [SAP HANA på Azures drift guide](./hana-vm-operations.md).

För konfigurationer med hög tillgänglighet och haveri beredskap runt SAP HANA som databas för företag en i Azure bör du läsa dokumentationen [SAP HANA hög tillgänglighet för Azure Virtual Machines](./sap-hana-availability-overview.md) och dokumentationen som pekas på dokumentet.

För SAP HANA säkerhets kopierings-och återställnings strategier bör du läsa [guiden dokumentera säkerhets kopiering för SAP HANA på Azure Virtual Machines](./sap-hana-backup-guide.md) och dokumentationen som pekas från dokumentet.

 
### <a name="business-one-client-server"></a>Företag en klient server
Det är inte viktigt att tänka på vid lagring av de här komponenterna. du vill dock ha en tillförlitlig plattform. Därför bör du använda Azure Premium Storage för den här virtuella datorn, även för den virtuella hård disken. Ändra storlek på den virtuella datorn med de data som finns i [hand boken för SAP Business en maskin varu krav](https://help.sap.com/doc/bfa9770d12284cce8509956dcd4c5fcb/9.3/en-US/B1_Hardware_Requirements_Guide.pdf). För Azure måste du fokusera och beräkna med de krav som anges i kapitel 2,4 i dokumentet. När du beräknar kraven måste du jämföra dem med följande dokument för att hitta den perfekta virtuella datorn för dig:

- [Storlekar för virtuella Windows-datorer i Azure](../../sizes.md)
- [SAP-anteckning #1928533](https://launchpad.support.sap.com/#/notes/1928533)

Jämför antalet processorer och minne som krävs för det som dokumenteras av Microsoft. Tänk också på nätverks genom strömning när du väljer de virtuella datorerna.
