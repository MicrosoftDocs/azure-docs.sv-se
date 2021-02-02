---
title: 'Snabb start: Konfigurera SAML-baserad enkel inloggning (SSO) för ett program i din Azure Active Directory (Azure AD)-klient'
description: Den här snabb starten vägleder dig genom processen att konfigurera SAML-baserad enkel inloggning (SSO) för ett program i din Azure Active Directory (Azure AD)-klient.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 3278c9956dd5017572f063059250c97727656923
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252380"
---
# <a name="quickstart-set-up-saml-based-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Snabb start: Konfigurera SAML-baserad enkel inloggning (SSO) för ett program i din Azure Active Directory (Azure AD)-klient

Kom igång med förenklade användar inloggningar genom att konfigurera enkel inloggning (SSO) för ett program som du har lagt till i din Azure Active Directory (Azure AD)-klient. När du har konfigurerat SSO kan användarna logga in i ett program genom att använda sina autentiseringsuppgifter för Azure AD. SSO ingår i den kostnads fria versionen av Azure AD.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera SSO för ett program som du har lagt till i din Azure AD-klient behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En av följande roller: global administratör, moln program administratör, program administratör eller ägare av tjänstens huvud namn.
- Ett program som stöder SSO och som redan har förkonfigurerats och lagts till i Azure AD-galleriet. De flesta appar kan använda Azure AD för SSO. Apparna i Azure AD-galleriet är förkonfigurerade. Om din app inte finns med i listan eller är en anpassad-utvecklad app kan du fortfarande använda den med Azure AD. Titta närmare på självstudierna och annan dokumentation i innehålls förteckningen. Den här snabb starten fokuserar på appar som har förkonfigurerats för enkel inloggning och lagts till i Azure AD-galleriet av apps-utvecklare.
- Valfritt: Slutför [visningen av Visa dina appar](view-applications-portal.md).
- Valfritt: slut för ande av [Lägg till en app](add-application-portal.md).
- Valfritt: Slutför [konfiguration av en app](add-application-portal-configure.md).
- Valfritt: slut för ande av [tilldela användare till en app](add-application-portal-assign-users.md).


>[!IMPORTANT]
>Använd en icke-produktions miljö för att testa stegen i den här snabb starten.

## <a name="enable-single-sign-on-for-an-app"></a>Aktivera enkel inloggning för en app

När du har lagt till ett program i Azure AD-klienten visas sidan Översikt. Om du konfigurerar ett program som redan har lagts till tittar du på den första snabb starten. Den vägleder dig genom att visa de program som har lagts till i din klient organisation. 

Så här konfigurerar du enkel inloggning för ett program:

1. I Azure AD-portalen väljer du **företags program**. Hitta och välj sedan det program som du vill konfigurera för enkel inloggning.
1. I avsnittet **Hantera** väljer du **enkel inloggning** för att öppna fönstret **enkel inloggning** för redigering.

    > [!IMPORTANT]
    > Om appen använder OpenID Connect (OIDC)-standarden för SSO visas inget alternativ för enkel inloggning i navigeringen. Läs snabb starten för OIDC-baserad SSO för att lära dig hur du konfigurerar den.

    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Skärm bild som visar konfigurations sidan för enkel inloggning i Azure AD-portalen.":::

1. Välj **SAML** för att öppna sidan SSO-konfiguration. I det här exemplet är det program vi konfigurerar för SSO GitHub. När GitHub har kon figurer ATS kan användarna logga in på GitHub genom att använda sina autentiseringsuppgifter från din Azure AD-klient.

    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Skärm bild som visar konfigurations sidan för enkel inloggning på GitHub.":::

1. Processen att konfigurera ett program för att använda Azure AD för SAML-baserad SSO varierar beroende på vilket program som används. Det finns en länk till vägledningen för GitHub. För att hitta guider för andra appar, se [självstudier för att integrera SaaS-program med Azure Active Directory](/azure/active-directory/saas-apps/).
1. Följ anvisningarna för att konfigurera SSO för programmet. Många program har särskilda prenumerations krav för SSO-funktioner. Till exempel kräver GitHub en Enterprise-prenumeration.
    > [!TIP]
    > Mer information om alternativen för SAML-konfiguration finns i [Konfigurera SAML-baserad enkel inloggning](configure-saml-single-sign-on.md).

    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Skärm bild som visar alternativet för enkel inloggning i Enterprise-prenumerationen på prissättnings sidan för GitHub.":::

> [!TIP]
> Du kan automatisera hantering av appar med hjälp av Graph API, se [Automatisera program hantering med Microsoft Graph API](/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabb starts serien bör du ta bort appen för att rensa test klienten. Borttagning av appen beskrivs i den senaste snabb starten i den här serien. se [ta bort en app](delete-application-portal.md).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du tar bort en app.
> [!div class="nextstepaction"]
> [Ta bort en app](delete-application-portal.md)