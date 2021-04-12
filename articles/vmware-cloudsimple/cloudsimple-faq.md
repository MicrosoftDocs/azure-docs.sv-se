---
title: Vanliga frågor och svar om Azure VMware-lösningar från CloudSimple
description: Dessa vanliga frågor och svar om Azure VMware-lösningen av CloudSimple inkluderar anslutning, nätverk, säkerhet, lagring, Azure-integrering och andra.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3551327f9aa07fe218d3b05f73057e36f92abcad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895571"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Vanliga frågor om VMware-lösning av CloudSimple

## <a name="cloudsimple-service"></a>CloudSimple-tjänst

**Vad är Azure VMware-lösning av CloudSimple?**

Azure VMware-lösningen genom CloudSimple transformerar och utökar VMware-arbetsbelastningar till privata, dedikerade moln på Azure på några minuter. CloudSimple tar hand om etablering, hantering av infrastruktur och dirigering av arbets belastningar mellan lokalt och Azure. Eftersom dina appar körs exakt på samma plats och i Azure drar du nytta av elastiskheten och tjänsterna i molnet utan att du behöver bygga om dina appar. CloudSimple sänker den totala ägande kostnaden med en moln förbruknings modell som tillhandahåller etablering på begäran, betala per användning och kapacitets optimering.  Se [Vad är VMware-lösning på Azure av CloudSimple](cloudsimple-vmware-solutions-overview.md) för funktioner, förmåner och scenarier.

**Vad är ett privat CloudSimple-moln?**

Ett privat CloudSimple-moln är ett privat, dedikerat moln som består av en beräknings-, lagrings-och nätverks miljö med höga prestanda som distribueras på Microsoft Azure infrastruktur (maskin vara och data Center utrymme) på Azure-platser.  Ett privat moln tillhandahåller en inbyggd VMware-plattform som tjänst. I VMware-termer innehåller varje privat moln exakt en instans av vCenter Server. VCenter Server hanterar flera ESXi-noder som finns i ett eller flera vSphere-kluster, tillsammans med motsvarande Virtual SAN-lagring (virtuellt SAN). En CloudSimple-tjänst kan innehålla flera privata moln i din Azure-prenumeration.  Mer information finns i [Översikt över privata moln](cloudsimple-private-cloud.md).

**Var finns CloudSimple-tjänsten?**

CloudSimple är tillgängligt i regionerna USA, västra USA, västra USA och västra Europa med ytterligare regioner som kommer snart.

**Hur gör jag för att aktivera min prenumeration för CloudSimple?**

Du kan kontakta din Microsoft-konto representant på [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) för att aktivera din prenumeration för CloudSimple-tjänsten. Ange ditt prenumerations-ID i e-postmeddelandet där du vill att CloudSimple-tjänsten är aktive rad  

**Hur gör jag för att du till gång till CloudSimple-portalen?**

Du kommer åt CloudSimple-portalen från Azure Portal.  Mer information finns i [komma åt VMware-lösningen från CloudSimple-portalen från Azure Portal](access-cloudsimple-portal.md).

**Hur gör jag för att ökar du kapaciteten för ett privat moln?**

Du ökar kapaciteten genom att köpa ytterligare noder från Azure Portal och sedan använda noderna för att expandera ditt privata moln från CloudSimple-portalen.  Du kan lägga till ytterligare noder i ett befintligt vSphere-kluster eller lägga till dem i ett nytt vSphere-kluster.  Mer information finns i [expandera ett privat CloudSimple-moln](expand-private-cloud.md).

**Vad händer med mitt privata moln under underhållet?**

CloudSimple tillhandahåller ett meddelande flera dagar före ett schemalagt underhålls intervall.  Underhåll sker på ett icke-störande sätt för att säkerställa att ditt privata moln är tillgängligt.  Underhåll kan vara av följande typer:

