---
title: 'Azure AD-klient för användares VPN-anslutningar: Azure AD-autentisering'
description: Du kan använda Azures virtuella WAN-användare VPN (punkt-till-plats) för att ansluta till ditt VNet med Azure AD-autentisering
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91367860"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Förbereda Azure Active Directory-klient för VPN-OpenVPN för användare

När du ansluter till en virtuell hubb över IKEv2-protokollet kan du använda certifikatbaserad autentisering eller RADIUS-autentisering. Men när du använder OpenVPN-protokollet kan du också använda Azure Active Directory autentisering. Den här artikeln hjälper dig att skapa en Azure AD-klient för virtuella WAN-användare VPN (punkt-till-plats) med OpenVPN-autentisering.

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN- &reg; protokoll anslutningar.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Skapa Azure AD-klienten

Kontrol lera att du har en Azure AD-klient. Om du inte har en Azure AD-klient kan du skapa en med hjälp av stegen i artikeln [skapa en ny klient](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisationsnamn
* Ursprungligt domännamn

Exempel:

   ![Ny Azure AD-klient](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Skapa Azure AD-klient användare

Skapa sedan två användar konton i den nyligen skapade Azure AD-klienten, ett globalt administratörs konto och ett användar konto. Användar kontot kan användas för att testa OpenVPN-autentiseringen och det globala administratörs kontot kommer att användas för att bevilja godkännande till Azure VPN-appens registrering. När du har skapat ett Azure AD-användarkonto tilldelar du en **katalog roll** till användaren för att kunna delegera administratörs behörighet.

Följ stegen i [den här artikeln](../active-directory/fundamentals/add-users-azure-active-directory.md) för att skapa de två användarna för din Azure AD-klient. Se till att ändra **katalog rollen** på ett av de skapade kontona till **Global administratör**.

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. bevilja medgivande till registreringen av Azure VPN-appen

1. Logga in på Azure Portal som en användare som har tilldelats rollen som **Global administratör** .

2. Därefter beviljar du administratörs medgivande för din organisation, så att Azure VPN-appen kan logga in och läsa användar profiler. Kopiera och klistra in webb adressen som hör till distributions platsen i webbläsarens Adress fält:

    Offentliga

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Tyskland

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure Kina 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

3. Välj det **globala administratörs** kontot om du uppmanas att göra det.

    ![Katalog-ID](./media/openvpn-create-azure-ad-tenant/pick.png)

4. Välj **acceptera** när du uppmanas till detta.

    ![Skärm bild som visar dialog rutan med de meddelande behörigheter som har begärts acceptera för din organisation och ytterligare information.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. I **företags program** i Azure AD bör du nu se **Azure VPN** i listan.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>Nästa steg

För att kunna ansluta till dina virtuella nätverk med Azure AD-autentisering måste du skapa en VPN-konfiguration för användare och koppla den till en virtuell hubb. Se [Konfigurera Azure AD-autentisering för punkt-till-plats-anslutning till Azure](virtual-wan-point-to-site-azure-ad.md).
