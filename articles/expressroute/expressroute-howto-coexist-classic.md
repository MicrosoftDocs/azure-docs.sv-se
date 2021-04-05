---
title: 'Konfigurera ExpressRoute och S2S VPN samtidiga anslutningar: klassisk'
description: Den här artikeln vägleder dig genom att konfigurera ExpressRoute och en VPN-anslutning för plats till plats som kan samexistera för den klassiska distributionsmodellen.
documentationcenter: na
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.openlocfilehash: 70407d726b697dd6c4dfb6777cf6c445092d3b73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206333"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-classic"></a>Konfigurera ExpressRoute-anslutningar och VPN-anslutningar för plats-till-plats som kan samexistera (klassisk)
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Klassisk](expressroute-howto-coexist-classic.md)
> 
> 

Den här artikeln hjälper dig att konfigurera ExpressRoute och VPN-anslutningar för plats till plats som samexisterar. Att kunna konfigurera VPN för plats till plats och ExpressRoute har flera fördelar. Du kan konfigurera en VPN för plats till plats som en säker redundanssökväg för ExpressRoute, eller använda plats-till-plats-VPN för att ansluta till platser som inte är anslutna via ExpressRoute. Vi beskriver stegen för att konfigurera båda scenarierna i den här artikeln. Den här artikeln gäller den klassiska distributionsmodellen. Den här konfigurationen är inte tillgänglig i portalen.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Om Azures distributionsmodeller**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> ExpressRoute-kretsarna måste förkonfigureras innan du följer anvisningarna nedan. Kontrollera att du har följt riktlinjerna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) och [konfigurera routning](expressroute-howto-routing-classic.md) innan du följer stegen nedan.
> 
> 

## <a name="limits-and-limitations"></a>Gränser och begränsningar
* **Transit-routing stöds inte.** Du kan inte routa (via Azure) mellan ditt lokala nätverk som ansluter via plats-till-plats-VPN och ditt lokala nätverk som ansluter via ExpressRoute.
* **Punkt-till-plats stöds inte.** Du kan inte aktivera punkt-till-plats-VPN-anslutningar till samma VNet som är anslutet till ExpressRoute. Punkt-till-plats-VPN och ExpressRoute kan inte samexistera för samma VNet.
* **Framtvingad tunneling kan inte aktiveras för plats-till-plats-VPN-gatewayen.** Du kan bara "tvinga" all Internetriktad trafik tillbaka till ditt lokala nätverk via ExpressRoute.
* **Basic-SKU-gatewayen stöds inte.** Du måste använda en icke-Basic SKU-gateway för både [ExpressRoute-gatewayen](expressroute-about-virtual-network-gateways.md) och [VPN-gatewayen](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Enbart routebaserad VPN-gateway stöds.** Du måste använda en routebaserad [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Statiska vägar ska konfigureras för din VPN-gateway.** Om ditt lokala nätverk är anslutet både till ExpressRoute och en plats-till-plats-VPN så måste du ha konfigurerat en statisk väg i ditt lokala nätverk för att routa plats-till-plats-VPN-anslutningen till det offentliga Internet.
* **ExpressRoute-gatewayen måste konfigureras först.** Du måste skapa ExpressRoute-gatewayen först innan du lägger till en plats-till-plats-VPN-gateway för plats till plats.

## <a name="configuration-designs"></a>Konfigurationsdesign
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Konfigurera en VPN för plats till plats som en redundanssökväg för ExpressRoute
Du kan konfigurera en VPN-anslutning för plats till plats som en säkerhetskopia av ExpressRoute. Detta gäller endast virtuella nätverk som är länkade till Azures privata peering-sökväg. Det finns ingen VPN-baserad redundanslösning för tjänster som är tillgängliga via Azures offentliga och Microsofts peerings. ExpressRoute-kretsen är alltid den primära länken. Data flödar endast via VPN-sökvägen för plats till plats om ExpressRoute-kretsen misslyckas. 

> [!NOTE]
> Medan ExpressRoute-kretsen är prioriterad över plats-till-plats-VPN när bägge vägarna är samma, kommer Azure att använda den längsta prefixmatchning för att välja väg till paketets mål.
> 
> 

![Diagram som visar en plats-till-plats-VPN-anslutning som en säkerhets kopia för ExpressRoute.](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Konfigurera en VPN för plats till plats för att ansluta till platser som inte är anslutna via ExpressRoute
Du kan konfigurera nätverket där vissa platser ansluter direkt till Azure via VPN för plats till plats och vissa platser ansluter via ExpressRoute. 

![Samexistera](media/expressroute-howto-coexist-classic/scenario2.jpg)

> [!NOTE]
> Det går inte att konfigurera ett virtuellt nätverk som en överföringsrouter.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Välj vilka steg du ska använda
Det finns två uppsättningar procedurer att välja mellan för att konfigurera anslutningar som kan existera tillsammans. Vilken konfigurationsprocedur du väljer beror på om du har ett befintligt virtuellt nätverk som du vill ansluta till, eller om du vill skapa ett nytt virtuellt nätverk.

* Jag har inte något VNet och behöver skapa ett.
  
    Om du inte redan har ett virtuellt nätverk kan den här proceduren hjälpa dig att skapa ett nytt virtuellt nätverk med hjälp av en klassisk distributionsmodell, samt skapa nya ExpressRoute- och VPN-anslutningar för plats till plats. Om du vill konfigurera följer du stegen i artikelavsnittet [Så här skapar du ett nytt virtuellt nätverk och samtidiga anslutningar](#new).
* Jag har redan en klassisk distributionsmodell för VNet.
  
    Du kanske redan har ett virtuellt nätverk på plats med en befintlig VPN-anslutning för plats till plats eller en ExpressRoute-anslutning. Artikelavsnittet [Så här konfigurerar du samexisterande anslutningar för ett befintligt VNet](#add) visar hur du tar bort gatewayen och sedan skapar nya ExpressRoute- och VPN-anslutningar för plats till plats. Observera att när du skapar nya anslutningar måste stegen utföras i en mycket specifik ordning. Följ inte anvisningarna i andra artiklar när du skapar dina gateways och anslutningar.
  
    I den här proceduren skapar du anslutningar som kan samexistera, vilket kräver att du tar bort din gateway och sedan konfigurerar nya gateways. Detta innebär att du har stilleståndstid för anslutningar på flera platser när du tar bort och återskapar din gateway och dina anslutningar, men du behöver inte migrera några virtuella datorer eller tjänster till ett nytt virtuellt nätverk. Dina virtuella datorer och tjänster kommer fortfarande att kunna kommunicera ut via lastbalanseraren när du konfigurerar din gateway, om de är konfigurerade för att göra det.

## <a name="install-powershell-cmdlets"></a>Installera PowerShell-cmdletar

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="to-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Så här skapar du ett nytt virtuellt nätverk och samexisterande anslutningar
Den här proceduren vägleder dig genom att skapa ett VNet samt plats-till-plats- och ExpressRoute-anslutningar som ska finnas samtidigt.

1. Du måste installera den senaste versionen av Azure PowerShell-cmdletarna. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/). Observera att de cmdletar som du använder för den här konfigurationen kan se annorlunda ut än de du tidigare använt. Var noga med att använda de cmdletar som anges i instruktionerna. 
2. Skapa ett schema för det virtuella nätverket. Mer information om konfigurationsschemat finns i [Konfigurationsschema för Azure Virtual Network](/previous-versions/azure/reference/jj157100(v=azure.100)).
   
    När du skapar ditt schema bör du vara noga med att använda följande värden:
   
   * Gateway-undernätet för det virtuella nätverket måste vara /27 eller ett kortare prefix (till exempel /26 eller /25).
   * Anslutningstypen för gatewayen är ”Dedikerad”.
     
    ```xml
    <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
      <AddressSpace>
        <AddressPrefix>10.17.159.192/26</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="Subnet-1">
          <AddressPrefix>10.17.159.192/27</AddressPrefix>
        </Subnet>
        <Subnet name="GatewaySubnet">
          <AddressPrefix>10.17.159.224/27</AddressPrefix>
          /Subnet>
      </Subnets>
      <Gateway>
        <ConnectionsToLocalNetwork>
          <LocalNetworkSiteRef name="MyLocalNetwork">
            <Connection type="Dedicated" />
          </LocalNetworkSiteRef>
        </ConnectionsToLocalNetwork>
      </Gateway>
    </VirtualNetworkSite>
    ```
3. När du har skapat och konfigurerat schemats XML-fil, överför du filen. Detta skapar det virtuella nätverket.
   
    Använd följande cmdlet för att överföra filen och ersätta värdet med ditt egna.
   
    ```azurepowershell
    Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'
    ```
4. <a name="gw"></a>Skapa en ExpressRoute-gateway. Se till att ange GatewaySKU som *standard*, *HighPerformance*, eller *UltraPerformance* och GatewayType som *DynamicRouting*.
   
    Använd följande exempel och ersätt värdena med dina egna.

    ```azurepowershell
    New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance
    ```
5. Länka ExpressRoute-gatewayen till ExpressRoute-kretsen. När det här steget har slutförts har anslutningen mellan ditt lokala nätverk och Azure, via ExpressRoute, upprättats.
   
    ```azurepowershell
    New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET
    ```
6. <a name="vpngw"></a>Skapa sedan din VPN-gateway för plats till plats. GatewaySKU måste vara *Standard*, *HighPerformance*, eller *UltraPerformance* och GatewayType måste vara *DynamicRouting*.
   
    ```azurepowershell
    New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance
    ```
   
    Om du ska hämta inställningarna för den virtuella nätverksgatewayen, inklusive gateway-ID och offentlig IP, använder du cmdleten `Get-AzureVirtualNetworkGateway`.
   
    ```azurepowershell
    Get-AzureVirtualNetworkGateway
   
    GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
    GatewayName          : S2SVPN
    LastEventData        :
    GatewayType          : DynamicRouting
    LastEventTimeStamp   : 5/29/2015 4:41:41 PM
    LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
    LastEventID          : 23002
    State                : Provisioned
    VIPAddress           : 104.43.x.y
    DefaultSite          :
    GatewaySKU           : HighPerformance
    Location             :
    VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
    SubnetId             :
    EnableBgp            : False
    OperationDescription : Get-AzureVirtualNetworkGateway
    OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
    OperationStatus      : Succeeded
    ```
7. Skapa en lokal plats för VPN-gatewayen. Det här kommandot konfigurerar inte din lokala VPN-gateway. I stället kan du ange lokala gateway-inställningar, som t.ex. offentlig IP-adress och lokalt adressutrymme, så att Azure VPN-gatewayen kan ansluta till den.
   
   > [!IMPORTANT]
   > Den lokala platsen för VPN för plats till plats är inte definierad i netcfg. I stället måste du använda denna cmdlet för att ange de lokala platsparametrarna. Du kan inte definiera den med hjälp av portalen eller netcfg-filen.
   > 
   > 
   
    Använd följande exempel och ersätt värdena med dina egna.
   
    ```azurepowershell
    New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>
    ```
   
   > [!NOTE]
   > Om det lokala nätverket har flera routningar kan du ange dem i en matris.  $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  
   > 
   > 

    Om du ska hämta inställningarna för den virtuella nätverksgatewayen, inklusive gateway-ID och offentlig IP, använder du cmdleten `Get-AzureVirtualNetworkGateway`. Se följande exempel.

    ```azurepowershell
    Get-AzureLocalNetworkGateway

    GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
    GatewayName          : MyLocalNetwork
    IpAddress            : 23.39.x.y
    AddressSpace         : {10.1.2.0/24}
    OperationDescription : Get-AzureLocalNetworkGateway
    OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
    OperationStatus      : Succeeded
    ```


1. Konfigurera din lokala VPN-enhet så att den ansluter till den nya gatewayen. Använd den information som du hämtade i steg 6 när du konfigurerar VPN-enheten. Mer information om VPN-enhetskonfiguration finns i [VPN-enhetskonfiguration](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
2. Länka VPN-gatewayen för plats till plats på Azure till den lokala gatewayen.
   
    I det här exemplet är connectedEntityId det lokala gateway-ID som du hittar genom att köra `Get-AzureLocalNetworkGateway`. Du kan hitta virtualNetworkGatewayId med hjälp av `Get-AzureVirtualNetworkGateway`-cmdleten. Efter det här steget upprättas anslutningen mellan ditt lokala nätverk och Azure via VPN-anslutningen för plats till plats.

    ```azurepowershell
    New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>
    ```

## <a name="to-configure-coexisting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Så här konfigurerar du samexisterande anslutningar för ett befintligt VNet
Om du har ett befintligt virtuellt nätverk, kontrollerar du storleken på gateway-undernätet. Om gateway-undernätet är /28 eller /29, måste du först ta bort den virtuella nätverksgatewayen och öka storleken för gateway-undernätet. Stegen i det här avsnittet visar hur du gör.

Om gateway-undernätet är /27 eller större och det virtuella nätverket är anslutet via ExpressRoute, kan du hoppa över stegen nedan och gå vidare till [”Steg 6 – Skapa en VPN-gateway för plats till plats”](#vpngw) i föregående avsnitt. 

> [!NOTE]
> När du tar bort den befintliga gatewayen förlorar dina lokala platser anslutningen till ditt virtuella nätverk medan du arbetar med konfigurationen.
> 
> 

1. Du måste installera den senaste versionen av Azure Resource Managers PowerShell-cmdletar. Mer information om hur man installerar PowerShell-cmdletar finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/). Observera att de cmdletar som du använder för den här konfigurationen kan se annorlunda ut än de du tidigare använt. Var noga med att använda de cmdletar som anges i instruktionerna. 
2. Ta bort den befintliga ExpressRoute- eller VPN-gatewayen för plats till plats. Använd följande cmdlet och ersätt värdena med dina egna.
   
    ```azurepowershell
    Remove-AzureVNetGateway –VnetName MyAzureVNET
    ```
3. Exportera det virtuella nätverksschemat. Använd följande PowerShell-cmdlet och ersätt värdena med dina egna.
   
    ```azurepowershell
    Get-AzureVNetConfig –ExportToFile "C:\NetworkConfig.xml"
    ```
4. Redigera nätverksschemat för konfigurationsfilen så att gateway-undernätet är /27 eller ett kortare prefix (till exempel /26 eller /25). Se följande exempel. 
   
   > [!NOTE]
   > Om du inte har tillräckligt med IP-adresser kvar i det virtuella nätverket för att kunna öka storleken på gateway-undernätet, måste du lägga till mer IP-adressutrymme. Mer information om konfigurationsschemat finns i [Konfigurationsschema för Azure Virtual Network](/previous-versions/azure/reference/jj157100(v=azure.100)).
   > 
   > 
   
    ```xml
    <Subnet name="GatewaySubnet">
      <AddressPrefix>10.17.159.224/27</AddressPrefix>
    </Subnet>
    ```
5. Om din gateway tidigare var en VPN för plats till plats, måste du dessutom ändra anslutningstypen till **Dedikerad**.
   
    ```xml
    <Gateway>
      <ConnectionsToLocalNetwork>
        <LocalNetworkSiteRef name="MyLocalNetwork">
          <Connection type="Dedicated" />
        </LocalNetworkSiteRef>
      </ConnectionsToLocalNetwork>
    </Gateway>
    ```
6. Just nu har du ett VNet utan gateways. Om du vill slutföra dina anslutningar och skapa nya gateways kan du fortsätta med [Steg 4 – Skapa en ExpressRoute-gateway](#gw), som finns i den föregående uppsättningen med steg.

## <a name="next-steps"></a>Nästa steg
Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md)