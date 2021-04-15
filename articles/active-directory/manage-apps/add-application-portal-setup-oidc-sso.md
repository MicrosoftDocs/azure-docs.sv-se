---
title: 'Snabbstart: Konfigurera OIDC-baserad enkel inloggning (SSO) för ett program i din Azure Active Directory -klientorganisation (Azure AD)'
description: Den här snabbstarten går igenom processen för att konfigurera OIDC-baserad enkel inloggning (SSO) för ett program i din Azure Active Directory-klientorganisation (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: iangithinji
ms.openlocfilehash: 2e0b49a73422a2f71af37e103e9d4fd6a18a1f61
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378949"
---
# <a name="quickstart-set-up-oidc-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Snabbstart: Konfigurera OIDC-baserad enkel inloggning (SSO) för ett program i din Azure Active Directory -klientorganisation (Azure AD)

Kom igång med förenklade användarinloggningar genom att konfigurera enkel inloggning (SSO) för ett program som du har lagt till i din Azure Active Directory-klientorganisation (Azure AD). När du har ställt in enkel inloggning kan användarna logga in på ett program med sina Autentiseringsuppgifter för Azure AD. Enkel inloggning ingår i den kostnadsfria versionen av Azure AD.

Mer information om OIDC-baserad enkel inloggning finns [i Förstå OIDC-baserad enkel inloggning.](configure-oidc-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera enkel inloggning för ett program som du har lagt till i Din Azure AD-klientorganisation behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- En av följande roller: Global administratör, Molnprogramadministratör, Programadministratör eller ägare av tjänstens huvudnamn.
- Ett program som har stöd för enkel inloggning och som redan har förkonfigurerats och lagts till i Azure AD-galleriet. De flesta appar kan använda Azure AD för enkel inloggning. Apparna i Azure AD-galleriet är förkonfigurerade. Om din app inte visas eller är en egenutvecklad app kan du fortfarande använda den med Azure AD. Kolla in självstudierna och annan dokumentation i innehållsförteckningen. Den här snabbstarten fokuserar på appar som har förkonfigurerats för enkel inloggning och lagts till i Azure AD-galleriet av apputvecklare.
- Valfritt: Slutförande [av Visa dina appar](view-applications-portal.md).
- Valfritt: Slutförande [av Lägg till en app](add-application-portal.md).
- Valfritt: Slutförande [av Konfigurera en app](add-application-portal-configure.md).
- Valfritt: [Slutförande av Tilldela användare till en app](add-application-portal-assign-users.md).

>[!IMPORTANT]
>Använd en icke-produktionsmiljö för att testa stegen i den här snabbstarten.

## <a name="enable-single-sign-on-for-an-app"></a>Aktivera enkel inloggning för en app

När du lägger till en app som använder OIDC-standarden för enkel inloggning har du en konfigurationsknapp. När du väljer knappen går du till programwebbplatsen och slutför registreringsprocessen för appen. Processen att lägga till en app tas upp i snabbstarten Lägg till en app tidigare i den här serien. Om du konfigurerar ett program som redan har lagts till kan du titta på den första snabbstarten. Du får hjälp med att visa de program som redan finns i din klientorganisation. 

Konfigurera enkel inloggning för ett program:

1. I snabbstarten tidigare i den här serien har du lärt dig hur du lägger till en app som använder din Azure AD-klient för identitetshantering. Om apputvecklaren använde OIDC-standarden för att implementera enkel inloggning visas en registrering-knapp när du lägger till appen. 

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png" alt-text="Skärmbild som visar alternativet för enkel inloggning och knappen för registrering." lightbox="media/add-application-portal-setup-oidc-sso/sign-up-oidc-sso.png":::


2. Välj **Registrera dig** så kommer du till inloggningssidan för apputvecklare. Logga in med Azure Active Directory inloggningsuppgifter. 

   > [!IMPORTANT]
    > Om du redan har en prenumeration på programmet sker validering av användarinformation och klient-/kataloginformation. Om programmet inte kan verifiera användaren omdirigeras du till att registrera dig för programtjänsten eller till felsidan.

3. Efter lyckad autentisering visas en dialogruta som ber om administratörsmedgivande. Välj **Consent (Medgivande) för din organisations** räkning och välj sedan Accept **(Acceptera).** Mer information om användar- och administratörsmedgivande finns i [Förstå användar- och administratörsmedgivande.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/consent.png" alt-text="Skärmbild som visar skärmen för medgivande för en app." lightbox="media/add-application-portal-setup-oidc-sso/consent.png":::

4. Programmet läggs till i din klientorganisation och programmets startsida visas.


> [!TIP]
> Du kan automatisera apphantering med hjälp av Graph API, se [Automatisera apphantering med Microsoft Graph API.](/graph/application-saml-sso-configure-api)

Här är en video som går igenom ytterligare information om hur du lägger till en OIDC-baserad app i Azure AD.

Lägga till en OIDC-baserad app i Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabbstartsserien kan du ta bort appen för att rensa testklienten. Du kan ta bort appen i den senaste snabbstarten i den här serien. Se [Ta bort en app.](delete-application-portal.md)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du tar bort en app.
> [!div class="nextstepaction"]
> [Ta bort en app](delete-application-portal.md)
