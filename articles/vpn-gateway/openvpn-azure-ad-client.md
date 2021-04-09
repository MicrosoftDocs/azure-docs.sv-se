---
title: 'VPN Gateway: VPN-klienten för P2S OpenVPN-protokoll anslutningar: Azure AD-autentisering'
description: Lär dig hur du konfigurerar en VPN-klient för att ansluta till ett virtuellt nätverk med hjälp av punkt-till-plats-VPN och Azure Active Directory autentisering.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: alzam
ms.openlocfilehash: 02ce8e1809c5dd404e7afa25178acf37e7346cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548418"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory autentisering: Konfigurera en VPN-klient för P2S OpenVPN-protokoll anslutningar

Den här artikeln hjälper dig att konfigurera en VPN-klient för att ansluta till ett virtuellt nätverk med hjälp av punkt-till-plats-VPN och Azure Active Directory autentisering. Innan du kan ansluta och autentisera med Azure AD måste du först konfigurera Azure AD-klienten. Mer information finns i [Konfigurera en Azure AD-klient](openvpn-azure-ad-tenant.md).

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="working-with-client-profiles"></a><a name="profile"></a>Arbeta med klient profiler

För att ansluta måste du ladda ned Azure VPN-klienten och konfigurera en VPN-klient profil på varje dator som vill ansluta till det virtuella nätverket. Du kan skapa en klient profil på en dator, exportera den och sedan importera den till ytterligare datorer.

### <a name="to-download-the-azure-vpn-client"></a>Ladda ned Azure VPN-klienten

