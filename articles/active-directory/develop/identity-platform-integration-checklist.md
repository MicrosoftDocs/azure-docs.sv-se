---
title: Metod tips för Microsoft Identity Platform | Azure
description: Lär dig mer om metod tips, rekommendationer och vanliga överblickar när du integrerar med Microsoft Identity Platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: a6a7bf24571660d8e728c1acba29af2504539a18
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219968"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Metod tips och rekommendationer för Microsoft Identity Platform

I den här artikeln beskrivs bästa praxis, rekommendationer och vanliga översikter vid integrering med Microsoft Identity Platform.  Den här check listan leder dig till en högkvalitativ och säker integrering. Granska listan regelbundet för att se till att du upprätthåller kvaliteten och säkerheten för appens integrering med identitets plattformen. Check listan är inte avsedd att granska hela programmet. Innehållet i check listan kan ändras när vi gör förbättringar av plattformen.

Om du precis har kommit igång kan du läsa dokumentationen om autentisering i [Microsoft Identity Platform](index.yml) för att lära dig grunderna för autentisering, program scenarier i Microsofts identitets plattform med mera.

Använd följande check lista för att se till att programmet är effektivt integrerat med [Microsoft Identity Platform](./index.yml).

> [!TIP]
> *Integrations assistenten* i Azure Portal kan hjälpa dig att använda många av de rekommenderade metoderna och rekommendationerna. Välj någon av dina [program registreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Azure Portal och välj sedan meny alternativet **integrations assistenten** för att komma igång med assistenten.

## <a name="basics"></a>Grunder

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) läsa och förstå [Microsofts plattforms principer](/legal/microsoft-identity-platform/terms-of-use). Se till att ditt program följer de villkor som har utformats för att skydda användare och plattform.

## <a name="ownership"></a>Ägarskap

![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) Se till att informationen som är kopplad till det konto som du använde för att registrera och hantera appar är uppdaterad.

## <a name="branding"></a>Anpassning

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) följer [rikt linjerna för anpassning av program](howto-add-branding-in-azure-ad-apps.md).

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) ger ett beskrivande namn och en logo typ för ditt program. Den här informationen visas i [appens godkännande fråga](application-consent-experience.md). Kontrol lera att namnet och logo typen är representativa för företaget/produkten så att användarna kan fatta välgrundade beslut. Se till att du inte bryter mot varumärken.

## <a name="privacy"></a>Sekretess

![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) Ange länkar till appens användnings villkor och sekretess policy.

## <a name="security"></a>Säkerhet

![kryss ruta ](./media/active-directory-integration-checklist/checkbox-two.svg) Hantera omdirigerings-URI: er: <ul><li>Behåll ägarskapet för alla dina omdirigerings-URI: er och se till att DNS-posterna är aktuella.</li><li>Använd inte jokertecken (*) i dina URI: er.</li><li>För Web Apps ser du till att alla URI: er är säkra och krypterade (till exempel med hjälp av https-scheman).</li><li>För offentliga klienter använder du plattformsspecifika omdirigerings-URI: er om tillämpligt (huvudsakligen för iOS och Android). Annars använder du omdirigerings-URI: er med hög slumpmässighet för att förhindra kollisioner när du ringer tillbaka till din app.</li><li>Om din app används från en isolerad webb agent kan du använda `https://login.microsoftonline.com/common/oauth2/nativeclient` .</li><li>Granska och rensa alla oanvända eller onödiga omdirigerings-URI: er regelbundet.</li></ul>

