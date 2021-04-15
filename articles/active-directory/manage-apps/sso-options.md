---
title: Alternativ för enkel inloggning i Azure AD
description: Läs mer om de tillgängliga alternativen för enkel inloggning (SSO) i Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: iangithinji
ms.reviewer: arvindh, japere
ms.openlocfilehash: d81ad34c81a451f1faecb6cb33c0cc427567f122
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379612"
---
# <a name="single-sign-on-options-in-azure-ad"></a>Alternativ för enkel inloggning i Azure AD

Enkel inloggning ger många fördelar över traditionella inloggningsmetoder.

- **Med enkel inloggning** loggar användarna in en gång med ett konto för att få åtkomst till domän anslutna enheter, företagsresurser, SaaS-program (programvara som en tjänst) och webbprogram. När du har loggat in kan användaren starta program från Office 365-portalen eller Mina appar. Administratörer kan centralisera hanteringen av användarkonton och automatiskt lägga till eller ta bort användaråtkomst till program baserat på gruppmedlemskap.

- **Utan enkel inloggning måste** användarna komma ihåg programspecifika lösenord och logga in på varje program. IT-personal måste skapa och uppdatera användarkonton för varje program, till exempel Microsoft 365, Box och Salesforce. Användarna måste komma ihåg sina lösenord och lägga tid på att logga in på varje program.

Mer information om enkel inloggning finns i [Vad är enkel inloggning?](what-is-single-sign-on.md).

## <a name="choosing-a-single-sign-on-method"></a>Välja en metod för enkel inloggning

Det finns flera sätt att konfigurera ett program för enkel inloggning. Hur du väljer en metod för enkel inloggning beror på hur programmet är konfigurerat för autentisering.

- Molnprogram kan använda OpenID Connect, OAuth, SAML, lösenordsbaserade, länkade eller inaktiverade metoder för enkel inloggning. 
- Lokala program kan använda lösenordsbaserade, Integrerad Windows-autentisering, rubrikbaserade, länkade eller inaktiverade metoder för enkel inloggning. De lokala alternativen fungerar när program konfigureras för Programproxy.

Det här flödesschemat hjälper dig att avgöra vilken metod för enkel inloggning som passar bäst för din situation.

