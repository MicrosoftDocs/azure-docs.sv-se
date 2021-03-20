---
title: Bygg appar som loggar in Azure AD-användare
titleSuffix: Microsoft identity platform
description: Visar hur du skapar ett program med flera innehavare som kan logga in en användare från en Azure Active Directory klient.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: marsma, jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 825a7d8c53552120a861657c7f3df7ae8f488c18
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581728"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Anvisningar: Loggar in valfri Azure Active Directory-användare med programmönstret för flera klienter

Om du erbjuder ett SaaS-program (program vara som en tjänst) till många organisationer kan du konfigurera ditt program så att det accepterar inloggningar från valfri Azure Active Directory (Azure AD)-klient. Den här konfigurationen kallas *att göra programmet till flera klienter*. Användare i en Azure AD-klient kommer att kunna logga in till ditt program när de har samtyckt till att använda sitt konto med ditt program.

Om du har ett befintligt program som har ett eget konto system, eller stöder andra typer av inloggningar från andra moln leverantörer, är det enkelt att lägga till Azure AD-inloggning från en klient. Registrera bara din app, Lägg till inloggnings kod via OAuth2, OpenID Connect eller SAML och Lägg till [knappen "logga in med Microsoft"][AAD-App-Branding] i ditt program.

> [!NOTE]
> Den här artikeln förutsätter att du redan är bekant med att skapa ett program för en klient organisation för Azure AD. Om du inte är det börjar du med en av snabb starterna på [Start sidan för Developer Guide][AAD-Dev-Guide].

Det finns fyra steg för att konvertera ditt program till en Azure AD-App för flera klienter:

