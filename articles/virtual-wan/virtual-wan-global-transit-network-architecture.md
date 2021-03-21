---
title: 'Arkitektur: global överföring nätverks arkitektur'
titleSuffix: Azure Virtual WAN
description: Lär dig hur Azure Virtual WAN möjliggör en global överförings nätverks arkitektur genom att aktivera allmänt förekommande, alla anslutningar mellan moln arbets belastningar.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: cherylmc
ms.openlocfilehash: cfb75b6383d8ca449b4bc54b9d21cb16b3a4ad40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99428191"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Global överföring av nätverks arkitektur och virtuellt WAN

Moderna företag kräver allmänt förekommande-anslutning mellan Hyper-distribuerade program, data och användare i hela molnet och lokalt. Global transit nätverks arkitektur antas av företag för att konsolidera, ansluta och kontrol lera det molnbaserade moderna, globala företags IT-utrymmet.

Den globala överförings nätverks arkitekturen baseras på en klassisk nav-och-eker-nätverksanslutning där det molnbaserade nätverkets hubb möjliggör transitiv anslutning mellan slut punkter som kan fördelas mellan olika typer av ekrar.

I den här modellen kan ekrar vara:
* Virtuellt nätverk (virtuella nätverk)
* Fysisk gren plats
* Fjärran vändare
* Internet

![hubb och eker](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Bild 1: global transiterings nav-och-eker-nätverk**

Bild 1 visar den logiska vyn för det globala överförings nätverket där geografiskt distribuerade användare, fysiska platser och virtuella nätverk är anslutna via en nätverks hubb som finns i molnet. Den här arkitekturen möjliggör logisk överföring av ett hopp mellan nätverks slut punkterna.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Globalt överförings nätverk med virtuellt WAN

Azure Virtual WAN är en Microsoft-hanterad moln nätverks tjänst. Alla nätverks komponenter som den här tjänsten består av är värdbaserade och hanteras av Microsoft. Mer information om virtuellt WAN finns i artikeln [Översikt över virtuella WAN-nätverk](virtual-wan-about.md) .

Azure Virtual WAN möjliggör en global överförings nätverks arkitektur genom att aktivera allmänt förekommande, alla anslutningar mellan globalt distribuerade uppsättningar av moln arbets belastningar i virtuella nätverk, avdelnings platser, SaaS och PaaS program och användare.

![Azure Virtual WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Bild 2: globalt överförings nätverk och virtuellt WAN**

I Azure Virtual WAN-arkitekturen är virtuella WAN-hubbar etablerade i Azure-regioner, som du kan välja att ansluta dina grenar, virtuella nätverk och fjärran vändare till. De fysiska gren platserna är anslutna till hubben från Premium-eller standard-ExpressRoute eller plats-till-plats-VPN, virtuella nätverk är anslutna till hubben via VNet-anslutningar och fjärran vändare kan ansluta direkt till hubben med hjälp av användarens VPN (punkt-till-plats-VPN). Virtual WAN stöder även VNet-anslutning över flera regioner där ett VNet i en region kan anslutas till en virtuell WAN-hubb i en annan region.

Du kan upprätta ett virtuellt WAN-nätverk genom att skapa en enda virtuell WAN-hubb i den region som har det största antalet ekrar (grenar, virtuella nätverk, användare) och sedan ansluta de ekrar som finns i andra regioner till hubben. Det här är ett användbart alternativ när ett företags avtryck huvudsakligen finns i en region med några få fjärrekers.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>NAV-till-hubb-anslutning

Ett företags moln utrymme kan omfatta flera moln regioner och är optimal (svars tid) för att få åtkomst till molnet från en region som är närmast deras fysiska plats och användare. En av nyckel principerna för global överförings nätverks arkitektur är att aktivera anslutningar mellan olika regioner mellan alla moln-och lokala nätverks slut punkter. Det innebär att trafik från en gren som är ansluten till molnet i en region kan komma åt en annan gren eller ett VNet i en annan region med nav-till-hubb-anslutning som är aktive rad av [Azure globalt nätverk](https://azure.microsoft.com/global-infrastructure/global-network/).

![över-region](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Bild 3: virtuell WAN-anslutning mellan regioner**

När flera hubbar är aktiverade i ett enda virtuellt WAN-nätverk, kopplas hubbarna automatiskt samman via nav-till-hubb-länkar, vilket möjliggör global anslutning mellan grenar och virtuella nätverk som distribueras över flera regioner. 

Dessutom kan hubbar som är alla delar av samma virtuella WAN-nätverk associeras med olika regionala åtkomst-och säkerhets principer. Mer information finns i [säkerhet och princip kontroll](#security) längre fram i den här artikeln.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Alla-till-alla-anslutningar

Global transit nätverks arkitektur möjliggör alla-till-alla-anslutningar via virtuella WAN-hubbar. Den här arkitekturen eliminerar eller minskar behovet av fullständigt nät eller en partiell nät anslutning mellan ekrar, som är mer komplexa för att bygga och underhålla. Dessutom är routnings kontroll i nav-och-eker vs. nät nätverk enklare att konfigurera och underhålla.

Alla anslutningar (i kontexten för en global arkitektur) gör det möjligt för företag med globalt distribuerade användare, grenar, data Center, virtuella nätverk och program att ansluta till varandra via "transitering"-hubben. Azure Virtual WAN fungerar som globalt system för överföring.

![valfri](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Bild 4: vägar för virtuella WAN-trafik**

Azure Virtual WAN stöder följande anslutnings vägar för global överföring. Bokstäverna i parenteser mappas till bild 4.

* Branch-till-VNet (a)
* Gren-till-gren (b)
  * ExpressRoute Global Reach och virtuellt WAN
* Fjärran vändare till VNet (c)
* Fjärran sluten användare-till-gren (d)
* VNet-till-VNet (e)
* Gren till hubb – hubb-till-gren (f)
* Gren till hubb – hubb-till-VNet (g)
* VNet-till-hubb – hubb-till-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Gren-till-VNet (a) och gren-till-VNet över flera regioner (g)

Branch-till-VNet är den primära sökvägen som stöds av Azure Virtual WAN. Med den här sökvägen kan du ansluta grenar till Azure IAAS Enterprise-arbetsbelastningar som har distribuerats i Azure virtuella nätverk. Grenar kan anslutas till det virtuella WAN-nätverket via ExpressRoute eller VPN för plats till plats. Trafik överföringarna till virtuella nätverk som är anslutna till de virtuella WAN-hubbarna via VNet-anslutningar. Explicit [Gateway-överföring](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) krävs inte för virtuellt WAN-nätverk eftersom det virtuella WAN-nätverket automatiskt aktiverar Gateway-överföring till gren platsen. Se artikeln om [virtuella WAN-partners](virtual-wan-configure-automation-providers.md) om hur du ansluter en SD-WAN-CPE till virtuellt WAN.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach och virtuellt WAN

ExpressRoute är ett privat och flexibelt sätt att ansluta dina lokala nätverk till Microsoft Cloud. Virtuellt WAN stöder Express Route-anslutningar. Anslutning av en lokal plats till ett virtuellt WAN med Express Route kräver 1) krets för Premium eller standard krets 2) för att vara på en Global Reach aktive rad plats.

ExpressRoute Global Reach är en tilläggs funktion för ExpressRoute. Med Global Reach kan du länka ExpressRoute-kretsar tillsammans för att skapa ett privat nätverk mellan dina lokala nätverk. Grenar som är anslutna till Azure Virtual WAN med ExpressRoute kräver att ExpressRoute-Global Reach kommunicerar med varandra.

I den här modellen kan varje gren som är ansluten till den virtuella WAN-hubben med ExpressRoute ansluta till virtuella nätverk med hjälp av gren-till-VNet-sökvägen. Gren-till-gren-trafik översätter inte navet eftersom ExpressRoute Global Reach ger en mer optimal sökväg över Azure WAN.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Gren-till-gren (b) och gren-till-gren över flera regioner (f)

Grenar kan anslutas till ett virtuellt Azure-nav med ExpressRoute-kretsar och/eller plats-till-plats-VPN-anslutningar. Du kan ansluta grenar till det virtuella WAN-hubben i den region som ligger närmast grenen.

Med det här alternativet kan företag utnyttja Azures stamnät för att ansluta grenar. Men även om den här funktionen är tillgänglig bör du väga fördelarna med att ansluta grenar över Azure Virtual WAN jämfört med ett privat WAN.  

> [!NOTE]
> Inaktive ring av anslutningar mellan olika förgreningar i virtuella WAN-nätverk kan konfigureras för att inaktivera anslutningar mellan olika grenar. Den här konfigurations kommer att blockera väg spridning mellan VPN (S2S och P2S) och ExpressRoute-anslutna platser. Den här konfigurationen påverkar inte gren-till-VNet-och VNET-till-VNet-routning propogation och anslutning. Om du vill konfigurera den här inställningen med hjälp av Azure Portal väljer du inställning: gren-till-gren-inaktive rad på konfigurations menyn för virtuellt WAN. 

### <a name="remote-user-to-vnet-c"></a>Fjärran vändare till VNet (c)

Du kan aktivera direkt säker fjärråtkomst till Azure med punkt-till-plats-anslutning från en fjärran sluten användar klient till ett virtuellt WAN. Företags-fjärran vändare behöver inte längre hairpin till molnet med hjälp av ett företags-VPN.

### <a name="remote-user-to-branch-d"></a>Fjärran sluten användare-till-gren (d)

Med sökvägen för fjärran sluten användare kan fjärran vändare som använder en punkt-till-plats-anslutning till Azure komma åt lokala arbets belastningar och program genom att använda molnet. Den här sökvägen ger fjärran vändare möjlighet att komma åt arbets belastningar som båda är distribuerade i Azure och lokalt. Företag kan aktivera central molnbaserad säker fjärråtkomst i Azure Virtual WAN.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet-till-VNet-överföring (e) och VNet-till-VNet över flera regioner (h)

VNet-till-VNet-överföringen gör att virtuella nätverk kan ansluta till varandra för att koppla samman flera nivåer som implementeras över flera virtuella nätverk. Alternativt kan du ansluta virtuella nätverk till varandra via VNet-peering och detta kan vara lämpligt för vissa scenarier där överföring via VWAN Hub inte är nödvändigt.


## <a name="force-tunneling-and-default-route-in-azure-virtual-wan"></a><a name="DefaultRoute"></a>Tvingad tunnel trafik och standard väg i Azure Virtual WAN

Tvingad tunnel trafik kan aktive ras genom att konfigurera Aktivera standard väg på en VPN-, ExpressRoute-eller Virtual Network anslutning i virtuellt WAN-nätverk.

En virtuell hubb sprider en förväntad standard väg till en virtuell nätverk-eller plats-till-plats-VPN/ExpressRoute-anslutning om Aktivera standard flagga är "aktive rad" på anslutningen. 

Den här flaggan visas när användaren redigerar en virtuell nätverks anslutning, en VPN-anslutning eller en ExpressRoute-anslutning. Som standard inaktive ras den här flaggan när en plats eller en ExpressRoute-krets är ansluten till en hubb. Den aktive ras som standard när en virtuell nätverks anslutning läggs till för att ansluta ett VNet till en virtuell hubb. Standard vägen kommer inte från den virtuella WAN-hubben. standard vägen sprids om den redan har belärts av den virtuella WAN-hubben som ett resultat av distributionen av en brand vägg i hubben, eller om en annan ansluten plats har Tvingad tunnel trafik aktive rad.

## <a name="security-and-policy-control"></a><a name="security"></a>Säkerhet och princip kontroll

Azures virtuella WAN-hubbar sammankopplar alla nätverks slut punkter i hybrid nätverket och kan eventuellt se all trafik i överförings nätverket. Virtuella WAN-hubbar kan konverteras till säkra virtuella hubbar genom att distribuera Azure-brandväggen inuti VWAN-hubbar för att aktivera molnbaserad säkerhet, åtkomst och princip kontroll. Dirigering av Azure-brandväggar i virtuella WAN-hubbar kan utföras av Azure Firewall Manager.

[Azure Firewall Manager](../firewall-manager/index.yml) innehåller funktioner för att hantera och skala säkerhet för globala överförings nätverk. Azure Firewall Manager ger möjlighet till central hantering av routning, global princip hantering, avancerade Internet säkerhets tjänster via tredje part tillsammans med Azure-brandväggen.

![skyddad virtuell hubb med Azure-brandvägg](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Bild 5: säker virtuell hubb med Azure-brandvägg**

Azure-brandväggen till det virtuella WAN-nätverket stöder följande anslutnings vägar för Global säker överföring. Bokstäverna i parenteser mappas till bild 5.

* Säker överföring mellan virtuella nätverk (e)
* VNet-till-Internet eller säkerhets tjänst från tredje part (i)
* Lokal-till-Internet eller säkerhets tjänst från tredje part (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>Säker överföring mellan virtuella nätverk (e)

Skydd mot VNet-till-VNet gör att virtuella nätverk kan ansluta till varandra via Azure-brandväggen i den virtuella WAN-hubben.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet-till-Internet eller säkerhets tjänst från tredje part (i)

VNet-till-Internet gör att virtuella nätverk kan ansluta till Internet via Azure-brandväggen i den virtuella WAN-hubben. Trafik till Internet via tredjeparts säkerhets tjänster som stöds flödar inte över Azure-brandväggen. Du kan konfigurera VNet-till-Internet-sökväg via tredjeparts säkerhets tjänst från tredje part med hjälp av Azure Firewall Manager.  

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Lokal-till-Internet eller säkerhets tjänst från tredje part (j)
Filial till Internet gör det möjligt för grenar att ansluta till Internet via Azure-brandväggen i den virtuella WAN-hubben. Trafik till Internet via tredjeparts säkerhets tjänster som stöds flödar inte över Azure-brandväggen. Du kan konfigurera en gren-till-Internet-sökväg via en säkerhets tjänst från tredje part med hjälp av Azure Firewall Manager. 

### <a name="branch-to-branch-secured-transit-cross-region-f"></a>Gren till gren säker överföring över region (f)

Grenar kan anslutas till en säker virtuell hubb med Azure-brandväggen med hjälp av ExpressRoute-kretsar och/eller plats-till-plats-VPN-anslutningar. Du kan ansluta grenar till det virtuella WAN-hubben i den region som ligger närmast grenen.

Med det här alternativet kan företag utnyttja Azures stamnät för att ansluta grenar. Men även om den här funktionen är tillgänglig bör du väga fördelarna med att ansluta grenar över Azure Virtual WAN jämfört med ett privat WAN.  

> [!NOTE]
> Bearbetning av trafik mellan olika platser i brand väggen stöds inte för närvarande. Trafiken mellan hubbarna dirigeras till rätt gren inom den säkra virtuella hubben, men trafiken kringgår Azure-brandväggen i varje hubb.

### <a name="branch-to-vnet-secured-transit-g"></a>Skyddad mellan nivå till VNet-överföring (g)

Den skyddade transporten mellan virtuella nätverk gör att grenar kan kommunicera med virtuella nätverk i samma region som den virtuella WAN-hubben och till ett annat virtuellt nätverk som är anslutet till en annan virtuell WAN-hubb i en annan region.

> [!NOTE]
> Inter-Hub med brand vägg stöds inte för närvarande. Trafik mellan hubbar kommer att flytta direkt till Azure-brandväggen i varje hubb.  Trafik via en anslutning som är avsedd för ett virtuellt nätverk i samma region bearbetas av Azure-brandväggen i den skyddade hubben.


### <a name="how-do-i-enable-default-route-00000-in-a-secured-virtual-hub"></a>Hur gör jag för att aktivera standard väg (0.0.0.0/0) i en säker virtuell hubb

Azure-brandväggen som distribueras i en virtuell WAN-hubb (säker virtuell hubb) kan konfigureras som standard-router till Internet eller betrodd säkerhets leverantör för alla grenar (anslutna via VPN eller Express Route), ekrar virtuella nätverk och användare (anslutna via P2S VPN). Den här konfigurationen måste utföras med hjälp av Azure Firewall Manager.  Se dirigera trafik till hubben för att konfigurera all trafik från grenar (inklusive användare) samt virtuella nätverk till Internet via Azure-brandväggen. 

Detta är en konfiguration i två steg:

1. Konfigurera routning av Internet trafik med hjälp av en säker väg inställnings meny för virtuell hubb. Konfigurera virtuella nätverk och grenar som kan skicka trafik till Internet via brand väggen.

2. Konfigurera vilka anslutningar (VNet och gren) som kan dirigera trafik till Internet (0.0.0.0/0) via Azure-VB i hubben eller den betrodda säkerhets leverantören. Det här steget ser till att standard vägen sprids till valda grenar och virtuella nätverk som är kopplade till den virtuella WAN-hubben via anslutningarna. 

### <a name="force-tunneling-traffic-to-on-premises-firewall-in-a-secured-virtual-hub"></a>Tvinga tunnel trafik till lokal brand vägg i en säker virtuell hubb

Om det redan finns en standard väg som har lärts (via BGP) av den virtuella hubben från en av grenarna (VPN eller ER-platser), åsidosätts den här standard vägen av standard vägen från Azure Firewall Manager-inställningen. I det här fallet kommer all trafik som anger hubben från virtuella nätverk och grenar som är avsedd för Internet att dirigeras till Azure Firewall eller den betrodda säkerhets leverantören.

> [!NOTE]
> För närvarande finns det inget alternativ för att välja lokal brand vägg eller Azure-brandvägg (och betrodd säkerhetsprovider) för Internet bindnings trafik som härstammar från virtuella nätverk, grenar eller användare. Standard vägen som har lärts från Azure Firewall Manager-inställningen prioriteras alltid över standard vägen som lärts från en av grenarna.


## <a name="next-steps"></a>Nästa steg

Skapa en anslutning med Virtual WAN och Distribuera Azure-brandväggen i VWAN Hub (s).

* [Plats-till-plats-anslutningar med hjälp av virtuellt WAN](virtual-wan-site-to-site-portal.md)
* [ExpressRoute-anslutningar med hjälp av virtuellt WAN](virtual-wan-expressroute-portal.md)
* [Azure Firewall Manager för att distribuera Azure VB i VWAN](../firewall-manager/index.yml)