* **CloudSimple-infrastruktur**.  CloudSimple-infrastrukturen är utformad för att vara hög tillgänglig.  Vid den här typen av underhålls intervall uppdateras redundanta komponenter en i taget för att undvika avbrott i tjänsten. Du upprätthåller åtkomst till ditt privata moln vCenter, alla virtuella datorer, Internet anslutningen från ditt privata moln och anslutningar till lokalt eller Azure.
* **CloudSimple-portalen**. Under den här typen av underhålls intervall kan vissa funktioner på CloudSimple-portalen vara inaktiverade eller otillgängliga.  Meddelandet före underhålls intervallet innehåller information om funktions begränsningar medan underhåll sker.

## <a name="connectivity"></a>Anslutning

**Vilka är mina anslutnings alternativ till CloudSimple regions nätverk?**

CloudSimple tillhandahåller följande anslutnings alternativ för att ansluta till ditt CloudSimple-regions nätverk. Flera alternativ kan användas samtidigt.

* **ExpressRoute anslutning från ditt lokala data Center till CloudSimple regions nätverk**. Detta är en säker privat anslutning med hög hastighet och låg latens som använder Global Reach för att överbrygga din lokala ExpressRoute-krets till din CloudSimple ExpressRoute-krets. Instruktioner för hur du konfigurerar anslutningen finns i [ansluta från lokalt till CloudSimple med ExpressRoute](on-premises-connection.md).
* **ExpressRoute anslutning från ditt virtuella Azure-nätverk till ditt CloudSimple-regions nätverk**. Detta är en säker privat anslutning med hög hastighet och låg latens som använder virtuella nätverks-gatewayer för att överbrygga ditt virtuella nätverk på Azure till din CloudSimple ExpressRoute-krets. Instruktioner för hur du konfigurerar anslutningen finns i [ansluta din CloudSimple privata moln miljö till det virtuella Azure-nätverket med hjälp av ExpressRoute](azure-expressroute-connection.md).
* **Plats-till-plats-VPN-anslutning från ditt lokala data Center till ditt CloudSimple-region nätverk**. Det här är ett säkert virtuellt privat nätverk från din lokala VPN-enhet till din CloudSimple privata moln region.  Mer information finns i [Konfigurera VPN-gateways i CloudSimple-nätverk](vpn-gateway.md).

**Hur gör jag för att ansluta till ett privat moln?**

Du kan visa information om ditt privata moln i CloudSimple-portalen. För att ansluta till den vCenter som motsvarar ditt privata moln ska du först kontrol lera att en nätverks anslutning har upprättats med plats-till-plats-VPN, punkt-till-plats-VPN eller ExpressRoute. Starta sedan CloudSimple-portalen från Azure Portal och klicka på **Starta vSphere-klient** på Start sidan eller på informations sidan för det privata molnet.

**Vad är fördelen med ExpressRoute-kretsar?**

En Azure ExpressRoute-krets är en snabb, låg latens, säker anslutning med hög hastighet.  CloudSimple tillhandahåller en dedikerad ExpressRoute-krets per region per kund.  Med den här kretsen kan du upprätta en säker anslutning från en lokal plats eller din Azure-prenumeration.

**Vad är nätverks kostnaderna för att ansluta till CloudSimple?  Gäller alla utgående avgifter mellan CloudSimple och Azure, eller mellan regioner?**

Det finns ingen CloudSimple avgift för utgående nätverk.  Azure standard rates gäller för all utgående trafik från ditt virtuella nätverk eller från din lokala ExpressRoute-krets.

## <a name="networking"></a>Nätverk

**Vilka nätverksfunktioner är tillgängliga för mitt privata moln?**

Du kan etablera VLAN (och deras undernät) och brand Väggs tabeller och tilldela offentliga IP-adresser som mappar till en virtuell dator som körs i ditt privata moln. Mer information om nätverksfunktioner finns i [Översikt över VLAN och undernät](cloudsimple-vlans-subnets.md), översikt [över brand Väggs tabeller](cloudsimple-firewall-tables.md)och [Översikt över offentliga IP-adresser](cloudsimple-public-ip-address.md).