Använd den här [länken](https://go.microsoft.com/fwlink/?linkid=2117554) för att ladda ned Azure VPN-klienten. Kontrol lera att Azure VPN-klienten har behörighet att köra i bakgrunden. Om du vill kontrol lera/aktivera behörigheten följer du stegen nedan:

1. Gå till start och välj sedan inställningar > sekretess > bakgrunds program.
2. Under huvudappar kontrollerar du att **Låt appar köras i bakgrunden** är aktiverat.
3. Under Välj vilka appar som kan köras i bakgrunden aktiverar du inställningarna för Azure VPN-klienten till **på**.

  ![permission](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Så här skapar du en certifikatbaserad klient profil

När du arbetar med en certifikatbaserad profil bör du kontrol lera att rätt certifikat är installerade på klient datorn. Mer information om certifikat finns i [Installera klient certifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![certifikatet](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Så här skapar du en RADIUS-klient profil

  ![RADIUS](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Server hemligheten kan exporteras i P2S VPN-klient profil.  Anvisningar om hur du exporterar en klient profil finns [här](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Så här exporterar och distribuerar du en klient profil

När du har en fungerande profil och behöver distribuera den till andra användare kan du exportera den med hjälp av följande steg:

1. Markera den VPN-klientkonfiguration som du vill exportera, Välj **...** och välj sedan **Exportera**.

    ![Skärm bild som visar sidan "Azure VPN-klient", där tre punkter har valts och "export" är markerat.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Välj den plats där du vill spara profilen, lämna fil namnet som det är och välj sedan **Spara** för att spara XML-filen.

    ![exportera](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Så här importerar du en klient profil

1. På sidan väljer du **Importera**.

    ![Skärm bild som visar knappen "Lägg till" markerad och åtgärden Importera har marker ATS i den nedre vänstra delen av fönstret.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Bläddra till profil-XML-filen och markera den. När filen är vald väljer du **Öppna**.

    ![Skärm bild som visar en profil x m l-fil vald.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Ange namnet på profilen och välj **Spara**.

    ![Skärm bild som visar "anslutnings namnet" markerat och knappen "Spara" är markerad.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Välj **Anslut** för att ansluta till VPN.

    ![Skärm bild som visar knappen VPN och "Anslut" vald.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. När du har anslutit ikonen blir den grön och säg **ansluten**.

    ![importera](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Ta bort en klient profil

1. Välj ellipserna bredvid den klient profil som du vill ta bort. Välj sedan **ta bort**.

    ![Skärm bild som visar alternativet ellipser och alternativet "ta bort" markerat.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Välj **ta bort** för att ta bort.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Skapa en anslutning

1. På sidan väljer du **+** och sedan **+ Lägg till**.

    ![Skärm bild som visar knappen Lägg till markerad.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Fyll i anslutnings informationen. Kontakta administratören om du är osäker på värdena. När du har fyllt i värdena väljer du **Spara**.

    ![Skärm bild som visar VPN-anslutningens egenskaper markerat och knappen "Spara" är markerad.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Välj **Anslut** för att ansluta till VPN.

    ![Skärm bild som visar knappen "Anslut" vald.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Välj rätt autentiseringsuppgifter och välj sedan **Fortsätt**.

    ![Skärm bild som visar exempel på autentiseringsuppgifter markerat och knappen "Fortsätt" är markerad.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. När anslutningen är klar blir ikonen grön och säg **ansluten**.

    ![anslutning](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Så här ansluter du automatiskt

Dessa steg hjälper dig att konfigurera anslutningen till att ansluta automatiskt med Always On.

1. På Start sidan för VPN-klienten väljer du **VPN-inställningar**.

    ![Skärm bild av VPN-start sidan med alternativet VPN-inställningar valda.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Välj **Ja** i dialog rutan Växla appar.

    ![Skärm bild av "menade du att växla appar?" dialog rutan med knappen Ja markerad.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Kontrol lera att den anslutning som du vill ange inte redan är ansluten, markera sedan profilen och markera kryss rutan **Anslut automatiskt** .

    ![Skärm bild av fönstret "Inställningar", där rutan "Anslut automatiskt" är markerad.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Välj **Anslut** för att initiera VPN-anslutningen.

    ![disk](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostisera anslutnings problem

1. För att diagnostisera anslutnings problem kan du använda verktyget **diagnostisera** . Välj **...** bredvid den VPN-anslutning som du vill diagnostisera för att Visa menyn. Välj sedan **diagnostisera**.

    ![Skärm bild av ellipsen och "diagnostisera valda".](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. På sidan **anslutnings egenskaper** väljer du **Kör diagnostik**.

    ![Skärm bild som visar sidan "anslutnings egenskaper" med alternativet "Kör diagnostik" valt.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Logga in med dina autentiseringsuppgifter.

    ![Skärm bild som visar dialog rutan "Låt oss bli inloggad" med ett arbets-eller skol konto har marker ATS.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Visa diagnos resultatet.

    ![diagnostisera](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Vanliga frågor

### <a name="is-the-azure-vpn-client-supported-with-windows-fips-mode"></a>Stöds Azure VPN-klienten med Windows FIPS-läge?

Ja, med snabb korrigeringen [KB4577063](https://support.microsoft.com/help/4577063/windows-10-update-kb4577063) .

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Hur gör jag för att lägga till DNS-suffix till VPN-klienten?

Du kan ändra den nedladdade profil-XML- **\<dnssuffixes> \<dnssufix> \</dnssufix> \</dnssuffixes>** filen och lägga till taggarna

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Hur gör jag för att lägga till anpassade DNS-servrar i VPN-klienten?

Du kan ändra den nedladdade profil-XML- **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** filen och lägga till taggarna

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> OpenVPN Azure AD-klienten använder NRPT-poster (namn matchnings princip tabell), vilket innebär att DNS-servrar inte visas under utdata från `ipconfig /all` . Om du vill bekräfta dina DNS-inställningar som du använder, se [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy) i PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Hur gör jag för att lägga till anpassade vägar i VPN-klienten?

Du kan ändra den nedladdade profil-XML- **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** filen och lägga till taggarna

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Vill du Hur gör jag för att blockera vägar (undanta) från VPN-klienten?

Du kan ändra den nedladdade profil-XML- **\<excluderoutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</excluderoutes>** filen och lägga till taggarna

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>Kan jag importera profilen från kommando tolken?

Du kan importera profilen från en kommando rads kommando tolk genom att placera den hämtade **azurevpnconfig.xml** filen i mappen **%USERPROFILE%\appdata\local\packages\ Microsoft.AzureVpn_8wekyb3d8bbwe \localstate** och köra följande kommando:

```
azurevpn -i azurevpnconfig.xml 
```
för att framtvinga import använder du **-f-** växeln också


## <a name="next-steps"></a>Nästa steg

Mer information finns i [skapa en Azure Active Directory-klient för P2s öppna VPN-anslutningar som använder Azure AD-autentisering](openvpn-azure-ad-tenant.md).