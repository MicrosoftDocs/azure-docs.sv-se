---
title: 'VPN Gateway: VPN-klienten för OpenVPN-protokoll P2S anslutningar: Azure AD-autentisering'
description: Lär dig hur du använder P2S VPN för att ansluta till ditt VNet med Azure AD-autentisering.
services: vpn-gateway
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 3dbdfb461bc2ae79439f39df3efbc2c564782110
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548401"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Konfigurera en VPN-klient för P2S OpenVPN-protokollanslutningar: Azure AD-autentisering

Den här artikeln hjälper dig att konfigurera en VPN-klient för att ansluta till ett virtuellt nätverk med hjälp av punkt-till-plats-VPN och Azure Active Directory autentisering. Innan du kan ansluta och autentisera med Azure AD måste du först konfigurera Azure AD-klienten. Mer information finns i [Konfigurera en Azure AD-klient](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN-® protokoll anslutningar.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Arbeta med klient profiler

För att ansluta måste du ladda ned Azure VPN-klienten och konfigurera en VPN-klient profil på varje dator som vill ansluta till det virtuella nätverket. Du kan skapa en klient profil på en dator, exportera den och sedan importera den till ytterligare datorer.

### <a name="to-download-the-azure-vpn-client"></a>Ladda ned Azure VPN-klienten

Använd den här [länken](https://go.microsoft.com/fwlink/?linkid=2117554) för att ladda ned Azure VPN-klienten. Kontrol lera att Azure VPN-klienten har behörighet att köra i bakgrunden. Om du vill kontrol lera/aktivera behörigheten följer du stegen nedan:

1. Gå till start och välj sedan inställningar > sekretess > bakgrunds program.
2. Under huvudappar kontrollerar du att **Låt appar köras i bakgrunden** är aktiverat.
3. Under Välj vilka appar som kan köras i bakgrunden aktiverar du inställningarna för Azure VPN-klienten till **på**.

  ![permission](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Så här skapar du en certifikatbaserad klient profil

När du arbetar med en certifikatbaserad profil bör du kontrol lera att rätt certifikat är installerade på klient datorn. Mer information om certifikat finns i [Installera klient certifikat](certificates-point-to-site.md).

  ![certifikatet](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Så här skapar du en RADIUS-klient profil

  ![RADIUS](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Server hemligheten kan exporteras i P2S VPN-klient profil.  Anvisningar om hur du exporterar en klient profil finns [här](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Så här exporterar och distribuerar du en klient profil

När du har en fungerande profil och behöver distribuera den till andra användare kan du exportera den med hjälp av följande steg:

1. Markera den VPN-klientkonfiguration som du vill exportera, Välj **...** och välj sedan **Exportera**.

    ![Skärm bild som visar Exportera markerade från menyn.](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Välj den plats där du vill spara profilen, lämna fil namnet som det är och välj sedan **Spara** för att spara XML-filen.

    ![Skärm bild som visar dialog rutan Spara som där du kan ange ett fil namn.](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Så här importerar du en klient profil

1. På sidan väljer du **Importera**.

    ![Skärm bild som visar importera markerade från menyn plus.](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Bläddra till profil-XML-filen och markera den. När filen är vald väljer du **Öppna**.

    ![Skärm bild som visar en öppen dialog ruta där du kan välja en fil.](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Ange namnet på profilen och välj **Spara**.

    ![Skärm bild som visar det tillagda anslutnings namnet och knappen Spara valt.](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Välj **Anslut** för att ansluta till VPN.

    ![Skärm bild som visar knappen Anslut för den anslutning som du nyss skapade.](./media/openvpn-azure-ad-client/import/import4.jpg)

5. När du har anslutit ikonen blir den grön och säg **ansluten**.

    ![Skärm bild som visar anslutningen i en ansluten status med alternativet att koppla från.](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Ta bort en klient profil

1. Välj ellipserna bredvid den klient profil som du vill ta bort. Välj sedan **ta bort**.

    ![Skärm bild som visar ta bort valda från menyn.](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Välj **ta bort** för att ta bort.

    ![Skärm bild som visar en bekräftelse dialog ruta med alternativet att ta bort eller avbryta.](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Skapa en anslutning

1. På sidan väljer du **+** och sedan **+ Lägg till**.

    ![Skärm bild som visar Lägg till valda från menyn plus.](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Fyll i anslutnings informationen. Kontakta administratören om du är osäker på värdena. När du har fyllt i värdena väljer du **Spara**.

    ![Skärm bild som visar fönstret där du kan ange de värden som krävs.](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Välj **Anslut** för att ansluta till VPN.

    ![Skärm bild som visar knappen Anslut för anslutningen.](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Välj rätt autentiseringsuppgifter och välj sedan **Fortsätt**.

    ![Skärm bild som visar dialog rutan logga in.](./media/openvpn-azure-ad-client/create/create4.jpg)

5. När anslutningen är klar blir ikonen grön och säg **ansluten**.

    ![Skärm bild som visar anslutningen i en ansluten status.](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Så här ansluter du automatiskt

Dessa steg hjälper dig att konfigurera anslutningen till att ansluta automatiskt med Always On.

1. På Start sidan för VPN-klienten väljer du **VPN-inställningar**.

    ![Skärm bild som visar V P N-anslutningar där du kan välja V P N-inställningar.](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Välj **Ja** i dialog rutan Växla appar.

    ![Skärm bild som visar ett verifierings meddelande om att växla appar.](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Kontrol lera att den anslutning som du vill ange inte redan är ansluten, markera sedan profilen och markera kryss rutan **Anslut automatiskt** .

    ![Skärm bild som visar dialog rutan inställningar där du kan välja Anslut automatiskt.](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Välj **Anslut** för att initiera VPN-anslutningen.

    ![Skärm bild som visar knappen Anslut.](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostisera anslutnings problem

1. För att diagnostisera anslutnings problem kan du använda verktyget **diagnostisera** . Välj **...** bredvid den VPN-anslutning som du vill diagnostisera för att Visa menyn. Välj sedan **diagnostisera**.

    ![Skärm bild som visar diagnostiserat valt i menyn.](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. På sidan **anslutnings egenskaper** väljer du **Kör diagnostik**.

    ![Skärm bild som visar knappen Kör diagnostik för en anslutning.](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Logga in med dina autentiseringsuppgifter.

    ![Skärm bild som visar dialog rutan logga in för den här åtgärden.](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Visa diagnos resultatet.

    ![Skärm bild som visar resultatet av diagnostiken.](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>Vanliga frågor

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
### <a name="how-do-i-direct-all-traffic-to-the-vpn-tunnel-force-tunnel"></a>Hur gör jag för att dirigera all trafik till VPN-tunneln (tvinga tunnel)?

Du kan ändra den nedladdade profil-XML- **\<includeroutes> \<route> \<destination> \<mask> \</destination> \</mask> \</route> \</includeroutes>** filen och lägga till taggarna

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>0.0.0.0</destination><mask>1</mask>
        </route>
        <route>
            <destination>128.0.0.0</destination><mask>1</mask>
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