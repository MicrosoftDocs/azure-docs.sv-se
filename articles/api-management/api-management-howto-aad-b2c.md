---
title: Auktorisera utvecklares konton genom att använda Azure Active Directory B2C
titleSuffix: Azure API Management
description: Lär dig hur du auktoriserar användare genom att använda Azure Active Directory B2C i API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7b586edd7adce8bcea61419005a3ce8cfc814fb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013572"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Så här auktoriserar du utvecklarkonton med Azure Active Directory B2C i Azure API Management

## <a name="overview"></a>Översikt

Azure Active Directory B2C är en lösning för moln identitets hantering för webb-och mobil program som riktas mot konsumenter. Du kan använda den för att hantera åtkomst till Developer-portalen. Den här guiden visar den konfiguration som krävs i API Managements tjänsten för att integrera med Azure Active Directory B2C. Information om hur du aktiverar åtkomst till Developer-portalen med hjälp av klassisk Azure Active Directory finns i [så här auktoriserar du utvecklares konton med Azure Active Directory].

> [!NOTE]
> För att slutföra stegen i den här hand boken måste du först ha en Azure Active Directory B2C-klient för att skapa ett program i. Du måste också ha registrerings-och inloggnings principer som är klara. Mer information finns i [Azure Active Directory B2C översikt].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Auktorisera utvecklares konton genom att använda Azure Active Directory B2C

