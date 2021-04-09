---
title: 'Virtuellt WAN: Azure AD-klient för olika användar grupper: Azure AD-autentisering'
description: Konfigurera en Azure AD-klient för P2S OpenVPN-autentisering och skapa och registrera flera appar i Azure AD för att tillåta olika åtkomst för olika användare och grupper.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: e25ef7f55492be4ee491b9ebbbef4aa1eb03c80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898095"
---
# <a name="create-an-azure-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Skapa en Azure Active Directory-klient (AD) för P2S OpenVPN-protokoll anslutningar

När du ansluter till ditt VNet kan du använda certifikatbaserad autentisering eller RADIUS-autentisering. Men när du använder det öppna VPN-protokollet kan du också använda Azure Active Directory autentisering. Om du vill att en annan uppsättning användare ska kunna ansluta till olika gatewayer kan du registrera flera appar i AD och länka dem till olika gatewayer.

Den här artikeln hjälper dig att skapa en Azure AD-klient för P2S OpenVPN-autentisering och skapa och registrera flera appar i Azure AD för att ge olika åtkomst till olika användare och grupper.

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN- &reg; protokoll anslutningar.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. skapa en ny P2S-konfiguration

En P2S-konfiguration definierar parametrarna för att ansluta fjärrklienter.

1. Ange följande variabler och ersätt värden efter behov för din miljö.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Kör följande kommandon för att skapa konfigurationen:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Använd inte Azure VPN-klientens program-ID i kommandona ovan: det ger alla användare åtkomst till gatewayen. Använd ID: t för det/de program som du har registrerat.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. redigera Hubbs tilldelning

1. Gå till bladet **hubbar** under det virtuella WAN-nätverket.

2. Välj den hubb som du vill koppla VPN-serverkonfigurationen till och klicka på ellipsen (...).

    ![Skärm bild som visar redigera virtuell hubb som valts på menyn.](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Klicka på **Redigera virtuell hubb**.

4. Markera kryss rutan **Inkludera punkt-till-plats-Gateway** och välj den **enhet för gateway-skalning** som du vill använda.

    ![Skärm bild som visar dialog rutan Redigera virtuellt nav där du kan välja din skalnings enhet för gateway.](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Ange adresspoolen **från vilken** VPN-klienter ska tilldelas IP-adresser.

6. Klicka på **Bekräfta**.

7. Åtgärden kan ta upp till 30 minuter att slutföra.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. Ladda ned VPN-profil

Använd VPN-profilen för att konfigurera dina klienter.

1. På sidan för ditt virtuella WAN-nätverk klickar du på **användare VPN-konfigurationer**.

2. Klicka på **Hämta VPN-konfiguration för användare** längst upp på sidan.

3. När filen har skapats klickar du på länken för att ladda ned den.

4. Använd profil filen för att konfigurera VPN-klienterna.

5. Extrahera den hämtade ZIP-filen.

6. Bläddra till mappen unzippad "AzureVPN".

7. Anteckna platsen för filen "azurevpnconfig.xml". azurevpnconfig.xml innehåller inställningen för VPN-anslutningen och kan importeras direkt till Azure VPN-klientprogrammet. Du kan också distribuera filen till alla användare som behöver ansluta via e-post eller på annat sätt. Användaren måste ha giltiga autentiseringsuppgifter för Azure AD för att kunna ansluta.

## <a name="9-configure-user-vpn-clients"></a>9. Konfigurera användares VPN-klienter

För att ansluta måste du ladda ned Azure VPN-klienten och importera VPN-klienttjänsten som hämtades i föregående steg på varje dator som vill ansluta till det virtuella nätverket.

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN- &reg; protokoll anslutningar.
>

#### <a name="to-download-the-azure-vpn-client"></a>Ladda ned Azure VPN-klienten

Använd den här [länken](https://go.microsoft.com/fwlink/?linkid=2117554) för att ladda ned Azure VPN-klienten.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Så här importerar du en klient profil

1. På sidan väljer du **Importera**.

    ![Skärm bild som visar importera markerade från menyn plus.](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Bläddra till profil-XML-filen och markera den. När filen är vald väljer du **Öppna**.

    ![Skärm bild som visar en öppen dialog ruta där du kan välja en fil.](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Ange namnet på profilen och välj **Spara**.

    ![Skärm bild som visar det tillagda anslutnings namnet och knappen Spara valt.](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Välj **Anslut** för att ansluta till VPN.

    ![Skärm bild som visar knappen Anslut för den anslutning som du nyss skapade.](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. När du har anslutit ikonen blir den grön och säg **ansluten**.

    ![Skärm bild som visar anslutningen i en ansluten status med alternativet att koppla från.](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Ta bort en klient profil

1. Välj ellipsen (...) bredvid den klient profil som du vill ta bort. Välj sedan **ta bort**.

    ![Skärm bild som visar ta bort valda från menyn.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Välj **ta bort** för att ta bort.

    ![Skärm bild som visar en bekräftelse dialog ruta med alternativet att ta bort eller avbryta.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostisera anslutnings problem

1. För att diagnostisera anslutnings problem kan du använda verktyget **diagnostisera** . Välj ellipsen (...) bredvid den VPN-anslutning som du vill diagnostisera för att Visa menyn. Välj sedan **diagnostisera**.

    ![Skärm bild som visar diagnostiserat valt i menyn.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. På sidan **anslutnings egenskaper** väljer du **Kör diagnostik**.

    ![Skärm bild som visar knappen Kör diagnostik för en anslutning.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Logga in med dina autentiseringsuppgifter.

    ![diagnostisera 3](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Visa diagnos resultatet.

    ![Skärm bild som visar knappen Kör diagnostik för en anslutning.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Logga in med dina autentiseringsuppgifter.

    ![Skärm bild som visar dialog rutan logga in för den här åtgärden.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Visa diagnos resultatet.

    ![Skärm bild som visar resultatet av diagnostiken.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Visa ditt virtuella WAN-nätverk

1. Navigera till det virtuella WAN-nätverket.

2. Varje punkt på kartan på sidan Översikt motsvarar en hubb.

3. I avsnittet om hubbar och anslutningar kan du visa hubbstatus, plats, region, VPN-anslutningsstatus och byte in och ut.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