**Hur gör jag för att du konfigurera olika undernät för mina program i mitt privata moln?**

Du skapar VLAN i ditt privata moln från CloudSimple-portalen.  När du har skapat ett VLAN kan du skapa en distribuerad port grupp i ditt privata moln vCenter med hjälp av VLAN och skapa virtuella datorer som är anslutna till den distribuerade port gruppen.  Du kan aktivera brand Väggs tabeller för VLAN/undernät och definiera brand Väggs regler för säker nätverks trafik.

**Vilka brand Väggs inställningar är tillgängliga för mina privata moln?**

Du kan konfigurera regler för Nord-Syd-och öst-väst-trafik.  Reglerna definieras i en brand Väggs tabell.  Brand Väggs tabellen kan kopplas till VLAN i ditt privata moln.  Mer information finns i [Konfigurera brand Väggs tabeller och regler för privata moln](firewall.md).

**Kan jag tilldela offentliga IP-adresser till virtuella datorer i min privata moln miljö?**

I CloudSimple-portalen kan du allokera en ny offentlig IP-adress och koppla den till den privata IP-adressen för en virtuell dator eller en installation.  Du kan också skapa nya brand Väggs regler eller använda befintliga brand Väggs regler för att tillåta trafik från vissa portar och IP-adresser i portalen. Mer information finns i [tilldela offentliga IP-adresser för privat moln miljö](public-ips.md).

## <a name="security"></a>Säkerhet

**Vilka är mina säkerhets alternativ på CloudSimple?**

CloudSimple innehåller följande säkerhetsfunktioner för att skydda din privata moln miljö:

* **Data vid rest-kryptering**. Du kan kryptera data i vila på virtuellt San-lagringen i ditt privata moln. Virtuellt San stöder hanterings servrar för externa nycklar, som kan distribueras i din Azure vNet eller lokala miljö.  Mer information finns i [Konfigurera virtuellt San-kryptering för ditt CloudSimple privata moln](vsan-encryption.md).
* **Nätverks säkerhet**. Kontrol lera nätverks trafikflöde med brand Väggs regler som gäller mellan ditt privata moln och Internet, ditt privata moln och den lokala miljön eller inom undernät i ditt privata moln.
* **Säker, privat anslutning**. En säker, privat anslutning upprättas mellan ditt lokala nätverk och din Azure-prenumeration.

## <a name="compute"></a>Compute

**Vilken typ av värdar är tillgängliga?**

CloudSimple erbjuder följande värd typer:

* **CS28-nod:** PROCESSOR: 2x 2,2 GHz, totalt 28 kärnor, 48 HT.  RAM: 256 GB.  Lagring: 1600 GB NVMe-cache, 5760 GB data (alla-blinkar). Nätverk: 4x25Gbe NIC
* **CS36-nod:** CPU 2x 2,3 GHz, totalt 36 kärnor, 72 HT.  RAM: 512 GB.  Lagring: 3200 GB NVMe cache 11520 GB data (alla-blinkar).  Nätverk: 4x25Gbe NIC
* **CS36m-nod:** CPU 2x 2,3 GHz, totalt 36 kärnor, 72 HT.  RAM: 576 GB.  Lagring: 3200 GB NVMe cache 13360 GB data (alla-blinkar).  Nätverk: 4x25Gbe NIC

**Hur hanteras eventuella maskin varu fel?**

All CloudSimple-infrastruktur övervakas kontinuerligt av CloudSimple-plattformen och våra tjänst drifts team.  Om ett maskin varu fel upptäcks läggs en ny nod till i det privata molnet och den felande noden tas bort.

## <a name="storage"></a>Storage

**Vilken typ av lagrings utrymme stöds i ett privat moln?**

