---
title: 'Ansluta en dator till ett virtuellt nätverk med P2S: certifikatautentisering: Azure Portal klassisk'
titleSuffix: Azure VPN Gateway
description: Skapa en klassisk VPN Gateway-anslutning från punkt-till-plats med hjälp av Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94657082"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Konfigurera en punkt-till-plats-anslutning med hjälp av certifikatautentisering (klassisk)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Den här artikeln beskriver hur du skapar ett virtuellt nätverk med en punkt-till-plats-anslutning. Du skapar detta VNet med den klassiska distributions modellen med hjälp av Azure Portal. Den här konfigurationen använder certifikat för att autentisera den anslutande klienten, antingen självsignerade eller från en certifikatutfärdare. Du kan också skapa konfigurationen med ett annat distributionsverktyg eller en annan distributionsmodell med hjälp av alternativen som beskrivs i följande artiklar:

> [!div class="op_single_selector"]
> * [Azure-portalen](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (klassisk)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Du använder en VPN-gateway med P2S-konfiguration (punkt-till-plats) för att skapa en säker anslutning till ditt virtuella nätverk från en enskild klientdator. P2S-VPN-anslutningar är användbara när du vill ansluta till ditt virtuella nätverk från en fjärransluten plats. P2S-VPN-anslutningar är en bra lösning att använda i stället för en plats-till-plats-VPN-anslutning när du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk. En P2S-VPN-anslutning upprättas genom att du startar den från klientdatorn.

> [!IMPORTANT]
> Den klassiska distributionsmodellen stöder endast Windows VPN-klienter och använder Secure Socket Tunneling Protocol (SSTP), ett SSL-baserat VPN-protokoll. För att ge stöd för icke-Windows-baserade VPN-klienter måste du skapa ditt virtuella nätverk med Resource Manager-distributionsmodellen. Resource Manager-distributionsmodellen stöder IKEv2 VPN, utöver SSTP. Mer information finns i [om P2S-anslutningar](point-to-site-about.md).
>
>

![Punkt-till-plats-diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="settings-and-requirements"></a>Inställningar och krav

### <a name="requirements"></a>Krav

Anslutningar för autentisering med punkt-till-plats-certifikat kräver följande objekt. Det finns steg i den här artikeln som hjälper dig att skapa dem.

* En dynamisk VPN gateway.
* Den offentliga nyckeln (CER-fil) för ett rotcertifikat, som överförts till Azure. Den här nyckeln anses vara ett betrott certifikat och används för autentisering.
* Ett klientcertifikat genereras från rotcertifikatet och installerad på varje klientdator som ska ansluta. Det här certifikatet används för klientautentisering.
* Ett konfigurationspaket för VPN-klienter måste skapas och installeras på varje klientdator som ansluter. Klientkonfigurationspaketet konfigurerar den interna VPN-klienten som redan finns i operativsystemet med den information som krävs för att ansluta till det virtuella nätverket.

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet eller en lokal offentlig IP-adress. VPN-anslutningen upprättas via SSTP (Secure Socket Tunneling Protocol). På serversidan stöder vi SSTP version 1.0, 1.1 och 1.2. Klienten avgör vilken version som ska användas. För Windows 8.1 och senare, använder SSTP version 1.2 som standard.

Mer information finns i [om punkt-till-plats-anslutningar](point-to-site-about.md) och [vanliga frågor och svar](#faq).

### <a name="example-settings"></a>Exempelinställningar

Använd följande värden för att skapa en testmiljö eller för att bättre förstå exemplen i den här artikeln:

* **Resurs grupp:** TestRG
* **Namn på virtuellt nätverk:** VNet1
* **Adress utrymme:** 192.168.0.0/16 <br>I det här exemplet använder vi bara ett adressutrymme. Du kan ha fler än ett adressutrymme för ditt virtuella nätverk.
* **Under näts namn:** Delen
* **Adress intervall för under nätet:** 192.168.1.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Region:** (USA) USA, östra
* **Klient adress utrymme:** 172.16.201.0/24 <br> VPN-klienter som ansluter till VNet med den här punkt-till-plats-anslutningen får en IP-adress från den angivna poolen.
* **Anslutnings typ**: Välj **punkt-till-plats**.
* **GatewaySubnet-adress intervall (CIDR-block):** 192.168.200.0/24

Innan du börjar kontrollerar du att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Skapa ett virtuellt nätverk

Om du redan har ett VNet, kontrollerar du att inställningarna är kompatibla med din VPN-gatewaydesign. Var särskilt noga med alla undernät som överlappar med andra nätverk.

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>Skapa en VPN-gateway

1. Navigera till det virtuella nätverk som du skapade.
1. På sidan VNet under Inställningar väljer du **Gateway**. På sidan **Gateway** kan du Visa gatewayen för det virtuella nätverket. Det här virtuella nätverket har ännu ingen gateway. Klicka på anteckningen där **du vill lägga till en anslutning och en gateway**.
1. På sidan **Konfigurera en VPN-anslutning och gateway** väljer du följande inställningar:

   * Anslutningstyp: Punkt-till-plats
   * Klient adress utrymme: Lägg till det IP-adressintervall från vilket VPN-klienterna får en IP-adress vid anslutning. Använd ett intervall för privata IP-adresser som inte överlappar med den lokala platsen som du ansluter från, eller med det virtuella nätverk som du ansluter till.
1. Lämna kryss rutan för **Konfigurera inte en gateway vid den här tidpunkten** avmarkerad. Vi kommer att skapa en gateway.
1. Längst ned på sidan väljer du **Nästa: Gateway->**.
1. På fliken **Gateway** väljer du följande värden:

   * **Storlek:** Storleken är Gateway-SKU: n för den virtuella Nätverksgatewayen. Standard-SKU på Azure Portal är **Standard**. Mer information om gateway-SKU:er finns i [Om VPN Gateway-inställningar](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Typ av dirigering:** Du måste välja **dynamisk** för en punkt-till-plats-konfiguration. Statisk routning kommer inte att fungera.
   * **Gateway-undernät:** Det här fältet är redan ifyllt. Du kan inte ändra namnet. Om du försöker ändra namnet med hjälp av PowerShell eller något annat sätt fungerar inte gatewayen som den ska.
   * **Adress intervall (CIDR-block):** Även om det är möjligt att skapa ett Gateway-undernät så litet som/29, rekommenderar vi att du skapar ett större undernät som innehåller fler adresser genom att välja minst/28 eller/27. På så sätt finns det tillräckligt med adresser för eventuella konfigurationer som du kan behöva i framtiden. När du arbetar med gateway-undernät, bör du undvika att associera en nätverkssäkerhetsgrupp (NSG) till gateway-undernätet. Om du kopplar en nätverkssäkerhetsgrupp till det här undernätet kan det ledat till att din VPN-gateway inte fungerar som förväntat.
1. Välj **Granska + skapa** för att validera inställningarna.
1. När verifieringen har godkänts väljer du **skapa**. Det kan ta upp till 45 minuter innan en VPN-gateway är klar, beroende på vilken gateway-SKU du väljer.

## <a name="create-certificates"></a><a name="generatecerts"></a>Skapa certifikat

Azure använder certifikat för att autentisera VPN-klienter för punkt-till-plats-VPN. Du överför informationen om den offentliga nyckeln för rotcertifikatet till Azure. Den offentliga nyckeln anses sedan vara *betrodd*. Klientcertifikat måste skapas från det betrodda rotcertifikatet och sedan installeras på varje dator i certifikatarkivet Certifikat – aktuell användare\Personlig\Certifikat. Certifikatet används för att autentisera klienten när den ansluter till det virtuella nätverket. 

Om du använder självsignerade certifikat måste de skapas med specifika parametrar. Du kan skapa ett självsignerat certifikat med hjälp av anvisningarna för [PowerShell och Windows 10](vpn-gateway-certificates-point-to-site.md) eller [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Det är viktigt att du följer stegen i anvisningarna när du använder självsignerade rotcertifikat och genererar klientcertifikat från det självsignerade rotcertifikatet. Annars kommer dina certifikat inte att vara kompatibla med P2S-anslutningar och du får ett anslutningsfel.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Hämta den offentliga nyckeln (.cer) för rotcertifikatet

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Generera ett klientcertifikat

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Ladda upp .cer-filen med rotcertifikatet

När gatewayen har skapats laddar du upp CER-filen (som innehåller informationen om den offentliga nyckeln) för ett betrott rotcertifikat till Azure-servern. Ladda inte upp den privata nyckeln för rotcertifikatet. När du har laddat upp certifikatet använder Azure det för att autentisera klienter som har installerat ett klientcertifikat som har genererats från det betrodda rotcertifikatet. Du kan ladda upp ytterligare betrodda rotcertifikatfiler (upp till 20) senare om det behövs.

1. Navigera till det virtuella nätverk som du har skapat.
1. Under **Inställningar** väljer **du punkt-till-plats-anslutningar**.
1. Välj **Hantera certifikat**.
1. Välj **Överför**.
1. I fönstret **Ladda upp ett certifikat** väljer du mappikonen och navigerar till det certifikat som du vill ladda upp.
1. Välj **Överför**.
1. När certifikatet har laddats upp kan du se det på sidan Hantera certifikat. Du kan behöva välja **Uppdatera** för att visa det certifikat som du just har laddat upp.

## <a name="configure-the-client"></a>Konfigurera klienten

För att ansluta till ett virtuellt nätverk med hjälp av ett VPN för punkt-till-plats måste varje klient installera ett paket för konfiguration av den interna Windows VPN-klienten. Konfigurationspaketet ställer in den interna Windows VPN-klienten med de inställningar som krävs för att ansluta till det virtuella nätverket.

Du kan använda samma VPN-klientkonfigurationspaket på varje klientdator, förutsatt att versionen matchar arkitekturen för klienten. En lista över klient operativ system som stöds finns i [om punkt-till-plats-anslutningar](point-to-site-about.md) och [vanliga frågor och svar](#faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Skapa och installera ett VPN-klientkonfigurationspaket

1. Navigera till inställningarna för **punkt-till-plats-anslutningar** för ditt VNet.
1. Överst på sidan väljer du det hämtnings paket som motsvarar det klient operativ system där det ska installeras:

   * För 64-bitars klienter väljer du **VPN-klient (64-bitars)**.
   * För 32-bitars klienter väljer du **VPN-klient (32-bitars)**.

1. Azure skapar ett paket med de angivna inställningar som klienten behöver. Varje gången du gör ändringar i VNet eller gatewayen måste du hämta ett nytt klient konfigurations paket och installera dem på klient datorerna.
1. När paketet har genererats väljer du **Hämta**.
1. Installera klient konfigurations paketet på klient datorn. Om du ser ett SmartScreen-fönster när du installerar, väljer du **Mer information** och väljer **kör ändå** om du ser ett SmartScreen-fönster. Du kan också spara paketet om du vill installera det på andra klientdatorer.

### <a name="install-a-client-certificate"></a>Installera ett klientcertifikat

I den här övningen installerades det automatiskt på datorn när du skapade klient certifikatet. Om du vill skapa en P2S-anslutning från en annan klient dator än den som används för att generera klient certifikaten måste du installera det genererade klient certifikatet på den datorn.

När du installerar ett klientcertifikat behöver du lösenordet som skapades när klientcertifikatet exporterades. Normalt kan du installera certifikatet genom att bara dubbelklicka på det. Mer information finns i [Installera ett exporterat klientcertifikat](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect-to-your-vnet"></a>Anslut till ditt VNet

>[!NOTE]
>Du måste ha administratörsbehörigheter på den klientdator som du använder för att ansluta.
>

1. Gå till VPN-inställningar på klient datorn.
1. Välj det VPN som du har skapat. Om du använde exempel inställningarna kommer anslutningen att vara märkt med **grupp TestRG VNet1**.
1. Välj **Anslut**.
1. I rutan Windows Azure Virtual Network väljer du **Anslut**. Om ett popup-meddelande om certifikatet visas väljer du **Fortsätt** för att använda utökade privilegier och **Ja** för att acceptera konfigurations ändringar.
1. När anslutningen lyckas visas ett **anslutet** meddelande.

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Verifiera VPN-anslutningen

1. Kontrollera att VPN-anslutningen är aktiv. Öppna en upphöjd kommandotolk på klientdatorn och kör **ipconfig/all**.
1. Granska resultaten. Observera att den IP-adress som du har fått är en av adresserna inom adressintervallet för punkt-till-plats-anslutningen som du angav när du skapade ditt VNet. Resultatet bör likna det här exemplet:

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a>Ansluta till en virtuell dator

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>Så här lägger du till eller tar bort betrodda rotcertifikat

Du kan lägga till och ta bort betrodda rotcertifikat från Azure. När du tar bort ett rotcertifikat kan klienter som har ett certifikat som genererats från det rotcertifikatet inte längre autentisera och ansluta. För att dessa klienter ska kunna autentisera och ansluta igen måste du installera ett nytt klientcertifikat som genereras från ett rotcertifikat som är betrott av Azure.

### <a name="add-a-trusted-root-certificate"></a>Lägga till ett betrott rotcertifikat

Du kan lägga till upp till 20 betrodda rot certifikat. CER-filer i Azure genom att använda samma process som du använde för att lägga till det första betrodda rot certifikatet.

### <a name="remove-a-trusted-root-certificate"></a>Ta bort ett betrott rot certifikat

1. I avsnittet **punkt-till-plats-anslutningar** på sidan för ditt VNet väljer du **Hantera certifikat**.
1. Välj ellipsen bredvid det certifikat som du vill ta bort och välj sedan **ta bort**.

## <a name="to-revoke-a-client-certificate"></a>Återkalla ett klientcertifikat

Om det behövs kan du återkalla ett klientcertifikat. Du kan använda listan över återkallade certifikat för att selektivt neka punkt-till-plats-anslutningar baserat på enskilda klientcertifikat. Den här metoden skiljer sig från hur du tar bort ett betrott rotcertifikat. Om du tar bort CER-filen för ett betrott rotcertifikat i Azure återkallas åtkomsten för alla klientcertifikat som genererats/signerats med det återkallade rotcertifikatet. När du återkallar ett klientcertifikat snarare än rotcertifikatet så kan de andra certifikat som har skapats med rotcertifikatet fortfarande användas för autentisering av P2S-anslutningen.

Den vanligaste metoden är att använda rotcertifikatet för att hantera åtkomst på grupp- eller organisationsnivå, och att återkalla klientcertifikat för mer detaljerad åtkomstkontroll för enskilda användare.

Du kan återkalla ett klientcertifikat genom att lägga till tumavtrycket i listan över återkallade certifikat.

1. Hämta klientcertifikatets tumavtryck. Mer information finns i [How to: Hämta tumavtrycket för ett certifikat](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Kopiera informationen till en textredigerare och ta bort alla blanksteg så att strängen är i ett stycke.
1. Navigera till **punkt-till-plats-VPN-anslutning** och välj sedan **Hantera certifikat**.
1. Öppna sidan **Lista över återkallade certifikat** genom att välja **Lista över återkallade certifikat**.
1. I **Tumavtryck** klistrar du in certifikattumavtrycket som en kontinuerlig textrad, utan blanksteg.
1. Välj **+ Lägg till i listan** om du vill lägga till tumavtrycket i listan över återkallade certifikat (CRL).

När uppdateringen har slutförts kan certifikatet inte längre användas för att ansluta. Klienter som försöker ansluta med det här certifikatet får ett meddelande om att certifikatet inte längre är giltigt.

## <a name="faq"></a><a name="faq"></a>Vanliga frågor

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Nästa steg

* När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i [Virtuella datorer](../index.yml).

* Mer information om nätverk och virtuella Linux-datorer finns i [Azure and Linux VM network overview](../virtual-machines/network-overview.md) (Översikt över nätverk för virtuella Azure- och Linux-datorer).

* Information om P2S-felsökning finns i [Felsöka punkt-till-plats-anslutningar i Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).