![Beslutsflödesschema för metod för enkel inloggning](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

I följande tabell sammanfattas metoderna för enkel inloggning och länkar till mer information.

| Metod för enkel inloggning | Programtyper | När du ska använda detta |
| :------ | :------- | :----- |
| [OpenID Connect och OAuth](#openid-connect-and-oauth) | molnet och lokalt | Använd OpenID Connect och OAuth när du utvecklar ett nytt program. Det här protokollet förenklar programkonfigurationen, har lättanvända SDK:er och gör det möjligt för ditt program att använda MS Graph.
| [SAML](#saml-sso) | molnet och lokalt | Välj SAML när det är möjligt för befintliga program som inte använder OpenID Connect eller OAuth. SAML fungerar för program som autentiserar med ett av SAML-protokollen.|
| [Lösenordsbaserad](#password-based-sso) | molnet och lokalt | Välj lösenordsbaserad när programmet autentiseras med användarnamn och lösenord. Lösenordsbaserad enkel inloggning möjliggör säker lagring av programlösenord och återuppspelning med hjälp av ett webbläsartillägg eller en mobilapp. Den här metoden använder den befintliga inloggningsprocessen som tillhandahålls av programmet, men gör det möjligt för en administratör att hantera lösenorden. |
| [Länkade](#linked-sign-on) | molnet och lokalt | Välj länkad inloggning när programmet har konfigurerats för enkel inloggning i en annan identitetsprovidertjänst. Det här alternativet lägger inte till enkel inloggning i programmet. Programmet kan dock redan ha enkel inloggning implementerat med en annan tjänst, till exempel Active Directory Federation Services (AD FS).|
| [Disabled](#disabled-sso) (Inaktiverat) | molnet och lokalt | Välj inaktiverad enkel inloggning när appen inte är redo att konfigureras för enkel inloggning. Det här läget är standard när du skapar appen.|
| [Integrerad Windows-autentisering (IWA)](#integrated-windows-authentication-iwa-sso) | endast lokalt | Välj IWA enkel inloggning för program som [använder Integrerad Windows-autentisering (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)eller anspråksmedvetna program. För IWA använder Programproxy-anslutningsappar Kerberos-begränsad delegering (KCD) för att autentisera användare till programmet. |
| [Rubrikbaserad](#header-based-sso) | endast lokalt | Använd rubrikbaserad enkel inloggning när programmet använder huvuden för autentisering. Programproxy använder Azure AD för att autentisera användaren och skickar sedan trafik via anslutningstjänsten.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect och OAuth

När du utvecklar nya program bör du använda moderna protokoll som OpenID Connect och OAuth för att uppnå bästa möjliga enkel inloggning för din app på flera enhetsplattformar. OAuth gör det möjligt för användare eller administratörer [att bevilja medgivande](configure-user-consent.md) för skyddade resurser som [Microsoft Graph](/graph/overview). Vi tillhandahåller enkla att [använda-SDK:er](../develop/reference-v2-libraries.md) för din app och dessutom är din app redo att använda [Microsoft Graph](/graph/overview).

Mer information finns i:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Utvecklarhandbok för Microsoft Identity Platform.](../develop/index.yml)

## <a name="saml-sso"></a>SAML SSO

Med **enkel inloggning med SAML** autentiserar Azure AD till programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar inloggningsinformationen till programmet via ett anslutningsprotokoll. Med SAML-baserad enkel inloggning kan du mappa användare till specifika programroller baserat på regler som du definierar i dina SAML-anspråk.

Välj SAML-baserad enkel inloggning när programmet stöder det.

SAML-baserad enkel inloggning stöds för program som använder något av följande protokoll:

- SAML 2.0
- WS-Federation

Information om hur du konfigurerar ett SaaS-program för SAML-baserad enkel inloggning finns i [Konfigurera SAML-baserad enkel inloggning.](configure-saml-single-sign-on.md) Dessutom har många [SaaS-program](../saas-apps/tutorial-list.md) (Programvara som en tjänst) en programspecifik självstudie som steg för stegar igenom konfigurationen för SAML-baserad enkel inloggning.

Om du vill konfigurera ett program för WS-Federation följer du samma riktlinjer för att konfigurera program för SAML-baserad enkel inloggning. I steget för att konfigurera programmet så att det använder Azure AD måste du ersätta Azure AD-inloggnings-URL:en för WS-Federation till slutpunkten `https://login.microsoftonline.com/<tenant-ID>/wsfed` .

Information om hur du konfigurerar ett lokalt program för SAML-baserad enkel inloggning finns i [SAML single-sign-on for on-premises applications with Programproxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Mer information om SAML-protokollet finns i [SAML-protokoll för enkel inloggning.](../develop/single-sign-on-saml-protocol.md)

## <a name="password-based-sso"></a>Lösenordsbaserad enkel inloggning

Med lösenordsbaserad inloggning loggar användarna in på programmet med ett användarnamn och lösenord första gången de använder det. Efter den första inloggningen tillhandahåller Azure AD användarnamnet och lösenordet till programmet.

Lösenordsbaserad enkel inloggning använder den befintliga autentiseringsprocessen som tillhandahålls av programmet. När du aktiverar enkel inloggning med lösenord för ett program samlar Azure AD in och lagrar användarnamn och lösenord för programmet på ett säkert sätt. Användarautentiseringsuppgifter lagras i ett krypterat tillstånd i katalogen.

Välj lösenordsbaserad enkel inloggning när:

- Ett program stöder inte SAML-protokoll för enkel inloggning.
- Ett program autentiseras med ett användarnamn och lösenord i stället för åtkomsttoken och huvuden.

>[!NOTE]
>Du kan inte använda principer för villkorlig åtkomst eller multifaktorautentisering för lösenordsbaserad enkel inloggning.

Lösenordsbaserad enkel inloggning stöds för alla molnbaserade program som har en HTML-baserad inloggningssida. Användaren kan använda någon av följande webbläsare:

- Internet Explorer 11 på Windows 7 eller senare
   > [!NOTE]
   > Internet Explorer har begränsad support och tar inte längre emot nya programuppdateringar. Microsoft Edge är den rekommenderade webbläsaren.

- Microsoft Edge på Windows 10 Anniversary Edition eller senare
- Microsoft Edge för iOS och Android
- Intune Managed Browser
- Chrome på Windows 7 eller senare och på macOS X eller senare
- Firefox 26.0 eller senare på Windows XP SP2 eller senare och på macOS X 10.6 eller senare

Information om hur du konfigurerar ett molnprogram för lösenordsbaserad enkel inloggning finns [i Konfigurera enkel inloggning med lösenord.](configure-password-single-sign-on-non-gallery-applications.md)

Information om hur du konfigurerar ett lokalt program för enkel inloggning via Programproxy finns i Lösenordsvalv för enkel [inloggning med Programproxy](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Så här fungerar autentisering för lösenordsbaserad enkel inloggning

För att autentisera en användare för ett program hämtar Azure AD användarens autentiseringsuppgifter från katalogen och anger dem på programmets inloggningssida.  Azure AD skickar autentiseringsuppgifterna på ett säkert sätt via ett webbläsartillägg eller en mobilapp. Den här processen gör det möjligt för en administratör att hantera autentiseringsuppgifter för användare och kräver inte att användarna kommer ihåg sina lösenord.

> [!IMPORTANT]
> Autentiseringsuppgifterna är fördrövade från användaren under den automatiserade inloggningsprocessen. Autentiseringsuppgifterna kan dock upptäckas med hjälp av webbfelsökningsverktyg. Användare och administratörer måste följa samma säkerhetsprinciper som om autentiseringsuppgifterna angavs direkt av användaren.

### <a name="managing-credentials-for-password-based-sso"></a>Hantera autentiseringsuppgifter för lösenordsbaserad enkel inloggning

Lösenord för varje program kan antingen hanteras av Azure AD-administratören eller av användarna.

När Azure AD-administratören hanterar autentiseringsuppgifterna:  

- Användaren behöver inte återställa eller komma ihåg användarnamnet och lösenordet. Användaren kan komma åt programmet genom att klicka på det i Mina appar eller via en antingen länk.
- Administratören kan utföra hanteringsuppgifter med autentiseringsuppgifterna. Administratören kan till exempel uppdatera programåtkomsten enligt användargruppmedlemskap och medarbetarstatus.
- Administratören kan använda administrativa autentiseringsuppgifter för att ge åtkomst till program som delas mellan många användare. Administratören kan till exempel tillåta att alla som har åtkomst till ett program har åtkomst till ett program för delning av sociala medier eller dokument.

När slutanvändaren hanterar autentiseringsuppgifterna:

- Användarna kan hantera sina lösenord genom att uppdatera eller ta bort dem efter behov.
- Administratörer kan fortfarande ange nya autentiseringsuppgifter för programmet.

## <a name="linked-sign-on"></a>Länkad inloggning
Med länkad inloggning kan Azure AD tillhandahålla enkel inloggning till ett program som redan har konfigurerats för enkel inloggning i en annan tjänst. Det länkade programmet kan visas för slutanvändare i Office 365-portalen eller Azure AD MyApps-portalen. En användare kan till exempel starta ett program som har konfigurerats för enkel inloggning i Active Directory Federation Services (AD FS) 2.0 (AD FS) från Office 365-portalen. Ytterligare rapportering är också tillgängligt för länkade program som startas från Office 365-portalen eller Azure AD MyApps-portalen. Information om hur du konfigurerar ett program för länkad inloggning [finns i Konfigurera länkad inloggning.](configure-linked-sign-on.md)

### <a name="linked-sign-on-for-application-migration"></a>Länkad inloggning för programmigrering

Länkad inloggning kan ge en konsekvent användarupplevelse när du migrerar program under en viss tidsperiod. Om du migrerar program till Azure Active Directory kan du använda länkad inloggning för att snabbt publicera länkar till alla program som du planerar att migrera.  Användarna kan hitta alla länkar i [MyApps-portalen](../user-help/my-apps-portal-end-user-access.md) eller [Microsoft 365 programstartaren](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Användarna vet inte om de har åtkomst till ett länkat program eller ett migrerat program.  

När en användare har autentiserats med ett länkat program måste en kontopost skapas innan slutanvändaren får åtkomst med enkel inloggning. Etablering av den här kontoposten kan antingen ske automatiskt eller manuellt av en administratör.

>[!NOTE]
>Du kan inte använda principer för villkorlig åtkomst eller multifaktorautentisering för ett länkat program. Det beror på att ett länkat program inte tillhandahåller funktioner för enkel inloggning via Azure AD. När du konfigurerar ett länkat program lägger du bara till en länk som visas i appstartaren eller MyApps-portalen. 

## <a name="disabled-sso"></a>Inaktiverad enkel inloggning

Inaktiverat läge innebär att enkel inloggning inte används för programmet. När enkel inloggning har inaktiverats kan användarna behöva autentisera två gånger. Först autentiserar användare till Azure AD och sedan loggar de in i programmet.

Använd inaktiverat läge för enkel inloggning:

- Om du inte är redo att integrera det här programmet med enkel inloggning med Azure AD, eller
- Om du testar andra aspekter av programmet, eller
- Som ett säkerhetslager för ett lokalt program som inte kräver att användarna autentiseras. Med inaktiverad måste användaren autentiseras.

Observera att om du har konfigurerat programmet för SP-initierad SAML-baserad enkel inloggning och du ändrar SSO-läget till inaktivera, hindras inte användarna från att logga in på programmet utanför MyApps-portalen. För att uppnå detta måste [du inaktivera möjligheten för användare att logga in](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrerad Windows-autentisering (IWA) enkel inloggning

[Programproxy](application-proxy.md) tillhandahåller enkel inloggning (SSO) för program [som använder Integrerad Windows-autentisering (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)eller anspråksmedvetna program. Om programmet använder IWA Programproxy till programmet med hjälp av Kerberos-begränsad delegering (KCD). För ett anspråksmedvetet program som litar Azure Active Directory fungerar enkel inloggning eftersom användaren redan har autentiserats med hjälp av Azure AD.

Välj Integrerad Windows-autentisering läge för enkel inloggning för att tillhandahålla enkel inloggning till en lokal app som autentiserar med IWA.

Information om hur du konfigurerar en lokal app för IWA finns i [Kerberos-begränsad](application-proxy-configure-single-sign-on-with-kcd.md)delegering för enkel inloggning till dina program med Programproxy .

### <a name="how-single-sign-on-with-kcd-works"></a>Så här fungerar enkel inloggning med KCD
Det här diagrammet förklarar flödet när en användare kommer åt ett lokalt program som använder IWA.

![Microsoft Azure AD för autentiseringsflöde](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Användaren anger URL:en för att få åtkomst till det lokala programmet via Programproxy.
1. Programproxy omdirigerar begäran till Azure AD-autentiseringstjänster till förautentisering. Nu tillämpar Azure AD alla tillämpliga autentiserings- och auktoriseringsprinciper, till exempel multifaktorautentisering. Om användaren verifieras skapar Azure AD en token och skickar den till användaren.
1. Användaren skickar token till Programproxy.
1. Programproxy verifierar token och hämtar UPN (User Principal Name) från token. Den skickar sedan begäran, UPN och tjänstens huvudnamn (SPN) till anslutningsappen via en dually autentiserad säker kanal.
1. Anslutningsappen använder Kerberos-begränsad delegering (KCD)-förhandling med den lokala AD:n, vilket personifierar användaren för att hämta en Kerberos-token till programmet.
1. Active Directory skickar Kerberos-token för programmet till anslutningsprogrammet.
1. Anslutningsappen skickar den ursprungliga begäran till programservern med hjälp av den Kerberos-token som togs emot från AD.
1. Programmet skickar svaret till anslutningsappen, som sedan returneras till Programproxy-tjänsten och slutligen till användaren.

## <a name="header-based-sso"></a>Huvudbaserad enkel inloggning

Huvudbaserad enkel inloggning fungerar för program som använder HTTP-huvuden för autentisering.

Välj rubrikbaserad enkel inloggning när Programproxy har konfigurerats för det lokala programmet.

Mer information om rubrikbaserad autentisering finns i [Sidhuvudbaserad enkel inloggning.](application-proxy-configure-single-sign-on-with-headers.md)


## <a name="next-steps"></a>Nästa steg
* [Snabbstartsserie om programhantering](view-applications-portal.md)
* [Planera en distribution för enkel inloggning](plan-sso-deployment.md)
* [Enkel inloggning med lokala appar](application-proxy-config-sso-how-to.md)