1. Kom igång genom att logga in på [Azure Portal](https://portal.azure.com) och leta upp API Management-instansen.

   > [!NOTE]
   > Om du ännu inte har skapat en API Management tjänst instans, se [skapa en API Management tjänst instans][Create an API Management service instance] i [själv studie kursen komma igång med Azure API Management][Get started with Azure API Management].

1. Under **identiteter**. Klicka på **+ Lägg till** överst.

   Fönstret **Lägg till identitetsprovider** visas till höger. Välj **Azure Active Directory B2C**.
    
   ![Lägg till AAD B2C som identitets leverantör][api-management-howto-add-b2c-identity-provider]

1. Kopiera **omdirigerings-URL: en**.

   ![Omdirigerings-URL för AAD B2C-identitetsprovider][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. På en ny flik öppnar du din Azure Active Directory B2C-klient i Azure Portal och öppnar bladet **program** .

   ![Registrera ett nytt program 1][api-management-howto-aad-b2c-portal-menu]

1. Klicka på knappen **Lägg till** för att skapa ett nytt Azure Active Directory B2C-program.

   ![Registrera ett nytt program 2][api-management-howto-aad-b2c-add-button]

1. På bladet **ny program** anger du ett namn för programmet. Välj **Ja** under **Web App/Web API** och välj **Ja** under **Tillåt implicit flöde**. Klistra sedan in **omdirigerings-URL: en** som kopierades i steg 3 i text rutan **svars-URL** .

   ![Registrera ett nytt program 3][api-management-howto-aad-b2c-app-details]

1. Om du använder den nya Developer-portalen (inte den äldre Developer-portalen) inkluderar du det **aktuella namnet** **, efter namn och** **användarens objekt-ID** i program anspråk.

    ![Programanspråk](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Klicka på knappen **Skapa**. När programmet har skapats visas det på bladet **program** . Klicka på program namnet om du vill se information om det.

   ![Registrera ett nytt program 4][api-management-howto-aad-b2c-app-created]

1. Kopiera **program-ID: t** till Urklipp från bladet **Egenskaper** .

   ![Program-ID 1][api-management-howto-aad-b2c-app-id]

1. Växla tillbaka till fönstret API Management **Lägg till identitetsprovider** och klistra in ID: t i text rutan **klient-ID** .
    
1.  Växla tillbaka till B2C-appens registrering, klicka på knappen **nycklar** och klicka sedan på **generera nyckel**. Klicka på **Spara** för att spara konfigurationen och visa **appens nyckel**. Kopiera nyckeln till Urklipp.

    ![App-nyckel 1][api-management-howto-aad-b2c-app-key]

1.  Gå tillbaka till fönstret API Management **Lägg till identitetsprovider** och klistra in nyckeln i text rutan för **klient hemlighet** .
    
1.  Ange domän namnet för den Azure Active Directory B2C klienten i **inloggnings klienten**.

1.  I fältet **auktoritet** kan du kontrol lera Azure AD B2C inloggnings-URL: en som ska användas. Ange värdet **<your_b2c_tenant_name>. b2clogin.com**.

1. Ange **registrerings principen** och **inloggnings principen** från B2C-klient principer. Alternativt kan du även ange **profil för profil redigering** och princip för **lösen ords återställning**.

1. När du har angett önskad konfiguration klickar du på **Spara**.

    När ändringarna har sparats kommer utvecklare att kunna skapa nya konton och logga in på Developer-portalen med hjälp av Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Utvecklings Portal – Lägg till Azure AD B2C konto-autentisering

I Developer-portalen är det möjligt att logga in med AAD B2C med **inloggnings knappen: OAuth-** widgeten. Widgeten är redan inkluderad på inloggnings sidan för standard innehållet i Developer-portalen.

Även om ett nytt konto skapas automatiskt när en ny användare loggar in med AAD B2C, kan du överväga att lägga till samma widget på registrerings sidan.

**Registrerings formuläret: OAuth** widget representerar ett formulär som används för att registrera sig med OAuth.

> [!IMPORTANT]
> Du måste [publicera portalen](api-management-howto-developer-portal-customize.md#publish) igen för att ändringarna i AAD ska börja gälla.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Äldre utvecklares Portal – så här registrerar du dig med Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Om du vill registrera ett konto för utvecklare med hjälp av Azure Active Directory B2C öppnar du ett nytt webbläsarfönster och går till Developer-portalen. Klicka på knappen **Registrera dig** .

   ![Utvecklings Portal 1][api-management-howto-aad-b2c-dev-portal]

2. Välj att registrera dig med **Azure Active Directory B2C**.

   ![Utvecklings Portal 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Du omdirigeras till den registrerings princip som du konfigurerade i föregående avsnitt. Välj att registrera dig med din e-postadress eller något av dina befintliga sociala konton.

   > [!NOTE]
   > Om Azure Active Directory B2C är det enda alternativet som är aktiverat på fliken **identiteter** i utgivar portalen, omdirigeras du till registrerings principen direkt.

   ![Utvecklarportalen][api-management-howto-aad-b2c-dev-portal-b2c-options]

   När registreringen är slutförd omdirigeras du tillbaka till Developer-portalen. Du är nu inloggad på Developer-portalen för din API Management tjänst instans.

    ![Registreringen är klar][api-management-registration-complete]

## <a name="next-steps"></a>Nästa steg

*  [Översikt över Azure Active Directory B2C]
*  [Azure Active Directory B2C: utöknings Bart princip ramverk]
*  [Använd en Microsoft-konto som identitets leverantör i Azure Active Directory B2C]
*  [Använd ett Google-konto som identitets leverantör i Azure Active Directory B2C]
*  [Använd ett LinkedIn-konto som identitets leverantör i Azure Active Directory B2C]
*  [Använd ett Facebook-konto som identitets leverantör i Azure Active Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Översikt över Azure Active Directory B2C]: ../active-directory-b2c/overview.md
[Så här auktoriserar du utvecklares konton med Azure Active Directory]: ./api-management-howto-aad.md
[Azure Active Directory B2C: utöknings Bart princip ramverk]: ../active-directory-b2c/user-flow-overview.md
[Använd en Microsoft-konto som identitets leverantör i Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-microsoft-account.md
[Använd ett Google-konto som identitets leverantör i Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-google.md
[Använd ett Facebook-konto som identitets leverantör i Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-facebook.md
[Använd ett LinkedIn-konto som identitets leverantör i Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