![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) Om din app är registrerad i en katalog minimerar du och övervakar sedan listan över program registrerings ägare manuellt.

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) aktiverar inte stöd för det [OAuth2 implicita beviljade flödet](v2-oauth2-implicit-grant-flow.md) om det inte uttryckligen krävs. Lär dig mer om det giltiga scenariot [här](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant).

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) Flytta bortom användar namn/lösen ord. Använd inte [ROPC (Resource Owner Password Credential Flow)](v2-oauth-ropc.md)som direkt hanterar användarnas lösen ord. Det här flödet kräver en hög grad av förtroende och användar exponering och bör endast användas när andra, säkrare flöden inte kan användas. Det här flödet behövs fortfarande i vissa scenarier (t. ex. DevOps), men tänk på att använda det för att införa begränsningar i ditt program.  För fler moderna metoder, Läs [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md).

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) skydda och hantera dina konfidentiella autentiseringsuppgifter för webb program, webb-API: er och daemon-appar. Använd [autentiseringsuppgifter för certifikat](active-directory-certificate-credentials.md), inte autentiseringsuppgifter för lösen ord (klient hemligheter). Om du måste använda autentiseringsuppgifter för lösen ord ska du inte ange det manuellt. Lagra inte autentiseringsuppgifter i koden eller config och Tillåt aldrig att de hanteras av människa. Använd om möjligt [hanterade identiteter för Azure-resurser](../managed-identities-azure-resources/overview.md) eller [Azure Key Vault](../../key-vault/general/basic-concepts.md) för att lagra och regelbundet Rotera dina autentiseringsuppgifter.

![Markera kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) kontrol lera att ditt program begär minsta behörighet. Fråga bara efter behörigheter som programmet absolut behöver, och endast när du behöver dem. Förstå de olika [typerna av behörigheter](v2-permissions-and-consent.md#permission-types). Använd endast program behörigheter om det behövs. Använd delegerade behörigheter där det är möjligt. En fullständig lista över Microsoft Graph behörigheter finns i [referensen för behörighet](/graph/permissions-reference).

![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) Om du skyddar ett API med hjälp av Microsoft Identity Platform kan du noga tänka igenom de behörigheter som den ska visa. Överväg vad som är rätt kornig het för din lösning och vilka behörigheter som kräver administratörs medgivande. Sök efter förväntade behörigheter i inkommande token innan du fattar auktoriseringsbeslut.

## <a name="implementation"></a>Implementering

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) Använd moderna autentiserings lösningar (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) för att på ett säkert sätt logga in användare.

![CheckBox är ](./media/active-directory-integration-checklist/checkbox-two.svg) inte ett program direkt mot protokoll som OAuth 2,0 och Open ID. Använd i stället [Microsoft Authentication Library (MSAL)](msal-overview.md). MSAL-biblioteken kan på ett säkert sätt figursätta säkerhets protokoll i ett lättanvänt bibliotek, och du får inbyggt stöd för [villkorliga åtkomst](../conditional-access/overview.md) scenarier, [enkel inloggning (SSO)](../manage-apps/what-is-single-sign-on.md)och inbyggt stöd för cachelagring av token. Mer information finns i listan över [klient bibliotek](reference-v2-libraries.md)som stöds av Microsoft. Om du behöver manuell kod för autentiseringsprotokollen bör du följa [Microsofts sdl](https://www.microsoft.com/sdl/default.aspx) eller liknande utvecklings metod. Var noga med säkerhets aspekterna i specifikationerna för varje protokoll.

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) migrera befintliga appar från [Azure Active Directory Authentication Library (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) till [Microsoft Authentication Library](msal-overview.md). MSAL är Microsofts senaste identitets plattforms lösning och föredras som ADAL. Den är tillgänglig på .NET, Java Script, Android, iOS, macOS och finns även i offentlig för hands version för python och Java. Läs mer om att migrera [ADAL.net](msal-net-migration.md)-, [ADAL.js](msal-compare-msal-js-and-adal-js.md)-och [ADAL.net-och iOS Broker](msal-net-migration-ios-broker.md) -appar.

![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) För Mobile Apps konfigurerar du varje plattform med hjälp av program registrerings upplevelsen. För att ditt program ska kunna dra nytta av Microsoft Authenticator eller Microsoft Företagsportal för enkel inloggning måste appen ha konfigurerat en "Broker-omdirigerings-URI". Detta gör att Microsoft kan returnera kontroll till programmet efter autentisering. När du konfigurerar varje plattform vägleder vi dig genom processen. Använd snabb starten för att hämta ett arbets exempel. Använd utjämnare och system webbvy när det är möjligt på iOS.

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) i Web Apps eller webb-API: er, Behåll ett token-cache per konto.  För Web Apps bör token-cachen anges av konto-ID: t.  För webb-API: er ska kontot anges genom hashen för den token som används för att anropa API: et. MSAL.NET tillhandahåller anpassad cachelagring av token i .NET Framework-och .NET Core-underplattformar. Av säkerhets-och prestanda skäl är vår rekommendation att serialisera en cache per användare. Mer information finns i about [cache-serialisering för token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) om data som din app kräver är tillgängliga via [Microsoft Graph](https://developer.microsoft.com/graph), begär behörigheter för dessa data med hjälp av Microsoft Graph-slutpunkten i stället för enskilda API.

![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) Titta inte på värdet för åtkomsttoken eller försök att parsa det som en klient.  De kan ändra värden, format eller till och med bli krypterade utan varning – Använd alltid id_token om klienten behöver lära sig något om användaren eller anropa Microsoft Graph.  Endast webb-API: er ska parsa åtkomsttoken (eftersom de är de som definierar formatet och ställer in krypterings nycklarna).

## <a name="end-user-experience"></a>Slutanvändarens upplevelse

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) [förstå medgivande upplevelsen](application-consent-experience.md) och konfigurera delarna i appens godkännande fråga så att slutanvändare och administratörer har tillräckligt med information för att avgöra om de litar på din app.

