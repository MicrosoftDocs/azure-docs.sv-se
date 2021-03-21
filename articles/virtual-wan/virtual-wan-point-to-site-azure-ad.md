---
title: 'Konfigurera Azure AD-autentisering för användares VPN-anslutning: virtuellt WAN'
description: Lär dig hur du konfigurerar Azure Active Directory autentisering för användar-VPN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alzam
ms.openlocfilehash: 9cc68eb60096c4431acfc988c87ca9bf99f1f045
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93043406"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Konfigurera Azure Active Directory autentisering för användar-VPN

Den här artikeln visar hur du konfigurerar Azure AD-autentisering för användar-VPN i virtuella WAN-nätverk för att ansluta till dina resurser i Azure över en OpenVPN VPN-anslutning. Azure Active Directory autentisering är endast tillgängligt för gateways med OpenVPN-protokoll och klienter som kör Windows.

Den här typen av anslutning kräver att en klient konfigureras på klientdatorn. Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md).

I den här artikeln kan du se hur du:

* Skapa ett virtuellt WAN
* Skapa en virtuell hubb
* Skapa en VPN-konfiguration för användare
* Hämta en VPN-profil för virtuella WAN-användare
* Använd VPN-konfiguration för användare till en virtuell hubb
* Ansluta ett VNet till en virtuell hubb
* Hämta och Använd konfigurationen för VPN-klienten för användare
* Visa ditt virtuella WAN

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Du har ett virtuellt nätverk som du vill ansluta till. Kontrol lera att inget av under näten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure Portal finns i [snabb](../virtual-network/quick-create-portal.md)starten.

* Det virtuella nätverket har inga virtuella Nätverksgatewayen. Om ditt virtuella nätverk har en gateway (antingen VPN eller ExpressRoute) måste du ta bort alla gatewayer. Den här konfigurationen kräver att virtuella nätverk är anslutna i stället till den virtuella WAN Hub-gatewayen.

* Hämta ett IP-adressintervall för din hubbregion. Hubben är ett virtuellt nätverk som skapas och används av virtuellt WAN-nätverk. Det adress intervall som du anger för hubben får inte överlappa något av dina befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa det adressintervall som du ansluter till lokalt. Om du inte känner till IP-adressintervall som finns i din lokala nätverks konfiguration, koordinerar du med någon som kan ge den informationen åt dig.

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Skapa ett virtuellt WAN

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

