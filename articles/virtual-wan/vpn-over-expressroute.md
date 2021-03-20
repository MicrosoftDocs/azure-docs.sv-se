---
title: 'Konfigurera ExpressRoute-kryptering: IPsec över ExpressRoute för Azure Virtual WAN'
description: Lär dig hur du använder Azure Virtual WAN för att skapa en plats-till-plats-VPN-anslutning via ExpressRoute privata peering.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: b8dde3ed76587e2343edaec8626287853ec6ef9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487515"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute-kryptering: IPsec över ExpressRoute för virtuellt WAN

Den här artikeln visar hur du använder Azure Virtual WAN för att upprätta en IPsec/IKE VPN-anslutning från ditt lokala nätverk till Azure över en privat peering av en Azure ExpressRoute-krets. Den här tekniken kan ge en krypterad överföring mellan de lokala nätverken och virtuella Azure-nätverk via ExpressRoute, utan att gå över det offentliga Internet eller använda offentliga IP-adresser.

## <a name="topology-and-routing"></a>Topologi och routning

I följande diagram visas ett exempel på en VPN-anslutning via ExpressRoute privat peering:

:::image type="content" source="./media/vpn-over-expressroute/vwan-vpn-over-er.png" alt-text="VPN över ExpressRoute":::

Diagrammet visar ett nätverk i det lokala nätverket som är anslutet till Azure Hub VPN-gateway över ExpressRoute-privat peering. Anslutnings etableringen är enkel:

1. Upprätta ExpressRoute-anslutningar med en ExpressRoute-krets och privat peering.
2. Upprätta en VPN-anslutning enligt beskrivningen i den här artikeln.

En viktig aspekt av den här konfigurationen är routning mellan de lokala nätverken och Azure över både ExpressRoute-och VPN-sökvägar.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Trafik från lokala nätverk till Azure

För trafik från lokala nätverk till Azure annonseras Azure-prefixen (inklusive den virtuella hubben och alla eker-virtuella nätverk som är anslutna till hubben) via både ExpressRoute privata peering BGP och VPN BGP. Detta resulterar i två nätverks vägar (sökvägar) mot Azure från lokala nätverk:

- En över IPsec-skyddad sökväg
- En direkt över ExpressRoute *utan* IPSec-skydd 

Om du vill använda kryptering för kommunikationen måste du se till att för det VPN-anslutna nätverket i diagrammet föredras Azure-vägar via lokal VPN-gateway över den direkta ExpressRoute-sökvägen.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Trafik från Azure till lokala nätverk

Samma krav gäller för trafiken från Azure till lokala nätverk. För att säkerställa att IPsec-sökvägen föredras över den direkta ExpressRoute-sökvägen (utan IPsec) har du två alternativ:

- Annonsera mer detaljerade prefix på VPN BGP-sessionen för det VPN-anslutna nätverket. Du kan annonsera ett större intervall som omfattar VPN-anslutna nätverk via ExpressRoute privat peering, och sedan mer exakta intervall i VPN BGP-sessionen. Annonsera till exempel 10.0.0.0/16 över ExpressRoute och 10.0.1.0/24 via VPN.

- Annonsera åtskilda prefix för VPN-och ExpressRoute. Om de VPN-anslutna nätverks intervallen är åtskilda från andra ExpressRoute anslutna nätverk, kan du annonsera prefixen i VPN-och ExpressRoute BGP-sessionerna. Meddela till exempel 10.0.0.0/24 över ExpressRoute och 10.0.1.0/24 via VPN.

I båda dessa exempel skickar Azure trafik till 10.0.1.0/24 via VPN-anslutningen i stället för direkt över ExpressRoute utan VPN-skydd.

> [!WARNING]
> Om du annonserar *samma* prefix över både ExpressRoute och VPN-anslutningar, kommer Azure att använda ExpressRoute-sökvägen direkt utan VPN-skydd.
>

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. skapa ett virtuellt WAN-nätverk och hubb med gateways

Följande Azure-resurser och motsvarande lokala konfigurationer måste vara på plats innan du fortsätter:

- Ett virtuellt Azure-WAN
- En virtuell WAN-hubb med en [ExpressRoute-Gateway](virtual-wan-expressroute-portal.md) och en [VPN-gateway](virtual-wan-site-to-site-portal.md)

