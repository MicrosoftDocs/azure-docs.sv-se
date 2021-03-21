---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0c05eb88df27ba9e71c0f61bad75457d893a7ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95558993"
---
> [!NOTE]
> De här exemplen gäller inte för S2S/ExpressRoute-konfigurationer.
> Mer information om hur du arbetar med gateways i en befintlig konfiguration finns i [Konfigurera sambefintliga anslutningar.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Lägga till en gateway

När du lägger till en gateway till ett virtuellt nätverk med hjälp av den klassiska resurs modellen ändrar du nätverks konfigurations filen direkt innan du skapar gatewayen. Värdena i exemplen nedan måste finnas i filen för att skapa en gateway. Om det virtuella nätverket tidigare hade en gateway som är kopplad till den, finns det redan en del av dessa värden. Ändra filen så att den visar värdena nedan.

### <a name="download-the-network-configuration-file"></a>Ladda ned nätverks konfigurations filen

1. Hämta nätverks konfigurations filen med hjälp av stegen i artikeln [nätverks konfigurations fil](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file) . Öppna filen med en text redigerare.
2. Lägg till en lokal nätverks plats i filen. Du kan använda valfritt giltigt adressprefix. Du kan lägga till en giltig IP-adress för VPN-gatewayen. Adress värden i det här avsnittet används inte för ExpressRoute-åtgärder, men krävs för fil verifiering. I exemplet är "branch1" namnet på platsen. Du kan använda ett annat namn, men se till att använda samma värde i Gateway-avsnittet i filen.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Navigera till VirtualNetworkSites och ändra fälten.

   * Kontrol lera att Gateway-undernätet finns för det virtuella nätverket. Om den inte gör det kan du lägga till en just nu. Namnet måste vara "GatewaySubnet".
   * Kontrol lera att Gateway-avsnittet i filen finns. Om den inte gör det lägger du till den. Detta krävs för att associera det virtuella nätverket med den lokala nätverks platsen (som representerar nätverket som du ansluter till).
   * Kontrol lera att anslutnings typen = dedikerat. Detta krävs för ExpressRoute-anslutningar.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Spara filen och ladda upp den till Azure.

### <a name="create-the-gateway"></a>Skapa gatewayen

Använd kommandot nedan för att skapa en gateway. Ersätt dina egna värden.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Kontrol lera att gatewayen har skapats

Använd kommandot nedan för att kontrol lera att gatewayen har skapats. Det här kommandot hämtar också Gateway-ID: t som du behöver för andra åtgärder.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Ändra storlek på en gateway

Det finns ett antal [Gateway-SKU: er](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan använda följande kommando för att ändra Gateway-SKU: n när som helst.

> [!IMPORTANT]
> Det här kommandot fungerar inte för UltraPerformance-Gateway. Om du vill ändra din gateway till en UltraPerformance-Gateway måste du först ta bort den befintliga ExpressRoute-gatewayen och sedan skapa en ny UltraPerformance-Gateway. Om du vill nedgradera din gateway från en UltraPerformance-Gateway måste du först ta bort UltraPerformance-gatewayen och sedan skapa en ny Gateway.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Ta bort en gateway

Använd kommandot nedan för att ta bort en gateway

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```