1. Gå till den virtuella WAN-sidan. Klicka på **+Skapa en resurs** i portalen. Skriv det **virtuella WAN-nätverket** i sökrutan och välj RETUR.
2. Välj **virtuellt WAN** från resultaten. På den virtuella WAN-sidan klickar du på **skapa** för att öppna sidan Skapa WAN.
3. På sidan **skapa WAN** , på fliken **grundläggande** , fyller du i följande fält:

   ![Virtuellt WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Prenumeration** – Välj vilken prenumeration du vill använda.
   * **Resurs grupp** – skapa ny eller Använd befintlig.
   * **Resurs grupps plats** – Välj en resurs plats i list rutan. Ett WAN är en global resurs och är inte kopplad till en viss region. Du måste dock välja en region för att lättare att hantera och leta upp WAN-resursen som du skapar.
   * **Namn** – ange det namn som du vill anropa ditt WAN.
   * **Typ:** Standard. Om du skapar ett grundläggande WAN-nätverk kan du bara skapa en Basic-hubb. Basic-hubbar har endast stöd för VPN för plats-till-plats-anslutning.
4. När du har fyllt i fälten väljer du **Granska + skapa**.
5. När verifieringen har godkänts väljer du **skapa** för att skapa det virtuella WAN-nätverket.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Skapa en tom virtuell hubb

1. Under ditt virtuella WAN-nätverk väljer du hubbar och klickar på **+ ny hubb**.

   ![Skärm bild som visar dialog rutan Hubbs konfiguration med ny hubb vald.](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Fyll i följande fält på sidan Skapa virtuellt nav.

   **Region** – Välj den region som du vill distribuera den virtuella hubben i.

   **Namn** – ange det namn som du vill använda för att anropa den virtuella hubben.

   **Hubb privat adress utrymme** – hubbens adress intervall i CIDR-format.

   ![Skärm bild som visar fönstret Skapa virtuellt nav där du kan ange värden.](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Klicka på **Granska + skapa**.
4. Klicka på **skapa** på sidan **valideringen har slutförts** .

## <a name="create-a-new-user-vpn-configuration"></a><a name="site"></a>Skapa en ny VPN-konfiguration för användare

En VPN-konfiguration för användare definierar parametrar för att ansluta fjärrklienter.

1. Välj **VPN-konfigurationer för användare** under ditt virtuella WAN-nätverk.

   ![Skärm bild som visar meny alternativet för användare V P N-inställningar valt.](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. Klicka på **+ skapa användare VPN-konfiguration**.

   ![Skärm bild som visar länken Skapa användare V P N config.](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Ange informationen och klicka på **skapa**.

   * **Konfigurations namn** – ange det namn som du vill anropa din användar-VPN-konfiguration.
   * **Tunnel typ** – Välj OpenVPN.
   * **Autentiseringsmetod** – Välj Azure Active Directory.
   * **Audience** – typ i program-ID för [Azure VPN-Enterprise-](openvpn-azure-ad-tenant.md) programmet som registrerats i din Azure AD-klient. 
   * **Utfärdare** - `https://sts.windows.net/<your Directory ID>/`
   * **AAD-klient** - `https://login.microsoftonline.com/<your Directory ID>`
  
   ![Skärm bild som visar konfigurations rutan skapa ny användare V P N där du kan ange värden.](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Redigera hubbtilldelning

1. Gå till bladet **hubbar** under det virtuella WAN-nätverket.
2. Välj den hubb som du vill koppla VPN-serverkonfigurationen till och klicka på ellipsen (...).

   ![Skärm bild som visar redigera virtuell hubb som valts på menyn.](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Klicka på **Redigera virtuell hubb**.
4. Markera kryss rutan **Inkludera punkt-till-plats-Gateway** och välj den **enhet för gateway-skalning** som du vill använda.

   ![Skärm bild som visar dialog rutan Redigera virtuellt nav där du kan välja din skalnings enhet för gateway.](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Ange adresspoolen **från vilken** VPN-klienter ska tilldelas IP-adresser.
6. Klicka på **Bekräfta**.
7. Åtgärden kan ta upp till 30 minuter att slutföra.

## <a name="download-user-vpn-profile"></a><a name="device"></a>Ladda ned användare VPN-profil

Använd VPN-profilen för att konfigurera dina klienter.

1. På sidan för ditt virtuella WAN-nätverk klickar du på **användare VPN-konfigurationer**.
2. Klicka på **Hämta VPN-konfiguration för användare** längst upp på sidan.
3. När filen har skapats klickar du på länken för att ladda ned den.
4. Använd profil filen för att konfigurera VPN-klienterna.

## <a name="configure-user-vpn-clients"></a>Konfigurera användares VPN-klienter

För att ansluta måste du ladda ned Azure VPN-klienten och importera VPN-klienttjänsten som hämtades i föregående steg på varje dator som vill ansluta till det virtuella nätverket.

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN- &reg; protokoll anslutningar.
>

#### <a name="to-download-the-azure-vpn-client"></a>Ladda ned Azure VPN-klienten

Använd den här [länken](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) för att ladda ned Azure VPN-klienten.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Så här importerar du en klient profil

1. På sidan väljer du **Importera**.

    ![Skärm bild som visar importera markerade från menyn plus.](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Bläddra till profil-XML-filen och markera den. När filen är vald väljer du **Öppna**.

    ![Skärm bild som visar en öppen dialog ruta där du kan välja en fil.](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Ange namnet på profilen och välj **Spara**.

    ![Skärm bild som visar det tillagda anslutnings namnet och knappen Spara valt.](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Välj **Anslut** för att ansluta till VPN.

    ![Skärm bild som visar knappen Anslut för den anslutning som du nyss skapade.](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. När du har anslutit ikonen blir den grön och säg **ansluten**.

    ![Skärm bild som visar anslutningen i en ansluten status med alternativet att koppla från.](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Ta bort en klient profil

1. Välj ellipsen (...) bredvid den klient profil som du vill ta bort. Välj sedan **ta bort**.

    ![Skärm bild som visar ta bort valda från menyn.](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Välj **ta bort** för att ta bort.

    ![Skärm bild som visar en bekräftelse dialog ruta med alternativet att ta bort eller avbryta.](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostisera anslutnings problem

1. För att diagnostisera anslutnings problem kan du använda verktyget **diagnostisera** . Välj ellipsen (...) bredvid den VPN-anslutning som du vill diagnostisera för att Visa menyn. Välj sedan **diagnostisera**.

    ![Skärm bild som visar diagnostiserat valt i menyn.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. På sidan **anslutnings egenskaper** väljer du **Kör diagnostik**.

    ![Skärm bild som visar knappen Kör diagnostik för en anslutning.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Logga in med dina autentiseringsuppgifter.

    ![Skärm bild som visar dialog rutan logga in för den här åtgärden.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Visa diagnos resultatet.

    ![Skärm bild som visar resultatet av diagnostiken.](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visa virtuellt WAN

1. Navigera till det virtuella WAN-nätverket.
2. Varje punkt på kartan på sidan Översikt motsvarar en hubb.
3. I avsnittet om hubbar och anslutningar kan du visa hubbstatus, plats, region, VPN-anslutningsstatus och byte in och ut.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Rensa resurser

När du inte längre behöver dessa resurser kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser den innehåller. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
