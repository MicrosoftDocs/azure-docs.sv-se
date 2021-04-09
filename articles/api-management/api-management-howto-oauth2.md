---
title: Auktorisera utvecklares konton med OAuth 2,0 i API Management
titleSuffix: Azure API Management
description: 'Lär dig hur du auktoriserar användare som använder OAuth 2,0 i API Management. OAuth 2,0 skyddar API: et så att användarna endast kan komma åt resurser som de är berättigade till.'
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/14/2020
ms.author: apimpm
ms.openlocfilehash: fae4e349d46425c0c2b2b923d6a61e2e588708c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93077259"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Så här auktoriserar du utvecklares konton med OAuth 2,0 i Azure API Management

Många API: er stöder [OAuth 2,0](https://oauth.net/2/) för att skydda API: et och se till att endast giltiga användare har åtkomst och att de endast kan komma åt resurser som de är berättigade till. För att kunna använda Azure API Managements interaktiva utvecklares konsol med sådana API: er kan du konfigurera tjänst instansen så att den fungerar med ditt OAuth 2,0-aktiverade API.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Förutsättningar

Den här guiden visar hur du konfigurerar API Management tjänst instansen att använda OAuth 2,0-auktorisering för utvecklarresurser, men visar inte hur du konfigurerar en OAuth 2,0-Provider. Konfigurationen för varje OAuth 2,0-Provider är annorlunda, även om stegen är likartade, och de nödvändiga delarna av information som används för att konfigurera OAuth 2,0 i API Management tjänst instansen är desamma. Det här avsnittet innehåller exempel som använder Azure Active Directory som en OAuth 2,0-Provider.

> [!NOTE]
> Mer information om hur du konfigurerar OAuth 2,0 med hjälp av Azure Active Directory finns i exemplet [webapp-GraphAPI-dotNet][WebApp-GraphAPI-DotNet] .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>Konfigurera en OAuth 2,0-auktoriseringsservern i API Management

> [!NOTE]
> Om du ännu inte har skapat en API Management tjänst instans, se [skapa en API Management tjänst instans][Create an API Management service instance].

1. Klicka på fliken OAuth 2,0 i menyn till vänster och klicka på **+ Lägg till**.

    ![OAuth 2,0-menyn](./media/api-management-howto-oauth2/oauth-01.png)

2. Ange ett namn och en valfri beskrivning i fälten **namn** och **Beskrivning** .

    > [!NOTE]
    > De här fälten används för att identifiera OAuth 2,0-auktoriseringsservern i den aktuella API Management tjänst instansen och deras värden kommer inte från OAuth 2,0-servern.

3. Ange **URL för klient registrerings sidan**. Den här sidan är den plats där användare kan skapa och hantera sina konton och varierar beroende på vilken OAuth 2,0-provider som används. **URL: en för klient registrerings sidan** pekar på sidan som användarna kan använda för att skapa och konfigurera sina egna konton för OAuth 2,0-leverantörer som stöder användar hantering av konton, till exempel `https://contoso.com/login` . Vissa organisationer konfigurerar eller använder inte den här funktionen även om OAuth 2,0-providern stöder det. Om din OAuth 2,0-Provider inte har användar hantering av konfigurerade konton anger du en URL för plats hållare, till exempel URL: en för ditt företag eller en URL som `https://placeholder.contoso.com` .

    ![OAuth 2,0 ny server](./media/api-management-howto-oauth2/oauth-02.png)

4. Nästa avsnitt i formuläret innehåller **behörighets typerna**, **URL: en för auktoriserings slut punkt** och **metod** inställningar för auktorisering.

    Ange de **typer av auktorisering** som ska beviljas genom att kontrol lera de önskade typerna. **Auktoriseringskod** anges som standard.

    Ange **slut punktens URL för auktorisering**. För Azure Active Directory kommer den här URL: en att likna följande URL, där `<tenant_id>` ersätts med ID: t för din Azure AD-klient.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    **Metoden för begäran om auktorisering** anger hur auktoriseringsbegäran skickas till OAuth 2,0-servern. Som standard väljs **Hämta** .

5. Sedan måste du ange **token-slutpunkt-URL**, **klientens autentiseringsmetoder** och **Skicka metod för åtkomst-token** och **standard omfång** .

    ![Skärm bild som visar skärmen Lägg till OAuth2-tjänst.](./media/api-management-howto-oauth2/oauth-03.png)

    För en Azure Active Directory OAuth 2,0-server har **URL: en för token-slutpunkt** följande format, där `<TenantID>`  har formatet `yourapp.onmicrosoft.com` .

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Standardvärdet för **klientautentisering** är **Basic**, och  **sändnings metoden för åtkomst-token** är **Authorization-huvud**. Dessa värden konfigureras i det här avsnittet av formuläret, tillsammans med **standard omfånget**.

6. Avsnittet **klientautentiseringsuppgifter** innehåller **klient-ID** och **klient hemlighet**, som erhålls när du skapar och konfigurerar en OAuth 2,0-servers process. När **klient-ID** och **klient hemlighet** har angetts genereras **redirect_uri** för **auktoriseringskod** . Denna URI används för att konfigurera svars-URL: en i konfigurationen för OAuth 2,0-servern.

    I den nya Developer-portalen är URI-suffixet av form:

    - `/signin-oauth/code/callback/{authServerName}` för utfärdande flöde för auktoriseringskod
    - `/signin-oauth/implicit/callback` för implicit beviljande flöde

    ![Skärm bild som visar var du kan lägga till klientautentiseringsuppgifterna för den nya OAuth2-tjänsten.](./media/api-management-howto-oauth2/oauth-04.png)

    Om **Authorization Grant-typer** har angetts till **resurs ägar lösen ord**, används avsnittet **autentiseringsuppgifter för resurs ägare** för att ange dessa autentiseringsuppgifter. Annars kan du lämna det tomt.

    När formuläret har slutförts klickar du på **skapa** för att spara API Management OAuth 2,0-Authorization Server-konfigurationen. När du har sparat Server konfigurationen kan du konfigurera API: er för att använda den här konfigurationen, som du ser i nästa avsnitt.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>Konfigurera ett API för att använda OAuth 2,0-användarauktorisering

1. Klicka på **API: er** på **API Management** menyn till vänster.

    ![OAuth 2,0-API: er](./media/api-management-howto-oauth2/oauth-05.png)

2. Klicka på namnet på önskat API och klicka på **Inställningar**. Bläddra till avsnittet **säkerhet** och markera sedan kryss rutan för **OAuth 2,0**.

    ![OAuth 2,0-inställningar](./media/api-management-howto-oauth2/oauth-06.png)

3. Välj önskad **auktoriseringsbegäran** i list rutan och klicka på **Spara**.

    ![Skärm bild som markerar den valda auktoriseringsservern och knappen Spara.](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Äldre utvecklare-Portal – testa OAuth 2,0-användarauktorisering

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

När du har konfigurerat OAuth 2,0-auktoriseringsservern och konfigurerat ditt API för att använda den servern kan du testa den genom att gå till Developer-portalen och anropa ett API. Klicka på **Developer portal (bakåtkompatibelt)** på den översta menyn från **översikts** sidan för Azure API Management-instansen.

Klicka på **API: er** på den översta menyn och välj **ECHO API**.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Om du bara har ett API som är konfigurerat eller som visas för ditt konto och du klickar på API:er så kommer du direkt till åtgärderna för det API:et.

Välj åtgärden **Hämta resurs** , klicka på **Öppna konsol** och välj sedan **auktoriseringskod** i list rutan.

![Öppna konsolen][api-management-open-console]

När du väljer **auktoriseringskod** visas ett popup-fönster med inloggnings formatet för OAuth 2,0-providern. I det här exemplet tillhandahålls inloggnings formuläret av Azure Active Directory.

> [!NOTE]
> Om du har aktiverat popup-fönster uppmanas du att aktivera dem i webbläsaren. När du har aktiverat dem väljer du **auktoriseringskod** igen så visas inloggnings formuläret.

![Logga in][api-management-oauth2-signin]

När du har loggat in fylls **begärandehuvuden i** med ett `Authorization : Bearer` huvud som godkänner begäran.

![Token för begäran-huvud][api-management-request-header-token]

Nu kan du konfigurera de önskade värdena för de återstående parametrarna och skicka begäran.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder OAuth 2,0 och API Management finns i följande video och medföljande [artikel](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps
