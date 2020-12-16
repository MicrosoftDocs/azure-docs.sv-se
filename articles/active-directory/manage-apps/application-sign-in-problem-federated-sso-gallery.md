---
title: Problem med att logga in på SAML-baserade appar som konfigurerats för enkel inloggning
description: Vägledning för de specifika felen vid inloggning i ett program som du har konfigurerat för SAML-baserad federerad enkel inloggning med Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperf-fy21q2
ms.openlocfilehash: 596af29fe72a41b5a86a09e4e6d5072d63b6ac71
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586354"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>Problem med att logga in på SAML-baserade appar som konfigurerats för enkel inloggning
För att felsöka inloggnings problemen nedan rekommenderar vi följande för att bättre diagnostisera och automatisera lösnings stegen:

- Installera [tillägget säker webbläsare för mina appar](./access-panel-deployment-plan.md) för att hjälpa Azure Active Directory (Azure AD) att ge bättre diagnoser och lösningar när du använder testmiljön i Azure Portal.
- Återskapa felet med hjälp av testfunktionen på appkonfigurationssidan i Azure Portal. Läs mer om [Debug SAML-baserade enkla inloggnings program](./debug-saml-sso-issues.md)

Om du använder [test upplevelsen](./debug-saml-sso-issues.md) i Azure Portal med tillägget mina appars säkra webbläsare, behöver du inte manuellt följa stegen nedan för att öppna konfigurations sidan för SAML-baserad enkel inloggning.

Så här öppnar du konfigurationssidan för SAML-baserad enkel inloggning:
1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **administratör**.
1.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
1.  Skriv **"Azure Active Directory"** i rutan filtrera sökning och välj **Azure Active Directory** objektet.
1.  Välj **Företagsprogram** på den vänstra navigeringsmenyn i Azure Active Directory.
1.  Välj **Alla program** för att visa en lista över alla dina program.

    Om du inte ser programmet du behöver kan du använda **filterkontrollen** överst i **listan med alla program** och ställa in alternativet **Visa** på **Alla program**.

1.  Välj programmet du vill konfigurera för enkel inloggning.
1. När programmet har lästs in väljer du **enkel inloggning** från programmets vänstra navigerings meny.
1. Välj SAML-baserad enkel inloggning.

## <a name="application-not-found-in-directory"></a>Programmet hittades inte i katalogen
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**Möjlig orsak**

`Issuer`Attributet som skickas från programmet till Azure AD i SAML-begäran matchar inte det ID-värde som har kon figurer ATS för programmet i Azure AD.

**Lösning**

Kontrol lera att `Issuer` attributet i SAML-begäran matchar det ID-värde som kon figurer ATS i Azure AD.

På sidan SAML-baserad SSO-konfiguration, i avsnittet **grundläggande SAML-konfiguration** , kontrollerar du att värdet i text rutan identifierare matchar värdet för det ID-värde som visas i felet.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Svars adressen stämmer inte överens med de svars adresser som har kon figurer ATS för programmet
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

**Möjlig orsak**

`AssertionConsumerServiceURL`Värdet i SAML-begäran matchar inte svars-URL-värdet eller det mönster som kon figurer ATS i Azure AD. `AssertionConsumerServiceURL`Värdet i SAML-begäran är den URL som visas i fel meddelandet.

**Lösning**

Se till att `AssertionConsumerServiceURL` värdet i SAML-begäran matchar svars-URL-värdet som kon figurer ATS i Azure AD. 

Verifiera eller uppdatera värdet i text rutan svars-URL så att det matchar `AssertionConsumerServiceURL` värdet i SAML-begäran.   

När du har uppdaterat svars-URL-värdet i Azure AD och det matchar värdet som skickas av programmet i SAML-begäran, bör du kunna logga in på programmet.

## <a name="user-not-assigned-a-role"></a>Användaren har inte tilldelats någon roll
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**Möjlig orsak**

Användaren har inte beviljats åtkomst till programmet i Azure AD.

**Lösning**

Om du vill tilldela en eller flera användare till ett program direkt, se [snabb start: tilldela användare till en app](add-application-portal-assign-users.md).

## <a name="not-a-valid-saml-request"></a>Inte en giltig SAML-begäran
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**Möjlig orsak**

Azure AD stöder inte den SAML-begäran som skickas av programmet för enkel inloggning. Några vanliga problem är:
- Obligatoriska fält som saknas i SAML-begäran
- SAML-begärandekodad metod

**Lösning**

1. Avbilda SAML-begäran. Följ självstudien [för att FELSÖKA SAML-baserad enkel inloggning till program i Azure AD](./debug-saml-sso-issues.md) för att lära dig hur du samlar in SAML-begäran.
1. Kontakta programvaruleverantören och dela följande information:
    - SAML-begäran
    - [Krav för SAML-protokoll för enkel inloggning i Azure AD](../develop/single-sign-on-saml-protocol.md)

Program leverantören bör kontrol lera att de stöder Azure AD SAML-implementering för enkel inloggning.

## <a name="misconfigured-application"></a>Felkonfigurerat program
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**Möjlig orsak**

`Issuer`Attributet som skickas från programmet till Azure AD i SAML-begäran matchar inte det ID-värde som har kon figurer ATS för programmet i Azure AD.

**Lösning**

Kontrol lera att `Issuer` attributet i SAML-begäran matchar det ID-värde som kon figurer ATS i Azure AD. 

