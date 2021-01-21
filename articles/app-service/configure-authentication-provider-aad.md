---
title: Konfigurera Azure AD-autentisering
description: Lär dig hur du konfigurerar Azure Active Directory-autentisering som identitets leverantör för din App Service-eller Azure Functions-app.
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.topic: article
ms.date: 04/14/2020
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 3d1e0eb90005abf69d90b46acc59e0258c9914c6
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630038"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-azure-ad-login"></a>Konfigurera din App Service-eller Azure Functions-app för att använda Azure AD-inloggning

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Den här artikeln visar hur du konfigurerar Azure App Service eller Azure Functions att använda Azure Active Directory (Azure AD) som autentiseringsprovider.

> [!NOTE]
> Med Express Settings Flow konfigurerar du en AAD v1-programregistrering. Om du vill använda [Azure Active Directory v 2.0](../active-directory/develop/v2-overview.md) (inklusive [MSAL](../active-directory/develop/msal-overview.md)) följer du [anvisningarna för Avancerad konfiguration](#advanced).

Följ dessa rekommendationer när du konfigurerar din app och autentisering:

- Ge varje App Service app sina egna behörigheter och sitt medgivande.
- Konfigurera varje App Service-app med en egen registrering.
- Undvik att dela behörighet mellan miljöer genom att använda separata registrerings program för olika distributions platser. När du testar ny kod kan den här övningen hjälpa till att förhindra att problem påverkar produktions programmet.

> [!NOTE]
> Den här funktionen är för närvarande inte tillgänglig i användnings planen för Linux för Azure Functions

## <a name="configure-with-express-settings"></a><a name="express"> </a>Konfigurera med Express inställningar

> [!NOTE]
> **Express** alternativet är inte tillgängligt för offentliga moln.

1. I [Azure Portal]söker du efter och väljer **app Services** och väljer sedan din app.
2. Välj **autentisering/auktorisering** på i det vänstra navigerings fältet  >  .
3. Välj **Azure Active Directory**  >  **Express**.

   Om du vill välja en befintlig app-registrering i stället:

   1. Välj **Välj befintlig AD-App** och klicka sedan på **Azure AD App**.
   2. Välj en befintlig app-registrering och klicka på **OK**.

4. Välj **OK** för att registrera App Service-appen i Azure Active Directory. En ny app-registrering skapas.

    ![Express inställningar i Azure Active Directory](./media/configure-authentication-provider-aad/express-settings.png)

5. Valfritt Som standard tillhandahåller App Service autentisering, men begränsar inte tillåten åtkomst till webbplatsens innehåll och API: er. Du måste auktorisera användare i din app-kod. Om du vill begränsa åtkomsten till appar enbart till användare som autentiserats av Azure Active Directory anger du **åtgärd som ska vidtas när begäran inte autentiseras** att **logga in med Azure Active Directory**. När du ställer in den här funktionen kräver appen att alla begär Anden ska autentiseras. Det omdirigerar också all oautentiserad till Azure Active Directory för autentisering.

    > [!CAUTION]
    > Att begränsa åtkomsten på det här sättet gäller alla anrop till appen, vilket kanske inte är önskvärt för appar som har en offentligt tillgänglig start sida, som i många program med en enda sida. För sådana program kan du **tillåta anonyma begär Anden (ingen åtgärd)** , med appen manuellt startar inloggningen. Mer information finns i [Authentication Flow](overview-authentication-authorization.md#authentication-flow).
6. Välj **Spara**.

Ett exempel på hur du konfigurerar Azure AD-inloggning för en webbapp som har åtkomst till Azure Storage och Microsoft Graph finns i [den här självstudien](scenario-secure-app-authentication-app-service.md).

## <a name="configure-with-advanced-settings"></a><a name="advanced"> </a>Konfigurera med avancerade inställningar

Du kan konfigurera inställningar för appar manuellt om du vill använda en app-registrering från en annan Azure AD-klient. För att slutföra den här anpassade konfigurationen:

1. Skapa en registrering i Azure AD.
2. Ange en del av registrerings informationen för att App Service.

### <a name="create-an-app-registration-in-azure-ad-for-your-app-service-app"></a><a name="register"> </a>Skapa en app-registrering i Azure AD för din app service-app

Du behöver följande information när du konfigurerar din App Service-app:

- Klient-ID
- Klientorganisations-ID
- Klient hemlighet (valfritt)
- Program-ID-URI

Utför följande steg:

1. Logga in på [Azure Portal], Sök efter och välj **app Services** och välj sedan din app. Anteckna appens **URL**. Du använder den för att konfigurera din Azure Active Directory app-registrering.
1. Välj **Azure Active Directory**  >  **Appregistreringar**  >  **ny registrering**.
1. På sidan **Registrera ett program** anger du ett **namn** för din app-registrering.
1. I **omdirigerings-URI** väljer du **webb** och typ `<app-url>/.auth/login/aad/callback` . Ett exempel är `https://contoso.azurewebsites.net/.auth/login/aad/callback`.
1. Välj **register**.
1. När appens registrering har skapats kopierar du **program-ID: t** och **katalogen (klient)-ID:** t för senare.
1. Välj **Autentisering**. Under **implicit beviljande**, aktiverar du **ID-token** för att tillåta OpenID Connect-användarkonton från App Service.
1. Valfritt Välj **anpassning**. På **Start sidans URL** anger du URL: en för din app service-app och väljer **Spara**.
1. Välj **exponera en API**-  >  **uppsättning**. För en app med en klient, klistra in URL: en för din App Service-app och välj **Spara** och för appar för flera klient organisationer klistrar du in webb adressen som baseras på en av klientens verifierade domäner och väljer sedan **Spara**.

   > [!NOTE]
   > Det här värdet är **program-ID-URI: n** för appens registrering. Om din webbapp kräver åtkomst till ett API i molnet behöver du **program-ID-URI: n** för webbappen när du konfigurerar Cloud App Service-resursen. Du kan använda detta, till exempel om du vill att moln tjänsten uttryckligen ska bevilja åtkomst till webbappen.

1. Välj **Lägg till omfång**.
   1. I **omfångs namn** anger du *user_impersonation*.
   1. I text rutorna anger du namn och beskrivning för medgivande omfånget som du vill att användarna ska se på sidan för medgivande. Ange till exempel *åtkomst till min app*.
   1. Välj **Lägg till omfattning**.
1. Valfritt Om du vill skapa en klient hemlighet väljer du **certifikat & hemligheter**  >  **ny klient hemlighet**  >  **Lägg till**. Kopiera klientens hemliga värde som visas på sidan. Den visas inte igen.
1. Valfritt Om du vill lägga till flera **svars-URL: er** väljer du **autentisering**.

### <a name="enable-azure-active-directory-in-your-app-service-app"></a><a name="secrets"> </a>Aktivera Azure Active Directory i App Service-appen

1. I [Azure Portal]söker du efter och väljer **app Services** och väljer sedan din app.
1. Välj **autentisering/auktorisering** på i den vänstra rutan under **Inställningar**  >  .
1. Valfritt Som standard tillåter App Service autentisering oautentiserad åtkomst till din app. Om du vill framtvinga användarautentisering anger du **åtgärden som ska vidtas när begäran inte autentiseras** att **logga in med Azure Active Directory**.
1. Under **autentiseringsproviders** väljer du **Azure Active Directory**.
1. I **hanterings läge** väljer du **avancerat** och konfigurerar app service autentisering enligt följande tabell:

    |Fält|Beskrivning|
    |-|-|
    |Klient-ID| Använd **program-ID: t (klient)** för appens registrering. |
    |Utfärdar-URL| Använd `<authentication-endpoint>/<tenant-id>/v2.0` och Ersätt *\<authentication-endpoint>* med [slut punkten för autentisering för din moln miljö](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (t. ex. " https://login.microsoftonline.com " för Global Azure), och ersätt även *\<tenant-id>* med den **katalog (klient) ID** där appens registrering skapades. Det här värdet används för att omdirigera användare till rätt Azure AD-klient, samt för att hämta lämpliga metadata för att fastställa lämpliga token för signerings nycklar och token Issuer-anspråk till exempel. För program som använder Azure AD v1 och för Azure Functions appar, utelämna `/v2.0` i URL: en.|
    |Klient hemlighet (valfritt)| Använd den klient hemlighet som du genererade i appens registrering.|
    |Tillåtna token-mottagare| Om det här är en moln-eller Server App och du vill tillåta autentiseringstoken från en webbapp lägger du till **program-ID-URI: n** för webbappen här. Det konfigurerade **klient-ID: t** anses *alltid* vara en tillåten mål grupp. |

2. Välj **OK** och välj sedan **Spara**.

Du är nu redo att använda Azure Active Directory för autentisering i din App Service-app.

## <a name="configure-a-native-client-application"></a>Konfigurera ett internt klient program

Du kan registrera interna klienter för att tillåta autentisering till webb-API: n som finns i din app med hjälp av ett klient bibliotek som **Active Directory-autentiseringsbibliotek**.

1. I [Azure Portal]väljer du **Active Directory**  >  **Appregistreringar**  >  **ny registrering**.
1. På sidan **Registrera ett program** anger du ett **namn** för din app-registrering.
1. I **omdirigerings-URI** väljer du **offentlig klient (mobil & Desktop)** och anger URL: en `<app-url>/.auth/login/aad/callback` . Ett exempel är `https://contoso.azurewebsites.net/.auth/login/aad/callback`.

    > [!NOTE]
    > För ett Microsoft Store-program använder du [paket-sid](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#package-sid) som URI i stället.
1. Välj **Skapa**.
1. När appens registrering har skapats kopierar du värdet för **program-ID (klient)**.
1. Välj **API-behörigheter**  >  **Lägg till en behörighet**  >  **Mina API: er**.
1. Välj den app-registrering som du skapade tidigare för din App Service-app. Om du inte ser appens registrering ser du till att du har lagt till **user_impersonation** omfattning i [skapa en app-registrering i Azure AD för din app service-app](#register).
1. Under **delegerade behörigheter** väljer du **user_impersonation** och väljer sedan **Lägg till behörigheter**.

Nu har du konfigurerat ett internt klient program som kan komma åt din App Service-app för en användares räkning.

## <a name="configure-a-daemon-client-application-for-service-to-service-calls"></a>Konfigurera ett daemon-klientprogram för tjänst-till-tjänst-anrop

Ditt program kan hämta en token för att anropa ett webb-API som finns i din App Service-eller Function-app åt sig själv (inte för en användares räkning). Det här scenariot är användbart för icke-interaktiva daemon-program som utför uppgifter utan en inloggad användare. Den använder standard beviljast OAuth 2,0 [-klientautentiseringsuppgifter.](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)

1. I [Azure Portal]väljer du **Active Directory**  >  **Appregistreringar**  >  **ny registrering**.
1. På sidan **Registrera ett program** anger du ett **namn** för daemon-appens registrering.
1. För ett daemon-program behöver du inte en omdirigerings-URI så att du kan behålla detta tomt.
1. Välj **Skapa**.
1. När appens registrering har skapats kopierar du värdet för **program-ID (klient)**.
1. Välj **certifikat & hemligheter**  >  **ny klient hemlighet**  >  **Lägg till**. Kopiera klientens hemliga värde som visas på sidan. Den visas inte igen.

Du kan nu [begära en åtkomsttoken med hjälp av klient-ID och klient hemlighet](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) genom `resource` att ange parametern till **program-ID-URI: n** för mål programmet. Den resulterande åtkomsttoken kan sedan visas för mål appen med hjälp av standard- [OAuth 2,0-Authorization-huvudet](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#use-the-access-token-to-access-the-secured-resource)och App Service autentisering/auktorisering validerar och använder token som vanligt för att ange att anroparen (ett program i det här fallet, inte en användare) autentiseras.

Detta gör att _alla_ klient program i Azure AD-klienten kan begära en åtkomsttoken och autentisera till mål programmet. Om du även vill framtvinga _behörighet_ att endast tillåta vissa klient program måste du utföra en del ytterligare konfiguration.

1. [Definiera en app-roll](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) i manifestet för den app-registrering som representerar den app service eller Function-app som du vill skydda.
1. På den app-registrering som representerar klienten som måste auktoriseras väljer du **API-behörigheter**  >  **Lägg till en behörighet**  >  **Mina API: er**.
1. Välj den app-registrering som du skapade tidigare. Om du inte ser appens registrering ser du till att du har [lagt till en app-roll](../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md).
1. Under **program behörigheter** väljer du den app-roll som du skapade tidigare och väljer sedan **Lägg till behörigheter**.
1. Se till att klicka på **bevilja administratörs medgivande** för att auktorisera klient programmet för att begära behörighet.
1. Precis som i föregående scenario (innan några roller lades till) kan du nu [begära en åtkomsttoken](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#first-case-access-token-request-with-a-shared-secret) för samma mål `resource` , och åtkomsttoken innehåller ett `roles` anspråk som innehåller de app-roller som har auktoriserats för klient programmet.
1. I appens mål App Service-eller Function-kod kan du nu kontrol lera att de förväntade rollerna finns i token (detta utförs inte av App Service autentisering/auktorisering). Mer information finns i [användar anspråk för åtkomst](app-service-authentication-how-to.md#access-user-claims).

Nu har du konfigurerat ett daemon-klientcertifikat som kan komma åt din App Service-app med sin egen identitet.

## <a name="next-steps"></a><a name="related-content"> </a>Nästa steg

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
* [Självstudie: autentisera och auktorisera användare i en webbapp som har åtkomst till Azure Storage och Microsoft Graph](scenario-secure-app-authentication-app-service.md)
* [Självstudie: Autentisera och auktorisera användare från slutpunkt till slutpunkt i Azure App Service](tutorial-auth-aad.md)
<!-- URLs. -->

[Azure-portalen]: https://portal.azure.com/