1. [Uppdatera program registreringen så att den blir flera innehavare](#update-registration-to-be-multi-tenant)
2. [Uppdatera din kod för att skicka begär anden till/vanliga-slutpunkten](#update-your-code-to-send-requests-to-common)
3. [Uppdatera din kod för att hantera flera Issuer-värden](#update-your-code-to-handle-multiple-issuer-values)
4. [Förstå användar-och administratörs medgivande och gör lämpliga kod ändringar](#understand-user-and-admin-consent)

Nu ska vi titta närmare på varje steg. Du kan också gå direkt till exemplet och [bygga ett SaaS-webbprogram för flera innehavare som anropar Microsoft Graph att använda Azure AD och OpenID Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Uppdatera registreringen till att vara flera innehavare

Som standard är Web App/API-registreringar i Azure AD en enda klient. Du kan registrera flera klient organisationer genom att söka efter de **konto typer som stöds** i fönstret **verifiering** i program registreringen i [Azure Portal][AZURE-portal] och ange dem som **konton i en organisations katalog**.

Innan ett program kan göras till flera klienter kräver Azure AD att app-ID-URI: n för programmet är globalt unik. App-ID-URI är en av de sätt som ett program identifieras i protokollmeddelanden. För ett program för en enskild klientorganisation räcker det att app-ID-URI är unikt i den klientorganisationen. För ett program för flera klientorganisationer måste den vara globalt unikt så att Azure AD kan hitta programmet bland alla klientorganisationer. Global unikhet framtvingas genom att det krävs att app-ID-URI har ett värdnamn som matchar en verifierad domän i Azure AD-klientorganisationen.

Som standard har appar som skapats via Azure Portal en globalt unik app-ID-URI inställd på att skapa appar, men du kan ändra det här värdet. Om namnet på din klient till exempel var contoso.onmicrosoft.com, skulle en giltig app-ID-URI vara `https://contoso.onmicrosoft.com/myapp` . Om klienten hade en verifierad domän för `contoso.com` , skulle en giltig app-ID-URI också vara `https://contoso.com/myapp` . Om App-ID-URI inte följer detta mönster misslyckas konfigurationen av ett program som ett program för flera klientorganisationer.

## <a name="update-your-code-to-send-requests-to-common"></a>Uppdatera din kod för att skicka begär anden till/vanliga

I ett program med en enda klient organisation skickas inloggnings förfrågningar till klient organisationens inloggnings slut punkt. För contoso.onmicrosoft.com skulle till exempel slut punkten bli: `https://login.microsoftonline.com/contoso.onmicrosoft.com` . Begär Anden som skickas till en klients slut punkt kan logga in användare (eller gäster) i den klienten till program i den klient organisationen.

Med ett program med flera klient organisationer vet inte programmet var den klient som användaren är från, så du kan inte skicka begär anden till en innehavares slut punkt. I stället skickas begär anden till en slut punkt som flera plexar över alla Azure AD-klienter: `https://login.microsoftonline.com/common`

När Microsoft Identity Platform tar emot en begäran på/vanliga-slutpunkten loggar den in användaren och, som en följd, identifierar vilken klient som användaren är från. /Vanliga-slutpunkten fungerar med alla autentiseringsprotokoll som stöds av Azure AD: OpenID Connect, OAuth 2,0, SAML 2,0 och WS-Federation.

Inloggnings svaret till programmet innehåller sedan en token som representerar användaren. Utfärdarens värde i token anger ett program som klienten som användaren kommer från. När ett svar returneras från/vanliga-slutpunkten motsvarar utfärdarens värde i token användarens klient.

> [!IMPORTANT]
> /Vanliga-slutpunkten är inte en klient och är inte en utfärdare. det är bara en multiplexor. När du använder/vanliga måste logiken i programmet för att verifiera token uppdateras för att ta med detta i kontot.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Uppdatera din kod för att hantera flera Issuer-värden

Webb program och webb-API: er får och validerar tokens från Microsoft Identity Platform.

> [!NOTE]
> När interna klient program begär och tar emot token från Microsofts identitets plattform, gör de det så att de skickar dem till API: er, där de verifieras. Interna program validerar inte åtkomsttoken och måste behandla dem som täckande.

Nu ska vi titta på hur ett program validerar de tokens som tas emot från Microsoft Identity Platform. Ett program med en enda klient tar normalt ett slut punkts värde som:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com
```

... och använder den för att skapa en URL för metadata (i det här fallet OpenID Connect) som:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

Hämta två kritiska delar av information som används för att verifiera token: klientens signerings nycklar och Issuer-värde.

Varje Azure AD-klient har ett unikt Issuer-värde i formuläret:

```http
https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

... där GUID-värdet är den Rename-säkra versionen av klient organisations-ID: t för klient organisationen. Om du väljer föregående metadata-länk för `contoso.onmicrosoft.com` kan du se det här Issuer-värdet i dokumentet.

När ett program med en enda klient verifierar en token kontrollerar det signaturen för token mot signerings nycklarna från Metadatadokumentet. Det här testet gör det möjligt för IT att se till att utfärdarens värde i token matchar det som hittades i Metadatadokumentet.

Eftersom/vanliga-slutpunkten inte motsvarar en klient och inte är en utfärdare, och du undersöker utfärdarens värde i metadata för/vanliga har den en mall-URL i stället för ett faktiskt värde:

```http
https://sts.windows.net/{tenantid}/
```

Ett program med flera klienter kan därför inte validera tokens genom att matcha utfärdarens värde i metadata med `issuer` värdet i token. Ett program med flera innehavare kräver logik för att bestämma vilka Issuer-värden som är giltiga och som inte baseras på innehavarens ID-del av Issuer-värdet.

Om ett program för flera klienter till exempel bara tillåter inloggning från specifika klienter som har registrerat sig för tjänsten, måste det kontrol lera antingen utfärdarens värde eller `tid` anspråks värde i token för att säkerställa att klienten finns i listan över prenumeranter. Om ett program för flera klienter bara hanterar individer och inte fattar några åtkomst beslut baserat på klienter, kan det ignorera utfärdarens värde helt.

I [exemplen för flera klienter][AAD-Samples-MT]inaktive ras verifiering av utfärdare för att möjliggöra för Azure AD-klienten att logga in.

## <a name="understand-user-and-admin-consent"></a>Förstå användar-och administratörs medgivande

För att en användare ska kunna logga in i ett program i Azure AD måste programmet representeras i användarens klient organisation. Detta gör att organisationen kan göra saker som att tillämpa unika principer när användare från deras klient loggar in i programmet. Den här registreringen är enklare för ett program med en enda klient. Det är det som händer när du registrerar programmet i [Azure Portal][AZURE-portal].

För ett program med flera innehavare används den första registreringen för programmet i Azure AD-klienten som används av utvecklaren. När en användare från en annan klient loggar in på programmet för första gången ber Azure AD sig att godkänna de behörigheter som begärs av programmet. Om de godkänner så skapas en åter givning av programmet som kallas för *tjänstens huvud namn* i användarens klient organisation och inloggningen kan fortsätta. En delegering skapas också i katalogen som registrerar användarens medgivande till programmet. Mer information om programmets program-och ServicePrincipal-objekt och hur de relaterar till varandra finns i [program objekt och tjänst huvud objekt][AAD-App-SP-Objects].

![Illustrerar medgivande till en app med en nivå][Consent-Single-Tier]

Den här medgivande upplevelsen påverkas av de behörigheter som har begärts av programmet. Microsoft Identity Platform stöder två typer av behörigheter, endast app-och delegerade.

* En delegerad behörighet beviljar ett program möjligheten att fungera som en inloggad användare för en delmängd av de saker som användaren kan göra. Du kan till exempel ge ett program behörigheten delegerad för att läsa den inloggade användarens kalender.
* En app-only-behörighet beviljas direkt till appens identitet. Du kan till exempel ge ett program appens behörighet att läsa listan över användare i en klient organisation, oavsett vem som är inloggad i programmet.

Vissa behörigheter kan skickas till av en vanlig användare, medan andra kräver en klient administratörs medgivande.

Mer information om användar-och administratörs medgivande finns i [Konfigurera arbets flödet för administratörs medgivande](../manage-apps/configure-admin-consent-workflow.md).

### <a name="admin-consent"></a>Administratörsmedgivande

Appspecifika behörigheter kräver alltid medgivande av en klientadministratör. Om ditt program begär en app-only-behörighet och en användare försöker logga in i programmet visas ett fel meddelande om att användaren inte kan godkänna.

Vissa delegerade behörigheter kräver också en klient administratörs medgivande. Möjligheten till exempel att skriva tillbaka till Azure AD eftersom den inloggade användaren kräver administratörs medgivande. Som endast app-only-behörigheter, om en vanlig användare försöker logga in till ett program som begär en delegerad behörighet som kräver administratörs medgivande, får programmet ett fel meddelande. Om en behörighet kräver administratörs medgivande bestäms av utvecklaren som publicerade resursen, och du hittar den i dokumentationen för resursen. Behörighets dokumentationen för [Microsoft Graph-API: et][MSFT-Graph-permission-scopes] anger vilka behörigheter som kräver administratörs medgivande.

Om ditt program använder behörigheter som kräver administratörs medgivande, har du en gest som en knapp eller länk där administratören kan starta åtgärden. Begäran som ditt program skickar för den här åtgärden är den vanliga OAuth2/OpenID Connect-auktoriseringsbegäran som också innehåller frågesträngparametern `prompt=admin_consent` . När administratören har samtyckt och tjänstens huvud namn har skapats i kundens klient behöver efterföljande inloggnings begär Anden inte `prompt=admin_consent` parametern. Eftersom administratören har beslutat att de begärda behörigheterna är acceptabla, uppmanas inga andra användare i klienten att tillfrågas om godkännande från den punkten.

En klientadministratör kan inaktivera möjligheten för vanliga användare att samtycka till program. Om den här funktionen har inaktiverats krävs alltid administratörens godkännande för program som ska användas i klienten. Om du vill testa att ditt program har inaktiverats för slutanvändare kan du hitta konfigurations växeln i [Azure Portal][AZURE-portal] i avsnittet **[användar inställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** under **företags program**.

`prompt=admin_consent`Parametern kan också användas av program som begär behörigheter som inte kräver administratörs medgivande. Ett exempel på när det används är om programmet kräver en upplevelse där klient administratören "registrerar sig" en gång, och inga andra användare tillfrågas om medgivande från den tidpunkten.

Om ett program kräver administratörs medgivande och en administratör loggar in utan att den `prompt=admin_consent` parameter skickas, gäller när administratören har samtyckt till det program som den endast kommer att använda **för sitt användar konto**. Vanliga användare kommer fortfarande inte att kunna logga in eller godkänna programmet. Den här funktionen är användbar om du vill ge klient organisations administratören möjlighet att utforska ditt program innan andra användare får åtkomst.

### <a name="consent-and-multi-tier-applications"></a>Medgivande och program på flera nivåer

Ditt program kan ha flera nivåer som representeras av sin egen registrering i Azure AD. Till exempel ett internt program som anropar ett webb-API eller ett webb program som anropar ett webb-API. I båda fallen begär klienten (den interna appen eller webb programmet) behörigheter att anropa resursen (webb-API). För att klienten ska kunna skickas till en kund klient måste alla resurser som den begär behörigheter redan finnas i kundens klient organisation. Om det här villkoret inte uppfylls returnerar Azure AD ett fel meddelande om att resursen måste läggas till först.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Flera nivåer i en enda klient

Detta kan vara ett problem om ditt logiska program består av två eller flera program registreringar, till exempel en separat klient och resurs. Hur får du resursen i kund klienten först? Azure AD täcker det här fallet genom att aktivera att klienten och resursen samtycks i ett enda steg. Användaren ser summan av de behörigheter som begärs av både klienten och resursen på godkännande sidan. För att aktivera det här beteendet måste resursens program registrering innehålla klientens app-ID som en `knownClientApplications` i dess [applikations manifest][AAD-App-Manifest]. Exempel:

```json
"knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

Detta visas i en intern klient med flera nivåer som anropar webb-API-exemplet i avsnittet [relaterat innehåll](#related-content) i slutet av den här artikeln. Följande diagram ger en översikt över medgivande för en app med flera nivåer som är registrerad i en enda klient.

![Illustrerar medgivande till en känd klient app med flera nivåer][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Flera nivåer i flera klienter

Ett liknande fall händer om de olika nivåerna av ett program registreras i olika klienter. Anta till exempel att du skapar ett internt klient program som anropar Exchange Online-API: et. För att utveckla det ursprungliga programmet och senare för det interna programmet att köras i en kunds klient måste Exchange Online-tjänstens huvud namn finnas. I det här fallet måste utvecklare och kunden köpa Exchange Online för att det ska gå att skapa tjänstens huvud namn i sina klienter.

Om det är ett API som skapats av en annan organisation än Microsoft måste utvecklaren av API: et tillhandahålla ett sätt för kunderna att godkänna programmet till sina kunders klienter. Den rekommenderade designen är för utvecklare av tredje part som skapar API: t, så att den också kan fungera som en webb klient för att implementera registrering. Gör så här:

1. Följ de tidigare avsnitten för att säkerställa att API: t implementerar program registrerings-/kod kraven för flera innehavare.
2. Förutom att exponera API: ernas omfattningar/roller kontrollerar du att registreringen innehåller behörigheten "logga in och Läs användar profil" (anges som standard).
3. Implementera en inloggnings-/registrerings sida i webb klienten och följ vägledningen för [Administratörs medgivande](#admin-consent) .
4. När användaren samtycker till programmet skapas länkar till tjänstens huvud namn och medgivande delegering i sin klient, och det interna programmet kan hämta token för API: et.

Följande diagram ger en översikt över medgivande för en app med flera nivåer som registrerats i olika klienter.

![Illustrerar medgivande till Multi-Tier-appen för flera leverantörer][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Återkalla godkännande

Användare och administratörer kan när som helst återkalla sitt medgivande till ditt program:

* Användare återkallar åtkomst till enskilda program genom att ta bort dem från listan över [program i åtkomst panelen][AAD-Access-Panel] .
* Administratörer återkallar åtkomst till program genom att ta bort dem med hjälp av avsnittet [företags program](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) i [Azure Portal][AZURE-portal].

Om en administratör godkänner ett program för alla användare i en klient organisation, kan användarna inte återkalla åtkomst individuellt. Endast administratören kan återkalla åtkomsten och endast för hela programmet.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Program för flera innehavare och cachelagring av åtkomst-token

Program med flera klienter kan också få åtkomsttoken för att anropa API: er som skyddas av Azure AD. Ett vanligt fel när du använder Microsoft Authentication Library (MSAL) med ett program med flera innehavare är att först begära en token för en användare som använder/vanliga, få ett svar och sedan begära en efterföljande token för samma användare som även använder/common. Eftersom svaret från Azure AD kommer från en klient, inte/vanliga, cachelagrar MSAL token som från klienten. Det efterföljande anropet till/vanliga för att hämta en åtkomsttoken för användaren missar cacheposten och användaren uppmanas att logga in igen. För att undvika att cachen saknas, se till att efterföljande anrop för en redan inloggad användare görs till klientens slut punkt.

## <a name="related-content"></a>Relaterat innehåll

* [Exempel på program för flera innehavare](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Varumärkesriktlinjer för program][AAD-App-Branding]
* [Program objekt och tjänst huvud objekt][AAD-App-SP-Objects]
* [Integrera program med Azure Active Directory][AAD-Integrating-Apps]
* [Översikt över medgivande ramverket][AAD-Consent-Overview]
* [Microsoft Graph API-behörighet omfattningar][MSFT-Graph-permission-scopes]

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar ett program som kan logga in en användare från valfri Azure AD-klient. När du har aktiverat enkel Sign-On (SSO) mellan din app och Azure AD kan du också uppdatera ditt program för att få åtkomst till API: er som exponeras av Microsoft-resurser som Microsoft 365. På så sätt kan du erbjuda en anpassad upplevelse i ditt program, till exempel Visa sammanhangsbaserad information till användarna, till exempel deras profil bild eller nästa kalender möte.

Mer information om hur du gör API-anrop till Azure AD och Microsoft 365-tjänster som Exchange, SharePoint, OneDrive, OneNote och mer finns i [Microsoft Graph API][MSFT-Graph-overview].

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken