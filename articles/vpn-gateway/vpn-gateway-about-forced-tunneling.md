---
title: 'Azure VPN Gateway: Konfigurera Tvingad tunnel trafik-plats-till-plats-anslutningar: klassisk'
description: Lär dig hur du konfigurerar Tvingad tunnel trafik för virtuella nätverk som skapats med den klassiska distributions modellen.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: af4359efb48898c12bb8ee7ffb882448b5012d19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92151355"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurera framtvingad tunneling med den klassiska distributionsmodellen

Med tvingad tunneltrafik kan du omdirigera eller ”tvinga” all Internetbunden trafik tillbaka till din lokala plats via en plats-till-plats-VPN-tunnel för kontroll och granskning. Detta är ett kritiskt säkerhets krav för de flesta företagets IT-principer. Utan Tvingad tunnel trafik kommer Internet-baserad trafik från dina virtuella datorer i Azure alltid att gå från Azures nätverks infrastruktur direkt till Internet, utan alternativet att låta dig inspektera eller granska trafiken. Otillåten Internet åtkomst kan potentiellt leda till information som avslöjas eller andra typer av säkerhets överträdelser.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Den här artikeln beskriver hur du konfigurerar Tvingad tunnel trafik för virtuella nätverk som skapats med den klassiska distributions modellen. Tvingad tunnel trafik kan konfigureras med hjälp av PowerShell, inte via portalen. Om du vill konfigurera Tvingad tunnel trafik för distributions modellen för Resource Manager väljer du Resource Manager-artikel i följande listruta:

> [!div class="op_single_selector"]
> * [Form](vpn-gateway-about-forced-tunneling.md)
> * [Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 

## <a name="requirements-and-considerations"></a>Krav och överväganden

Tvingad tunnel trafik i Azure konfigureras via virtuella nätverks användar definierade vägar (UDR). Att omdirigera trafik till en lokal plats uttrycks som en standard väg till Azure VPN-gatewayen. I följande avsnitt visas den aktuella begränsningen för routningstabellen och vägar för en Azure-Virtual Network:

* Varje undernät för virtuellt nätverk har en inbyggd system routningstabell. Tabellen system routning har följande tre grupper av vägar:

  * **Lokala VNet-vägar:** Direkt till de virtuella mål datorerna i samma virtuella nätverk.
  * **Lokala vägar:** Till Azure VPN-gatewayen.
  * **Standard väg:** Direkt till Internet. Paket som är avsedda för privata IP-adresser som inte omfattas av de föregående två vägarna kommer att tas bort.
* Med den här versionen av användardefinierade vägar kan du skapa en routningstabell för att lägga till en standard väg och sedan associera routningstabellen till dina VNet-undernät för att aktivera Tvingad tunnel trafik på dessa undernät.
* Du måste ange en "standard webbplats" bland de lokala lokala platserna som är anslutna till det virtuella nätverket.
* Tvingad tunnel trafik måste vara kopplad till ett VNet som har en dynamisk routning VPN-gateway (inte en statisk Gateway).
* ExpressRoute-Tvingad tunnel trafik har inte kon figurer ATS via den här mekanismen, utan är i stället aktive rad genom att annonsera en standard väg via ExpressRoute BGP-peering-sessioner. Mer information finns i avsnittet [Vad är ExpressRoute?](../expressroute/expressroute-introduction.md).

## <a name="configuration-overview"></a>Översikt över konfiguration

I följande exempel tvingas inte frontend-undernätet att tunnlas. Arbets belastningarna i klient delens undernät kan fortsätta att acceptera och svara på kund förfrågningar direkt från Internet. Mellanlagrings-och backend-undernät framtvingas med tunnel. Utgående anslutningar från dessa två undernät till Internet tvingas eller omdirigeras tillbaka till en lokal plats via en av S2S VPN-tunnlarna.

På så sätt kan du begränsa och kontrol lera Internet åtkomst från dina virtuella datorer eller moln tjänster i Azure, samtidigt som du fortsätter att aktivera din tjänst arkitektur för flera nivåer som krävs. Du kan också använda Tvingad tunnel trafik till hela virtuella nätverk om det inte finns några Internet-riktade arbets belastningar i dina virtuella nätverk.

![Tvingad tunnel trafik](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande innan du påbörjar konfigurationen:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Ett konfigurerat virtuellt nätverk. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="configure-forced-tunneling"></a>Konfigurera tvingad tunneltrafik

Med följande procedur kan du ange Tvingad tunnel trafik för ett virtuellt nätverk. Konfigurations stegen motsvarar konfigurations filen för VNet-nätverket.  I det här exemplet har det virtuella nätverket "multitier-VNet" tre undernät: "frontend", "midtier" och "backend"-undernät, med fyra anslutningar på flera platser: "DefaultSiteHQ" och tre grenar.


```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

Följande steg anger "DefaultSiteHQ" som standard plats anslutning för Tvingad tunnel trafik och konfigurerar midtier-och backend-undernät för att använda Tvingad tunnel trafik.

1. Öppna PowerShell-konsolen med utökade rättigheter. Anslut till ditt konto med hjälp av följande exempel:

   ```powershell
   Add-AzureAccount
   ```

1. Skapa en routningstabell. Använd följande cmdlet för att skapa en routningstabell.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

1. Lägg till en standard väg i routningstabellen. 

   I följande exempel läggs en standard väg till i routningstabellen som skapats i steg 1. Observera att den enda vägen som stöds är adressprefixet "0.0.0.0/0" till "VPNGateway" NextHop.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

1. Koppla routningstabellen till under näten. 

   När en routningstabell har skapats och en väg lagts till, använder du följande exempel för att lägga till eller koppla routningstabellen till ett VNet-undernät. Exemplet lägger till routningstabellen "MyRouteTable" i midtier-och backend-undernätet för VNet flera nivåer-VNet.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

1. Tilldela en standard webbplats för Tvingad tunnel trafik. 

   I föregående steg skapade exempel cmdlet-skripten routningstabellen och kopplade till routningstabellen till två av VNet-undernätet. Det återstående steget är att välja en lokal plats mellan flera webbplats anslutningar för det virtuella nätverket som standard plats eller tunnel.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Ytterligare PowerShell-cmdletar

### <a name="to-delete-a-route-table"></a>Ta bort en routningstabell

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Visa en routningstabell

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Ta bort en väg från en routningstabell

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Ta bort en väg från ett undernät

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Visa en lista över routningstabellen som är associerade med ett undernät

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Ta bort en standard webbplats från en VNet VPN-gateway

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
