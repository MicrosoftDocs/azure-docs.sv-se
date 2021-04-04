---
title: 'Ta bort en virtuell nätverksgateway: Azure Classic'
description: Ta bort en virtuell nätverksgateway med PowerShell i den klassiska distributions modellen.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 39585a68c5cddc50cd04e82caca71209270f7b68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874126"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Ta bort en virtuell nätverksgateway med PowerShell (klassisk)

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klassisk – PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Den här artikeln hjälper dig att ta bort en VPN-gateway i den klassiska distributions modellen med hjälp av PowerShell. När den virtuella Nätverksgatewayen har tagits bort ändrar du nätverks konfigurations filen för att ta bort element som du inte längre använder.

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>Steg 1: Anslut till Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. installera de senaste PowerShell-cmdletarna.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. Anslut till ditt Azure-konto.

Öppna PowerShell-konsolen med utökade rättigheter och anslut till ditt konto. Använd följande exempel för att ansluta:

1. Öppna PowerShell-konsolen med utökade rättigheter.
2. Anslut till ditt konto. Använd följande exempel för att ansluta:

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>Steg 2: exportera och Visa nätverks konfigurations filen

Skapa en katalog på datorn och exportera sedan nätverkskonfigurationsfilen till katalogen. Du kan använda den här filen för att visa den aktuella konfigurations informationen och även ändra nätverks konfigurationen.

I det här exemplet exporteras nätverkskonfigurationsfilen till C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Öppna filen med en text redigerare och visa namnet på det klassiska VNet. När du skapar ett VNet i Azure Portal, visas inte det fullständiga namnet som används i Azure i portalen. Ett VNet som till exempel verkar ha namnet "ClassicVNet1" i Azure Portal kan ha ett mycket längre namn i nätverks konfigurations filen. Namnet kan se ut ungefär så här: "Group ClassicRG1 ClassicVNet1". Virtuella nätverks namn anges som **"VirtualNetworkSite Name ="**. Använd namnen i nätverks konfigurations filen när du kör dina PowerShell-cmdlets.

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>Steg 3: ta bort den virtuella Nätverksgatewayen

När du tar bort en virtuell nätverksgateway frånkopplas alla anslutningar till det virtuella nätverket via gatewayen. Om du har P2S-klienter som är anslutna till det virtuella nätverket kommer de att frånkopplas utan varning.

Det här exemplet tar bort den virtuella Nätverksgatewayen. Se till att använda det fullständiga namnet på det virtuella nätverket från nätverks konfigurations filen.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Om det lyckas visas RETUR:

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>Steg 4: ändra nätverks konfigurations filen

När du tar bort en virtuell nätverksgateway, ändrar cmdleten inte nätverks konfigurations filen. Du måste ändra filen för att ta bort de element som inte längre används. Följande avsnitt hjälper dig att ändra nätverks konfigurations filen som du laddade ned.

### <a name="local-network-site-references"></a><a name="lnsref"></a>Referenser till lokala nätverks platser

Om du vill ta bort referens information för webbplatsen gör du konfigurations ändringar i **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. Att ta bort en lokal plats referens utlöser Azure för att ta bort en tunnel. Beroende på vilken konfiguration du har skapat kanske du inte har någon **LocalNetworkSiteRef** angiven.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Exempel:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="local-network-sites"></a><a name="lns"></a>Lokala nätverks platser

Ta bort alla lokala platser som du inte längre använder. Beroende på vilken konfiguration du har skapat, är det möjligt att du inte har en **LocalNetworkSite** listad.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

I det här exemplet har vi bara tagit bort Site3.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="client-addresspool"></a><a name="clientaddresss"></a>Klientadresspool för klient

Om du har en P2S-anslutning till ditt VNet kommer du att ha en **VPNClientAddressPool**. Ta bort de klient adress grupper som motsvarar den virtuella Nätverksgatewayen som du har tagit bort.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Exempel:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

Ta bort **GatewaySubnet** som motsvarar det virtuella nätverket.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Exempel:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>Steg 5: Ladda upp nätverks konfigurations filen

Spara ändringarna och överför nätverks konfigurations filen till Azure. Se till att du ändrar fil Sök vägen efter behov för din miljö.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Om det lyckas, visar returen något som liknar det här exemplet:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