![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) Minimera antalet gånger som en användare måste ange inloggnings uppgifter när de använder din app genom att försöka utföra tyst autentisering (tyst token-hämtning) innan interaktiva flöden.

![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) Använd inte "prompt = medgivande" för varje inloggning. Använd inte prompt = medgivande om du har fastställt att du behöver be om ytterligare behörigheter (till exempel om du har ändrat appens nödvändiga behörigheter).

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) där tillämpligt, utöka ditt program med användar data. Att använda [Microsoft Graph API](https://developer.microsoft.com/graph) är ett enkelt sätt att göra detta. [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) -verktyget som kan hjälpa dig att komma igång.

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) registrera den fullständiga uppsättningen behörigheter som din app kräver så att administratörer enkelt kan ge sitt medgivande till sin klient organisation. Använd ett [stegvist godkännande](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) vid körning för att hjälpa användarna att förstå varför appen begär behörigheter som kan innebära eller förväxla användare när de begär det första starten.

![kryss rutan ](./media/active-directory-integration-checklist/checkbox-two.svg) implementerar en [ren enkel inloggning](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Det är en sekretess och ett säkerhets krav och ger en bättre användar upplevelse.

## <a name="testing"></a>Testning

![kryss Rute ](./media/active-directory-integration-checklist/checkbox-two.svg) test för [principer för villkorlig åtkomst](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) som kan påverka användarnas möjlighet att använda programmet.

![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) Testa ditt program med alla möjliga konton som du planerar att stödja (till exempel arbets-eller skol konton, personliga Microsoft-konton, underordnade konton och suveräna konton).

## <a name="additional-resources"></a>Ytterligare resurser

Utforska detaljerad information om v2.0:

* [Microsoft Identity Platform (översikt)](v2-overview.md)
* [Referens för Microsoft Identity Platform-protokoll](active-directory-v2-protocols.md)
* [Referens för åtkomsttoken](access-tokens.md)
* [Referens för ID-token](id-tokens.md)
* [Referens för autentiserings bibliotek](reference-v2-libraries.md)
* [Behörigheter och medgivande i Microsoft Identity Platform](v2-permissions-and-consent.md)
* [Microsoft Graph API](https://developer.microsoft.com/graph)
