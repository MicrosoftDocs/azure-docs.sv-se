---
title: Konfigurera VPN NAT-regler för din gateway
titleSuffix: Azure Virtual WAN
description: Lär dig hur du konfigurerar NAT-regler för din VWAN VPN-gateway
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431248"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Konfigurera NAT-regler för din virtuella WAN-Gateway – för hands version

> [!IMPORTANT]
> NAT-regler finns för närvarande i offentlig för hands version.
> Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan konfigurera din virtuella WAN-Gateway med statiska en-till-ett NAT-regler. En NAT-regel ger en mekanism för att ställa in en-till-en-översättning av IP-adresser. NAT kan användas för att koppla samman två IP-nätverk som har inkompatibla eller överlappande IP-adresser. Ett typiskt scenario är grenar med överlappande IP-adresser som vill komma åt Azure VNet-resurser.

Den här konfigurationen använder en Flow-tabell för att dirigera trafik från en extern (värd) IP-adress till en intern IP-adress som är kopplad till en slut punkt i ett virtuellt nätverk (virtuell dator, dator, behållare osv.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagram som visar arkitektur.":::

## <a name="configure-nat-rules"></a><a name="rules"></a>Konfigurera NAT-regler

Du kan konfigurera och Visa NAT-regler på dina VPN Gateway-inställningar när som helst.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Skärm bild som visar redigerings regler.":::

1. Navigera till din virtuella hubb.
1. Välj **VPN (plats till plats)**.
1. Välj **NAT-regler (redigera)**.
1. På sidan **Redigera NAT-regel** kan du **lägga till/redigera/ta bort** en NAT-regel med följande värden:

   * **Namn:** Ett unikt namn för din NAT-regel.
   * **Typ:** Oföränderlig. Statisk en-till-en NAT upprättar en 1-till-1-relation mellan en intern adress och en extern adress.
   * **Läge:** IngressSnat eller EgressSnat.  
      * IngressSnat-läget (även kallat ingångs källans NAT) är tillämpligt för trafik som anger Azure Hub-VPN-gatewayen för plats-till-plats.
      * EgressSnat-läget (även kallat utgående käll-NAT) är tillämpligt för trafik som lämnar Azure Hub: s VPN-gateway för plats-till-plats.
   * **InternalMapping:** Ett adressprefix som är ett intervall av käll-IP: er på det inre nätverket som ska mappas till en uppsättning externa IP-adresser. Med andra ord är ditt adressprefix för före NAT.
   * **ExternalMapping:** Ett adressprefix som är ett mål-IP-adressintervall för det externa nätverket som Källans IP-adresser mappas till. Med andra ord är ditt adressprefix för efter NAT.
   * **Länk anslutning:** Anslutnings resurs som i stort sett ansluter en VPN-plats till Azure Hub-VPN-gatewayen för plats-till-plats.

### <a name="configuration-considerations"></a><a name="considerations"></a>Konfigurations överväganden

* Under näts storleken för både intern och extern mappning måste vara samma för statisk 1-till-en NAT.
* Se till att redigera VPN-platsen i Azure Portal för att lägga till **ExternalMapping** -prefix i fältet "privat adress utrymme". För närvarande måste platser som har BGP aktiverat se till att den lokala BGP-tillkännageren (enhetens BGP-inställningar) innehåller en post för de externa mappnings-prefixen.

## <a name="examples-and-verification"></a><a name="examples"></a>Exempel och verifiering

### <a name="ingress-mode-nat"></a>NAT för ingress-läge

Ingångs läge NAT-regler tillämpas på paket som anger Azure via den virtuella WAN-gatewayen för plats-till-plats-VPN. I det här scenariot vill du ansluta två grenar för plats-till-plats-VPN till Azure. VPN-plats 1 ansluter via link1 och VPN-plats 2 ansluter via länk 2. Varje plats har adress utrymmet 192.169.1.0/24.

Följande diagram visar resultatet av den planerade slut resultatet:

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="Diagram som visar inkommande NAT-läge.":::

1. Ange en NAT-regel.

   Ange en NAT-regel för att säkerställa att plats-till-plats-VPN-gatewayen kan skilja mellan de två grenarna med överlappande adress utrymmen (t. ex. 192.168.1.0/24). I det här exemplet fokuserar vi på link1 för VPN-plats 1.

   Följande NAT-regel kan konfigureras och kopplas till länk 1 i en av grenarna. Eftersom det här är en statisk NAT-regel innehåller adress utrymmena för InternalMapping och ExternalMapping samma antal IP-adresser.

   * **Namn:** IngressRule01
   * **Typ:** Oföränderlig
   * **Läge:** IngressSnat
   * **InternalMapping:** 192.168.1.0/24
   * **ExternalMapping:** 10.1.1.0/24
   * **Länk anslutning:** Länk 1

1. Annonsera rätt ExternalMapping.

   I det här steget kontrollerar du att din plats-till-plats-VPN-gateway meddelar rätt ExternalMapping-adress utrymme till resten av dina Azure-resurser. Det finns olika instruktioner, beroende på om BGP är aktiverat eller inte.

   **Exempel 1: BGP har Aktiver ATS**

   * Se till att den lokala BGP-högtalaren på VPN-plats 1 har kon figurer ATS för annonsering av 10.1.1.0/24-adressutrymmet.
   * Under den här för hands versionen måste platser som har BGP aktiverat se till att den lokala BGP-enheten (enhetens BGP-inställningar) inkluderar en post för externa mappnings-prefix.

   **Exempel 2: BGP är inte aktiverat**

   * Navigera till den virtuella Hub-resurs som innehåller plats-till-plats-VPN-gatewayen. På sidan virtuellt nav väljer du **VPN (plats-till-plats)** under **anslutning**.
   * Välj VPN-platsen som är ansluten till den virtuella WAN-hubben via länk 1. Välj **Redigera webbplats** och indata 10.1.1.0/24 som det privata adress utrymmet för VPN-platsen.

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="Skärm bild som visar sidan Redigera VPN-webbplats.":::

### <a name="packet-flow"></a>Paket flöde

I det här exemplet vill en lokal enhet komma åt ett eker-virtuellt nätverk. Paket flödet är enligt följande, med NAT-översättningar i fetstil.

1. Trafik från lokal plats initieras.
   * Käll-IP-adress: **192.168.1.1**
   * Mål-IP-adress: 30.0.0.1
1. Trafiken anger plats-till-plats-gatewayen och översätts med hjälp av NAT-regeln och skickas sedan till ekern.
   * Källans IP-adress: **10.1.1.1**
   * Mål-IP-adress: 30.0.0.1
1. Svar från eker initieras.
   * Käll-IP-adress: 30.0.0.1
   * Mål-IP-adress: **10.1.1.1**
1. Trafiken anger plats-till-plats-VPN-gatewayen och översättningen återförs och skickas till lokalt.
   * Käll-IP-adress: 30.0.0.1
   * Mål-IP-adress: **192.168.1.1**

### <a name="verification-checks"></a>Verifierings kontroller

I det här avsnittet visas kontroller för att kontrol lera att konfigurationen är korrekt konfigurerad.

#### <a name="validate-defaultroutetable-rules-and-routes"></a>Verifiera DefaultRouteTable, regler och vägar

Grenar i det virtuella WAN-nätverket associeras med **DefaultRouteTable**, vilket innebär att alla förgrenings anslutningar lär sig vägar som är ifyllda i DefaultRouteTable. NAT-regeln visas med det externa mappnings-prefixet i de effektiva vägarna för DefaultRouteTable.

Exempel:

* **Prefix:** 10.1.1.0/24  
* **Typ av nästa hopp:** VPN_S2S_Gateway
* **Nästa hopp:** VPN_S2S_Gateway resurs

#### <a name="validate-address-prefixes"></a>Verifiera adressprefix

Det här exemplet gäller för resurser i virtuella nätverk som är kopplade till DefaultRouteTable.

De **effektiva vägarna** på nätverkskorten (NIC) för virtuella datorer som finns i ett eker-virtuellt nätverk som är anslutna till den virtuella WAN-hubben bör också innehålla de adressprefix som finns i NAT-reglerna **ExternalMapping**.

#### <a name="validate-bgp-advertisements"></a>Verifiera BGP-annonser

Om du har konfigurerat BGP på VPN-platsens anslutning, kontrollerar du den lokala BGP-högtalaren för att se till att den annonserar en post för externa mappnings-prefix.

## <a name="next-steps"></a>Nästa steg

Mer information om plats-till-plats-konfigurationer finns i [Konfigurera en virtuell WAN-plats-till-plats-anslutning](virtual-wan-site-to-site-portal.md).
