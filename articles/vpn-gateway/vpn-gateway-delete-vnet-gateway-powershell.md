---
title: 'Azure-VPN Gateway: ta bort en gateway: PowerShell'
description: Ta bort en virtuell nätverksgateway med PowerShell i distributions modellen för Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 09/03/2020
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 48e0998719ea19c0b360b50d8c9171d2b448ad19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89440770"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Ta bort en virtuell nätverksgateway med PowerShell
> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassisk)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Det finns ett par olika metoder som du kan vidta när du vill ta bort en virtuell nätverksgateway för en VPN gateway-konfiguration.

- Om du vill ta bort allt och börja om, som i fallet med en test miljö, kan du ta bort resurs gruppen. När du tar bort en resurs grupp tas alla resurser i gruppen bort. Det här är metoden rekommenderas endast om du inte vill behålla någon av resurserna i resurs gruppen. Du kan inte selektivt ta bort några få resurser med hjälp av den här metoden.

- Om du vill behålla några av resurserna i resurs gruppen blir det något mer komplicerat att ta bort en virtuell nätverksgateway. Innan du kan ta bort den virtuella Nätverksgatewayen måste du först ta bort alla resurser som är beroende av gatewayen. De steg du följer beror på vilken typ av anslutningar du har skapat och de beroende resurserna för varje anslutning.

## <a name="before-beginning"></a>Innan du börjar



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. Hämta de senaste Azure Resource Manager PowerShell-cmdletarna.

Hämta och installera den senaste versionen av Azure Resource Manager PowerShell-cmdletar. Mer information om att ladda ned och installera PowerShell-cmdlets finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

### <a name="2-connect-to-your-azure-account"></a>2. Anslut till ditt Azure-konto.

Öppna PowerShell-konsolen och anslut till ditt konto. Använd följande exempel för att ansluta:

```powershell
Connect-AzAccount
```

Kontrollera prenumerationerna för kontot.

```powershell
Get-AzSubscription
```

Om du har mer än en prenumeration anger du den prenumeration som du vill använda.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Ta bort en plats-till-plats-VPN-gateway

Om du vill ta bort en virtuell nätverksgateway för en S2S-konfiguration måste du först ta bort alla resurser som hör till den virtuella Nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan måste vissa värden anges, medan andra värden är resultatet av utdata. Vi använder följande värden i exemplen i demonstrations syfte:

VNet-namn: VNet1<br>
Resurs grupp namn: RG1<br>
Namn på virtuell nätverksgateway: GW1<br>

Följande steg gäller för distributions modellen för Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella Nätverksgatewayen som du vill ta bort.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. se efter om den virtuella Nätverksgatewayen har några anslutningar.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. ta bort alla anslutningar.

Du kan uppmanas att bekräfta borttagningen av varje anslutning.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. ta bort den virtuella Nätverksgatewayen.

Du kan uppmanas att bekräfta borttagningen av gatewayen. Om du har en P2S-konfiguration för det här virtuella nätverket förutom S2S-konfigurationen tar borttagning av den virtuella Nätverksgatewayen automatiskt bort alla P2S-klienter utan varning.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Den virtuella Nätverksgatewayen har nu tagits bort. Du kan använda nästa steg för att ta bort alla resurser som inte längre används.

### <a name="5-delete-the-local-network-gateways"></a>5 ta bort de lokala Nätverksgatewayen.

Hämta listan över motsvarande lokala nätverksgateway.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Ta bort de lokala Nätverksgatewayen. Du kan uppmanas att bekräfta borttagningen av var och en av de lokala Nätverksgatewayen.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. ta bort de offentliga IP-adress resurserna.

Hämta IP-konfigurationer för den virtuella Nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentliga IP-adressresurser som används för den här virtuella Nätverksgatewayen. Om den virtuella Nätverksgatewayen var aktiv-aktiv, visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort de offentliga IP-resurserna.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. ta bort Gateway-undernätet och ange konfigurationen.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Ta bort en VPN-gateway för VNet-till-VNet

Om du vill ta bort en virtuell nätverksgateway för en V2V-konfiguration måste du först ta bort alla resurser som hör till den virtuella Nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan måste vissa värden anges, medan andra värden är resultatet av utdata. Vi använder följande värden i exemplen i demonstrations syfte:

VNet-namn: VNet1<br>
Resurs grupp namn: RG1<br>
Namn på virtuell nätverksgateway: GW1<br>

Följande steg gäller för distributions modellen för Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella Nätverksgatewayen som du vill ta bort.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. se efter om den virtuella Nätverksgatewayen har några anslutningar.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Det kan finnas andra anslutningar till den virtuella Nätverksgatewayen som ingår i en annan resurs grupp. Sök efter ytterligare anslutningar i varje ytterligare resurs grupp. I det här exemplet kontrollerar vi om det finns anslutningar från RG2. Kör detta för varje resurs grupp som kan ha en anslutning till den virtuella Nätverksgatewayen.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. Hämta listan över anslutningar i båda riktningarna.