Kontrol lera att värdet i text rutan identifierare matchar värdet för det ID-värde som visas i fel meddelandet.

## <a name="certificate-or-key-not-configured"></a>Certifikatet eller nyckeln har inte kon figurer ATS
`Error AADSTS50003: No signing key configured.`

**Möjlig orsak**

Programobjektet är skadat och Azure AD känner inte igen det certifikat som har kon figurer ATS för programmet.

**Lösning**

Följ stegen nedan om du vill ta bort och skapa ett nytt certifikat:
1. På skärmen SAML-baserad SSO-konfiguration väljer du **Skapa nytt certifikat** under avsnittet **SAML-signerings certifikat** .
1. Välj förfallo datum och klicka sedan på **Spara**.
1. Kontrol lera att det **nya certifikatet är aktivt** för att åsidosätta det aktiva certifikatet. Klicka sedan på **Spara** överst i fönstret och acceptera för att aktivera förnyelsecertifikatet.
1. Under avsnittet **SAML-signerings certifikat** klickar du på **ta bort** för att ta bort det **oanvända** certifikatet.

## <a name="saml-request-not-present-in-the-request"></a>SAML-begäran saknas i begäran
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**Möjlig orsak**

Azure AD kunde inte identifiera SAML-begäran inom URL-parametrarna i HTTP-begäran. Detta kan inträffa om programmet inte använder HTTP-omdirigering när SAML-begäran skickas till Azure AD.

**Lösning**

Programmet måste skicka SAML-begäran kodad till plats rubriken med hjälp av HTTP-omdirigering. Mer information om hur du implementerar det finns i avsnittet om HTTP-omdirigeringsbindning i [dokumentet om SAML-protokollets specifikationer](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD skickar token till en felaktig slut punkt
**Möjlig orsak**

Om inloggnings förfrågan inte innehåller en explicit svars-URL (intygs-URL) under enkel inloggning, kommer Azure AD att välja någon av de konfigurerade svars-URL: erna för programmet. Även om programmet har en explicit svars-URL konfigurerad, kan användaren vara Omdirigerad https://127.0.0.1:444 . 

När programmet lades till som en icke-galleriapp skapade Azure Active Directory den här svars-URL:en som ett standardvärde. Det här beteendet har ändrats och Azure Active Directory lägger inte längre till den här URL:en som standard. 

**Lösning**

Ta bort de oanvända svars-URL: erna som kon figurer ATS för programmet.

På sidan SAML-baserad SSO-konfiguration, i avsnittet **svars-URL för försäkrans konsument tjänst** , tar du bort oanvända eller vanliga svars-URL: er som skapats av systemet. Ett exempel är `https://127.0.0.1:444/applications/default.aspx`.


## <a name="authentication-method-by-which-the-user-authenticated-with-the-service-doesnt-match-requested-authentication-method"></a>Autentiseringsmetoden med vilken användaren autentiseras med tjänsten matchar inte den begärda autentiseringsmetoden
`Error: AADSTS75011 Authentication method by which the user authenticated with the service doesn't match requested authentication method 'AuthnContextClassRef'. `

**Möjlig orsak**

`RequestedAuthnContext`Är i SAML-begäran. Det innebär att appen förväntar sig den `AuthnContext` som anges av `AuthnContextClassRef` . Användaren har dock redan autentiserats innan åtkomst till programmet och `AuthnContext` (autentiseringsmetoden) som används för den tidigare autentiseringen skiljer sig från den som begärs. Till exempel har en federerad användare åtkomst till Mina appar och WIA. `AuthnContextClassRef`Blir `urn:federation:authentication:windows` . AAD utför inte en ny autentiseringsbegäran, den använder den autentisering som skickades genom IdP (ADFS eller någon annan Federations tjänst i det här fallet). Därför är det ett matchnings fel om appen begär andra än `urn:federation:authentication:windows` . Ett annat scenario är när multifaktorn användes: `'X509, MultiFactor` .

**Lösning**


`RequestedAuthnContext` är ett valfritt värde. Sedan, om möjligt, fråga programmet om det kan tas bort.

Ett annat alternativ är att se till att det `RequestedAuthnContext` kommer att användas. Detta görs genom att begära en ny autentisering. Genom att göra detta när SAML-begäran bearbetas utförs en ny autentisering och `AuthnContext` kommer att användas. Om du vill begära en ny autentisering innehåller SAML-begäran mest värdet `forceAuthn="true"` . 



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem vid anpassning av SAML-anspråk som skickas till ett program
Information om hur du anpassar SAML-attributets anspråk som skickas till programmet finns i [anspråks mappning i Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="errors-related-to-misconfigured-apps"></a>Fel som rör felkonfigurerade appar
Kontrol lera att båda konfigurationerna i portalen matchar det du har i din app. Mer specifikt jämför klient/program-ID, svars-URL: er, klient hemligheter/nycklar och app-ID-URI.

Jämför resursen som du begär åtkomst till i kod med de konfigurerade behörigheterna på fliken **obligatoriska resurser** för att se till att du endast begär resurser som du har konfigurerat.

## <a name="next-steps"></a>Nästa steg
- [Snabb starts serie för program hantering](add-application-portal-assign-users.md)
- [Felsöka SAML-baserad enkel inloggning till program i Azure AD](./debug-saml-sso-issues.md)
- [Krav för SAML-protokoll för enkel inloggning i Azure AD](../develop/single-sign-on-saml-protocol.md)