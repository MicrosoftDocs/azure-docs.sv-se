---
title: 'Ansluta till ett VNet från en P2S VPN och intern Azure-certifikatautentisering: PowerShell'
description: Anslut Windows-och macOS-klienter på ett säkert sätt till Azure Virtual Network med P2S och självsignerade eller CA-utfärdade certifikat. I den här artikeln används PowerShell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: ed0a60c88c33af70b7d780d6c4735c5f8e65b35b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660414"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Konfigurera en punkt-till-plats-VPN-anslutning till ett VNet med intern Azure-certifikatautentisering: PowerShell

Den här artikeln hjälper dig att på ett säkert sätt ansluta enskilda klienter som kör Windows, Linux eller macOS till ett Azure VNet. Punkt-till-plats-VPN-anslutningar är användbara när du vill ansluta till ditt VNet från en annan plats, till exempel när du arbetar hemifrån eller en konferens. Du kan också använda P2S i stället för en plats-till-plats-VPN-anslutning när du bara har ett fåtal klienter som behöver ansluta till ett VNet. Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. P2S skapar VPN-anslutningen via SSTP (Secure Socket Tunneling Protocol) eller IKEv2.

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="Ansluta från en dator till ett Azure VNet-punkt-till-plats-anslutnings diagram":::

Mer information om punkt-till-plats-VPN finns i [om punkt-till-plats-VPN](point-to-site-about.md). Om du vill skapa den här konfigurationen med hjälp av Azure Portal, se [Konfigurera en punkt-till-plats-VPN med hjälp av Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> Många av stegen i den här artikeln kan använda Azure Cloud Shell. Du kan dock inte använda Cloud Shell för att skapa certifikat. Om du vill ladda upp rot certifikatets offentliga nyckel måste du antingen använda Azure PowerShell lokalt eller Azure Portal.
>

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1. Logga in

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. deklarera variabler

Vi använder variabler för den här artikeln så att du enkelt kan ändra värdena så att de tillämpas på din egen miljö utan att behöva ändra själva exemplen. Deklarera de variabler som du vill använda. Du kan använda följande exempel och ersätta värdena efter behov. Om du stänger PowerShell/Cloud Shell-sessionen när som helst under övningen ska du bara kopiera och klistra in värdena igen för att omdeklarera variablerna.

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. Konfigurera ett VNet

1. Skapa en resursgrupp.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. Skapa under näts konfigurationerna för det virtuella nätverket och namnge dem som *FrontEnd* -och *GatewaySubnet*. Dessa prefix måste vara en del av VNet-adressutrymmet som du deklarerade.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. Skapa det virtuella nätverket.

   I det här exemplet är serverparametern -DnsServer valfri. Ingen ny DNS-server skapas när du anger ett värde. IP-adressen för DNS-servern som du anger måste vara en DNS-server som kan matcha namnen för de resurser som du ansluter till från ditt virtuella nätverk. Det här exemplet använder en privat IP-adress, men förmodligen inte IP-adressen till din DNS-server. Använd dina egna värden. Värdet som du anger används av de resurser som du distribuerar till det virtuella nätverket, inte av P2S-anslutningen eller VPN-klienten.

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. Ange variablerna för det virtuella nätverket som du skapade.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. En VPN-gateway måste ha en offentlig IP-adress. Först begär du IP-adressresursen och sedan hänvisar du till den när du skapar din virtuella nätverksgateway. IP-adressen tilldelas dynamiskt till resursen när en VPN-gateway har skapats. VPN Gateway stöder för närvarande endast *dynamisk* offentlig IP-adressallokering. Du kan inte begära en statisk offentlig IP-adresstilldelning. Det innebär emellertid inte att IP-adressen ändras när den har tilldelats till din VPN-gateway. Den enda gången den offentliga IP-adressen ändras är när gatewayen tas bort och återskapas. Den ändras inte vid storleksändring, återställning eller annat internt underhåll/uppgraderingar av din VPN-gateway.

   Begär en dynamiskt tilldelad offentlig IP-adress.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. Skapa VPN-gatewayen

I det här steget konfigurerar och skapar du den virtuella Nätverksgatewayen för ditt VNet.

* -GatewayType måste vara **Vpn** och -VpnType måste vara **RouteBased**.
* -VpnClientProtocol används för att ange vilka typer av tunnlar som ska aktiveras. Tunnel alternativen är **OpenVPN, SSTP** och **IKEv2**. Du kan välja att aktivera en av dem eller en kombination som stöds. Om du vill aktivera flera typer anger du namnen avgränsade med kommatecken. Det går inte att aktivera OpenVPN och SSTP tillsammans. StrongSwan-klienten i Android och Linux och den inbyggda IKEv2 VPN-klienten i iOS och OSX använder endast IKEv2-tunnlar för att ansluta. Windows-klienter provar IKEv2 först, och går över till SSTP om det inte går att ansluta. Du kan använda OpenVPN-klienten för att ansluta till OpenVPN-tunnel typen.
* Den virtuella Nätverksgatewayen ' Basic '-SKU: n stöder inte IKEv2, OpenVPN eller RADIUS-autentisering. Om du planerar att låta Mac-klienter ansluta till ditt virtuella nätverk ska du inte använda den grundläggande SKU: n.
* Det kan ta upp till 45 minuter innan en VPN-gateway är klar, beroende på vilken [gateway-sku](vpn-gateway-about-vpn-gateway-settings.md) du väljer. I det här exemplet används IKEv2.

1. Konfigurera och skapa VNet-gatewayen för ditt VNet. Det tar cirka 45 minuter för gatewayen att skapas.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. När din gateway har skapats kan du Visa den med hjälp av följande exempel. Om du har stängt PowerShell eller tids gränsen nåddes när gatewayen skapades, kan du [deklarera dina variabler](#declare) igen.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. Lägg till VPN-klientens adresspool

När VPN-gatewayen har skapats kan du lägga till VPN-klientadresspoolen. VPN-klientens adresspool är det adressintervall som VPN-klienterna får en IP-adress från när de ansluter. Använd ett intervall för privata IP-adresser som inte överlappar med den lokala platsen som du ansluter från, eller med det virtuella nätverk som du vill ansluta till.

I det här exemplet deklareras VPN-klientens adresspool som en [variabel](#declare) i ett tidigare steg.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. generera certifikat

>[!IMPORTANT]
> Du kan inte skapa certifikat med Azure Cloud Shell. Du måste använda en av de metoder som beskrivs i det här avsnittet. Om du vill använda PowerShell måste du installera den lokalt.
>

Certifikat används av Azure för att autentisera VPN-klienter för punkt-till-plats-VPN. Du överför informationen om den offentliga nyckeln för rotcertifikatet till Azure. Den offentliga nyckeln anses sedan vara ”betrodd”. Klientcertifikat måste skapas från det betrodda rotcertifikatet och sedan installeras på varje dator i certifikatarkivet Certifikat – aktuell användare/Personlig. Certifikatet används för att autentisera klienten när den upprättar en anslutning till det virtuella nätverket. 

Om du använder självsignerade certifikat, måste de skapas med specifika parametrar. Du kan skapa ett självsignerat certifikat med hjälp av anvisningarna för [PowerShell och Windows 10](vpn-gateway-certificates-point-to-site.md). Om du inte har Windows 10 kan du använda [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Det är viktigt att du följer stegen i anvisningarna när du skapar självsignerade rotcertifikat och klientcertifikat. Annars kommer de certifikat som du genererar inte att vara kompatibla med P2S-anslutningar och du får ett anslutningsfel.

### <a name="root-certificate"></a><a name="cer"></a>Rot certifikat

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. När du har skapat rot certifikatet [exporterar](vpn-gateway-certificates-point-to-site.md#cer) du offentliga certifikat data (inte den privata nyckeln) som en Base64-kodad X. 509. cer-fil.

### <a name="client-certificate"></a><a name="generate"></a>Klient certifikat

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. När du har skapat ett klient certifikat [exporterar](vpn-gateway-certificates-point-to-site.md#clientexport) du det. Klient certifikatet kommer att distribueras till de klient datorer som ska ansluta.

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. Ladda upp information om den offentliga nyckeln för rot certifikatet

Kontrollera att din VPN-gateway har skapats. När det är klart kan du ladda upp CER-filen (som innehåller informationen om den offentliga nyckeln) för ett betrott rotcertifikat till Azure. När en CER-fil har överförts kan Azure använda den för att autentisera klienter som har ett installerat klientcertifikat som har genererats från det betrodda rotcertifikatet. Du kan överföra ytterligare betrodda rotcertifikatfiler (upp till 20 stycken) senare om det behövs.

>[!NOTE]
> Du kan inte ladda upp CER-filen med Azure Cloud Shell. Du kan antingen använda PowerShell lokalt på datorn eller så kan du använda [Azure Portal stegen](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
>

1. Deklarera variabeln för certifikatnamnet och ersätt värdet med ditt eget.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. Ersätt sökvägen till filen med din egen och kör cmdlet:arna.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Ladda upp informationen om den offentliga nyckeln till Azure. När certifikat informationen har laddats upp anser Azure att den är ett betrott rot certifikat. När du laddar upp kontrollerar du att du kör PowerShell lokalt på datorn eller i stället kan du använda [Azure Portal stegen](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Du kan inte ladda upp med Azure Cloud Shell.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. Installera ett exporterat klient certifikat

Följande steg hjälper dig att installera på en Windows-klient. Ytterligare klienter och mer information finns i [Installera ett klient certifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

Kontrollera att klientcertifikatet har exporterats som PFX-fil tillsammans med hela certifikatkedjan (standardinställningen). I annat fall finns inte rotcertifikatuppgifterna på klientdatorn och klienten kan inte autentiseras.

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. Konfigurera VPN-klienten

I det här avsnittet konfigurerar du den interna klienten för datorn så att den ansluter till den virtuella Nätverksgatewayen. När du till exempel går till VPN-inställningar på en Windows-dator kan du lägga till VPN-anslutningar. En punkt-till-plats-anslutning kräver vissa konfigurations inställningar. Dessa steg hjälper dig att skapa ett paket med de specifika inställningar som din interna VPN-klient måste kunna ansluta till det virtuella nätverket via en punkt-till-plats-anslutning.

Du kan använda följande snabb exempel för att skapa och installera klient konfigurations paketet. Mer information om paket innehåll och ytterligare instruktioner om hur du genererar och installerar VPN-konfigurationsfiler finns i [skapa och installera VPN-klientens konfigurationsfiler](point-to-site-vpn-client-configuration-azure-cert.md).

Om du behöver deklarera variablerna igen kan du hitta dem [här](#declare).

### <a name="to-generate-configuration-files"></a>Så här genererar du konfigurationsfiler

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>Installera klient konfigurations paketet

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. Anslut till Azure

### <a name="windows-vpn-client"></a>Windows VPN-klient

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Mac VPN-klient

Från dialogrutan Nätverk letar du upp den klientprofil som du vill använda och klickar sedan på **Anslut**.
Mer information finns i [install-Mac (OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) . Om du har problem med att ansluta kontrollerar du att den virtuella Nätverksgatewayen inte använder en grundläggande SKU. Basic SKU stöds inte för Mac-klienter.

  ![Mac-anslutning](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>Så här verifierar du en anslutning

Dessa anvisningar gäller för Windows-klienter.

1. Verifiera att VPN-anslutningen är aktiv genom att öppna en upphöjd kommandotolk och köra *ipconfig/all*.
2. Granska resultaten. Observera att IP-adressen som du fick är en av adresserna i den punkt-till-plats-VPN-klient som du angav i konfigurationen. Resultatet är ungefär som i det här exemplet:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Ansluta till en virtuell dator

Dessa anvisningar gäller för Windows-klienter.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Kontrollera att paketet för VPN-klientkonfiguration har skapats efter att IP-adresser för DNS-server har angetts för VNet. Om du uppdaterade IP-adresserna för DNS-servern skapar och installerar du ett nytt paket för VPN-klientkonfiguration.

* Använd ”ipconfig” för att kontrollera vilken IPv4-adress som har tilldelats till Ethernet-adaptern på den dator som du ansluter från. Om IP-adressen ligger inom adressintervallet för det virtuella nätverk som du ansluter till eller inom adressintervallet för din VPNClientAddressPool, kallas detta för ett överlappande adressutrymme. När ditt adressutrymme överlappar på det här sättet når inte nätverkstrafiken Azure, utan stannar i det lokala nätverket.

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Så här lägger du till eller tar bort ett rotcertifikat

Du kan lägga till och ta bort betrodda rotcertifikat från Azure. När du tar bort ett rotcertifikat kan klienter som har ett certifikat som genereras från rotcertifikatet inte autentisera och kommer inte att kunna ansluta. Om du vill att en klient ska kunna autentisera och ansluta måste du installera ett nytt klientcertifikat som genererats från ett rotcertifikat som är betrott (uppladdat) i Azure. Dessa steg kräver att Azure PowerShell-cmdlets installeras lokalt på datorn (inte Azure Cloud Shell). Du kan också använda Azure Portal för att lägga till rot certifikat.

**För att lägga till:**

Du kan lägga till upp till 20 CER-filer för rotcertifikat i Azure. Följande steg hjälper dig att lägga till ett rot certifikat. 

1. Förbered CER-filen för uppladdning:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Ladda upp filen. Du kan bara ladda upp en fil i taget.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. Kontrollera att certifikatfilen har laddats upp:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**Ta bort:**

1. Deklarera variablerna. Ändra variablerna i exemplet för att matcha det certifikat som du vill ta bort.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. Ta bort certifikatet.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. Använd följande exempel till att kontrollera att certifikatet togs bort ordentligt.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>Återkalla eller återställa ett klient certifikat

Du kan återkalla certifikat. Listan över återkallade certifikat gör att du selektivt kan neka punkt-till-plats-anslutning baserat på enskilda klient certifikat. Det här skiljer sig från att ta bort ett betrott rotcertifikat. Om du tar bort CER-filen för ett betrott rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats med det återkallade rotcertifikatet. När du återkallar ett klientcertifikat, snarare än rotcertifikatet, så kan de andra certifikat som har genererats från rotcertifikatet fortfarande användas för autentisering.

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

**Återkalla:**

1. Hämta klientcertifikatets tumavtryck. Mer information finns i [Gör så här: Hämta tumavtrycket för ett certifikat](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).

1. Kopiera informationen till en textredigerare och ta bort alla blanksteg så att strängen är i ett stycke. Den här strängen deklareras som en variabel i nästa steg.

1. Deklarera variablerna. Deklarera tumavtrycket du hämtade i föregående steg.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Lägg till tumavtrycket i listan med återkallade certifikat. När tumavtrycket har lagts till visas ett meddelande om att åtgärden lyckades.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. Kontrollera att tumavtrycket har lagts till i listan över återkallade certifikat.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. När tumavtrycket har lagts till kan certifikatet inte längre användas för anslutning. Klienter som försöker ansluta med det här certifikatet får ett meddelande om att certifikatet inte längre är giltigt.

**Återställa:**

Du kan återställa ett klientcertifikat genom att ta bort tumavtrycket från listan över återkallade klientcertifikat.

1. Deklarera variablerna. Se till att du deklarerar rätt tumavtryck för det certifikat du vill återställa.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Ta bort certifikatets tumavtryck från listan med återkallade certifikat.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. Kontrollera att tumavtrycket har tagits bort från listan över återkallade certifikat.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

Mer information om punkt-till-plats finns i [vanliga frågor och svar om VPN gateway punkt-till-plats](vpn-gateway-vpn-faq.md#P2S)

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](../index.yml). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).

Information om P2S-felsökning finns i [Felsökning: Problem med punkt-till-plats-anslutningar i Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).