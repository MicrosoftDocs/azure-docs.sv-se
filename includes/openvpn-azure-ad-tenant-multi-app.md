---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/15/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3236f9c60cb359349d96e93f674c3e278e44f1e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93376118"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Skapa Azure AD-klienten

Skapa en Azure AD-klient med hjälp av stegen i artikeln [skapa en ny klient organisation](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisations namn
* Ursprungligt domännamn

  Exempel:

   ![Ny Azure AD-klient](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. skapa klient organisations användare

I det här steget skapar du två Azure AD-klient användare: ett globalt administratörs konto och ett huvud användar konto. Huvud användar kontot används som ditt huvud inbäddnings konto (tjänst konto). När du skapar ett användar konto för Azure AD-klienten justerar du katalog rollen för den typ av användare som du vill skapa. Följ stegen i [den här artikeln](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) för att skapa minst två användare för din Azure AD-klient. Se till att ändra **katalog rollen** för att skapa konto typerna:

* Global administratör
* User

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. registrera VPN-klienten

Registrera VPN-klienten i Azure AD-klienten.

1. Leta upp katalog-ID: t för den katalog som du vill använda för autentisering. Den visas i avsnittet Egenskaper på sidan Active Directory.

    ![Katalog-ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Kopiera katalog-ID:t.

3. Logga in på Azure Portal som en användare som har tilldelats rollen som **Global administratör** .

4. Ge sedan administrativt medgivande. Kopiera och klistra in webb adressen som hör till distributions platsen i webbläsarens Adress fält:

    Offentliga

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Tyskland

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure Kina 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

> [!NOTE]
> Om du använder ett globalt administratörs konto som inte är inbyggt i Azure AD-klienten för att ge tillåtelse, ersätter du "common" med Azure AD-katalog-ID: t i URL: en. Du kan också behöva ersätta "common" med ditt katalog-ID i vissa andra fall.
>

5. Välj det **globala administratörs** kontot om du uppmanas att göra det.

    ![Katalog-ID 2](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Välj **acceptera** när du uppmanas till detta.

    ![Skärm bild som visar ett fönster med de meddelande behörigheter som har begärts acceptera för din organisation och information om begäran.](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. I **företags program** i Azure AD kommer du att se **Azure VPN** i listan.

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. registrera ytterligare program

I det här steget registrerar du ytterligare program för olika användare och grupper.

1. Klicka på **Appregistreringar** under Azure Active Directory och sedan på **+ ny registrering**.

    ![Azure VPN 2](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Ange **namnet** på sidan **Registrera ett program** . Välj önskade **konto typer som stöds** och klicka sedan på **Registrera**.

    ![Azure VPN 3](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. När den nya appen har registrerats klickar du på **exponera ett API** på bladet app.

4. Klicka på **+ Lägg till ett omfång**.

5. Lämna standard **program-ID-URI: n**. Klicka på **Spara och fortsätt**.

    ![Azure VPN 4](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Fyll i de obligatoriska fälten och kontrol lera att **läget** är **aktiverat**. Klicka på **Lägg till omfattning**.

    ![Azure VPN 5](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Klicka på **exponera ett API** och sedan **+ Lägg till ett klient program**.  För **klient-ID** anger du följande värden beroende på molnet:

    - Ange **41b23e61-6c1e-4545-b367-cd054e0ed4b4** för Azure **Public**
    - Ange **51bb15d4-3a4f-4ebf-9dca-40096fe32426** för Azure **myndigheter**
    - Ange **538ee9e6-310A-468d-afef-ea97365856a9** för Azure **Germany**
    - Ange **49f817b6-84ae-4CC0-928c-73f27289b3aa** för Azure **Kina 21Vianet**

8. Klicka på **Lägg till program**.

    ![Azure VPN 6](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Kopiera **program-ID: t (klient)** från **översikts** sidan. Du behöver den här informationen för att konfigurera din VPN-gateway (er).

    ![Azure VPN 7](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Upprepa stegen i den här artikeln för att [registrera ytterligare program](#register-apps) för att skapa så många program som behövs för ditt säkerhets krav. Varje program kommer att associeras med en VPN-gateway och kan ha en annan uppsättning användare. Endast ett program kan associeras med en gateway.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. tilldela användare till program

Tilldela användarna till dina program.

1. Under **Azure AD-> företags program** väljer du det nyligen registrerade programmet och klickar på **Egenskaper**. Se till att **användar tilldelning krävs?** är inställt på **Ja**. Klicka på **Spara**.

    ![Azure VPN 8](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. På sidan app klickar du på **användare och grupper** och klickar sedan på **+ Lägg till användare**.

    ![Azure VPN 9](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. Under **Lägg till tilldelning**, klickar **du på användare och grupper**. Välj de användare som du vill ska kunna komma åt det här VPN-programmet. Klicka på **Välj**.

    ![Azure VPN 10](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
