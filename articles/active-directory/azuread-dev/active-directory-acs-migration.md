---
title: Migrera från Azure-Access Control Service | Microsoft Docs
description: Lär dig mer om alternativen för att flytta appar och tjänster från Azure Access Control Service (ACS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: bf50db4c463f5161adcc88d69eb2ae8970526103
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515625"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Gör så här: Migrera från Azure Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Access Control Service (ACS), en tjänst med Azure Active Directory (Azure AD), dras tillbaka den 7 november 2018. Program och tjänster som för närvarande Access Control måste migreras helt till en annan autentiseringsmekanism då. I den här artikeln beskrivs rekommendationer för aktuella kunder när du planerar att ta bort användningen av Access Control. Om du inte använder Access Control behöver du inte vidta några åtgärder.

## <a name="overview"></a>Översikt

Access Control är en molnautentiseringstjänst som erbjuder ett enkelt sätt att autentisera och auktorisera användare för åtkomst till dina webbprogram och tjänster. Det gör att många funktioner för autentisering och auktorisering kan tas med i koden. Access Control används främst av utvecklare och arkitekter för Microsoft .NET-klienter, ASP.NET-webbprogram och Windows Communication Foundation-webbtjänster (WCF).

Användningsfall för Access Control kan delas in i tre huvudkategorier:

- Autentisering för vissa Microsoft-molntjänster, inklusive Azure Service Bus och Dynamics CRM. Klientprogram hämtar token från Access Control för att autentisera till dessa tjänster för att utföra olika åtgärder.
- Lägga till autentisering till webbprogram, både anpassade och förpaketerade (t.ex. SharePoint). Med Access Control "passiv" autentisering kan webbprogram stödja inloggning med en Microsoft-konto (tidigare Live ID) och med konton från Google, Facebook, Yahoo, Azure AD och Active Directory Federation Services (AD FS) (AD FS).
- Skydda anpassade webbtjänster med token som utfärdats av Access Control. Med hjälp av "aktiv" autentisering kan webbtjänster se till att de endast tillåter åtkomst till kända klienter som har autentiserats med Access Control.

Var och en av dessa användningsfall och deras rekommenderade migreringsstrategier beskrivs i följande avsnitt.

> [!WARNING]
> I de flesta fall krävs betydande kodändringar för att migrera befintliga appar och tjänster till nyare tekniker. Vi rekommenderar att du omedelbart börjar planera och köra migreringen för att undvika eventuella avbrott eller driftstopp.

Access Control har följande komponenter:

- En säker tokentjänst (STS) som tar emot autentiseringsförfrågningar och utfärdar säkerhetstoken i utbyte.
- Den klassiska Azure-portalen, där du skapar, tar bort och aktiverar och inaktiverar Access Control namnområden.
- En separat Access Control hanteringsportal där du anpassar och konfigurerar Access Control namnområden.
- En hanteringstjänst som du kan använda för att automatisera portalfunktionerna.
- En regelmotor för tokentransformering som du kan använda för att definiera komplex logik för att ändra utdataformatet för token som Access Control problem.

Om du vill använda dessa komponenter måste du skapa en eller Access Control namnområden. Ett *namnområde* är en dedikerad instans av Access Control som dina program och tjänster kommunicerar med. Ett namnområde är isolerat från alla andra Access Control kunder. Andra Access Control kunder använder sina egna namnrymder. Ett namnområde i Access Control har en dedikerad URL som ser ut så här:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

All kommunikation med STS och hanteringsåtgärder sker via den här URL:en. Du använder olika sökvägar för olika syften. Kontrollera om dina program eller tjänster använder Access Control genom att övervaka all trafik till https:// &lt; &gt; .accesscontrol.windows.net. All trafik till denna URL hanteras av Access Control och måste upphöra. 

Undantaget till detta är all trafik till `https://accounts.accesscontrol.windows.net` . Trafik till den här URL:en hanteras redan av en annan **tjänst och påverkas** inte Access Control utfasningen. 

Mer information om Access Control finns i [Access Control Service 2.0 (arkiverad).](/previous-versions/azure/azure-services/hh147631(v=azure.100))

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Ta reda på vilka av dina appar som påverkas

Följ stegen i det här avsnittet för att ta reda på vilka av dina appar som påverkas av ACS-tillbakagången.

### <a name="download-and-install-acs-powershell"></a>Ladda ned och installera ACS PowerShell

1. Gå till PowerShell-galleriet och ladda ned [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. Installera modulen genom att köra

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Hämta en lista över alla möjliga kommandon genom att köra

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Om du vill ha hjälp med ett visst kommando kör du:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    där `[Command-Name]` är namnet på ACS-kommandot.

### <a name="list-your-acs-namespaces"></a>Lista dina ACS-namnområden

1. Anslut till ACS med **cmdleten Connect-AcsAccount.**
  
    Du kan behöva köra `Set-ExecutionPolicy -ExecutionPolicy Bypass` innan du kan köra kommandon och vara administratör för dessa prenumerationer för att kunna köra kommandona.

2. Visa en lista över dina tillgängliga **Azure-prenumerationer med hjälp av cmdleten Get-AcsSubscription.**
3. Lista dina ACS-namnområden med hjälp av **cmdleten Get-AcsNamespace.**

### <a name="check-which-applications-will-be-impacted"></a>Kontrollera vilka program som påverkas

1. Använd namnområdet från föregående steg och gå till `https://<namespace>.accesscontrol.windows.net`

    Om ett av namnrymderna till exempel är contoso-test går du till `https://contoso-test.accesscontrol.windows.net`

2. Under **Förtroenderelationer** väljer du **Förlitande partsprogram** för att se listan över appar som påverkas av ACS-tillbakadragande.
3. Upprepa steg 1–2 för alla andra ACS-namnområden som du har.

## <a name="retirement-schedule"></a>Schema för tillbakadragande

Från och med november 2017 stöds Access Control komponenter fullt ut och fungerar. Den enda begränsningen är att [du inte kan skapa nya Access Control-namnområden via den klassiska Azure-portalen.](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Här är schemat för inaktuella Access Control komponenter:

- **November 2017:** Azure AD-administratörsupplevelsen i den klassiska Azure-portalen [dras tillbaka.](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/) Nu är namnområdeshantering för Access Control tillgänglig på en ny dedikerad URL: `https://manage.windowsazure.com?restoreClassic=true` . Använd den här RL:en för att visa dina befintliga namnområden, aktivera och inaktivera namnområden och ta bort namnområden om du vill.
- **2 april 2018:** Den klassiska Azure-portalen dras tillbaka helt, vilket innebär Access Control hantering av namnområden inte längre är tillgängligt via någon URL. Nu kan du inte inaktivera eller aktivera, ta bort eller räkna upp dina Access Control namnområden. Dock är Access Control-hanteringsportalen fullt funktionell och finns på `https://<namespace>.accesscontrol.windows.net` . Alla andra komponenter i Access Control fortsätter att fungera normalt.
- **7 november 2018:** Access Control alla komponenter stängs av permanent. Detta inkluderar Access Control hanteringsportalen, hanteringstjänsten, STS och regelmotorn för tokentransformering. Nu misslyckas alla begäranden som skickas till Access Control (finns \<namespace\> på .accesscontrol.windows.net). Du bör ha migrerat alla befintliga appar och tjänster till andra tekniker långt före den här tiden.

> [!NOTE]
> En princip inaktiverar namnområden som inte har begärt en token under en viss tidsperiod. Från och med början av september 2018 är den här tidsperioden för närvarande vid 14 dagars inaktivitet, men detta förkortas till 7 dagars inaktivitet under de kommande veckorna. Om du har Access Control som för närvarande är inaktiverade kan du ladda ned och installera [ACS PowerShell](#download-and-install-acs-powershell) för att återaktivera namnrymderna.

## <a name="migration-strategies"></a>Migreringsstrategier

I följande avsnitt beskrivs rekommendationer på hög nivå för att migrera från Access Control till andra Microsoft-tekniker.

### <a name="clients-of-microsoft-cloud-services"></a>Klienter i Microsofts molntjänster

Varje Microsoft-molntjänst som accepterar token som utfärdas av Access Control stöder nu minst en alternativ form av autentisering. Rätt autentiseringsmekanism varierar för varje tjänst. Vi rekommenderar att du refererar till den specifika dokumentationen för varje tjänst för officiell vägledning. För enkelhetens skull finns varje uppsättning dokumentation här:

| Tjänst | Vägledning |
| ------- | -------- |
| Azure Service Bus | [Migrera till signaturer för delad åtkomst](../../service-bus-messaging/service-bus-migrate-acs-sas.md) |
| Azure Service Bus Relay | [Migrera till signaturer för delad åtkomst](../../azure-relay/relay-migrate-acs-sas.md) |
| Azure Managed Cache | [Migrera till Azure Cache for Redis](../../azure-cache-for-redis/cache-faq.md) |
| Azure DataMarket | [Migrera till API:er för Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) |
| BizTalk Services | [Migrera till Logic Apps i Azure App Service](https://azure.microsoft.com/services/cognitive-services/) |
| Azure Media Services | [Migrera till Azure AD-autentisering](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Uppgradera Azure Backup agent](../../backup/backup-azure-file-folder-backup-faq.yml) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint-kunder

Kunder med SharePoint 2013, 2016 och SharePoint Online har länge använt ACS för autentiseringsändamål i moln-, lokal- och hybridscenarier. Vissa SharePoint-funktioner och användningsfall påverkas av ATT ACS tas i bruk, medan andra inte gör det. I tabellen nedan sammanfattas migreringsvägledningen för några av de mest populära SharePoint-funktioner som använder ACS:

| Funktion | Vägledning |
| ------- | -------- |
| Autentisera användare från Azure AD | Tidigare hade Azure AD inte stöd för SAML 1.1-token som krävs av SharePoint för autentisering, och ACS användes som en mellanhand som gjorde SharePoint kompatibelt med Azure AD-tokenformat. Nu kan du ansluta [SharePoint direkt till Azure AD med hjälp Azure AD App-galleriets Lokala SharePoint-app](../saas-apps/sharepoint-on-premises-tutorial.md). |
| [Appautentisering & server-till-server-autentisering i Lokal SharePoint](/SharePoint/security-for-sharepoint-server/authentication-overview) | Påverkas inte av ACS-tillbakagången; inga ändringar krävs. | 
| [Låg förtroendeauktorisering för SharePoint-tillägg (providern är värd och SharePoint värdbaserade)](/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Påverkas inte av ACS-tillbakagången; inga ändringar krävs. |
| [Hybridsökning i SharePoint-moln](/archive/blogs/spses/cloud-hybrid-search-service-application) | Påverkas inte av ACS-tillbakagången; inga ändringar krävs. |

### <a name="web-applications-that-use-passive-authentication"></a>Webbprogram som använder passiv autentisering

För webbprogram som använder Access Control för användarautentisering Access Control tillhandahåller följande funktioner för utvecklare och arkitekter av webbprogram:

- Djupgående integrering med Windows Identity Foundation (WIF).
- Federation med Google, Facebook, Yahoo, Azure Active Directory och AD FS-konton och Microsoft-konton.
- Stöd för följande autentiseringsprotokoll: OAuth 2.0 Draft 13, WS-Trust och Web Services Federation (WS-Federation).
- Stöd för följande tokenformat: JSON Web Token (JWT), SAML 1.1, SAML 2.0 och Simple Web Token (SWT).
- En identifieringsupplevelse för hemsfär, integrerad i WIF, som gör att användarna kan välja vilken typ av konto de använder för att logga in. Den här upplevelsen finns i webbappen och är helt anpassningsbar.
- Tokentransformering som möjliggör omfattande anpassning av de anspråk som tas emot av webbprogrammet från Access Control, inklusive:
    - Skicka genom anspråk från identitetsproviders.
    - Lägga till ytterligare anpassade anspråk.
    - Enkel if-then-logik för att utfärda anspråk under vissa villkor.

Tyvärr finns det inte någon tjänst som erbjuder alla dessa motsvarande funktioner. Du bör utvärdera vilka funktioner i Access Control du behöver och sedan välja mellan att använda [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) eller en annan molnautentiseringstjänst.

#### <a name="migrate-to-azure-active-directory"></a>Migrera till Azure Active Directory

En väg att överväga är att integrera dina appar och tjänster direkt med Azure AD. Azure AD är den molnbaserade identitetsprovidern för Microsofts arbets- eller skolkonton. Azure AD är identitetsprovider för Microsoft 365, Azure och mycket mer. Den ger liknande federerade autentiseringsfunktioner som Access Control, men har inte stöd för alla Access Control funktioner. 

Det primära exemplet är federation med sociala identitetsleverantörer, till exempel Facebook, Google och Yahoo. Om användarna loggar in med dessa typer av autentiseringsuppgifter är Azure AD inte lösningen för dig. 

Azure AD har inte heller nödvändigtvis stöd för exakt samma autentiseringsprotokoll som Access Control. Även om både Access Control och Azure AD stöder OAuth finns det subtila skillnader mellan varje implementering. Olika implementeringar kräver att du ändrar kod som en del av en migrering.

Azure AD ger dock flera potentiella fördelar till Access Control kunder. Det har inbyggt stöd för Microsofts arbets- eller skolkonton som finns i molnet, som ofta används av Access Control kunder. 

En Azure AD-klientorganisation kan också federeras till en eller flera instanser av lokal Active Directory via AD FS. På så sätt kan din app autentisera molnbaserade användare och användare som finns lokalt. Det stöder även WS-Federation protokoll, vilket gör det relativt enkelt att integrera med en webbapp med hjälp av WIF.

I följande tabell jämförs funktionerna i Access Control som är relevanta för webbprogram med de funktioner som är tillgängliga i Azure AD. 

På hög nivå är Azure Active Directory förmodligen det bästa valet för migreringen om du tillåter att användarna bara loggar in med sina Microsoft-arbets- *eller skolkonton.*

| Funktion | Access Control support | Azure AD-stöd |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Microsoft-arbets- eller skolkonton | Stöds | Stöds |
| Konton från Windows Server Active Directory och AD FS |– Stöds via federation med en Azure AD-klientorganisation <br />– Stöds via direkt federation med AD FS | Stöds endast via federation med en Azure AD-klientorganisation | 
| Konton från andra identitetshanteringssystem för företag |– Möjligt via federation med en Azure AD-klientorganisation <br />– Stöds via direkt federation | Möjligt via federation med en Azure AD-klientorganisation |
| Microsoft-konton för personligt bruk | Stöds | Stöds via Azure AD v2.0 OAuth-protokollet, men inte över andra protokoll | 
| Facebook-, Google- och Yahoo-konton | Stöds | Stöds inte alls |
| **Protokoll och SDK-kompatibilitet** | | |
| WIF | Stöds | Stöds, men det finns begränsade instruktioner |
| WS-Federation | Stöds | Stöds |
| OAuth 2.0 | Stöd för utkast 13 | Stöd för RFC 6749, den mest moderna specifikationen |
| WS-Trust | Stöds | Stöds inte |
| **Tokenformat** | | |
| JWT | Stöds i betaversion | Stöds |
| SAML 1.1 | Stöds | Förhandsgranskning |
| SAML 2.0 | Stöds | Stöds |
| Swt | Stöds | Stöds inte |
| **Anpassningar** | | |
| Anpassningsbar identifiering av hemsfär/konto-välja användargränssnitt | Nedladdningsbar kod som kan införlivas i appar | Stöds inte |
| Ladda upp anpassade certifikat för tokensignering | Stöds | Stöds |
| Anpassa anspråk i token |– Skicka inkommande anspråk från identitetsproviders<br />– Hämta åtkomsttoken från identitetsprovidern som ett anspråk<br />– Utfärda utgående anspråk baserat på värden för inkommande anspråk<br />– Utfärda utgående anspråk med konstanta värden |– Det går inte att skicka anspråk från federerade identitetsproviders<br />– Det går inte att hämta åtkomsttoken från identitetsprovidern som ett anspråk<br />– Det går inte att utfärda utgående anspråk baserat på värden för inkommande anspråk<br />– Kan utfärda utgående anspråk med konstanta värden<br />– Kan utfärda utgående anspråk baserat på egenskaper för användare som har synkroniserats med Azure AD |
| **Automation** | | |
| Automatisera konfigurations- och hanteringsuppgifter | Stöds via Access Control Management Service | Stöds med hjälp Microsoft Graph API |

Om du bestämmer dig för att Azure AD är den bästa migreringsvägen för dina program och tjänster bör du vara medveten om två sätt att integrera din app med Azure AD.

Om du WS-Federation eller WIF för att integrera med Azure AD rekommenderar vi att du följer metoden som beskrivs i Konfigurera federerad enkel inloggning för ett program som inte [är ett galleriprogram.](../manage-apps/configure-saml-single-sign-on.md) Artikeln handlar om att konfigurera Azure AD för SAML-baserad enkel inloggning, men fungerar även för att konfigurera WS-Federation. För att följa den här metoden krävs Azure AD Premium licens. Den här metoden har två fördelar:

- Du får fullständig flexibilitet för anpassning av Azure AD-token. Du kan anpassa anspråken som utfärdas av Azure AD så att de matchar de anspråk som utfärdas av Access Control. Detta inkluderar särskilt användar-ID eller namnidentifieraranspråk. Om du vill fortsätta att ta emot konsekventa användaridentifierare för dina användare när du har ändrat teknik, se till att användar-ID:n som utfärdats av Azure AD matchar de som utfärdats av Access Control.
- Du kan konfigurera ett certifikat för tokensignering som är specifikt för ditt program och med en livslängd som du styr.

> [!NOTE]
> Den här metoden kräver en Azure AD Premium licens. Om du är Access Control kund och du behöver en Premium-licens för att konfigurera enkel inloggning för ett program kan du kontakta oss. Vi tillhandahåller gärna utvecklarlicenser som du kan använda.

En annan metod är att följa [det här kodexe](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)exemplet , som ger lite olika instruktioner för att konfigurera WS-Federation. Det här kodexe exemplet använder inte WIF, utan i stället ASP.NET 4.5 OWIN-mellanprogram. Instruktionerna för appregistrering är dock giltiga för appar som använder WIF och kräver inte en Azure AD Premium licens. 

Om du väljer den här metoden måste du förstå [signeringsnyckelns återställning i Azure AD.](../develop/active-directory-signing-key-rollover.md) Den här metoden använder den globala signeringsnyckeln för Azure AD för att utfärda token. Wif uppdaterar som standard inte signeringsnycklar automatiskt. När Azure AD roterar sina globala signeringsnycklar måste DIN WIF-implementering förberedas för att godkänna ändringarna. Mer information finns i Viktig [information om signering av nyckel-rollover i Azure AD.](/previous-versions/azure/dn641920(v=azure.100))

Om du kan integrera med Azure AD via OpenID Connect- eller OAuth-protokollen rekommenderar vi att du gör det. Vi har omfattande dokumentation och vägledning om hur du integrerar Azure AD i din webbapp som är tillgänglig i vår [Azure AD-utvecklarguide.](../develop/index.yml)

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrera till Azure Active Directory B2C

Den andra migreringsvägen att överväga är Azure AD B2C. Azure AD B2C är en molnautentiseringstjänst som, till exempel Access Control, gör det möjligt för utvecklare att outsourca sin logik för autentisering och identitetshantering till en molntjänst. Det är en betald tjänst (med kostnadsfria nivåer och premiumnivåer) som är utformad för konsumentriktade program som kan ha upp till miljontals aktiva användare.

Som Access Control är en av de mest snygga funktionerna i Azure AD B2C att den stöder många olika typer av konton. Med Azure AD B2C kan du logga in användare med hjälp av deras Microsoft-konto- eller Facebook-, Google-, LinkedIn-, GitHub- eller Yahoo-konton med mera. Azure AD B2C också stöd för "lokala konton" eller användarnamn och lösenord som användarna skapar specifikt för ditt program. Azure AD B2C ger också omfattande utökningsbarhet som du kan använda för att anpassa dina inloggningsflöden. 

Men Azure AD B2C stöder inte den bredd av autentiseringsprotokoll och tokenformat som Access Control kan kräva. Du kan inte heller använda Azure AD B2C för att hämta token och fråga efter ytterligare information om användaren från identitetsprovidern, Microsoft eller på annat sätt.

I följande tabell jämförs funktionerna i Access Control som är relevanta för webbprogram med de som är tillgängliga i Azure AD B2C. På hög nivå är Azure AD B2C förmodligen det rätta valet för migreringen om ditt program är konsumentriktad, eller om det stöder *många olika typer av konton.*

| Funktion | Access Control support | Azure AD B2C support |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Microsoft-arbets- eller skolkonton | Stöds | Stöds via anpassade principer  |
| Konton från Windows Server Active Directory och AD FS | Stöds via direkt federation med AD FS | Stöds via SAML-federation med hjälp av anpassade principer |
| Konton från andra identitetshanteringssystem för företag | Stöds via direkt federation via WS-Federation | Stöds via SAML-federation med hjälp av anpassade principer |
| Microsoft-konton för personligt bruk | Stöds | Stöds | 
| Facebook-, Google- och Yahoo-konton | Stöds | Facebook och Google stöds inbyggt, Yahoo stöds via OpenID Connect federation med hjälp av anpassade principer |
| **Protokoll och SDK-kompatibilitet** | | |
| Windows Identity Foundation (WIF) | Stöds | Stöds inte |
| WS-Federation | Stöds | Stöds inte |
| OAuth 2.0 | Stöd för utkast 13 | Stöd för RFC 6749, den mest moderna specifikationen |
| WS-Trust | Stöds | Stöds inte |
| **Tokenformat** | | |
| JWT | Stöds i betaversion | Stöds |
| SAML 1.1 | Stöds | Stöds inte |
| SAML 2.0 | Stöds | Stöds inte |
| Swt | Stöds | Stöds inte |
| **Anpassningar** | | |
| Anpassningsbar identifiering av hemsfär/konto-välja användargränssnitt | Nedladdningsbar kod som kan införlivas i appar | Fullständigt anpassningsbart användargränssnitt via anpassad CSS |
| Ladda upp anpassade certifikat för tokensignering | Stöds | Anpassade signeringsnycklar, inte certifikat, stöds via anpassade principer |
| Anpassa anspråk i token |– Skicka inkommande anspråk från identitetsproviders<br />– Hämta åtkomsttoken från identitetsprovidern som ett anspråk<br />– Utfärda utgående anspråk baserat på värden för inkommande anspråk<br />– Utfärda utgående anspråk med konstanta värden |– Kan skicka anspråk från identitetsproviders. anpassade principer som krävs för vissa anspråk<br />– Det går inte att hämta åtkomsttoken från identitetsprovidern som ett anspråk<br />– Kan utfärda utgående anspråk baserat på värden för inkommande anspråk via anpassade principer<br />– Kan utfärda utgående anspråk med konstanta värden via anpassade principer |
| **Automation** | | |
| Automatisera konfigurations- och hanteringsuppgifter | Stöds via Access Control Management Service |– Skapa användare som tillåts med hjälp av Microsoft Graph-API:et<br />– Det går inte att skapa B2C-klienter, program eller principer programmatiskt |

Om du bestämmer Azure AD B2C är den bästa migreringsvägen för dina program och tjänster börjar du med följande resurser:

- [Azure AD B2C dokumentation](../../active-directory-b2c/overview.md)
- [Anpassade principer i Azure AD B2C](../../active-directory-b2c/custom-policy-overview.md)
- [Azure AD B2C prissättning](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrera till Ping-identitet eller Auth0

I vissa fall kanske du upptäcker att Azure AD och Azure AD B2C inte är tillräckliga för att ersätta Access Control i dina webbprogram utan att göra större kodändringar. Några vanliga exempel kan vara:

- Webbprogram som använder WIF eller WS-Federation för inloggning med sociala identitetsproviders som Google eller Facebook.
- Webbprogram som utför direkt federation till en företagsidentitetsprovider via WS-Federation protokollet.
- Webbprogram som kräver den åtkomsttoken som utfärdats av en social identitetsprovider (till exempel Google eller Facebook) som ett anspråk i de token som utfärdats av Access Control.
- Webbprogram med komplexa tokentransformeringsregler som Azure AD Azure AD B2C inte kan återskapa.
- Webbprogram för flera innehavare som använder ACS för att centralt hantera federation till många olika identitetsprovidrar

I dessa fall kanske du vill migrera webbappen till en annan molnautentiseringstjänst. Vi rekommenderar att du utforskar följande alternativ. Vart och ett av följande alternativ erbjuder funktioner som liknar Access Control:

![Den här bilden visar Auth0-logotypen](./media/active-directory-acs-migration/rsz-auth0.png) 

[Auth0 är](https://auth0.com/acs) en flexibel molnidentitetstjänst som har skapat migreringsvägledning på hög nivå för [kunder med Access Control](https://auth0.com/acs)och har stöd för nästan alla funktioner som ACS gör.

![Den här bilden visar pingidentitetslogotypen](./media/active-directory-acs-migration/rsz-ping.png)

[Ping Identity](https://www.pingidentity.com) erbjuder två lösningar som liknar ACS. PingOne är en molnidentitetstjänst som stöder många av samma funktioner som ACS och PingFederate är en liknande lokal identitetsprodukt som ger större flexibilitet. Mer information om hur du använder dessa produkter finns i Pings vägledning för ACS-tillbakagångar.

Vårt mål med att arbeta med Ping Identity och Auth0 är att se till att alla Access Control-kunder har en migreringsväg för sina appar och tjänster som minimerar mängden arbete som krävs för att flytta från Access Control.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webbtjänster som använder aktiv autentisering

För webbtjänster som skyddas med token som utfärdats av Access Control Access Control erbjuder följande funktioner:

- Möjlighet att registrera en eller flera *tjänstidentiteter* i Access Control namnområdet. Tjänstidentiteter kan användas för att begära token.
- Stöd för OAuth WRAP- och OAuth 2.0 Draft 13-protokoll för att begära token med hjälp av följande typer av autentiseringsuppgifter:
    - Ett enkelt lösenord som har skapats för tjänstidentiteten
    - En signerad SWT med hjälp av en symmetrisk nyckel eller X509-certifikat
    - En SAML-token som utfärdats av en betrodd identitetsprovider (vanligtvis en AD FS instans)
- Stöd för följande tokenformat: JWT, SAML 1.1, SAML 2.0 och SWT.
- Enkla tokentransformeringsregler.

Tjänstidentiteter i Access Control används vanligtvis för att implementera server-till-server-autentisering. 

#### <a name="migrate-to-azure-active-directory"></a>Migrera till Azure Active Directory

Vår rekommendation för den här typen av autentiseringsflöde är att migrera [till Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD är den molnbaserade identitetsprovidern för Microsofts arbets- eller skolkonton. Azure AD är identitetsprovidern för Microsoft 365, Azure och mycket mer. 

Du kan också använda Azure AD för server-till-server-autentisering med hjälp av Azure AD-implementeringen av beviljandet av OAuth-klientautentiseringsuppgifter. I följande tabell jämförs funktionerna i Access Control i server-till-server-autentisering med de som är tillgängliga i Azure AD.

| Funktion | Access Control support | Azure AD-stöd |
| ---------- | ----------- | ---------------- |
| Registrera en webbtjänst | Skapa en förlitande part i Access Control-hanteringsportalen | Skapa en Azure AD-webbapp i Azure Portal |
| Registrera en klient | Skapa en tjänstidentitet i Access Control-hanteringsportalen | Skapa en annan Azure AD-webbapp i Azure Portal |
| Protokoll som används |– OAuth WRAP-protokoll<br />– Beviljande av autentiseringsuppgifter för OAuth 2.0 Draft 13-klient | Beviljande av autentiseringsuppgifter för OAuth 2.0-klient |
| Klientautentiseringsmetoder |– Enkelt lösenord<br />– Signerad SWT<br />– SAML-token från en federerad identitetsprovider |– Enkelt lösenord<br />– Signerad JWT |
| Tokenformat |– JWT<br />– SAML 1.1<br />– SAML 2.0<br />– SWT<br /> | Endast JWT |
| Tokentransformering |– Lägga till anpassade anspråk<br />– Enkel logik för anspråksutfärdande | Lägga till anpassade anspråk | 
| Automatisera konfigurations- och hanteringsuppgifter | Stöds via Access Control Management Service | Stöds med hjälp Microsoft Graph API |

Vägledning om hur du implementerar server-till-server-scenarier finns i följande resurser:

- Tjänst-till-tjänst-avsnittet i [utvecklarhandboken för Azure AD](../develop/index.yml)
- [Daemon-kodexempel med enkla lösenordsklientautentiseringsuppgifter](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Daemonkodexempel med certifikatklientautentiseringsuppgifter](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrera till Ping Identity eller Auth0

I vissa fall kanske du upptäcker att autentiseringsuppgifterna för Azure AD-klienten och OAuth-beviljandeimplementering inte är tillräckliga för att ersätta Access Control i arkitekturen utan större kodändringar. Några vanliga exempel kan vara:

- Server-till-server-autentisering med andra tokenformat än JWT.
- Server-till-server-autentisering med hjälp av en indatatoken som tillhandahålls av en extern identitetsprovider.
- Server-till-server-autentisering med tokentransformeringsregler som Azure AD inte kan återskapa.

I dessa fall kan du överväga att migrera webbappen till en annan molnautentiseringstjänst. Vi rekommenderar att du utforskar följande alternativ. Vart och ett av följande alternativ erbjuder funktioner som liknar Access Control:

![Den här bilden visar Auth0-logotypen](./media/active-directory-acs-migration/rsz-auth0.png)

[Auth0 är](https://auth0.com/acs) en flexibel molnidentitetstjänst som har skapat migreringsvägledning på hög nivå för [kunder med Access Control](https://auth0.com/acs)och har stöd för nästan alla funktioner som ACS gör.

![Den här bilden visar pingidentitetslogotypen ](./media/active-directory-acs-migration/rsz-ping.png)
 [Ping Identity](https://www.pingidentity.com) som erbjuder två lösningar som liknar ACS. PingOne är en molnidentitetstjänst som stöder många av de funktioner som ACS och PingFederate är en liknande lokal identitetsprodukt som ger mer flexibilitet. Mer information om hur du använder dessa produkter finns i Pings vägledning för ACS-tillbakagångar.

Vårt mål med att arbeta med Ping Identity och Auth0 är att se till att alla Access Control-kunder har en migreringsväg för sina appar och tjänster som minimerar mängden arbete som krävs för att flytta från Access Control.

#### <a name="passthrough-authentication"></a>Genomströmningsautentisering

För genomströmningsautentisering med godtycklig tokentransformering finns det ingen motsvarande Microsoft-teknik till ACS. Om det är vad dina kunder behöver kan Auth0 vara den som tillhandahåller den närmaste approximeringslösningen.

## <a name="questions-concerns-and-feedback"></a>Frågor, problem och feedback

Vi förstår att många Access Control inte hittar en tydlig migreringsväg när de har läst den här artikeln. Du kan behöva hjälp eller vägledning för att fastställa rätt plan. Om du vill diskutera dina migreringsscenarier och frågor kan du lämna en kommentar på den här sidan.