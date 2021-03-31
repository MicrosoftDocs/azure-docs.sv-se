---
title: 'Azure VPN Gateway: skapa & installera VPN-P2S RADIUS-anslutningar'
description: Skapa konfigurationsfiler för Windows-, OS X-och Linux VPN-klienter för anslutningar som använder RADIUS-autentisering.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: e6d811e19bb19c8c8bf96764cfcca2b1294f4a85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91440059"
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication"></a>Skapa och installera konfigurationsfiler för VPN-klienten för P2S RADIUS-autentisering

Om du vill ansluta till ett virtuellt nätverk över punkt-till-plats (P2S) måste du konfigurera den klient enhet som du ansluter från. Du kan skapa P2S VPN-anslutningar från Windows-, OS X-och Linux-klient enheter. 

När du använder RADIUS-autentisering finns det flera autentiseringsalternativ: användar namn/lösen ord, certifikatautentisering och andra autentiseringstyper. Konfigurationen av VPN-klienten skiljer sig åt för varje typ av autentisering. Om du vill konfigurera VPN-klienten använder du konfigurationsfiler för klienter som innehåller de nödvändiga inställningarna. Den här artikeln hjälper dig att skapa och installera VPN-klientkonfiguration för den RADIUS-autentiseringstyp som du vill använda.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

Konfigurations arbets flödet för P2S RADIUS-autentisering är följande:

