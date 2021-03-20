---
title: 'Anslut ett VNet till flera platser med VPN Gateway: klassisk'
description: Anslut flera lokala platser till ett klassiskt virtuellt nätverk med hjälp av en VPN Gateway.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 168bb9e06c73ec27ec1304813023889c9549b8e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660703"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Lägga till en plats-till-plats-anslutning till ett VNet med en befintlig VPN gateway-anslutning (klassisk)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisk)](vpn-gateway-multi-site.md)
>
>

Den här artikeln vägleder dig genom att använda PowerShell för att lägga till plats-till-plats-anslutningar (S2S) till en VPN-gateway som har en befintlig anslutning. Den här typen av anslutning kallas ofta för en "konfiguration för flera platser". Stegen i den här artikeln gäller virtuella nätverk som skapats med den klassiska distributions modellen (även kallat Service Management). De här stegen gäller inte för ExpressRoute/plats-till-plats-sambefintliga anslutnings konfiguration.

### <a name="deployment-models-and-methods"></a>Distributionsmodeller och distributionsmetoder

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Vi uppdaterar den här tabellen som nya artiklar och ytterligare verktyg blir tillgängliga för den här konfigurationen. När en artikel är tillgänglig länkar vi direkt till den från den här tabellen.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Om att ansluta

Du kan ansluta flera lokala platser till ett enda virtuellt nätverk. Detta är särskilt attraktivt för att skapa hybrid moln lösningar. Att skapa en anslutning med flera platser till din virtuella Azure-nätverksgateway liknar att skapa andra plats-till-plats-anslutningar. I själva verket kan du använda en befintlig Azure VPN-gateway, så länge gatewayen är dynamisk (Route-based).

Om du redan har en statisk Gateway ansluten till ditt virtuella nätverk kan du ändra Gateway-typen till dynamisk utan att behöva återskapa det virtuella nätverket för att kunna hantera flera platser. Innan du ändrar routningsmetod måste du kontrol lera att din lokala VPN-gateway stöder routbaserade VPN-konfigurationer.

![diagram över flera platser](./media/vpn-gateway-multi-site/multisite.png "flera platser")

## <a name="points-to-consider"></a>Några saker att tänka på

**Du kan inte använda portalen för att göra ändringar i det här virtuella nätverket.** Du måste göra ändringar i nätverks konfigurations filen i stället för att använda portalen. Om du gör ändringar i portalen skrivs dina referens inställningar för flera platser över för det här virtuella nätverket.

Du bör känna dig bekväm med att använda nätverks konfigurations filen när du har slutfört proceduren för flera platser. Men om du har flera personer som arbetar med din nätverks konfiguration måste du se till att alla känner till den här begränsningen. Det innebär inte att du inte kan använda portalen alls. Du kan använda den för allt annat, förutom att göra konfigurations ändringar i det här specifika virtuella nätverket.

## <a name="before-you-begin"></a>Innan du börjar

Innan du påbörjar konfigurationen kontrollerar du att du har följande:

* Kompatibel VPN-maskinvara för varje lokal plats. Kontrol lera [om VPN-enheter för Virtual Network anslutning](vpn-gateway-about-vpn-devices.md) för att kontrol lera om den enhet som du vill använda är något som är känt för att vara kompatibel.
* En externt riktad offentlig IPv4-IP-adress för varje VPN-enhet. Det går inte att hitta IP-adressen bakom en NAT. Detta är ett krav.
* Någon som är kunskap om hur du konfigurerar din VPN-maskinvara. Du måste ha en stark förståelse för hur du konfigurerar VPN-enheten eller arbetar med någon som gör det.
* De IP-adressintervall som du vill använda för det virtuella nätverket (om du inte redan har skapat ett).
* IP-adressintervall för var och en av de lokala nätverks platser som du ska ansluta till. Du måste kontrol lera att IP-adressintervallet för var och en av de lokala nätverks platser som du vill ansluta till inte överlappar varandra. Annars avvisar portalen eller REST API den konfiguration som överförs.<br>Om du till exempel har två lokala nätverks platser som båda innehåller IP-adressintervallet 10.2.3.0/24 och du har ett paket med en mål adress 10.2.3.3, vet inte Azure vilken plats du vill skicka paketet till eftersom adress intervallen överlappar varandra. Azure tillåter inte att du överför en konfigurations fil som har överlappande intervall för att förhindra problem med Routning.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. skapa en VPN för plats-till-plats
Om du redan har en plats-till-plats-VPN med en dynamisk routning Gateway, är det bra! Du kan fortsätta att [Exportera konfigurations inställningarna för det virtuella nätverket](#export). Annars gör du följande:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Om du redan har ett plats-till-plats-virtuellt nätverk, men har en statisk (principbaserad) routing Gateway:
1. Ändra din gateway-typ till dynamisk routning. En VPN-anslutning med flera platser kräver en dynamisk (även kallad Route-baserad) routning-Gateway. Om du vill ändra din typ av gateway måste du först ta bort den befintliga gatewayen och sedan skapa en ny.
2. Konfigurera din nya gateway och skapa din VPN-tunnel. Anvisningar finns i [Ange SKU och VPN-typ](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Se till att du anger typ av routning som dynamisk.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Om du inte har ett plats-till-plats-virtuellt nätverk:
1. Skapa ett plats-till-plats-virtuellt nätverk med hjälp av följande anvisningar: [skapa en Virtual Network med en plats-till-plats-VPN-anslutning](./vpn-gateway-howto-site-to-site-classic-portal.md).  
2. Konfigurera en dynamisk routning Gateway med hjälp av följande anvisningar: [Konfigurera en VPN gateway](./vpn-gateway-howto-site-to-site-classic-portal.md). Se till att välja **dynamisk routning** för din gateway-typ.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. exportera nätverks konfigurations filen

Öppna PowerShell-konsolen med utökade rättigheter. Använd följande kommando för att växla till Service Management:

```powershell
azure config mode asm
```

Anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Add-AzureAccount
```

Exportera Azure-nätverks konfigurations filen genom att köra följande kommando. Du kan ändra platsen för filen och exportera den till en annan plats om det behövs.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Öppna Nätverks konfigurations filen
Öppna Nätverks konfigurations filen som du laddade ned i det senaste steget. Använd valfri XML-redigerare som du vill. Filen bör se ut ungefär så här:

```xml
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <LocalNetworkSites>
      <LocalNetworkSite name="Site1">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
          <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
      </LocalNetworkSite>
      <LocalNetworkSite name="Site2">
        <AddressSpace>
          <AddressPrefix>10.2.0.0/16</AddressPrefix>
          <AddressPrefix>10.3.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
        <AddressSpace>
          <AddressPrefix>10.20.0.0/16</AddressPrefix>
          <AddressPrefix>10.21.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="FE">
            <AddressPrefix>10.20.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="BE">
            <AddressPrefix>10.20.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.20.2.0/29</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

## <a name="4-add-multiple-site-references"></a>4. Lägg till flera plats referenser
När du lägger till eller tar bort referens information för webbplatsen gör du konfigurations ändringar i ConnectionsToLocalNetwork/LocalNetworkSiteRef. Om du lägger till en ny lokal webbplats referens utlöses Azure för att skapa en ny tunnel. I exemplet nedan är nätverks konfigurationen för en anslutning på en plats. Spara filen när du är klar med ändringarna.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Lägg till ytterligare plats referenser (skapa en konfiguration för flera platser) genom att helt enkelt lägga till ytterligare "LocalNetworkSiteRef"-rader, som du ser i exemplet nedan:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. Importera nätverks konfigurations filen
Importera nätverks konfigurations filen. När du importerar den här filen med ändringarna läggs de nya tunnlarna till. Tunnlarna kommer att använda den dynamiska gateway som du skapade tidigare. Du kan importera filen med hjälp av PowerShell.

## <a name="6-download-keys"></a>6. hämta nycklar
När dina nya tunnlar har lagts till använder du PowerShell-cmdlet "Get-AzureVNetGatewayKey" för att hämta de i förväg delade IPsec/IKE-nycklarna för varje tunnel.

Exempel:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Om du vill kan du också använda REST API *hämta Virtual Network Gateway-delad nyckel* för att hämta de i förväg delade nycklarna.

## <a name="7-verify-your-connections"></a>7. kontrol lera dina anslutningar
Kontrol lera tunnel statusen för flera platser. När du har laddat ned nycklarna för varje tunnel ska du verifiera anslutningarna. Använd Get-AzureVnetConnection för att hämta en lista över virtuella nätverks tunnlar, som du ser i exemplet nedan. VNet1 är namnet på det virtuella nätverket.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Exempel RETUR:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>Nästa steg

Mer information om VPN-gatewayer finns i [om VPN-gatewayer](vpn-gateway-about-vpngateways.md).