Anvisningar för hur du skapar ett virtuellt Azure-nätverk och en hubb med en ExpressRoute-associering finns i [skapa en ExpressRoute-Association med Azure Virtual WAN](virtual-wan-expressroute-portal.md). Anvisningar om hur du skapar en VPN-gateway i det virtuella WAN-nätverket finns i [skapa en plats-till-plats-anslutning med Azure Virtual WAN](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. skapa en plats för det lokala nätverket

Plats resursen är samma som icke-ExpressRoute VPN-platser för ett virtuellt WAN-nätverk. IP-adressen för den lokala VPN-enheten kan nu vara antingen en privat IP-adress eller en offentlig IP-adress i det lokala nätverket som kan kommas åt via ExpressRoute privat peering som skapades i steg 1.

> [!NOTE]
> IP-adressen för den lokala VPN-enheten *måste* ingå i de adressprefix som annonseras till den virtuella WAN-hubben via Azure ExpressRoute Private peering.
>

1. Gå till Azure Portal i webbläsaren. 
1. Välj den hubb som du skapade. På sidan Virtual WAN Hub under **anslutning** väljer du **VPN-platser**.
1. På sidan **VPN-webbplatser** väljer du **+ Skapa webbplats**.
1. Fyll i följande fält på sidan **Skapa webbplats**:
   * **Prenumeration**: verifiera prenumerationen.
   * **Resurs grupp**: Välj eller skapa den resurs grupp som du vill använda.
   * **Region**: Ange Azure-regionen för VPN-webbplatsens resurs.
   * **Namn**: Ange det namn som du vill referera till din lokala plats.
   * **Enhets leverantör**: Ange leverantören för den lokala VPN-enheten.
   * **Border Gateway Protocol**: Välj "Aktivera" om ditt lokala nätverk använder BGP.
   * **Privat adress utrymme**: Ange det IP-adressutrymme som finns på din lokala plats. Trafik som är avsedd för det här adress utrymmet dirigeras till det lokala nätverket via VPN-gatewayen.
   * **Hubbar**: Välj ett eller flera hubbar för att ansluta den här VPN-platsen. De valda hubbarna måste ha VPN-gatewayer redan skapade.
1. Välj **Nästa: länkar >** för inställningarna för VPN-länken:
   * **Länk namn**: det namn som du vill referera till den här anslutningen.
   * **Providernamn**: namnet på Internet tjänst leverantören för den här platsen. För ett ExpressRoute lokalt nätverk är det namnet på ExpressRoute-tjänstprovidern.
   * **Hastighet**: hastigheten på Internet tjänst länken eller ExpressRoute-kretsen.
   * **IP-adress**: den offentliga IP-adressen för VPN-enheten som finns på din lokala plats. Eller, för ExpressRoute lokalt, är det den privata IP-adressen för VPN-enheten via ExpressRoute.

   Om BGP är aktiverat kommer det att gälla alla anslutningar som skapats för den här platsen i Azure. Konfiguration av BGP på ett virtuellt WAN-nätverk motsvarar att konfigurera BGP på en Azure VPN-gateway. 
   
   Din lokala BGP-peer-adress *får inte* vara samma som IP-adressen för ditt VPN till enheten eller adress utrymmet för det virtuella nätverket för VPN-platsen. Använd en annan IP-adress på VPN-enheten för din BGP-peer-IP. Det kan vara en adress som tilldelats till loopback-gränssnittet på enheten. Det kan dock *inte* vara en APIPA (169,254.*x*. *x*). Ange den här adressen på motsvarande VPN-plats som representerar platsen. För BGP-krav, se [om BGP med Azure VPN gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Välj **Nästa: granska + skapa >** för att kontrol lera inställnings värden och skapa VPN-platsen. Om du har valt **hubbar** för anslutning upprättas anslutningen mellan det lokala nätverket och HUBBENS VPN-gateway.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. uppdatera inställningen för VPN-anslutning för att använda ExpressRoute

När du har skapat VPN-platsen och anslutit till hubben använder du följande steg för att konfigurera anslutningen till att använda ExpressRoute-privat peering:

1. Gå tillbaka till sidan för den virtuella WAN-resursen och välj Hub-resursen. Eller navigera från VPN-platsen till det anslutna hubben.

   :::image type="content" source="./media/vpn-over-expressroute/hub-selection.png" alt-text="Välj en hubb":::
1. Under **anslutning** väljer du **VPN (plats-till-plats)**.

   :::image type="content" source="./media/vpn-over-expressroute/vpn-select.png" alt-text="Välj VPN (plats-till-plats)":::
1. Välj ellipsen (**...**) på VPN-platsen via ExpressRoute och välj **Redigera VPN-anslutning till den här hubben**.

   :::image type="content" source="./media/vpn-over-expressroute/config-menu.png" alt-text="Ange konfigurations menyn":::
1. Välj **Ja** för **Använd AZUREs privata IP-adress**. Inställningen konfigurerar hubbens VPN-gateway att använda privata IP-adresser inom hubbens adress intervall på gatewayen för den här anslutningen, i stället för offentliga IP-adresser. Detta säkerställer att trafiken från det lokala nätverket passerar de ExpressRoute privata peering-vägarna i stället för att använda det offentliga Internet för VPN-anslutningen. Följande skärm bild visar inställningen:

   :::image type="content" source="./media/vpn-over-expressroute/vpn-link-configuration.png" alt-text="Inställning för att använda en privat IP-adress för VPN-anslutningen" border="false":::
1. Välj **Spara**.

När du har sparat ändringarna använder Hub VPN-gatewayen de privata IP-adresserna på VPN-gatewayen för att upprätta IPsec/IKE-anslutningarna med den lokala VPN-enheten över ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Hämta de privata IP-adresserna för hubb-VPN-gatewayen

Hämta VPN-enhetens konfiguration för att hämta de privata IP-adresserna för hubbens VPN-gateway. Du behöver de här adresserna för att konfigurera den lokala VPN-enheten.

1. På sidan för din hubb väljer du **VPN (plats-till-plats)** under **anslutning**.
1. Överst på sidan **Översikt** väljer du **Hämta VPN-konfiguration**. 

   Azure skapar ett lagrings konto i resurs gruppen "Microsoft-Network-[location]" där *plats* är platsen för WAN. När du har tillämpat konfigurationen på VPN-enheterna kan du ta bort det här lagrings kontot.
1. När filen har skapats väljer du länken för att ladda ned den.
1. Tillämpa konfigurationen på VPN-enheten.

### <a name="vpn-device-configuration-file"></a>Konfigurations fil för VPN-enhet

Enhetens konfigurations fil innehåller de inställningar som ska användas när du konfigurerar den lokala VPN-enheten. När du visar den här filen ser du följande information:

* **vpnSiteConfiguration**: det här avsnittet anger enhets informationen som har kon figurer ATS som en plats som ansluter till det virtuella WAN-nätverket. Den innehåller avdelnings enhetens namn och offentliga IP-adress.
* **vpnSiteConnections**: det här avsnittet innehåller information om följande inställningar:

    * Adress utrymmet för den virtuella hubbens virtuella nätverk.<br/>Exempel:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Adress utrymmet för de virtuella nätverk som är anslutna till hubben.<br>Exempel:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * IP-adresserna för den virtuella hubbens VPN-gateway. Eftersom varje anslutning av VPN-gatewayen består av två tunnlar i en aktiv-aktiv-konfiguration ser du båda IP-adresserna som anges i den här filen. I det här exemplet visas `Instance0` och `Instance1` för varje plats och de är privata IP-adresser i stället för offentliga IP-adresser.<br>Exempel:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Konfigurations information för VPN gateway-anslutningen, till exempel BGP och i förväg delad nyckel. Den i förväg delade nyckeln skapas automatiskt åt dig. Du kan alltid redigera anslutningen på sidan **Översikt** för en anpassad i förväg delad nyckel.
  
### <a name="example-device-configuration-file"></a>Konfigurationsfil för exempelenhet

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Konfigurera en VPN-enhet

Om du behöver anvisningar för att konfigurera enheten kan du använda instruktionerna på sidan om [konfigurationsskript till VPN-enheter](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) med följande förbehåll:

* Anvisningarna på sidan VPN-enhet är inte skrivna för ett virtuellt WAN-nätverk. Men du kan använda de virtuella WAN-värdena från konfigurations filen för att konfigurera VPN-enheten manuellt. 
* De nedladdnings bara enhets konfigurations skripten för VPN-gatewayen fungerar inte för det virtuella WAN-nätverket eftersom konfigurationen är annorlunda.
* Ett nytt virtuellt WAN-nätverk kan stödja både IKEv1 och IKEv2.
* Ett virtuellt WAN kan bara använda routbaserade VPN-enheter och enhets instruktioner.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Visa ditt virtuella WAN-nätverk

1. Gå till det virtuella WAN-nätverket.
1. På sidan **Översikt** representerar varje punkt på kartan ett nav.
1. I avsnittet **hubbar och anslutningar** kan du Visa status för hubb, plats, region och VPN-anslutning. Du kan också Visa byte in och ut.

## <a name="6-monitor-a-connection"></a><a name="connectmon"></a>6. övervaka en anslutning

Skapa en anslutning för att övervaka kommunikationen mellan en virtuell Azure-dator (VM) och en fjärran sluten plats. Information om hur du ställer in en anslutningsövervakare finns i dokumentationen om att [övervaka nätverkskommunikation](~/articles/network-watcher/connection-monitor.md). Käll fältet är den virtuella datorns IP-adress i Azure och mål-IP-adressen är platsens IP-adress.

## <a name="7-clean-up-resources"></a><a name="cleanup"></a>7. Rensa resurser

När du inte längre behöver dessa resurser kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser som den innehåller. Kör följande PowerShell-kommando och Ersätt `myResourceGroup` med namnet på resurs gruppen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln hjälper dig att skapa en VPN-anslutning via ExpressRoute privat peering med hjälp av Virtual WAN. Mer information om virtuella WAN och relaterade funktioner finns i [Översikt över virtuella WAN-nätverk](virtual-wan-about.md).
