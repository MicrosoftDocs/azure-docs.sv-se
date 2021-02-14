---
title: 'Ansluta klassiska virtuella nätverk till Azure Resource Manager virtuella nätverk: PowerShell'
description: Skapa en VPN-anslutning mellan klassisk virtuella nätverk-och Resource Manager-virtuella nätverk med hjälp av VPN Gateway och PowerShell.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 7012a696684a4e7bc1d3b52943b11ba413c43037
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376496"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Anslut virtuella nätverk från olika distributionsmodeller med hjälp av PowerShell

Den här artikeln hjälper dig att ansluta klassiska virtuella nätverk till Resource Manager-virtuella nätverk så att resurserna som finns i de separata distributions modellerna kan kommunicera med varandra. Stegen i den här artikeln använder PowerShell, men du kan också skapa den här konfigurationen med hjälp av Azure Portal genom att välja artikeln i den här listan.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Att ansluta ett klassiskt VNet till ett virtuellt nätverk i Resource Manager liknar att ansluta ett VNet till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att få en säker tunnel med IPsec/IKE. Du kan skapa en anslutning mellan virtuella nätverk som finns i olika prenumerationer och i olika regioner. Du kan också ansluta virtuella nätverk som redan har anslutningar till lokala nätverk, så länge den gateway som de har kon figurer ATS med är dynamisk eller Route-baserad. Mer information om anslutningar mellan virtuella nätverk finns i [Vanliga frågor om VNet-till-VNet](#faq) i slutet av den här artikeln. 

Om du inte redan har en virtuell nätverksgateway och inte vill skapa en, kan du i stället överväga att ansluta din virtuella nätverk med VNet-peering. Ingen VPN-gateway används för VNet-peering. Mer information finns i [VNet peering (Vnet-peering)](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Innan du börjar

Följande steg vägleder dig genom de inställningar som krävs för att konfigurera en dynamisk eller Route-baserad Gateway för varje VNet och skapa en VPN-anslutning mellan gatewayerna. Den här konfigurationen stöder inte statiska eller principbaserad gatewayer.

### <a name="prerequisites"></a><a name="pre"></a>Förutsättningar

* Båda virtuella nätverk har redan skapats. Om du behöver skapa ett virtuellt Resource Manager-nätverk kan du läsa [skapa en resurs grupp och ett virtuellt nätverk](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Information om hur du skapar ett klassiskt virtuellt nätverk finns i [skapa ett klassiskt VNet](/previous-versions/azure/virtual-network/create-virtual-network-classic).
* Adress intervallen för virtuella nätverk överlappar inte varandra eller överlappar något av intervallen för andra anslutningar som gatewayerna kan vara anslutna till.
* Du har installerat de senaste PowerShell-cmdletarna. Mer information finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/) . Se till att du installerar både Service Management-och Resource Manager-cmdlets (RM). 

### <a name="example-settings"></a><a name="exampleref"></a>Exempelinställningar

Du kan använda värdena till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

**Klassiska VNet-inställningar**

VNet-namn = ClassicVNet <br>
Plats = västra USA <br>
Virtual Network adress utrymmen = 10.0.0.0/24 <br>
Undernät-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Lokalt nätverks namn = RMVNetLocal <br>
GatewayType = DynamicRouting

**VNet-inställningar för Resource Manager**

VNet-namn = RMVNet <br>
Resurs grupp = RG1 <br>
Virtual Network IP-adress utrymmen = 192.168.0.0/16 <br>
Undernät-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Plats = USA, östra <br>
Gatewayens offentliga IP-namn = gwpip <br>
Lokal nätverksgateway = ClassicVNetLocal <br>
Virtual Network Gateway-namn = RMGateway <br>
Gateway-IP-adresskonfiguration = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Avsnitt 1 – konfigurera det klassiska VNet
### <a name="1-download-your-network-configuration-file"></a>1. Ladda ned din nätverks konfigurations fil
1. Logga in på ditt Azure-konto i PowerShell-konsolen med utökade rättigheter. I följande cmdlet uppmanas du att ange inloggnings uppgifterna för ditt Azure-konto. När du har loggat in hämtas dina kontoinställningar så att de blir tillgängliga för Azure PowerShell. De klassiska tjänst hanterings Azure PowerShell-cmdletarna används i det här avsnittet.

   ```azurepowershell
   Add-AzureAccount
   ```

   Hämta din Azure-prenumeration.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Om du har mer än en prenumeration väljer du den du vill använda.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exportera Azure-nätverks konfigurations filen genom att köra följande kommando. Du kan ändra platsen för filen och exportera den till en annan plats om det behövs.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Öppna XML-filen som du laddade ned för att redigera den. Ett exempel på nätverks konfigurations filen finns i schemat för [nätverks konfiguration](/previous-versions/azure/reference/jj157100(v=azure.100)).

### <a name="2-verify-the-gateway-subnet"></a>2. kontrol lera Gateway-undernätet
I **VirtualNetworkSites** -elementet lägger du till ett Gateway-undernät till ditt VNet om det inte redan har skapats. När du arbetar med nätverks konfigurations filen måste Gateway-undernätet heta "GatewaySubnet" eller Azure kan inte identifiera och använda det som ett Gateway-undernät.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Exempel:**

```xml
<VirtualNetworkSites>
  <VirtualNetworkSite name="ClassicVNet" Location="West US">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/24</AddressPrefix>
    </AddressSpace>
    <Subnets>
      <Subnet name="Subnet-1">
        <AddressPrefix>10.0.0.0/27</AddressPrefix>
      </Subnet>
      <Subnet name="GatewaySubnet">
        <AddressPrefix>10.0.0.32/29</AddressPrefix>
      </Subnet>
    </Subnets>
  </VirtualNetworkSite>
</VirtualNetworkSites>
```

### <a name="3-add-the-local-network-site"></a>3. Lägg till den lokala nätverks platsen
Den lokala nätverks plats som du lägger till representerar det RM-VNet som du vill ansluta till. Lägg till ett **LocalNetworkSites** -element i filen om det inte redan finns en. Vid den här tidpunkten i konfigurationen kan VPNGatewayAddress vara vilken giltig offentlig IP-adress som helst eftersom vi ännu inte har skapat gatewayen för Resource Manager VNet. När vi skapar gatewayen ersätter vi IP-adressen för plats hållaren med rätt offentlig IP-adress som har tilldelats RM-gatewayen.

```xml
<LocalNetworkSites>
  <LocalNetworkSite name="RMVNetLocal">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  </LocalNetworkSite>
</LocalNetworkSites>
```

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. associera VNet med den lokala nätverks platsen
I det här avsnittet anger vi den lokala nätverks plats som du vill ansluta VNet till. I det här fallet är det Resource Manager VNet som du tidigare refererade till. Se till att namnen stämmer. Det här steget skapar inte någon Gateway. Den anger det lokala nätverk som gatewayen ska ansluta till.

```xml
<Gateway>
  <ConnectionsToLocalNetwork>
    <LocalNetworkSiteRef name="RMVNetLocal">
      <Connection type="IPsec" />
    </LocalNetworkSiteRef>
  </ConnectionsToLocalNetwork>
</Gateway>
```

### <a name="5-save-the-file-and-upload"></a>5. Spara filen och ladda upp
Spara filen och importera den sedan till Azure genom att köra följande kommando. Se till att du ändrar fil Sök vägen efter behov för din miljö.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Du ser ett liknande resultat som visar att importen lyckades.

```output
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                                
Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 
```

### <a name="6-create-the-gateway"></a>6. skapa gatewayen

Innan du kör det här exemplet kan du se den nätverks konfigurations fil som du laddade ned för de exakta namn som Azure förväntar sig att se. Nätverks konfigurations filen innehåller värdena för dina klassiska virtuella nätverk. Ibland ändras namnen på de klassiska virtuella nätverk i nätverks konfigurations filen när du skapar klassiska VNet-inställningar i Azure Portal på grund av skillnaderna i distributions modellerna. Om du till exempel använde Azure Portal för att skapa ett klassiskt VNet med namnet "klassisk VNet" och skapat det i en resurs grupp med namnet "ClassicRG", konverteras namnet som finns i nätverks konfigurations filen till "Group ClassicRG Classic VNet". När du anger namnet på ett VNet som innehåller blank steg ska du använda citat tecken runt värdet.


Använd följande exempel för att skapa en gateway för dynamisk routning:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Du kan kontrol lera status för gatewayen med hjälp av cmdleten **Get-AzureVNetGateway** .

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Avsnitt 2 – Konfigurera RM VNet Gateway



Kraven förutsätter att du redan har skapat ett RM VNet. I det här steget skapar du en VPN-gateway för RM VNet. Starta inte de här stegen förrän du har hämtat den offentliga IP-adressen för den klassiska VNet-gatewayen. 

1. Logga in på ditt Azure-konto i PowerShell-konsolen. I följande cmdlet uppmanas du att ange inloggnings uppgifterna för ditt Azure-konto. När du har loggat in hämtas dina konto inställningar så att de är tillgängliga för Azure PowerShell. Du kan också använda funktionen "prova" för att starta Azure Cloud Shell i webbläsaren.

   Om du använder Azure Cloud Shell hoppar du över följande cmdlet:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Kontrol lera att du använder rätt prenumeration genom att köra följande cmdlet:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Om du har mer än en prenumeration anger du den prenumeration som du vill använda.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Skapa en lokal nätverksgateway. I ett virtuellt nätverk refererar den lokala gatewayen vanligtvis till den lokala platsen. I det här fallet refererar den lokala Nätverksgatewayen till ditt klassiska VNet. Ge det ett namn som Azure kan referera till, och ange även adress utrymmes prefix. Azure använder det IP-adressprefix som du anger till att identifiera vilken trafik som ska skickas till den lokala platsen. Om du behöver justera informationen senare, innan du skapar din gateway, kan du ändra värdena och köra exemplet igen.
   
   **– Namn** är det namn som du vill tilldela för att referera till den lokala Nätverksgatewayen.<br>
   **-AddressPrefix** är adress utrymmet för ditt klassiska VNet.<br>
   **-GatewayIpAddress** är den offentliga IP-adressen för det klassiska VNet-gatewayen. Se till att ändra följande exempel text "n. n. n. n" för att avspegla rätt IP-adress.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Begär en offentlig IP-adress som ska allokeras till den virtuella Nätverksgatewayen för Resource Manager VNet. Du kan inte ange den IP-adress som du vill använda. IP-adressen tilldelas dynamiskt till den virtuella Nätverksgatewayen. Detta betyder dock inte IP-adressen kan ändras. Den enda gången den virtuella nätverks-gatewayens IP-adress ändras är när gatewayen tas bort och återskapas. Den ändras inte för storleks ändring, återställning eller annat internt underhåll/uppgraderingar av gatewayen.

   I det här steget anger vi också en variabel som används i ett senare steg.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Kontrol lera att det virtuella nätverket har ett Gateway-undernät. Om det inte finns något Gateway-undernät lägger du till ett. Kontrol lera att Gateway-undernätet heter *GatewaySubnet*.
5. Hämta det undernät som används för gatewayen genom att köra följande kommando. I det här steget anger vi också en variabel som ska användas i nästa steg.
   
   **-Name** är namnet på ditt Resource Manager VNet.<br>
   **-ResourceGroupName** är den resurs grupp som VNet är associerat med. Gateway-undernätet måste redan finnas för det här virtuella nätverket och måste ha namnet *GatewaySubnet* för att fungera korrekt.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Skapa konfigurationen för Gateway-IP-adressering. Gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas. Använd följande exempel för att skapa din gateway-konfiguration.

   I det här steget måste parametrarna **-SubnetId** och **-PublicIpAddressId** skickas till egenskapen ID från under nätet, respektive IP-adressprefix. Du kan inte använda en enkel sträng. Dessa variabler anges i steget för att begära en offentlig IP-adress och steget för att hämta under nätet.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Skapa den virtuella Resource Manager-Nätverksgatewayen genom att köra följande kommando. `-VpnType`Måste vara *routningsbaserad*. Det kan ta 45 minuter eller mer för gatewayen att skapa.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Kopiera den offentliga IP-adressen när VPN-gatewayen har skapats. Du använder den när du konfigurerar de lokala nätverks inställningarna för ditt klassiska VNet. Du kan använda följande cmdlet för att hämta den offentliga IP-adressen. Den offentliga IP-adressen visas i retur som *IpAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Avsnitt 3 – ändra de klassiska VNet-inställningarna för lokal plats

I det här avsnittet arbetar du med det klassiska VNet. Du ersätter IP-adressen för plats hållaren som du använde när du angett de lokala plats inställningarna som ska användas för att ansluta till Resource Manager VNet Gateway. Eftersom du arbetar med det klassiska VNet använder du PowerShell installerat lokalt på din dator, inte Azure Cloud Shell TryIt.

1. Exportera nätverks konfigurations filen.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Ändra värdet för VPNGatewayAddress med hjälp av en text redigerare. Ersätt IP-adressen för plats hållaren med den offentliga IP-adressen för Resource Manager-gatewayen och spara sedan ändringarna.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importera den ändrade nätverks konfigurations filen till Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Avsnitt 4 – skapa en anslutning mellan gatewayerna
Att skapa en anslutning mellan gateways kräver PowerShell. Du kan behöva lägga till ditt Azure-konto för att använda den klassiska versionen av PowerShell-cmdletar. Det gör du med hjälp av **Add-AzureAccount**.

1. Ange din delade nyckel i PowerShell-konsolen. Innan du kör cmdletarna, se den nätverks konfigurations fil som du laddade ned för de exakta namn som Azure förväntar sig att se. När du anger namnet på ett VNet som innehåller blank steg ska du använda enkla citat tecken runt värdet.<br><br>I följande exempel är **-VNetName** namnet på det klassiska VNet-och **-LocalNetworkSiteName-** namnet som du har angett för den lokala nätverks platsen. **SharedKey** är ett värde som du genererar och anger. I exemplet använde vi "vi abc123", men du kan skapa och använda något mer komplicerat. Det viktiga är att värdet du anger här måste vara samma värde som du anger i nästa steg när du skapar anslutningen. Returen ska visa **status: lyckades**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Skapa VPN-anslutningen genom att köra följande kommandon:
   
   Ange variablerna.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Skapa anslutningen. Observera att **-** startip är IPSec, inte Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Avsnitt 5 – kontrol lera dina anslutningar

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Så här kontrollerar du anslutningen från det klassiska VNet till ditt Resource Manager VNet

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Så här kontrollerar du anslutningen från det virtuella Resource Manager-nätverket till det klassiska VNet

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Vanliga frågor och svar om VNet-till-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
