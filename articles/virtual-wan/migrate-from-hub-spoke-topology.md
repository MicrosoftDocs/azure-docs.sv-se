---
title: 'Arkitektur: migrera till Azure Virtual WAN'
description: Lär dig hur du migrerar till Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: cherylmc
ms.openlocfilehash: e602905b461e370189cefed706ddc3a47e0199fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91839647"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrera till Azure Virtual WAN

Med Azure Virtual WAN kan företag förenkla sin globala anslutning för att dra nytta av Microsofts globala nätverks skala. Den här artikeln innehåller teknisk information för företag som vill migrera från en befintlig kundhanterad nav-och-eker-topologi till en design som använder Microsoft-hanterade virtuella WAN-hubbar.

För information om de fördelar som Azure Virtual WAN möjliggör för företag som använder ett molnbaseradt globalt Enterprise-nätverk, se [Global överföring nätverks arkitektur och virtuellt WAN](virtual-wan-global-transit-network-architecture.md).

:::image type="content" source="./media/migrate-from-hub-spoke-topology/hub-spoke.png" alt-text="hubb och eker":::
**Bild: Azure Virtual WAN**

Azure Hub-och-eker-nätverksanslutningen har antagits av tusentals kunder för att utnyttja standard beteendet för transitivt beteende i Azure-nätverk för att bygga enkla och skalbara moln nätverk. Azure Virtual WAN bygger på dessa begrepp och introducerar nya funktioner som tillåter globala anslutnings topologier, inte bara mellan lokala platser och Azure, utan också att kunderna kan utnyttja skalan i Microsoft Network för att utöka sina befintliga globala nätverk.

Den här artikeln visar hur du migrerar en befintlig kundhanterad nav-och-eker-miljö till en topologi som baseras på Azure Virtual WAN.

## <a name="scenario"></a>Scenario

Contoso är en global finansiell organisation med kontor i både Europa och Asien. De planerar att flytta sina befintliga program från ett lokalt Data Center i till Azure och har skapat en grund design baserat på den Kundhanterade nav-och-eker-arkitekturen, inklusive virtuella nätverk i regionala hubbar för Hybrid anslutning. Som en del av förflyttningen till molnbaserade tekniker har nätverks teamet varit inloggade med att se till att deras anslutning är optimerad för att företaget ska flytta framåt.

Följande bild visar en övergripande vy över det befintliga globala nätverket, inklusive anslutning till flera Azure-regioner.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png" alt-text="Contoso befintlig nätverkstopologi":::
**Bild: contoso befintlig nätverkstopologi**

Följande punkter kan förstås från den befintliga nätverk sto pol Ogin:

* En nav-och-eker-topologi används i flera regioner, inklusive ExpressRoute-kretsar för anslutning tillbaka till ett gemensamt privat WAN (Wide Area Network).

* Vissa av dessa platser har också VPN-tunnlar direkt i Azure för att uppnå program som finns i molnet.

## <a name="requirements"></a>Krav

Nätverks teamet har fått en uppgift om att leverera en global nätverks modell som har stöd för Contoso-migrering till molnet och måste optimera i områdena kostnad, skala och prestanda. I sammanfattning måste följande krav uppfyllas:

* Ange både Head-fjärdedel (HQ) och avdelnings kontor med optimerade vägar till program som körs i molnet.
* Ta bort beroendet av befintliga lokala data Center (DC) för VPN-avslutning och Behåll följande anslutnings vägar:
  * **Branch-till-VNet**: VPN-anslutna kontor måste kunna komma åt program som migrerats till molnet i den lokala Azure-regionen.
  * **Gren-till-hubb till hubb-till-VNet**: VPN-anslutna kontor måste kunna komma åt program som migrerats till molnet i den fjärranslutna Azure-regionen.
  * **Gren-till-gren**: regionala VPN-anslutna kontor måste kunna kommunicera med varandra och EXPRESSROUTE anslutna HQ/DC-platser.
  * **Gren-till-hubb till nav-till-gren**: globalt ÅTSKILDA VPN-anslutna kontor måste kunna kommunicera med varandra och alla EXPRESSROUTE anslutna HQ/DC-platser.
  * **Gren-till-Internet**: anslutna platser måste kunna kommunicera med Internet. Den här trafiken måste filtreras och loggas.
  * **VNet-till-VNet**: ekrar virtuella nätverk i samma region måste kunna kommunicera med varandra.
  * **VNet-till-hubb till hubb-till-VNet**: ekrar virtuella nätverk i olika regioner måste kunna kommunicera med varandra.
* Ange möjligheten för Contoso-centrala användare (bärbar dator och telefon) för att få åtkomst till företags resurser när de inte finns i företags nätverket.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Azure Virtual WAN-arkitektur

Följande bild visar en övergripande vy av den uppdaterade måldomänkontrollanten med Azure Virtual WAN för att uppfylla kraven som beskrivs i föregående avsnitt.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/vwan-architecture.png" alt-text="Contoso Virtual WAN-arkitektur":::
**Bild: Azure Virtual WAN-arkitektur**

Sammanfattning:

* HQ i Europa förblir ExpressRoute ansluten, den lokala Europa-DOMÄNKONTROLLANTen migreras fullständigt till Azure och tas nu ur bruk.
* Asien och HQ förblir anslutna till privat WAN. Azure Virtual WAN används nu för att utöka det lokala bärvåg nätverket och tillhandahålla global anslutning.
* Azure virtuella WAN-hubbar som distribueras i både Västeuropa och Asien, sydöstra Azure-regioner för att tillhandahålla anslutnings nav för ExpressRoute-och VPN-anslutna enheter.
* Hubbar ger också VPN-avslutning för centrala användare över flera klient typer som använder OpenVPN-anslutning till det globala nät nätverket, vilket ger åtkomst till inte bara program som migrerats till Azure, utan även eventuella resurser som återstår lokalt.
* Internet anslutning för resurser i ett virtuellt nätverk som tillhandahålls av Azure Virtual WAN.

Internet anslutning för fjärranslutna platser tillhandahålls även av Azure Virtual WAN. Lokala Internet-grupp som stöds via partner integration för optimerad åtkomst till SaaS-tjänster som Microsoft 365.

## <a name="migrate-to-virtual-wan"></a>Migrera till Virtual WAN

Det här avsnittet visar de olika stegen för att migrera till Azure Virtual WAN.

### <a name="step-1-single-region-customer-managed-hub-and-spoke"></a>Steg 1: en kundhanterad hubb-och-eker-region

Följande bild visar en topologi för en enda region för Contoso innan du inför distributionen av Azure Virtual WAN:

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure1.png" alt-text="Topologi för enskild region":::
**Bild 1: manuell hubb för en region och ekrar**

I enlighet med hubb-och eker-metoden innehåller det virtuella kund hanterade Hubbs nätverket flera funktions block:

* Delade tjänster (alla vanliga funktioner som krävs av flera ekrar). Exempel: Contoso använder Windows Server-domänkontrollanter på IaaS-datorer (Infrastructure-as-a-Service).
* IP/routing Firewall-tjänster tillhandahålls av en virtuell nätverks installation från tredje part, vilket aktiverar eker-till-ekrar Layer-3 IP-routning.
* Ingångs-och utgångs tjänster för Internet, inklusive Azure Application Gateway för inkommande HTTPS-begäranden och tredjeparts Proxy-tjänster som körs på virtuella datorer för filtrerad utgående åtkomst till Internet resurser.
* ExpressRoute och VPN-gateway för anslutning till lokala nätverk.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Steg 2: distribuera virtuella WAN-hubbar

Distribuera en virtuell WAN-hubb i varje region. Konfigurera den virtuella WAN-hubben med VPN-och ExpressRoute-funktioner enligt beskrivningen i följande artiklar:

* [Självstudie: Skapa en plats-till-plats-anslutning med Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
* [Självstudie: skapa en ExpressRoute-Association med Azure Virtual WAN](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Azure Virtual WAN måste använda standard-SKU: n för att aktivera vissa trafik Sök vägar som visas i den här artikeln.
>

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure2.png" alt-text="Distribuera virtuella WAN-hubbar":::
**Bild 2: Kundhanterade nav-och-eker till virtuell WAN-migrering**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Steg 3: ansluta fjärranslutna platser (ExpressRoute och VPN) till virtuellt WAN

Anslut den virtuella WAN-hubben till de befintliga ExpressRoute-kretsarna och Ställ in plats-till-plats-VPN via Internet till alla fjärranslutna grenar.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure3.png" alt-text="Ansluta fjärranslutna platser till virtuella WAN-nätverk":::
**Figur 3: kundhanterad hubb-och-eker till virtuell WAN-migrering**

I det här läget börjar den lokala nätverks utrustningen ta emot vägar som motsvarar IP-adressutrymmet som tilldelats det virtuella WAN-hanterade hubb-nätverket. Fjärr-VPN-anslutna grenar i det här skedet kommer att se två sökvägar till alla befintliga program i ekrarnas virtuella nätverk. De här enheterna bör konfigureras att fortsätta använda tunneln till den Kundhanterade hubben för att säkerställa symmetrisk routning under över gångs fasen.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Steg 4: testa hybrid anslutning via virtuellt WAN

Innan du använder den hanterade virtuella WAN-hubben för produktions anslutning, rekommenderar vi att du skapar ett virtuellt nätverk för testning av ekrar och virtuell WAN-anslutning. Verifiera att anslutningar till den här test miljön fungerar via ExpressRoute och plats till plats-VPN innan du fortsätter med nästa steg.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure4.png" alt-text="Testa hybrid anslutning via virtuellt WAN":::
**Figur 4: Kundhanterade nav-och-eker till virtuell WAN-migrering**

I det här skedet är det viktigt att känna till att både det ursprungliga kund hanterade hubb nätverket och det nya virtuella WAN-navet är anslutna till samma ExpressRoute-krets. På grund av detta har vi en trafik Sök väg som kan användas för att aktivera ekrar i båda miljöerna för att kommunicera. Trafik från en eker som är kopplad till den Kundhanterade hubben i det virtuella nätverket kommer till exempel att söka igenom de MSEE: N enheter som används för ExpressRoute-kretsen för att komma åt ekrar som är anslutna via en VNet-anslutning till den nya virtuella WAN-hubben. Detta möjliggör mellanlagrad migrering av ekrar i steg 5.

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Steg 5: över gångs anslutning till Virtual WAN Hub

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure5.png" alt-text="Över gångs anslutning till virtuell WAN-hubb":::
**Figur 5: kundhanterad hubb-och-eker till virtuell WAN-migrering**

**a**. Ta bort befintliga peering-anslutningar från ekrar virtuella nätverk till den gamla kund hanterade hubben. Åtkomst till program i ekrar virtuella nätverk är inte tillgänglig förrän steg a-c har slutförts.

**b**. Anslut de eker-virtuella nätverken till den virtuella WAN-hubben via VNet-anslutningar.

**c**. Ta bort alla användardefinierade vägar (UDR) som tidigare använts i ekrar för virtuella nätverk för eker-till-ekrar-kommunikation. Den här sökvägen är nu aktive rad genom dynamisk routning tillgänglig i den virtuella WAN-hubben.

**d**. Befintliga ExpressRoute-och VPN-gatewayer i det Kundhanterade navet har nu inaktiverats för att tillåta nästa steg (e).

**e**. Anslut den gamla kund hanterade hubben (hubb virtuellt nätverk) till den virtuella WAN-hubben via en ny VNet-anslutning.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Steg 6: den gamla hubben blir delade tjänster eker

Nu har vi gjort om vårt Azure-nätverk för att göra den virtuella WAN-hubben till den centrala punkten i vår nya topologi.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure6.png" alt-text="Den gamla hubben blir delade tjänster eker":::
**Figur 6: Kundhanterade nav-och-eker till virtuell WAN-migrering**

Eftersom den virtuella WAN-hubben är en hanterad entitet och inte tillåter distribution av anpassade resurser, t. ex. virtuella datorer, finns inte blocket Shared Services som ett eker-virtuellt nätverk och fungerar som Internet-inkommande trafik via Azure Application Gateway eller virtualiserad nätverks installation. Trafik mellan de delade tjänsterna och de virtuella datorerna i drifts miljön överför nu den virtuella WAN-hanterade hubben.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Steg 7: optimera lokal anslutning för att utnyttja virtuellt WAN

I det här skedet har contoso främst slutfört sina migreringar av affärs program i Microsoft Cloud, med bara några få äldre program kvar i den lokala DOMÄNKONTROLLANTen.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure7.png" alt-text="Optimera lokal anslutning för att utnyttja virtuellt WAN":::
**Figur 7: Kundhanterade nav-och-eker till virtuell WAN-migrering**

För att kunna utnyttja alla funktioner i Azure Virtual WAN väljer contoso att inaktivera sina äldre lokala VPN-anslutningar. Alla grenar som fortsätter att komma åt HQ eller DC-nätverk kan överföra Microsofts globala nätverk med hjälp av den inbyggda överförings dirigeringen för Azure Virtual WAN.

> [!NOTE]
> ExpressRoute Global Reach krävs för kunder som vill använda Microsoft stamnätet för att tillhandahålla ExpressRoute till ExpressRoute-överföring (visas inte i bild 7).
>

## <a name="end-state-architecture-and-traffic-paths"></a>Slut tillstånds arkitektur och trafik Sök vägar

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure8.png" alt-text="Slut tillstånds arkitektur och trafik Sök vägar":::
**Bild: virtuell WAN-WAN med dubbla regioner**

Det här avsnittet innehåller en översikt över hur den här topologin uppfyller de ursprungliga kraven genom att titta på några exempel på trafikflöden.

### <a name="path-1"></a>Sökväg 1

Sökväg 1 visar trafikflöde från en S2S VPN-ansluten gren i Asien till ett Azure VNet i den Asien, sydöstra regionen.

Trafiken dirigeras enligt följande:

* Asiens gren är ansluten via elastiska S2S BGP-aktiverade tunnlar till Asien, sydöstra virtuell WAN-hubb.

* Virtuellt WAN-nav i Asien dirigerar trafik lokalt till anslutna VNet.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow1.png" alt-text="Flöde 1":::

### <a name="path-2"></a>Sökväg 2

Sökväg 2 visar trafikflöde från ExpressRoute anslutna europeiska HQ till ett Azure VNet i den Asien, sydöstra regionen.

Trafiken dirigeras enligt följande:

* Europa HQ är anslutet via ExpressRoute-kretsen i den virtuella WAN-hubben i västra Europa.

* Global WAN Hub-till-hubb global anslutning möjliggör trafik överföring till VNet som är anslutet i fjärrregionen.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow2.png" alt-text="Flöde 2":::

### <a name="path-3"></a>Sökväg 3

Path 3 visar trafikflödet från den lokala Asien som är ansluten till privat WAN till en europeisk S2S-ansluten gren.

Trafiken dirigeras enligt följande:

* Asien DC är anslutet till lokalt privat WAN-bärvåg.

* ExpressRoute-kretsen slutar lokalt i privata WAN-anslutningar till den Asien, sydöstra virtuella WAN-hubben.

* Virtuell WAN Hub-till-hubb global anslutning möjliggör trafik överföring.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow3.png" alt-text="Flöde 3":::

### <a name="path-4"></a>Sökväg 4

Path 4 visar trafikflödet från ett Azure VNet i Asien, sydöstra region till ett Azure VNet i regionen Europa, västra.

Trafiken dirigeras enligt följande:

* Global WAN Hub-till-hubb global anslutning möjliggör inbyggd transitering av alla anslutna Azure-virtuella nätverk utan ytterligare användar konfiguration.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow4.png" alt-text="Flöde 4":::

### <a name="path-5"></a>Sökväg 5

Path 5 visar trafikflödet från centrala VPN-användare (P2S) till ett Azure VNet i regionen Europa, västra.

Trafiken dirigeras enligt följande:

* Användare av bärbara och mobila enheter använder OpenVPN-klienten för transparent anslutning i P2S VPN-gatewayen i Västeuropa.

* Den virtuella WAN-hubben i Västeuropa dirigerar trafik lokalt till anslutna VNet.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow5.png" alt-text="Flöde 5":::

## <a name="security-and-policy-control-via-azure-firewall"></a>Säkerhet och princip kontroll via Azure-brandväggen

Contoso har nu verifierat anslutningen mellan alla grenar och virtuella nätverk i enlighet med kraven som beskrivs ovan i den här artikeln. För att uppfylla kraven för säkerhets kontroll och nätverks isolering måste de fortsätta att separera och logga trafik via hubb nätverket. Tidigare utfördes den här funktionen av en virtuell nätverks installation (NVA). Contoso vill också inaktivera sina befintliga proxy Services och använda interna Azure-tjänster för utgående Internet filtrering.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/security-policy.png" alt-text="Säkerhet och princip kontroll via Azure-brandväggen":::
**Bild: Azure-brandvägg i virtuellt WAN-nätverk (säker virtuell hubb)**

Följande avancerade steg krävs för att presentera Azure-brandväggen i de virtuella WAN-hubbarna för att aktivera en enhetlig punkt för princip kontroll. Mer information om den här processen och begreppet säkra virtuella hubbar finns i [Azure Firewall Manager](../firewall-manager/index.yml).

1. Skapa Azure Firewall-princip.
2. Länka brand Väggs princip till Azure Virtual WAN Hub. Det här steget gör att den befintliga virtuella WAN-hubben fungerar som en säker virtuell hubb och distribuerar de nödvändiga Azure Firewall-resurserna.

> [!NOTE]
> Det finns begränsningar för användningen av skyddade virtuella hubbar, inklusive trafik mellan regioner. Mer information finns i [brand Väggs hanteraren – kända problem](../firewall-manager/overview.md#known-issues).
>

Följande sökvägar visar de anslutnings Sök vägar som Aktiver ATS med Azures säkra virtuella hubbar:

### <a name="path-6"></a>Sökväg 6

Path 6 visar ett säkert trafikflöde mellan virtuella nätverk inom samma region.

Trafiken dirigeras enligt följande:

* Virtuella nätverk som är anslutna till samma säkra virtuella hubb dirigerar nu trafik till via Azure-brandväggen.

* Azure-brandväggen kan använda principer för dessa flöden.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow6.png" alt-text="Flöde 6":::

### <a name="path-7"></a>Sökväg 7

Path 7 visar trafikflödet från ett virtuellt Azure-nätverk till Internet eller från en säkerhets tjänst från tredje part.

Trafiken dirigeras enligt följande:

* Virtuella nätverk som är anslutna till den säkra virtuella hubben kan skicka trafik till offentliga, destinationer på Internet, med hjälp av säker hubb som en central punkt för Internet åtkomst.

* Trafiken kan filtreras lokalt med hjälp av Azure Firewall FQDN-regler eller skickas till en säkerhets tjänst från tredje part för inspektion.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow7.png" alt-text="Flow 7":::

### <a name="path-8"></a>Sökväg 8

Path 8 visar trafikflödet från gren till Internet eller säkerhets tjänst från tredje part.

Trafiken dirigeras enligt följande:

* Grenar som är anslutna till den säkra virtuella hubben kan skicka trafik till offentliga destinationer på Internet med hjälp av säker hubb som en central punkt för Internet åtkomst.

* Trafiken kan filtreras lokalt med hjälp av Azure Firewall FQDN-regler eller skickas till en säkerhets tjänst från tredje part för inspektion.

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow8.png" alt-text="Flöde 8":::

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Virtual WAN](virtual-wan-about.md).
