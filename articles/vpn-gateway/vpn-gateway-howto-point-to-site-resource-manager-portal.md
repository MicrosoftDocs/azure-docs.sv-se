---
title: 'Ansluta till ett virtuellt nätverk med P2S VPN-& certifikatautentisering: Portal'
titleSuffix: Azure VPN Gateway
description: Anslut Windows-, macOS-och Linux-klienter på ett säkert sätt till ett virtuellt Azure-nätverk med P2S och självsignerade eller CA-utfärdade certifikat. Den här artikeln använder Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 1c6dad28ada14151b9a1cca0da490e38972ad54d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379173"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Konfigurera en punkt-till-plats-VPN-anslutning till ett VNet med intern Azure-certifikatautentisering: Azure Portal

Den här artikeln hjälper dig att på ett säkert sätt ansluta enskilda klienter som kör Windows, Linux eller macOS till ett Azure VNet. Punkt-till-plats-VPN-anslutningar är användbara när du vill fjärransluta till ditt VNet, exempelvis när du distansarbetar från hemmet eller en konferens. Du kan också använda P2S i stället för en plats-till-plats-VPN-anslutning när du bara har ett fåtal klienter som behöver ansluta till ett VNet. Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en offentlig IP-adress. P2S skapar VPN-anslutningen via SSTP (Secure Socket Tunneling Protocol) eller IKEv2. Mer information om punkt-till-plats-VPN finns i [About Point-to-Site VPN](point-to-site-about.md) (Om VPN för punkt-till-plats).

:::image type="content" source="./media\vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Ansluta från en dator till ett Azure VNet-punkt-till-plats-anslutnings diagram":::