CloudSimple erbjuder all-Flash VMware virtuellt SAN-lagring med varje privat moln.  Varje vSphere skapas med ett eget virtuellt San-datalager.  Mer information finns i [privat moln VMware-komponenter – virtuellt San Storage](vmware-components.md#vsan-storage).

**Stöds kryptering av data?**
Ja.  Du kan konfigurera virtuellt San-lagringen i ditt privata moln för att använda en nyckel hanterings Server (KMS) som distribueras lokalt eller på Azure för att kryptera data som lagras på virtuellt San.

**Hur hanteras de felande diskarna?**

CloudSimple övervakar kontinuerligt alla maskin varu komponenter i det privata molnet.  Om ett diskfel upptäcks eller en disk identifieras som misslyckad (baserat på heuristik) läggs en ny nod automatiskt till i det privata molnet.  Noden med den felande eller felaktiga disken tas bort från det privata molnet.

## <a name="vmware"></a>VMware

**Hur gör jag för att utföra storskalig uppladdning eller migrering av program och data från lokala enheter?**

CloudSimple tillhandahåller en inbyggd VMware vSphere-lösning.  Alla VMware-verktyg för datamigrering kan användas med ditt privata moln.  Alternativen är:

* VMware-HCX för Mass migrering av data.
* Kall migrering av data med hjälp av Storage vMotion från lokala enheter till CloudSimple.

**Kan jag installera alla VMware-verktyg?**

CloudSimple tillhandahåller en inbyggd VMware vSphere-lösning.  Alla VMware-verktyg som används för att hantera din lokala vSphere-miljö kan användas på CloudSimple.  CloudSimple har stöd för en BYOL-modell (a-Your-License) för att installera VMware-verktyg.

**Hur hanteras uppdateringar och uppgraderingar?**

CloudSimple hanterar och uppdaterar alla infrastruktur komponenter i ditt privata moln på ett smidigt sätt som inte störs.  Alla uppdateringar och säkerhets uppdateringar som publicerats av VMware-eller infrastruktur leverantörer är schemalagda för uppdatering så snart de kvalificeras av CloudSimple.

CloudSimple utför inte uppgraderingar eller uppdateringar av program som är installerade i det privata molnet.

## <a name="azure-integration"></a>Azure-integrering

**Vilka Azure-tjänster stöds?**

CloudSimple tillhandahåller en Azure ExpressRoute-anslutning till din prenumeration på Azure.  Alla tjänster som körs i din prenumeration kan ansluta till ditt privata moln.  Exempel:

* **Azure Active Directory** som identitets källa för CloudSimple vCenter.
* **Azure Storage** för lagring av säkerhets kopior, avbildningar och andra data från ditt privata moln.
* **Hybrid program** med en program arkitektur som omfattar offentliga och privata moln.  Du kan till exempel skapa webbservrar i Azure som ansluter till program-och databas servrar i ditt privata moln.
* **Azure Monitor** och **Azure Security Center** för arbets belastningar som körs på VMware support-loggning, prestanda mått och säkerhets hantering.

**Hur gör jag för att mappar jag mina VMware-klienter till Azure?**

CloudSimple tillhandahåller den unika möjligheten att hantera virtuella VMware-datorer i privata moln från Azure Portal.  En vCenter-resurspool som kon figurer ATS med önskade resurs begränsningar kan mappas till din prenumeration av din globala administratör.  

**Vilka licensierings fördelar får jag med Azure?**

Med CloudSimple kan du dra nytta av Azure Hybrid Usage-förmånen och Spara upp till 90% på licenser. Den här förmånen bevarar din investering i Microsoft-licenser och sänker din TCO i förhållande till andra moln lösningar. Du får också utökade säkerhets uppdateringar för Windows Server 2008 och Microsoft SQL Server 2008.  BYOL-modellen hjälper dig att hålla kostnaderna nere för vanliga appar, till exempel Veeam och Zerto.  
