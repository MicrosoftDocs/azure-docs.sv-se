---
title: 'Ansluta en dator till ett virtuellt nätverk med hjälp av punkt-till-plats-och RADIUS-autentisering: PowerShell | Azure'
description: Anslut Windows-och OS X-klienter på ett säkert sätt till ett virtuellt nätverk med P2S och RADIUS-autentisering.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: cherylmc
ms.openlocfilehash: 9d962d3a4757b4c7b2d217f91aaf73d6ad4164d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94964855"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Konfigurera en punkt-till-plats-anslutning till ett VNet med RADIUS-autentisering: PowerShell

Den här artikeln visar hur du skapar ett VNet med en punkt-till-plats-anslutning som använder RADIUS-autentisering. Den här konfigurationen är endast tillgänglig för distributions modellen Resource Manager.

Med en VPN-gateway med P2S-konfiguration (punkt-till-plats) kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. Punkt-till-plats-VPN-anslutningar är användbara när du vill ansluta till ditt VNet från en annan plats, till exempel när du arbetar hemifrån eller en konferens. En P2S-VPN-anslutning är också en bra lösning att använda i stället för en plats-till-plats-VPN-anslutning när du bara har ett fåtal klienter som behöver ansluta till ett VNet.

En P2S VPN-anslutning startas från Windows- och Mac-enheter. Anslutande klienter kan använda följande autentiseringsmetoder: 

* RADIUS-server
* VPN Gateway intern certifikatautentisering
* Intern Azure Active Directory-autentisering (endast Windows 10)

Den här artikeln hjälper dig att konfigurera en P2S-konfiguration med autentisering med RADIUS-server. Om du vill autentisera med hjälp av genererade certifikat och intern autentisering i VPN-gateway i stället, se [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med VPN-gatewayens interna certifikatautentisering](vpn-gateway-howto-point-to-site-rm-ps.md) eller [skapa en Azure Active Directory-klient för P2s OpenVPN-protokoll anslutningar](openvpn-azure-ad-tenant.md) för Azure Active Directory autentisering.