Eftersom det här är en VNet-till-VNet-konfiguration måste du ha en lista över anslutningar i båda riktningarna.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
I det här exemplet kontrollerar vi om det finns anslutningar från RG2. Kör detta för varje resurs grupp som kan ha en anslutning till den virtuella Nätverksgatewayen.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. ta bort alla anslutningar.

Du kan uppmanas att bekräfta borttagningen av varje anslutning.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. ta bort den virtuella Nätverksgatewayen.

Du kan uppmanas att bekräfta borttagningen av den virtuella Nätverksgatewayen. Om du har P2S-konfigurationer till din virtuella nätverk, förutom din V2V-konfiguration, kommer borttagningen av de virtuella Nätverksgatewayen automatiskt att koppla från alla P2S-klienter utan varning.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Den virtuella Nätverksgatewayen har nu tagits bort. Du kan använda nästa steg för att ta bort alla resurser som inte längre används.

### <a name="6-delete-the-public-ip-address-resources"></a>6. ta bort de offentliga IP-adressresurser

Hämta IP-konfigurationer för den virtuella Nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentliga IP-adressresurser som används för den här virtuella Nätverksgatewayen. Om den virtuella Nätverksgatewayen var aktiv-aktiv, visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort de offentliga IP-resurserna. Du kan uppmanas att bekräfta borttagningen av den offentliga IP-adressen.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. ta bort Gateway-undernätet och ange konfigurationen.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Ta bort en punkt-till-plats-VPN-gateway

Om du vill ta bort en virtuell nätverksgateway för en P2S-konfiguration måste du först ta bort alla resurser som hör till den virtuella Nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden. När du arbetar med exemplen nedan måste vissa värden anges, medan andra värden är resultatet av utdata. Vi använder följande värden i exemplen i demonstrations syfte:

VNet-namn: VNet1<br>
Resurs grupp namn: RG1<br>
Namn på virtuell nätverksgateway: GW1<br>

Följande steg gäller för distributions modellen för Resource Manager.


>[!NOTE]
> När du tar bort VPN-gatewayen kommer alla anslutna klienter att kopplas från VNet utan varning.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. Hämta den virtuella Nätverksgatewayen som du vill ta bort.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. ta bort den virtuella Nätverksgatewayen.

Du kan uppmanas att bekräfta borttagningen av den virtuella Nätverksgatewayen.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

Den virtuella Nätverksgatewayen har nu tagits bort. Du kan använda nästa steg för att ta bort alla resurser som inte längre används.

### <a name="3-delete-the-public-ip-address-resources"></a>3. ta bort de offentliga IP-adress resurserna

Hämta IP-konfigurationer för den virtuella Nätverksgatewayen.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Hämta listan över offentliga IP-adresser som används för den här virtuella Nätverksgatewayen. Om den virtuella Nätverksgatewayen var aktiv-aktiv, visas två offentliga IP-adresser.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Ta bort de offentliga IP-adresserna. Du kan uppmanas att bekräfta borttagningen av den offentliga IP-adressen.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. ta bort Gateway-undernätet och ange konfigurationen.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>Ta bort en VPN-gateway genom att ta bort resurs gruppen

Om du inte är bekymrad över att behålla någon av dina resurser i resurs gruppen och du bara vill börja om kan du ta bort en hel resurs grupp. Det här är ett snabbt sätt att ta bort allt. Följande steg gäller endast för distributions modellen Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. Hämta en lista över alla resurs grupper i din prenumeration.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. Leta upp den resurs grupp som du vill ta bort.

Leta upp den resurs grupp som du vill ta bort och Visa listan över resurser i resurs gruppen. I exemplet är namnet på resurs gruppen RG1. Ändra exemplet för att hämta en lista över alla resurser.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. kontrol lera resurserna i listan.

När listan returneras granskar du den för att kontrol lera att du vill ta bort alla resurser i resurs gruppen samt själva resurs gruppen. Om du vill behålla några av resurserna i resurs gruppen följer du stegen i de tidigare avsnitten i den här artikeln för att ta bort din gateway.

### <a name="4-delete-the-resource-group-and-resources"></a>4. ta bort resurs gruppen och resurserna.

Om du vill ta bort resurs gruppen och alla resurser som finns i resurs gruppen ändrar du exemplet och kör.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. kontrol lera statusen.

Det tar lite tid för Azure att ta bort alla resurser. Du kan kontrol lera statusen för din resurs grupp med hjälp av den här cmdleten.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Resultatet som returneras visar ' lyckades '.

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
