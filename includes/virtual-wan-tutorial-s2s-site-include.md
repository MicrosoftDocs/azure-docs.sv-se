---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/11/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 708baa83ca919adcc374be36c229ce3ff30da384
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362979"
---
1. På Portal sidan för ditt virtuella WAN-nätverk går du till avsnittet **anslutning** och väljer **VPN-platser** för att öppna sidan VPN-platser.
1. På sidan **VPN-platser** klickar du på **+ Skapa webbplats**.
1. På sidan **skapa VPN-webbplats** går du till fliken **grundläggande** och fyller i följande fält:

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="Fliken Grundläggande" lightbox="./media/virtual-wan-tutorial-site-include/site-basics.png":::

    * **Region** – tidigare kallad plats. Det här är den plats där du vill skapa den här webbplats resursen i.
    * **Namn** – namnet som du vill referera till din lokala plats.
    * **Enhets leverantör** – namnet på VPN-enhetens tillverkare (till exempel: Citrix, Cisco, Barracuda). Genom att lägga till enhets leverantören kan Azure-teamet bättre förstå din miljö för att lägga till ytterligare optimerings möjligheter i framtiden eller för att hjälpa dig att felsöka.
    * **Privat adress utrymme** – det IP-adressutrymme som finns på din lokala plats. Trafik till det här adressutrymmet dirigeras till den lokala platsen. Detta krävs när BGP inte är aktiverat för platsen.
    
      >[!NOTE]
      >Om du redigerar adress utrymmet efter att du har skapat platsen (till exempel lägger till ytterligare adress utrymme) kan det ta 8-10 minuter att uppdatera de effektiva vägarna medan komponenterna återskapas.
      >
1. Välj **länkar** om du vill lägga till information om de fysiska länkarna i grenen. Om du har en virtuell WAN-partner CPE-enhet, kontrollerar du med dem för att se om den här informationen utbyts med Azure som en del av överföringen av gren information från sina system.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-links.png" alt-text="Fliken Länkar" lightbox="./media/virtual-wan-tutorial-site-include/site-links.png":::

   * **Länknamn** – ett namn som du vill använda för den fysiska länken på VPN-platsen. Exempel: mylink1.
   * **Länk hastighet** – detta är hastigheten på VPN-enheten på gren platsen. Exempel: 50, vilket innebär att 50 Mbit/s är hastigheten på VPN-enheten på gren platsen.
   * **Länk leverantörs namn** – namnet på den fysiska länken på VPN-platsen. Exempel: till, Verizon.
   * **Länka IP-adress/FQDN** – offentlig IP-adress för den lokala enheten med hjälp av den här länken. Alternativt kan du ange den privata IP-adressen för din lokala VPN-enhet som ligger bakom ExpressRoute. Du kan också inkludera ett fullständigt kvalificerat domän namn. Till exempel *something.contoso.com*. FQDN bör matchas från VPN-gatewayen. Detta är möjligt om DNS-servern som är värd för detta FQDN kan kontaktas via Internet. IP-adressen prioriteras när både IP-adress och FQDN anges.

     >[!NOTE]
     >
     >* Stöder en IPv4-adress per fullständigt domän namn. Om det fullständiga domän namnet skulle matchas med flera IP-adresser, hämtar VPN-gatewayen den första IP4-adressen från listan. IPv6-adresser stöds inte för tillfället.
     >
     >* VPN-gatewayen upprätthåller en DNS-cache som uppdateras var 5: e minut. Gatewayen försöker bara matcha FQDN för frånkopplade tunnlar. En gateway-återställning eller konfigurations ändring kan även utlösa FQDN-matchning.
     >
   * **Länk Border Gateway Protocol** – konfigurera BGP på en virtuell WAN-länk motsvarar att konfigurera BGP på en virtuell Azure-nätverksgateway VPN. Din lokala BGP-peer-adress får inte vara samma som den offentliga IP-adressen för VPN-enheten eller det virtuella nätverkets adress utrymme för VPN-platsen. Använd en annan IP-adress på VPN-enheten för din BGP-peer-IP. Det kan vara en adress som tilldelats till loopback-gränssnittet på enheten. Ange den här adressen på motsvarande VPN-plats som representerar platsen.  För BGP-krav, se [om BGP med Azure VPN gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Du kan alltid redigera en VPN-länk anslutning för att uppdatera dess BGP-parametrar (peering-IP på länken och AS #).
1. Du kan lägga till eller ta bort fler länkar. Det finns stöd för fyra länkar per VPN-webbplats. Om du till exempel har fyra Internet leverantörer (Internet leverantör) på gren platsen kan du skapa fyra länkar, en per Internet-leverantör och ange informationen för varje länk.
1. När du är klar med att fylla i fälten väljer du **Granska + skapa** för att verifiera och skapa platsen.
1. Navigera till det virtuella hubb du vill ha och avmarkera **hubben** för att ansluta din VPN-plats till hubben.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/connect.png" alt-text="Anslut till den här hubben" lightbox="./media/virtual-wan-tutorial-site-include/connect.png":::