1. [Konfigurera Azure VPN-gatewayen för P2s-anslutning](point-to-site-how-to-radius-ps.md).
2. [Konfigurera RADIUS-servern för autentisering](point-to-site-how-to-radius-ps.md#radius). 
3. **Hämta VPN-klientkonfiguration för det autentiseringsalternativ du väljer och Använd den för att konfigurera VPN-klienten** (den här artikeln).
4. [Slutför din P2s-konfiguration och Anslut](point-to-site-how-to-radius-ps.md).

>[!IMPORTANT]
>Om det finns några ändringar i VPN-konfigurationen för punkt-till-plats efter att du har genererat konfigurations profilen för VPN-klienten, till exempel typen VPN-protokoll eller autentiseringstyp, måste du generera och installera en ny VPN-klientkonfiguration på användarnas enheter.
>
>

Om du vill använda avsnitten i den här artikeln bestämmer du först vilken typ av autentisering du vill använda: användar namn/lösen ord, certifikat eller andra typer av autentisering. I varje avsnitt finns det steg för Windows, OS X och Linux (begränsade steg som är tillgängliga just nu).


## <a name="usernamepassword-authentication"></a><a name="adeap"></a>Autentisering av användar namn/lösen ord

Du kan konfigurera autentisering av användar namn/lösen ord för att antingen använda Active Directory eller inte använda Active Directory. I båda fallen ser du till att alla anslutna användare har användar namn/lösen ord som kan autentiseras via RADIUS.

När du konfigurerar autentisering av användar namn/lösen ord kan du bara skapa en konfiguration för autentiseringsprotokollet EAP-MSCHAPv2 användar namn/lösen ord. I-kommandona `-AuthenticationMethod` är `EapMSChapv2` .

### <a name="1-generate-vpn-client-configuration-files"></a><a name="usernamefiles"></a> 1. generera konfigurationsfiler för VPN-klienten

Du kan generera VPN-klientens konfigurationsfiler genom att använda Azure Portal eller genom att använda Azure PowerShell.

#### <a name="azure-portal"></a>Azure Portal

1. Navigera till den virtuella Nätverksgatewayen.
2. Klicka på **punkt-till-plats-konfiguration**.
3. Klicka på **Ladda ned VPN-klient**.
4. Välj klienten och fyll i all information som begärs.
5. Klicka på **Hämta** för att generera. zip-filen.
6. Zip-filen laddas ned, vanligt vis till mappen Hämtade filer.

#### <a name="azure-powershell"></a>Azure PowerShell

Generera VPN-klientens konfigurationsfiler för användning med autentisering med användar namn/lösen ord. Du kan generera VPN-klientens konfigurationsfiler genom att använda följande kommando:

```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
Om du kör kommandot returneras en länk. Kopiera och klistra in länken i en webbläsare för att ladda ned **VpnClientConfiguration.zip**. Zippa upp filen om du vill visa följande mappar: 
 
* **WindowsAmd64** och **WindowsX86**: de här mapparna innehåller Windows 64-bitars-och 32-bitars installations paket. 
* **Allmän**: den här mappen innehåller allmän information som du använder för att skapa din egen VPN-klientkonfiguration. Du behöver inte den här mappen för konfiguration av användar namn/lösenordsautentisering.
* **Mac**: om du har konfigurerat IKEv2 när du skapade den virtuella Nätverksgatewayen visas en mapp med namnet **Mac** som innehåller en **mobileconfig** -fil. Du använder den här filen för att konfigurera Mac-klienter.

Om du redan har skapat konfigurationsfiler för klienter kan du hämta dem med hjälp av `Get-AzVpnClientConfiguration` cmdleten. Men om du gör ändringar i P2S VPN-konfigurationen, till exempel VPN-Protokollversionen eller autentiseringstypen, uppdateras inte konfigurationen automatiskt. Du måste köra `New-AzVpnClientConfiguration` cmdleten för att skapa en ny konfigurations nedladdning.

Om du vill hämta tidigare genererade konfigurationsfiler för klienter använder du följande kommando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```

### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. Konfigurera VPN-klienter

Du kan konfigurera följande VPN-klienter:

* [Windows](#adwincli)
* [Mac (OS X)](#admaccli)
* [Linux med strongSwan](#adlinuxcli)
 
#### <a name="windows-vpn-client-setup"></a><a name="adwincli"></a>Installation av Windows VPN-klient

Du kan använda samma konfigurations paket för VPN-klienten på varje Windows-klientdator, förutsatt att versionen överensstämmer med arkitekturen för-klienten. En lista över klient operativ system som stöds finns i [vanliga frågor och svar](vpn-gateway-vpn-faq.md#P2S).

Använd följande steg för att konfigurera den interna Windows VPN-klienten för certifikatautentisering:

1. Välj de VPN-klientkonfigurationsfiler som motsvarar Windows-datorns arkitektur. För en 64-bitars processor arkitektur väljer du installations paketet **VpnClientSetupAmd64** . För en 32-bitars processor arkitektur väljer du installations paketet **VpnClientSetupX86** . 
2. Dubbelklicka på det för att installera paketet. Om du ser en SmartScreen-pop-up väljer du **Mer information**  >  **kör ändå**.
3. På klient datorn bläddrar du till **nätverks inställningar** och väljer **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till. 

#### <a name="mac-os-x-vpn-client-setup"></a><a name="admaccli"></a>Installations program för Mac (OS X) VPN-klient

1. Välj filen **VpnClientSetup mobileconfig** och skicka den till var och en av användarna. Du kan använda e-post eller någon annan metod.

2. Leta upp **mobileconfig** -filen på Mac.

   ![Plats för mobileconfig-filen](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)

3. Valfritt steg – om du vill ange en anpassad DNS lägger du till följande rader i **mobileconfig** -filen:

   ```xml
    <key>DNS</key>
    <dict>
      <key>ServerAddresses</key>
        <array>
            <string>10.0.0.132</string>
        </array>
      <key>SupplementalMatchDomains</key>
        <array>
            <string>TestDomain.com</string>
        </array>
    </dict> 
   ```
4. Dubbelklicka på profilen för att installera den och välj **Fortsätt**. Profil namnet är detsamma som namnet på ditt virtuella nätverk.

   ![Installations meddelande](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
5. Välj **Fortsätt** att lita på avsändaren av profilen och fortsätt med installationen.

   ![Bekräftelsemeddelande](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
6. Under profil installationen har du möjlighet att ange användar namn och lösen ord för VPN-autentisering. Det är inte obligatoriskt att ange den här informationen. Om du gör det sparas informationen och används automatiskt när du startar en anslutning. Fortsätt genom att välja **Installera** .

   ![Rutorna användar namn och lösen ord för VPN](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
7. Ange ett användar namn och lösen ord för de behörigheter som krävs för att installera profilen på din dator. Välj **OK**.

   ![Rutorna användar namn och lösen ord för profil installation](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
8. När profilen har installerats visas den i dialog rutan **profiler** . Du kan också öppna den här dialog rutan senare från **Systeminställningar**.

   ![Dialog rutan profiler](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
9. För att få åtkomst till VPN-anslutningen öppnar du dialog rutan **nätverk** från **Systeminställningar**.

   ![Ikoner i Systeminställningar](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
10. VPN-anslutningen visas som **IkeV2-VPN**. Du kan ändra namnet genom att uppdatera **mobileconfig** -filen.

    ![Information om VPN-anslutningen](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
11. Välj **autentiseringsinställningar**. Välj **användar namn** i listan och ange dina autentiseringsuppgifter. Om du angav autentiseringsuppgifterna tidigare väljs **användar namn** automatiskt i listan och användar namn och lösen ord fylls i i förväg. Spara inställningarna genom att klicka på **OK** .

    ![Skärm bild som visar List rutan autentiseringsinställningar med "username" vald.](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
12. Tillbaka i dialog rutan **nätverk** väljer du **Verkställ** för att spara ändringarna. Om du vill initiera anslutningen väljer du **Anslut**.

#### <a name="linux-vpn-client-setup-through-strongswan"></a><a name="adlinuxcli"></a>Installation av Linux VPN-klienten via strongSwan

Följande instruktioner har skapats via strongSwan 5.5.1 på Ubuntu 17.0.4. De faktiska skärmarna kan vara olika beroende på din version av Linux och strongSwan.

1. Öppna **terminalen** för att installera **StrongSwan** och dess nätverks hanterare genom att köra kommandot i exemplet. Om du får ett fel som är relaterat till `libcharon-extra-plugins` ersätter du det med `strongswan-plugin-eap-mschapv2` .

   ```Terminal
   sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
   ```
2. Välj ikonen **Network Manager** (UPPIL/NEDPIL) och välj **Redigera anslutningar**.

   ![Alternativet "redigera anslutningar" i nätverks hanteraren](./media/point-to-site-vpn-client-configuration-radius/EditConnection.png)
3. Välj knappen **Lägg till** för att skapa en ny anslutning.

   ![Knappen Lägg till för en anslutning](./media/point-to-site-vpn-client-configuration-radius/AddConnection.png)
4. Välj **IPSec/IKEv2 (strongswan)** på den nedrullningsbara menyn och välj sedan **skapa**. Du kan byta namn på anslutningen i det här steget.

   ![Välja Anslutnings typ](./media/point-to-site-vpn-client-configuration-radius/AddIKEv2.png)
5. Öppna **VpnSettings.xml** -filen från den **allmänna** mappen i de hämtade klientens konfigurationsfiler. Hitta taggen som heter `VpnServer` och kopiera namnet, från och med `azuregateway` och slutar med `.cloudapp.net` .

   ![Innehåll i VpnSettings.xml-filen](./media/point-to-site-vpn-client-configuration-radius/VpnSettings.png)
6. Klistra in det här namnet i fältet **adress** i den nya VPN-anslutningen i **Gateway** -avsnittet. Sedan väljer du mappikonen i slutet av fältet **certifikat** , bläddrar till den **allmänna** mappen och väljer filen **VpnServerRoot** .
7. I avsnittet **klient** i anslutningen väljer du **EAP** för **autentisering** och anger ditt användar namn och lösen ord. Du kan behöva välja Lås ikonen till höger för att spara den här informationen. Välj sedan **Spara**.

   ![Redigera anslutnings inställningar](./media/point-to-site-vpn-client-configuration-radius/editconnectionsettings.png)
8. Välj **nätverks hanterarens** ikon (uppåt-nedåtpil/nedåtpil) och hovra över **VPN-anslutningar**. Du ser VPN-anslutningen som du skapade. För att initiera anslutningen väljer du den.

   ![Anslutning till "VPN RADIUS" i nätverks hanteraren](./media/point-to-site-vpn-client-configuration-radius/ConnectRADIUS.png)

## <a name="certificate-authentication"></a><a name="certeap"></a>Certifikatsautentisering
 
Du kan skapa VPN-klienters konfigurationsfiler för RADIUS-certifikatautentisering som använder EAP-TLS-protokollet. Normalt används ett Enterprise-utfärdat certifikat för att autentisera en användare för VPN. Se till att alla anslutna användare har ett certifikat installerat på sina enheter och att RADIUS-servern kan verifiera certifikatet.

>[!NOTE]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

I-kommandona `-AuthenticationMethod` är `EapTls` . Vid certifikatautentisering verifierar-klienten RADIUS-servern genom att verifiera dess certifikat. `-RadiusRootCert` är CER-filen som innehåller rot certifikatet som används för att verifiera RADIUS-servern.

Varje VPN-klienten het kräver ett installerat klient certifikat. Ibland har en Windows-enhet flera klient certifikat. Under autentiseringen kan det leda till att en popup-dialog ruta visas med en lista över alla certifikat. Användaren måste sedan välja det certifikat som ska användas. Rätt certifikat kan filtreras ut genom att ange det rot certifikat som klient certifikatet ska kedja till. 

`-ClientRootCert` är CER-filen som innehåller rot certifikatet. Det är en valfri parameter. Om den enhet som du vill ansluta från bara har ett klient certifikat behöver du inte ange den här parametern.

### <a name="1-generate-vpn-client-configuration-files"></a><a name="certfiles"></a>1. generera konfigurationsfiler för VPN-klienten

Generera VPN-klientens konfigurationsfiler för användning med certifikatautentisering. Du kan generera VPN-klientens konfigurationsfiler genom att använda följande kommando:
 
```azurepowershell-interactive
New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root> | fl
```

Om du kör kommandot returneras en länk. Kopiera och klistra in länken i en webbläsare för att ladda ned VpnClientConfiguration.zip. Zippa upp filen om du vill visa följande mappar:

* **WindowsAmd64** och **WindowsX86**: de här mapparna innehåller Windows 64-bitars-och 32-bitars installations paket. 
* **GenericDevice**: den här mappen innehåller allmän information som används för att skapa din egen konfiguration av VPN-klienten.

Om du redan har skapat konfigurationsfiler för klienter kan du hämta dem med hjälp av `Get-AzVpnClientConfiguration` cmdleten. Men om du gör ändringar i P2S VPN-konfigurationen, till exempel VPN-Protokollversionen eller autentiseringstypen, uppdateras inte konfigurationen automatiskt. Du måste köra `New-AzVpnClientConfiguration` cmdleten för att skapa en ny konfigurations nedladdning.

Om du vill hämta tidigare genererade konfigurationsfiler för klienter använder du följande kommando:

```azurepowershell-interactive
Get-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" | fl
```
 
### <a name="2-configure-vpn-clients"></a><a name="setupusername"></a> 2. Konfigurera VPN-klienter

Du kan konfigurera följande VPN-klienter:

* [Windows](#certwincli)
* [Mac (OS X)](#certmaccli)
* Linux (stöds, inga artikel steg ännu)

#### <a name="windows-vpn-client-setup"></a><a name="certwincli"></a>Installation av Windows VPN-klient

1. Välj ett konfigurations paket och installera det på klient enheten. För en 64-bitars processor arkitektur väljer du installations paketet **VpnClientSetupAmd64** . För en 32-bitars processor arkitektur väljer du installations paketet **VpnClientSetupX86** . Om du ser en SmartScreen-pop-up väljer du **Mer information**  >  **kör ändå**. Du kan också spara paketet om du vill installera det på andra klientdatorer.
2. Varje klient kräver ett klient certifikat för autentisering. Installera klient certifikatet. Information om klient certifikat finns i [klient certifikat för punkt-till-plats](vpn-gateway-certificates-point-to-site.md). Information om hur du installerar ett certifikat som har genererats finns i [Installera ett certifikat på Windows-klienter](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. På klient datorn bläddrar du till **nätverks inställningar** och väljer **VPN**. VPN-anslutningen visar namnet på det virtuella nätverk som den ansluter till.

#### <a name="mac-os-x-vpn-client-setup"></a><a name="certmaccli"></a>Installations program för Mac (OS X) VPN-klient

Du måste skapa en separat profil för varje Mac-enhet som ansluter till det virtuella Azure-nätverket. Detta beror på att de här enheterna kräver att användar certifikatet för autentisering anges i profilen. Den **allmänna** mappen innehåller all information som krävs för att skapa en profil:

* **VpnSettings.xml** innehåller viktiga inställningar som server adress och tunnel typ.
* **VpnServerRoot. cer** innehåller det rot certifikat som krävs för att verifiera VPN-gatewayen under installationen av P2s-anslutningen.
* **RadiusServerRoot. cer** innehåller rot certifikatet som krävs för att verifiera RADIUS-servern under autentisering.

Använd följande steg för att konfigurera den inbyggda VPN-klienten på en Mac för certifikatautentisering:

1. Importera **VpnServerRoot** -och **RadiusServerRoot** -rot certifikaten till din Mac. Kopiera varje fil till din Mac, dubbelklicka på den och välj sedan **Lägg till**.

   ![Lägga till VpnServerRoot-certifikatet](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

   ![Lägga till RadiusServerRoot-certifikatet](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. Varje klient kräver ett klient certifikat för autentisering. Installera klient certifikatet på klient enheten.
3. Öppna dialog rutan **nätverk** under **nätverks inställningar**. Välj **+** om du vill skapa en ny anslutnings profil för VPN-klienten för en P2s-anslutning till det virtuella Azure-nätverket.

   **Gränssnittets** värde är **VPN** och värdet för **VPN-typen** är **IKEv2**. Ange ett namn för profilen i rutan **tjänst namn** och välj sedan **skapa** för att skapa anslutnings profilen för VPN-klienten.

   ![Information om gränssnitt och tjänst namn](./media/point-to-site-vpn-client-configuration-radius/network.png)
4. I den **allmänna** mappen, från **VpnSettings.xml** -filen, kopierar du värdet för **VpnServer** -taggen. Klistra in det här värdet i rutorna **Server adress** och **fjärr-ID** i profilen. Lämna rutan **lokalt ID** tomt.

   ![Server information](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
5. Välj **autentiseringsinställningar** och välj **certifikat**. 

   ![Autentiseringsinställningar](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
6. Klicka på **Välj** för att välja det certifikat som du vill använda för autentisering.

   ![Välja ett certifikat för autentisering](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
7. **Välj en identitet** visar en lista över certifikat som du kan välja bland. Välj rätt certifikat och välj sedan **Fortsätt**.

   ![Listan Välj en identitet](./media/point-to-site-vpn-client-configuration-radius/identity.png)
8. I rutan **lokalt ID** anger du namnet på certifikatet (från steg 6). I det här exemplet är det **ikev2Client.com**. Välj sedan knappen **tillämpa** för att spara ändringarna.

   !["Lokalt ID"-fält](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
9. I dialog rutan **nätverk** väljer du **tillämpa** för att spara alla ändringar. Välj sedan **Anslut** för att starta P2s-anslutningen till det virtuella Azure-nätverket.

## <a name="working-with-other-authentication-types-or-protocols"></a><a name="otherauth"></a>Arbeta med andra autentiseringstyper eller protokoll

Om du vill använda en annan autentiseringstyp (till exempel eng ång slö sen ord), eller om du vill använda ett annat autentiseringsprotokoll (t. ex. PEAP-MSCHAPv2 i stället för EAP-MSCHAPv2), måste du skapa en egen konfigurations profil för VPN-klienten. Om du vill skapa profilen behöver du information, till exempel IP-adress, tunnel typ och delade tunnel vägar för virtuell nätverks-Gateway. Du kan hämta den här informationen med hjälp av följande steg:

1. Använd `Get-AzVpnClientConfiguration` cmdleten för att generera VPN-klientkonfiguration för EapMSChapv2.

2. Zippa upp VpnClientConfiguration.zip-filen och leta efter mappen **GenericDevice** . Ignorera mapparna som innehåller Windows installations program för 64-bitars-och 32-bitars arkitekturer.
 
3. Mappen **GenericDevice** innehåller en XML-fil med namnet **VpnSettings**. Den här filen innehåller all information som krävs:

   * **VpnServer**: FQDN för Azure VPN-gatewayen. Detta är den adress som klienten ansluter till.
   * **VpnType**: tunnel typ som du använder för att ansluta.
   * **Vägar**: vägar som du måste konfigurera i din profil så att endast trafik som är kopplad till det virtuella Azure-nätverket skickas via P2s-tunneln.
   
   Mappen **GenericDevice** innehåller också en. cer-fil med namnet **VpnServerRoot**. Den här filen innehåller det rot certifikat som krävs för att verifiera Azure VPN gateway under installationen av P2S-anslutningen. Installera certifikatet på alla enheter som ska ansluta till det virtuella Azure-nätverket.

## <a name="next-steps"></a>Nästa steg

Gå tillbaka till artikeln för att [slutföra din P2s-konfiguration](point-to-site-how-to-radius-ps.md).

Information om P2S-felsökning finns i [Felsöka Azure punkt-till-plats-anslutningar](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