Mer information om punkt-till-plats-VPN finns i [om punkt-till-plats-VPN](point-to-site-about.md). Om du vill skapa den här konfigurationen med hjälp av Azure PowerShell, se [Konfigurera en punkt-till-plats-VPN med Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

### <a name="example-values"></a><a name="example"></a>Exempelvärden

Du kan använda följande värden till att skapa en testmiljö eller hänvisa till dem för att bättre förstå exemplen i den här artikeln.

* **Namn på virtuellt nätverk:** VNet1
* **Adress utrymme:** 10.1.0.0/16<br>I det här exemplet använder vi bara ett adressutrymme. Du kan ha fler än ett adressutrymme för ditt virtuella nätverk.
* **Under näts namn:** Delen
* **Adress intervall för under nätet:** 10.1.0.0/24
* **Prenumeration:** Kontrollera att du använder rätt prenumeration om du har mer än en.
* **Resursgrupp:** TestRG1
* **Plats:** USA, östra
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Namn på virtuell nätverksgateway:** VNet1GW
* **Gateway-typ:** Konfigurera
* **VPN-typ:** Route-baserad
* **Namn på offentlig IP-adress:** VNet1GWpip
* **Anslutnings typ:** Punkt-till-plats
* **Klient-adresspool:** 172.16.201.0/24<br>VPN-klienter som ansluter till det virtuella nätverket med den här punkt-till-plats-anslutningen får en IP-adress från klientadresspoolen.

## <a name="virtual-network"></a><a name="createvnet"></a>Virtuellt nätverk

I det här avsnittet skapar du ett virtuellt nätverk.

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Virtuell nätverksgateway

I det här steget ska du skapa den virtuella nätverksgatewayen för ditt virtuella nätverk. Att skapa en gateway kan ofta ta 45 minuter eller mer, beroende på vald gateway-SKU.

>[!NOTE]
>Basic Gateway-SKU: n stöder inte IKEv2-eller RADIUS-autentisering. Om du planerar att låta Mac-klienter ansluta till ditt virtuella nätverk ska du inte använda den grundläggande SKU: n.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generate-certificates"></a><a name="generatecert"></a>Generera certifikat

Certifikat används av Azure för att autentisera klienter som ansluter till ett virtuella nätverk över en VPN-anslutning från punkt till plats. När du har hämtat ett rotcertifikat [laddar du upp](#uploadfile) information om den offentliga nyckeln till Azure. Rotcertifikatet betraktas av Azure som betrott för punkt till plats-anslutning till det virtuella nätverket. Du skapar också klientcertifikat från det betrodda rotcertifikatet och installerar dem sedan på varje klientdator. Klientcertifikatet används för att autentisera klienten när den påbörjar en anslutning till det virtuella nätverket.

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>Generera ett rot certifikat

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>Generera klient certifikat

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Klientadresspool

Klientens adresspool är ett intervall med privata IP-adresser som du anger. Klienterna som ansluter via en VPN-anslutning dynamiskt från punkt till plats får en IP-adress från det här intervallet. Använd ett intervall för privata IP-adresser som inte överlappar med den lokala platsen som du ansluter från, eller med det virtuella nätverk som du vill ansluta till. Om du konfigurerar flera protokoll och SSTP är ett av protokollen, delas den konfigurerade adresspoolen upp mellan de konfigurerade protokollen på samma sätt.

1. När den virtuella nätverksgatewayen har skapats går du till avsnittet **Inställningar** på sidan för den virtuella nätverksgatewayen. I **Inställningar** väljer **du punkt-till-plats-konfiguration**. Välj **Konfigurera nu** för att öppna konfigurations sidan.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Sidan punkt-till-plats-konfiguration" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. På sidan **punkt-till-plats-konfiguration** , i rutan **adresspool** , lägger du till det privata IP-adressintervall som du vill använda. VPN-klienter tar dynamiskt emot en IP-adress från intervallet som du anger. Den minsta under nät masken är 29 bitar för aktiv/passiv och 28-bit för aktiv/aktiv-konfiguration.
1. Fortsätt till nästa avsnitt för att konfigurera autentiserings-och tunnel typer.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Autentiserings-och tunnel typer

I det här avsnittet konfigurerar du autentiseringstyp och tunnel typ. På sidan **punkt-till-plats-konfiguration** , om du inte ser **tunnel typ** eller **Autentiseringstyp**, använder din gateway Basic SKU. Bas-SKU:n stöder inte IKEv2- eller RADIUS-autentisering. Om du vill använda de här inställningarna måste du ta bort och återskapa gatewayen med hjälp av en annan gateway-SKU.

### <a name="tunnel-type"></a><a name="tunneltype"></a>Tunneltyp

På sidan **punkt-till-plats-konfiguration** väljer du tunnel typ. Tunnel alternativen är OpenVPN, SSTP och IKEv2.

* StrongSwan-klienten i Android och Linux och den inbyggda IKEv2 VPN-klienten i iOS och OSX använder endast IKEv2-tunnlar för att ansluta.
* Windows-klienterna försöker med IKEv2 först och om det inte ansluter, så är de tillbaka till SSTP.
* Du kan använda OpenVPN-klienten för att ansluta till tunnel typen OpenVPN.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Autentiseringstyp

För **Autentiseringstyp** väljer du **Azure-certifikat**.


## <a name="root-certificate-data"></a><a name="uploadfile"></a>Rot certifikat data

I det här avsnittet kan du överföra offentliga rot certifikat data till Azure. När informationen har laddats upp kan Azure använda den för att autentisera klienter som har ett installerat klientcertifikat skapat från det betrodda rotcertifikatet.

1. Certifikat läggs till på sidan **Punkt-till-plats-konfiguration** i avsnittet **Rotcertifikat**.
1. Kontrollera att du har exporterat rotcertifikatet som en Base64-kodad X.509-fil (.cer). Du behöver exportera certifikatet i det här formatet så att du kan öppna certifikatet med textredigerare.
1. Öppna certifikatet med en textredigerare, till exempel Anteckningar. När du kopierar certifikatdata ska du se till att kopiera texten som en kontinuerlig rad utan vagnreturer och radmatningar. Du kan behöva ändra vyn i textredigeraren till ”Visa symbol/Visa alla tecken” så att vagnreturer och radmatningar visas. Kopiera enbart följande avsnitt som en kontinuerlig rad:

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="Certifikatdata" border="false":::
1. Klistra in certifikatdata i fältet **Offentliga certifikatdata**. **Namnge** certifikatet och välj sedan **Spara**. Du kan lägga till upp till 20 betrodda rotcertifikat.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png" alt-text="Klistra in certifikat data" border="false":::
1. Välj **Spara** längst upp på sidan för att spara alla konfigurations inställningar.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png" alt-text="Spara konfiguration" border="false":::

## <a name="client-certificate"></a><a name="installclientcert"></a>Klient certifikat

Om du vill skapa en P2S-anslutning från en annan klientdator än den som du använde för att generera klientcertifikat, måste du installera ett klientcertifikat. När du installerar ett klientcertifikat behöver du lösenordet som skapades när klientcertifikatet exporterades.

Kontrollera att klientcertifikatet har exporterats som PFX-fil tillsammans med hela certifikatkedjan (standardinställningen). I annat fall finns inte rotcertifikatuppgifterna på klientdatorn och klienten kan inte autentiseras.

Installationsanvisningar finns i [Install a client certificate](point-to-site-how-to-vpn-client-install-azure-cert.md) (Installera ett klientcertifikat).

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>Konfigurations paket för VPN-klient

VPN-klienter måste konfigureras med klient konfigurations inställningar. Konfigurations paketet för VPN-klienten innehåller filer med inställningarna för att konfigurera VPN-klienter för att ansluta till ett VNet via en P2S-anslutning.

Anvisningar för hur du skapar och installerar konfigurationsfiler för VPN-klienter finns i [skapa och installera konfigurationsfiler för VPN-klienter för interna P2s-konfigurationer för Azure-certifikatautentisering](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect-to-azure"></a><a name="connect"></a>Anslut till Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Så här ansluter du från en Windows VPN-klient

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Så här ansluter du från en Mac VPN-klient

I dialog rutan nätverk letar du upp den klient profil som du vill använda, anger inställningarna från [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac)och väljer sedan **Anslut**.

Mer information finns i [install-Mac (OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) . Om du har problem med att ansluta kontrollerar du att den virtuella Nätverksgatewayen inte använder en grundläggande SKU. Basic SKU stöds inte för Mac-klienter.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png" alt-text="Anslutning till Mac VPN-klient" border="false":::

## <a name="to-verify-your-connection"></a><a name="verify"></a>Så här verifierar du anslutningen

Dessa anvisningar gäller för Windows-klienter.

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

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Ansluta till en virtuell dator

Dessa anvisningar gäller för Windows-klienter.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Kontrollera att paketet för VPN-klientkonfiguration har skapats efter att IP-adresser för DNS-server har angetts för VNet. Om du uppdaterade IP-adresserna för DNS-servern skapar och installerar du ett nytt paket för VPN-klientkonfiguration.

* Använd ”ipconfig” för att kontrollera vilken IPv4-adress som har tilldelats till Ethernet-adaptern på den dator som du ansluter från. Om IP-adressen ligger inom adressintervallet för det virtuella nätverk som du ansluter till eller inom adressintervallet för din VPNClientAddressPool, kallas detta för ett överlappande adressutrymme. När ditt adressutrymme överlappar på det här sättet når inte nätverkstrafiken Azure, utan stannar i det lokala nätverket.

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Så här lägger du till eller tar bort betrodda rotcertifikat

Du kan lägga till och ta bort betrodda rotcertifikat från Azure. När du tar bort ett rotcertifikat kommer klienter som har ett certifikat som genereras från den roten inte att kunna autentisera, och därmed kommer de inte heller att kunna ansluta. Om du vill att en klient ska kunna autentisera och ansluta måste du installera ett nytt klientcertifikat som genererats från ett rotcertifikat som är betrott (uppladdat) i Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Lägga till ett betrott rotcertifikat

Du kan lägga till upp till 20 betrodda CER-filer för rotcertifikat i Azure. Mer information finns i avsnittet [Ladda upp ett betrott rotcertifikat](#uploadfile) i den här artikeln.

### <a name="to-remove-a-trusted-root-certificate"></a>Så här tar du bort ett betrott rotcertifikat

1. Om du vill ta bort ett betrott rotcertifikat går du till sidan **Punkt-till-plats-konfiguration** för den virtuella nätverksgatewayen.
1. I avsnittet **Rotcertifikat** letar du upp det certifikat som du vill ta bort.
1. Välj ellipsen bredvid certifikatet och välj sedan ta bort.

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Återkalla ett klientcertifikat

Du kan återkalla certifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Det här skiljer sig från att ta bort ett betrott rotcertifikat. Om du tar bort CER-filen för ett betrott rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats med det återkallade rotcertifikatet. När du återkallar ett klientcertifikat, snarare än rotcertifikatet, så kan de andra certifikat som har genererats från rotcertifikatet fortfarande användas för autentisering.

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

### <a name="revoke-a-client-certificate"></a>Återkalla ett klientcertifikat

Du kan återkalla ett klientcertifikat genom att lägga till tumavtrycket i listan över återkallade certifikat.

1. Hämta klientcertifikatets tumavtryck. Mer information finns i [Gör så här: Hämta tumavtrycket för ett certifikat](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Kopiera informationen till en textredigerare och ta bort alla blanksteg så att strängen är i ett stycke.
1. Gå till sidan **Punkt-till-plats-konfiguration** för den virtuella nätverksgatewayen. Det här är den sida du använde när du [laddade upp ett betrott rotcertifikat](#uploadfile).
1. I avsnittet **Återkallade certifikat** anger du ett eget namn på certifikatet (det inte behöver vara certifikatets CN-namn).
1. Kopiera och klistra in tumavtryckssträngen i fältet **Tumavtryck** fält.
1. Tumavtrycket valideras och läggs automatiskt till i listan över återkallade certifikat. Ett meddelande om att listan uppdateras visas på skärmen. 
1. När uppdateringen har slutförts kan certifikatet inte längre användas för att ansluta. Klienter som försöker ansluta med det här certifikatet får ett meddelande om att certifikatet inte längre är giltigt.

## <a name="point-to-site-faq"></a><a name="faq"></a>Vanliga frågor och svar om punkt-till-plats

Det här avsnittet innehåller information om vanliga frågor och svar om punkt-till-plats-konfigurationer. Du kan också visa [VPN gateway vanliga frågor och svar](vpn-gateway-vpn-faq.md) om du vill ha mer information om VPN gateway.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Nästa steg
När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](../index.yml). Mer information om virtuella datorer och nätverk finns i [Azure and Linux VM network overview](../virtual-machines/network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).

Information om P2S-felsökning finns i [Felsöka punkt-till-plats-anslutningar i Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
