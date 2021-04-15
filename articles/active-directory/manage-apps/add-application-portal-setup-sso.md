---
title: 'Snabbstart: Konfigurera SAML-baserad enkel inloggning (SSO) för ett program i din Azure Active Directory -klientorganisation (Azure AD)'
description: Den här snabbstarten går igenom processen för att konfigurera SAML-baserad enkel inloggning (SSO) för ett program i din Azure Active Directory-klientorganisation (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: iangithinji
ms.openlocfilehash: 802cc744063ba2bf1110915ae9b22c9c00e09cfa
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378915"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Snabbstart: Konfigurera SAML-baserad enkel inloggning (SSO) för ett program i din Azure Active Directory -klientorganisation (Azure AD)

Kom igång med förenklade användarinloggningar genom att konfigurera enkel inloggning (SSO) för ett program som du har lagt till i din Azure Active Directory-klientorganisation (Azure AD). När du har ställt in enkel inloggning kan användarna logga in på ett program med sina autentiseringsuppgifter för Azure AD. Enkel inloggning ingår i den kostnadsfria versionen av Azure AD.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera enkel inloggning för ett program som du har lagt till i Din Azure AD-klientorganisation behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- En av följande roller: Global administratör, molnprogramadministratör, programadministratör eller ägare av tjänstens huvudnamn.
- Ett program som stöder enkel inloggning och som redan har förkonfigurerats och lagts till i Azure AD-galleriet. De flesta appar kan använda Azure AD för enkel inloggning. Apparna i Azure AD-galleriet är förkonfigurerade. Om din app inte visas eller är en egenutvecklad app kan du fortfarande använda den med Azure AD. Kolla in självstudierna och annan dokumentation i innehållsförteckningen. Den här snabbstarten fokuserar på appar som har förkonfigurerats för enkel inloggning och lagts till i Azure AD-galleriet av apputvecklare.
- Valfritt: [Slutförande av Visa dina appar](view-applications-portal.md).
- Valfritt: Slutförande av [Lägg till en app](add-application-portal.md).
- Valfritt: Slutförande [av Konfigurera en app](add-application-portal-configure.md).
- Valfritt: [Slutförande av Tilldela användare till en app](add-application-portal-assign-users.md).


>[!IMPORTANT]
>Använd en icke-produktionsmiljö för att testa stegen i den här snabbstarten.

## <a name="enable-single-sign-on-for-an-app"></a>Aktivera enkel inloggning för en app

När du har lagt till ett program i Azure AD-klientorganisationen visas översiktssidan. Om du konfigurerar ett program som redan har lagts till kan du titta på den första snabbstarten. Den visar hur du visar de program som har lagts till i din klientorganisation.

Konfigurera enkel inloggning för ett program:

1. I Azure AD-portalen väljer du **Företagsprogram.** Leta sedan upp och välj det program som du vill konfigurera för enkel inloggning.
1. I **avsnittet Hantera** väljer du **Enkel inloggning för** att öppna fönstret Enkel inloggning **för** redigering.

    > [!IMPORTANT]
    > Om appen använder OpenID Connect (OIDC) standard för enkel inloggning visas inget alternativ för enkel inloggning i navigeringen. Se snabbstarten om OIDC-baserad enkel inloggning för att lära dig hur du ställer in den.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Skärmbild som visar konfigurationssidan för enkel inloggning i Azure AD-portalen.":::

1. Välj **SAML för** att öppna konfigurationssidan för enkel inloggning. I det här exemplet är det program som vi konfigurerar för enkel inloggning GitHub. När GitHub har ställts in kan användarna logga in på GitHub med sina autentiseringsuppgifter från din Azure AD-klientorganisation.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Skärmbild som visar konfigurationssidan för enkel inloggning på GitHub.":::

1. Processen för att konfigurera ett program att använda Azure AD för SAML-baserad enkel inloggning varierar beroende på programmet. Det finns en länk till vägledningen för GitHub. Guider för andra appar finns i Självstudier [för att integrera SaaS-program med Azure Active Directory](/azure/active-directory/saas-apps/).
1. Följ guiden för att konfigurera enkel inloggning för programmet. Många program har specifika prenumerationskrav för SSO-funktioner. GitHub kräver till exempel en Enterprise-prenumeration.
    > [!TIP]
    > Mer information om SAML-konfigurationsalternativen finns [i Konfigurera SAML-baserad enkel inloggning](configure-saml-single-sign-on.md).

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Skärmbild som visar alternativet Enkel inloggning i Enterprise-prenumerationen på sidan med GitHub-priser.":::

> [!TIP]
> Du kan automatisera apphantering med hjälp av Graph API i [Automatisera apphantering med Microsoft Graph API.](/graph/application-saml-sso-configure-api)


## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabbstartsserien bör du överväga att ta bort appen för att rensa testklienten. Du kan ta bort appen i den senaste snabbstarten i den här serien. Se [Ta bort en app.](delete-application-portal.md)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du tar bort en app.
> [!div class="nextstepaction"]
> [Ta bort en app](delete-application-portal.md)