![Diagram som visar P2S-konfigurationen med autentisering med hjälp av en RADIUS-server.](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. P2S skapar VPN-anslutningen via antingen SSTP (Secure Socket Tunneling Protocol), OpenVPN eller IKEv2.

* SSTP är en TLS-baserad VPN-tunnel som bara stöds på Windows-klient plattformar. Den kan ta sig förbi brandväggar, vilket gör den till ett utmärkt alternativ för att ansluta till Azure var som helst. På serversidan stöder vi SSTP version 1.0, 1.1 och 1.2. Klienten avgör vilken version som ska användas. För Windows 8.1 och senare, använder SSTP version 1.2 som standard.

* OpenVPN® protokoll, ett SSL/TLS-baserat VPN-protokoll. En TLS VPN-lösning kan tränga in brand väggar, eftersom de flesta brand väggar öppnar TCP-port 443 utgående, vilket TLS använder. OpenVPN kan användas för att ansluta från Android, iOS (version 11,0 och senare), Windows-, Linux-och Mac-enheter (OSX-versioner 10,13 och senare).

* IKEv2 VPN, en standardbaserad IPsec VPN-lösning. IKEv2 VPN kan användas för att ansluta från Mac-enheter (OSX-versionerna 10.11 och senare).

P2S-anslutningar kräver följande:

* En RouteBased VPN gateway. 
* En RADIUS-server för hantering av användarautentisering. RADIUS-servern kan distribueras lokalt eller i Azure VNet. Du kan också konfigurera två RADIUS-servrar för hög tillgänglighet.
* Ett konfigurations paket för VPN-klienten för de Windows-enheter som ska ansluta till det virtuella nätverket. Ett konfigurations paket för VPN-klienten ger de inställningar som krävs för att en VPN-klient ska kunna ansluta via P2S.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>Om Active Directory (AD) domänautentisering för P2S-VPN

AD Domain Authentication gör att användare kan logga in på Azure med sina organisations domän uppgifter. Den kräver en RADIUS-server som integreras med AD-servern. Organisationer kan också utnyttja sin befintliga RADIUS-distribution.
 
RADIUS-servern kan finnas lokalt eller i ditt Azure VNet. Under autentiseringen fungerar VPN-gatewayen som ett vidarekoppling och vidarebefordrar autentiseringsbegäranden fram och tillbaka mellan RADIUS-servern och den anslutande enheten. Det är viktigt att VPN-gatewayen kan komma åt RADIUS-servern. Om RADIUS-servern finns lokalt krävs en VPN plats-till-plats-anslutning från Azure till den lokala platsen.

Förutom Active Directory kan en RADIUS-server också integreras med andra externa identitets system. Detta öppnar mycket av autentiseringsinställningarna för punkt-till-plats-VPN, inklusive MFA-alternativ. Kontrol lera leverantörs dokumentationen för RADIUS-servern för att få en lista över identitets system som den integrerar med.

![Anslutnings diagram-RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Endast en VPN-anslutning från plats till plats kan användas för att ansluta till en lokal RADIUS-server. En ExpressRoute-anslutning kan inte användas.
>
>

## <a name="before-beginning"></a><a name="before"></a>Innan du börjar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Exempelvärden

Du kan använda exempelvärdena för att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln. Du kan antingen se stegen som en genomgång och använda värdena utan att ändra dem, eller ändra dem så att de avspeglar din egen miljö.

* **Namn: VNet1**
* **Adressutrymme: 192.168.0.0/16** and **10.254.0.0/16**<br>I det här exemplet används mer än ett adressutrymme för att illustrera att den här konfigurationen fungerar med flera adressutrymmen. Flera adressutrymmen krävs dock inte för den här konfigurationen.
* **Undernätsnamn: FrontEnd**
  * **Adressintervall för undernätet: 192.168.1.0/24**
* **Undernätsnamn: BackEnd**
  * **Adressintervall för undernätet: 10.254.1.0/24**
* **Undernätsnamn: GatewaySubnet**<br>Undernätsnamnet *GatewaySubnet* är obligatoriskt för att VPN-gatewayen ska fungera.
  * **Adressintervall för gateway-undernätet: 192.168.200.0/24** 
* **VPN-klientens adresspool: 172.16.201.0/24**<br>VPN-klienter som ansluter till det virtuella nätverket med den här punkt-till-plats-anslutningen får en IP-adress från VPN-klientadresspoolen.
* **Prenumeration:** Kontrollera att du använder rätt prenumeration om du har mer än en.
* **Resursgrupp: TestRG**
* **Plats: östra USA**
* **DNS-Server: IP-adressen** för den DNS-server som du vill använda för namn matchning för ditt VNet. (valfritt)
* **GW-namn: Vnet1GW**
* **Offentligt IP-namn: VNet1GWPIP**
* **VpnType: Routningsbaserad**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. ange variablerna

Deklarera de variabler som du vill använda. Använd följande exempel och ersätt värdena med dina egna om det behövs. Om du stänger PowerShell/Cloud Shell-sessionen när som helst under övningen ska du bara kopiera och klistra in värdena igen för att omdeklarera variablerna.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"></a> skapa resurs gruppen, VNet och den offentliga IP-adressen

Följande steg skapar en resurs grupp och ett virtuellt nätverk i resurs gruppen med tre undernät. När du ersätter värden är det viktigt att du alltid namnger ditt Gateway-undernät särskilt "GatewaySubnet". Om du namnger det något annat Miss lyckas Gateway-skapandet.

1. Skapa en resursgrupp.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Skapa undernätskonfigurationerna för det virtuella nätverket och ge dem namnen *FrontEnd*, *BackEnd* och *GatewaySubnet*. Dessa prefix måste vara en del av VNet-adressutrymmet som du deklarerade.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Skapa det virtuella nätverket.

   I det här exemplet är serverparametern -DnsServer valfri. Ingen ny DNS-server skapas när du anger ett värde. IP-adressen för DNS-servern som du anger måste vara en DNS-server som kan matcha namnen för de resurser som du ansluter till från ditt virtuella nätverk. I det här exemplet har vi använt en privat IP-adress, men förmodligen inte IP-adressen till din DNS-server. Använd dina egna värden. Värdet du anger används av de resurser som du distribuerar till VNet, inte av P2S-anslutningen.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. En VPN-gateway måste ha en offentlig IP-adress. Först begär du IP-adressresursen och sedan hänvisar du till den när du skapar din virtuella nätverksgateway. IP-adressen tilldelas dynamiskt till resursen när en VPN-gateway har skapats. VPN Gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Du kan inte begära en statisk offentlig IP-adresstilldelning. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

   Ange variabler för att begära en dynamiskt tilldelad offentlig IP-adress.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"></a> Konfigurera din RADIUS-server

Innan du skapar och konfigurerar den virtuella Nätverksgatewayen bör RADIUS-servern konfigureras på rätt sätt för autentisering.

1. Om du inte har en RADIUS-server distribuerad kan du distribuera en. Information om distributions steg finns i installations guiden som tillhandahålls av din RADIUS-leverantör.  
2. Konfigurera VPN-gatewayen som en RADIUS-klient på RADIUS. När du lägger till den här RADIUS-klienten anger du det virtuella nätverks GatewaySubnet som du skapade. 
3. När RADIUS-servern har kon figurer ATS hämtar du RADIUS-serverns IP-adress och den delade hemlighet som RADIUS-klienter ska använda för att kommunicera med RADIUS-servern. Om RADIUS-servern finns i Azure VNet använder du CA-IP för den virtuella RADIUS-servern.

Artikeln [nätverks princip Server (NPS)](/windows-server/networking/technologies/nps/nps-top) ger vägledning om hur du konfigurerar en Windows RADIUS-server (NPS) för AD-domänautentisering.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"></a> skapa VPN-gatewayen

Konfigurera och skapa VPN-gatewayen för ditt VNet.

* -GatewayType måste vara VPN och-VpnType måste vara Routningsbaserad.
* Det kan ta upp till 45 minuter att slutföra en VPN-gateway, beroende på vilken [Gateway-SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)   du väljer.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"></a> Lägg till RADIUS-servern och klient-adresspoolen
 
* -RadiusServer kan anges efter namn eller IP-adress. Om du anger namnet och servern finns lokalt kommer VPN-gatewayen kanske inte att kunna matcha namnet. I så fall är det bättre att ange IP-adressen för servern. 
* -RadiusSecret ska matcha det som har kon figurer ATS på RADIUS-servern.
* VpnClientAddressPool är det intervall som de anslutande VPN-klienterna får en IP-adress från. Använd ett intervall för privata IP-adresser som inte överlappar med den lokala platsen som du ansluter från, eller med det virtuella nätverk som du vill ansluta till. Se till att du har konfigurerat en tillräckligt stor adresspool.  

1. Skapa en säker sträng för RADIUS-hemligheten.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. Du uppmanas att ange RADIUS-hemligheten. Tecknen som du anger visas inte och ersätts istället av tecknet "*".

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Lägg till VPN-klientens adresspool och RADIUS-serverns information.

   För SSTP-konfigurationer:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   För OpenVPN® konfigurationer:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   För IKEv2-konfigurationer:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   För SSTP + IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Använd följande syntax för att ange **två** RADIUS-servrar. Ändra värdet **VpnClientProtocol** vid behov

    ```azurepowershell-interactive
    $radiusServer1 = New-AzRadiusServer -RadiusServerAddress 10.1.0.15 -RadiusServerSecret $radiuspd -RadiusServerScore 30
    $radiusServer2 = New-AzRadiusServer -RadiusServerAddress 10.1.0.16 -RadiusServerSecret $radiuspd -RadiusServerScore 1

    $radiusServers = @( $radiusServer1, $radiusServer2 )

    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $actual -VpnClientAddressPool 201.169.0.0/16 -VpnClientProtocol "IkeV2" -RadiusServerList $radiusServers
    ```

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"></a> Hämta konfigurations paketet för VPN-klienten och konfigurera VPN-klienten

Konfigurationen av VPN-klienten gör att enheter kan ansluta till ett VNet via en P2S-anslutning. Om du vill generera ett konfigurations paket för VPN-klienten och konfigurera VPN-klienten, se [skapa en VPN-klientkonfiguration för RADIUS-autentisering](point-to-site-vpn-client-configuration-radius.md).

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. Anslut till Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Så här ansluter du från en Windows VPN-klient

1. Anslut till ditt VNet genom att gå till VPN-anslutningarna på klientdatorn och leta upp den VPN-anslutning som du skapade. Den har samma namn som ditt virtuella nätverk. Ange dina autentiseringsuppgifter för domänen och klicka på Anslut. Ett popup-meddelande som begär utökade rättigheter visas. Godkänn den och ange autentiseringsuppgifterna.

   ![VPN-klient ansluter till Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Anslutningen upprättas.

   ![Anslutning upprättad](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Ansluta från en Mac VPN-klient

Från dialogrutan Nätverk letar du upp den klientprofil som du vill använda och klickar sedan på **Anslut**.

  ![Mac-anslutning](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>Så här verifierar du anslutningen

1. Verifiera att VPN-anslutningen är aktiv genom att öppna en upphöjd kommandotolk och köra *ipconfig/all*.
2. Granska resultaten. Observera att IP-adressen som du fick är en av adresserna i klientadresspoolen för VPN för punkt-till-plats som du angav i konfigurationen. Resultatet är ungefär som i det här exemplet:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

Information om hur du felsöker en P2S-anslutning finns i [Felsöka Azure punkt-till-plats-anslutningar](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Kontrollera att paketet för VPN-klientkonfiguration har skapats efter att IP-adresser för DNS-server har angetts för VNet. Om du uppdaterade IP-adresserna för DNS-servern skapar och installerar du ett nytt paket för VPN-klientkonfiguration.

* Använd ”ipconfig” för att kontrollera vilken IPv4-adress som har tilldelats till Ethernet-adaptern på den dator som du ansluter från. Om IP-adressen ligger inom adressintervallet för det virtuella nätverk som du ansluter till eller inom adressintervallet för din VPNClientAddressPool, kallas detta för ett överlappande adressutrymme. När ditt adressutrymme överlappar på det här sättet når inte nätverkstrafiken Azure, utan stannar i det lokala nätverket.

## <a name="faq"></a><a name="faq"></a>Vanliga frågor

Detta vanliga frågor och svar gäller för P2S med RADIUS-autentisering

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](../index.yml). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).
