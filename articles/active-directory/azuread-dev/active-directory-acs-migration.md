---
title: Migrera från Azure-Access Control Service | Microsoft Docs
description: Lär dig mer om alternativen för att flytta appar och tjänster från Azure-Access Control Service (ACS).
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
ms.openlocfilehash: 4f6b2b1c0f584e092c9e8f7d330a94b0b54fd6f2
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197429"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Gör så här: Migrera från Azure-Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Access Control Service (ACS), en tjänst Azure Active Directory (Azure AD) tas ur bruk den 7 november 2018. Program och tjänster som för närvarande använder Access Control måste migreras fullständigt till en annan autentiseringsmekanism av. Den här artikeln beskriver rekommendationer för aktuella kunder, när du planerar att använda Access Control. Om du inte använder Access Control för närvarande behöver du inte vidta några åtgärder.

## <a name="overview"></a>Översikt

Access Control är en molnbaserad autentiseringstjänst som ger ett enkelt sätt att autentisera och auktorisera användare för åtkomst till dina webb program och-tjänster. Det gör att många funktioner för autentisering och auktorisering kan delas ut från din kod. Access Control används främst av utvecklare och arkitekter av Microsoft .NET-klienter, ASP.NET webb program och Windows Communication Foundation (WCF)-webb tjänster.

Användnings fall för Access Control kan delas upp i tre huvud kategorier:

- Autentisering till vissa Microsofts moln tjänster, inklusive Azure Service Bus och Dynamics CRM. Klient program hämtar tokens från Access Control för att autentisera till dessa tjänster för att utföra olika åtgärder.
- Lägga till autentisering till webb program, både anpassade och förpaketerade (som SharePoint). Genom att använda Access Control "passiv" autentisering kan webb program stödja inloggning med en Microsoft-konto (tidigare Live ID) och med konton från Google, Facebook, Yahoo, Azure AD och Active Directory Federation Services (AD FS) (AD FS).
- Skydda anpassade webb tjänster med token som utfärdats av Access Control. Genom att använda "aktiv"-autentisering kan webb tjänster säkerställa att de endast tillåter åtkomst till kända klienter som har autentiserats med Access Control.

Var och en av dessa användnings områden och deras rekommenderade migrations strategier beskrivs i följande avsnitt.

> [!WARNING]
> I de flesta fall krävs betydande kod ändringar för att migrera befintliga appar och tjänster till nyare tekniker. Vi rekommenderar att du omedelbart börjar planera och utföra migreringen för att undvika eventuella avbrott eller avbrott.

Access Control innehåller följande komponenter:

- En säker token service (STS), som tar emot autentiseringsbegäranden och utfärdar säkerhetstoken i retur.
- Den klassiska Azure-portalen där du skapar, tar bort och aktiverar och inaktiverar Access Control namn områden.
- En separat Access Control hanterings portal där du kan anpassa och konfigurera Access Control namnrum.
- En hanterings tjänst som du kan använda för att automatisera funktionerna i portalerna.
- En regel motor för token-omvandling, som du kan använda för att definiera komplex logik för att ändra utdataformatet i tokens som Access Control problem.

Om du vill använda dessa komponenter måste du skapa en eller flera Access Control namnrum. Ett *namn område* är en dedikerad instans av Access Control som dina program och tjänster kommunicerar med. Ett namn område är isolerat från alla andra Access Control-kunder. Andra Access Control kunder använder sina egna namn rymder. Ett namn område i Access Control har en dedikerad URL som ser ut så här:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

All kommunikation med STS-och Management-åtgärderna görs på denna URL. Du använder olika sökvägar för olika syfte. För att avgöra om dina program eller tjänster använder Access Control övervaka för all trafik till https:// &lt; namespace &gt; . AccessControl.Windows.net. All trafik till denna URL hanteras av Access Control och måste avbrytas. 

Undantaget till detta är all trafik till `https://accounts.accesscontrol.windows.net` . Trafik till denna URL hanteras redan av en annan tjänst och påverkas **inte** av Access Control-utfasningen. 

Mer information om Access Control finns i [Access Control Service 2,0 (arkiverad)](/previous-versions/azure/azure-services/hh147631(v=azure.100)).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Ta reda på vilka appar som kommer att påverkas

Följ stegen i det här avsnittet för att ta reda på vilka av dina appar som kommer att påverkas av ACS-pensionering.

### <a name="download-and-install-acs-powershell"></a>Hämta och installera ACS PowerShell

1. Gå till PowerShell-galleriet och hämta [ACS. Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. Installera modulen genom att köra

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Hämta en lista över alla möjliga kommandon genom att köra

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Om du vill ha hjälp med ett speciellt kommando kör du:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    där `[Command-Name]` är namnet på ACS-kommandot.

### <a name="list-your-acs-namespaces"></a>Visa en lista över ACS-namnrymder

1. Anslut till ACS med cmdleten **Connect-AcsAccount** .
  
    Du kan behöva köra `Set-ExecutionPolicy -ExecutionPolicy Bypass` innan du kan köra kommandon och vara administratör för dessa prenumerationer för att kunna köra kommandona.

2. Lista dina tillgängliga Azure-prenumerationer med hjälp av cmdleten **Get-AcsSubscription** .
3. Visa en lista med ACS-namnområden med cmdleten **Get-AcsNamespace** .

### <a name="check-which-applications-will-be-impacted"></a>Kontrol lera vilka program som påverkas

1. Använd namn området från föregående steg och gå till `https://<namespace>.accesscontrol.windows.net`

    Om ett av namn områdena till exempel är contoso-test går du till `https://contoso-test.accesscontrol.windows.net`

2. Under **förtroende relationer** väljer du **förlitande part program** om du vill se en lista över appar som kommer att påverkas av ACS-pensionen.
3. Upprepa steg 1-2 för alla andra ACS-namnrymder som du har.

## <a name="retirement-schedule"></a>Schema för pensionering

Från och med november 2017 stöds alla Access Control-komponenter fullständigt och fungerar. Den enda begränsningen är att du [inte kan skapa nya Access Control-namnområden via den klassiska Azure-portalen](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Här är schemat för inaktuella Access Control-komponenter:

- **2017 november**: Azure AD-administrationskonsolen i den klassiska Azure-portalen [har dragits tillbaka](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Nu finns namn områdes hantering för Access Control på en ny, dedikerad URL: `https://manage.windowsazure.com?restoreClassic=true` . Använd den här URl: en för att visa befintliga namn områden, aktivera och inaktivera namn områden och ta bort namnrum om du väljer att.
- **2 April 2018**: den klassiska Azure-portalen är helt tillbakadragen, vilket innebär att Access Control namn områdes hantering inte längre är tillgängligt via någon URL. I det här läget kan du inte inaktivera eller aktivera, ta bort eller räkna upp Access Control namnrum. Access Control hanterings portalen är dock helt funktionell och finns på `https://\<namespace\>.accesscontrol.windows.net` . Alla andra komponenter i Access Control fortsätta att arbeta normalt.
- **7 November 2018**: alla Access Control-komponenter stängs av permanent. Detta inkluderar Access Control hanterings Portal, hanterings tjänst, STS och token för token för omvandling av token. I det här läget fungerar inga förfrågningar som skickats till Access Control (finns på \<namespace\> . AccessControl.Windows.net). Du bör ha migrerat alla befintliga appar och tjänster till andra tekniker precis innan den här gången.

> [!NOTE]
> En princip inaktiverar namn rymder som inte har begärt en token under en viss tids period. Från och med den tidigaste september 2018 är denna tids period för närvarande 14 dagar av inaktivitet, men detta kommer att kortas till 7 dagars inaktivitet under de kommande veckorna. Om du har Access Control namn områden som för närvarande är inaktiverade kan du [Hämta och installera ACS PowerShell](#download-and-install-acs-powershell) för att återaktivera namn områdena.

## <a name="migration-strategies"></a>Migreringsstrategier

I följande avsnitt beskrivs övergripande rekommendationer för migrering från Access Control till andra Microsoft-tekniker.

### <a name="clients-of-microsoft-cloud-services"></a>Klienter i Microsofts moln tjänster

Varje moln tjänst från Microsoft som accepterar token som utfärdas av Access Control stöder nu minst en annan form av autentisering. Rätt autentiseringsmekanism varierar för varje tjänst. Vi rekommenderar att du läser den detaljerade dokumentationen för varje tjänst för officiell vägledning. För enkelhetens skull finns varje uppsättning dokumentation här:

| Tjänst | Vägledning |
| ------- | -------- |
| Azure Service Bus | [Migrera till signaturer för delad åtkomst](../../service-bus-messaging/service-bus-migrate-acs-sas.md) |
| Azure Service Bus relä | [Migrera till signaturer för delad åtkomst](../../azure-relay/relay-migrate-acs-sas.md) |
| Azure Managed Cache | [Migrera till Azure Cache for Redis](../../azure-cache-for-redis/cache-faq.md) |
| Azure DataMarket | [Migrera till API:er för Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) |
| BizTalk Services | [Migrera till Logic Apps funktionen i Azure App Service](https://azure.microsoft.com/services/cognitive-services/) |
| Azure Media Services | [Migrera till Azure AD-autentisering](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Uppgradera Azure Backup Agent](../../backup/backup-azure-file-folder-backup-faq.md) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint-kunder

SharePoint 2013-, 2016-och SharePoint Online-kunder har länge använt ACS för autentisering i molnet, lokalt och i hybrid scenarion. Vissa SharePoint-funktioner och användnings fall kommer att påverkas av ACS-dragande, medan andra inte kommer. I tabellen nedan sammanfattas vägledningen för migrering för några av de mest populära SharePoint-funktionerna som utnyttjar ACS:

| Funktion | Vägledning |
| ------- | -------- |
| Autentisera användare från Azure AD | Tidigare har Azure AD inte stöd för SAML 1,1-token som krävs av SharePoint för autentisering, och ACS användes som en mellanhand som gjorde SharePoint kompatibelt med Azure AD-tokens. Nu kan du [ansluta SharePoint direkt till Azure AD med hjälp av Azure AD App galleriet SharePoint i den lokala appen](../saas-apps/sharepoint-on-premises-tutorial.md). |
| [App-autentisering & server-till-Server-autentisering i SharePoint lokalt](/SharePoint/security-for-sharepoint-server/authentication-overview) | Påverkas inte av ACS-dragande, inga ändringar krävs. | 
| [Auktorisering med lågt förtroende för SharePoint-tillägg (providern och SharePoint som värd)](/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Påverkas inte av ACS-dragande, inga ändringar krävs. |
| [Hybrid sökning för SharePoint-moln](/archive/blogs/spses/cloud-hybrid-search-service-application) | Påverkas inte av ACS-dragande, inga ändringar krävs. |

### <a name="web-applications-that-use-passive-authentication"></a>Webb program som använder passiv autentisering

För webb program som använder Access Control för användarautentisering är Access Control följande funktioner och funktioner för webbappens utvecklare och arkitekter:

- Djupgående integrering med Windows Identity Foundation (WIF).
- Federation med Google-, Facebook-, Yahoo-, Azure Active Directory-och AD FS-konton och Microsoft-konton.
- Stöd för följande autentiseringsprotokoll: OAuth 2,0 Draft 13, WS-Trust och Web Services Federation (WS-Federation).
- Stöd för följande token-format: JSON Web Token (JWT), SAML 1,1, SAML 2,0 och simple web token (SWT).
- En identifiering av start sfär, integrerad i WIF, som gör att användarna kan välja vilken typ av konto de använder för att logga in. Den här upplevelsen är värd för webb programmet och är helt anpassningsbar.
- Token-transformering som möjliggör omfattande anpassning av de anspråk som tagits emot av webb programmet från Access Control, inklusive:
    - Släpp igenom anspråk från identitets leverantörer.
    - Lägger till ytterligare anpassade anspråk.
    - Enkel if-then-logik för att utfärda anspråk under vissa förhållanden.

Tyvärr finns det ingen tjänst som erbjuder alla dessa motsvarande funktioner. Du bör utvärdera vilka funktioner i Access Control du behöver och sedan välja mellan att använda [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) eller en annan molnbaserad autentiseringstjänst-tjänst.

#### <a name="migrate-to-azure-active-directory"></a>Migrera till Azure Active Directory

En väg att överväga är att integrera dina appar och tjänster direkt med Azure AD. Azure AD är den molnbaserade identitets leverantören för Microsoft arbets-eller skol konton. Azure AD är identitets leverantören för Microsoft 365, Azure och mycket mer. Den ger till gång till liknande funktioner för federerade autentisering till Access Control, men har inte stöd för alla Access Controls funktioner. 

Det primära exemplet är Federation med sociala identitets leverantörer, till exempel Facebook, Google och Yahoo. Om dina användare loggar in med dessa typer av autentiseringsuppgifter är Azure AD inte lösningen för dig. 

Azure AD stöder inte heller exakt samma autentiseringsprotokoll som Access Control. Till exempel, även om både Access Control och Azure AD stöder OAuth, finns det diskreta skillnader mellan varje implementering. Olika implementeringar kräver att du ändrar kod som en del av en migrering.

Azure AD ger dock flera möjliga fördelar för att Access Control kunder. Den har inbyggt stöd för Microsoft arbets-eller skol konton som finns i molnet, som ofta används av Access Control kunder. 

En Azure AD-klient kan också vara federerad till en eller flera instanser av lokala Active Directory via AD FS. På så sätt kan din app autentisera molnbaserade användare och användare som finns lokalt. Det stöder också WS-Federation-protokollet, vilket gör det relativt enkelt att integrera med ett webb program med hjälp av WIF.

I följande tabell jämförs funktionerna i Access Control som är relevanta för webb program med de funktioner som är tillgängliga i Azure AD. 

På hög nivå *är Azure Active Directory förmodligen det bästa valet för din migrering om du låter användarna logga in enbart med sina arbets-eller skol konton i Microsoft*.

| Funktion | Access Control support | Support för Azure AD |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Microsoft arbets-eller skol konton | Stöds | Stöds |
| Konton från Windows Server Active Directory och AD FS |– Stöds via Federation med en Azure AD-klient <br />– Stöds via direkt Federation med AD FS | Stöds endast via Federation med en Azure AD-klient | 
| Konton från andra företags identitets hanterings system |– Möjlig via Federation med en Azure AD-klient <br />– Stöds via direkt Federation | Möjlig via Federation med en Azure AD-klient |
| Microsoft-konton för personligt bruk | Stöds | Stöds via Azure AD v 2.0 OAuth-protokollet, men inte över några andra protokoll | 
| Facebook-, Google-, Yahoo-konton | Stöds | Stöds inte alls |
| **Protokoll och SDK-kompatibilitet** | | |
| WIF | Stöds | Stöds, men det finns begränsade instruktioner |
| WS-Federation | Stöds | Stöds |
| OAuth 2.0 | Stöd för Draft 13 | Stöd för RFC 6749, den senaste moderna specifikationen |
| WS-Trust | Stöds | Stöds inte |
| **Token-format** | | |
| JWT | Stöds i beta | Stöds |
| SAML 1,1 | Stöds | Förhandsgranskning |
| SAML 2.0 | Stöds | Stöds |
| SWT | Stöds | Stöds inte |
| **Anpassningar** | | |
| Anpassningsbar start sfär identifiering/konto – plocknings-UI | Nedladdnings bar kod som kan införlivas i appar | Stöds inte |
| Ladda upp anpassade certifikat för tokensignering | Stöds | Stöds |
| Anpassa anspråk i token |-Släpp igenom inloggade anspråk från identitets leverantörer<br />– Hämta åtkomsttoken från identitets leverantören som ett anspråk<br />– Utfärda utgående anspråk baserat på värden för indata-anspråk<br />– Utfärda utgående anspråk med konstanta värden |-Det går inte att släppa igenom anspråk från federerade identitets leverantörer<br />-Det går inte att hämta åtkomsttoken från identitets leverantören som ett anspråk<br />-Det går inte att utfärda utgående anspråk baserat på värden för indata-anspråk<br />-Kan utfärda utgående anspråk med konstanta värden<br />-Kan utfärda utgående anspråk baserat på Egenskaper för användare som har synkroniserats till Azure AD |
| **Automation** | | |
| Automatisera konfigurations-och hanterings uppgifter | Stöds via Access Control hanterings tjänst | Stöds med Microsoft Graph-API: et |

Om du bestämmer att Azure AD är den bästa sökvägen för migrering för dina program och tjänster, bör du vara medveten om två sätt att integrera din app med Azure AD.

Om du vill använda WS-Federation eller WIF för att integrera med Azure AD rekommenderar vi att du följer metoden som beskrivs i [Konfigurera federerad enkel inloggning för ett program som inte är ett galleri program](../manage-apps/configure-saml-single-sign-on.md). Artikeln handlar om hur du konfigurerar Azure AD för SAML-baserad enkel inloggning, men som även fungerar för att konfigurera WS-Federation. Följande metod kräver en Azure AD Premium-licens. Den här metoden har två fördelar:

- Du får fullständig flexibilitet för Azure AD-token-anpassning. Du kan anpassa de anspråk som utfärdas av Azure AD för att matcha de anspråk som utfärdas av Access Control. Detta omfattar särskilt användar-ID eller namn-ID-anspråk. Om du vill fortsätta att ta emot konsekventa användar identifierare för användarna när du har ändrat teknik kontrollerar du att de användar-ID: n som utfärdats av Azure AD matchar de som utfärdats av Access Control.
- Du kan konfigurera ett certifikat för tokensignering som är specifika för ditt program och med en livs längd som du styr.

> [!NOTE]
> Den här metoden kräver en Azure AD Premium-licens. Om du är Access Control kund och behöver en Premium-licens för att ställa in en enkel inloggning för ett program, så kontakta oss. Vi är glada över att tillhandahålla utvecklings licenser som du kan använda.

En annan metod är att följa [det här kod exemplet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), vilket ger något annorlunda instruktioner för att konfigurera WS-Federation. Det här kod exemplet använder inte WIF, utan i stället ASP.NET 4,5 OWIN mellan. Instruktionerna för registrering av appar är dock giltiga för appar som använder WIF och kräver inte någon Azure AD Premium-licens. 

Om du väljer den här metoden måste du förstå [förnyelse av signerings nyckel i Azure AD](../develop/active-directory-signing-key-rollover.md). I den här metoden används den globala Azure AD-signerings nyckeln för att utfärda token. Som standard uppdaterar WIF inte signerings nycklar automatiskt. När Azure AD roterar sina globala signerings nycklar måste din WIF-implementering förberedas för att godkänna ändringarna. Mer information finns i [viktig information om förnyelse av signerings nyckel i Azure AD](/previous-versions/azure/dn641920(v=azure.100)).

Om du kan integrera med Azure AD via OpenID Connect eller OAuth-protokoll rekommenderar vi att du gör det. Vi har omfattande dokumentation och vägledning om hur du integrerar Azure AD i ditt webb program i [Azure AD Developer Guide](../develop/index.yml).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrera till Azure Active Directory B2C

Den andra sökvägen till migrering att överväga är Azure AD B2C. Azure AD B2C är en molnbaserad tjänst i molnet som Access Control, så att utvecklare kan hantera sin autentiserings-och identitets hanterings logik till en moln tjänst. Det är en betald tjänst (med kostnads fria och Premium-nivåer) som är avsedd för konsument program som kan ha upp till miljon tals aktiva användare.

Precis som Access Control är en av de mest attraktiva funktionerna i Azure AD B2C att den stöder många olika typer av konton. Med Azure AD B2C kan du logga in användare med deras Microsoft-konto-eller Facebook-, Google-, LinkedIn-, GitHub-eller Yahoo-konton med mera. Azure AD B2C stöder också "lokala konton" eller användar namn och lösen ord som användare skapar specifikt för ditt program. Azure AD B2C ger också omfattande utöknings barhet som du kan använda för att anpassa dina inloggnings flöden. 

Azure AD B2C stöder dock inte bredden på autentiseringsprotokoll och token-format som Access Control kunder kan behöva. Du kan inte heller använda Azure AD B2C för att hämta token och fråga om du vill ha mer information om användaren från identitets leverantören, Microsoft eller på annat sätt.

I följande tabell jämförs funktionerna i Access Control som är relevanta för webb program med de som är tillgängliga i Azure AD B2C. På hög nivå *är Azure AD B2C förmodligen det rätta valet för din migrering om ditt program är kund riktat eller om det stöder många olika typer av konton.*

| Funktion | Access Control support | Azure AD B2C support |
| ---------- | ----------- | ---------------- |
| **Typer av konton** | | |
| Microsoft arbets-eller skol konton | Stöds | Stöds via anpassade principer  |
| Konton från Windows Server Active Directory och AD FS | Stöds via direkt Federation med AD FS | Stöds via SAML Federation med anpassade principer |
| Konton från andra företags identitets hanterings system | Stöds via direkt Federation via WS-Federation | Stöds via SAML Federation med anpassade principer |
| Microsoft-konton för personligt bruk | Stöds | Stöds | 
| Facebook-, Google-, Yahoo-konton | Stöds | Facebook och Google stöds internt, Yahoo som stöds via OpenID Connect Federation med anpassade principer |
| **Protokoll och SDK-kompatibilitet** | | |
| Windows Identity Foundation (WIF) | Stöds | Stöds inte |
| WS-Federation | Stöds | Stöds inte |
| OAuth 2.0 | Stöd för Draft 13 | Stöd för RFC 6749, den senaste moderna specifikationen |
| WS-Trust | Stöds | Stöds inte |
| **Token-format** | | |
| JWT | Stöds i beta | Stöds |
| SAML 1,1 | Stöds | Stöds inte |
| SAML 2.0 | Stöds | Stöds inte |
| SWT | Stöds | Stöds inte |
| **Anpassningar** | | |
| Anpassningsbar start sfär identifiering/konto – plocknings-UI | Nedladdnings bar kod som kan införlivas i appar | Fullständigt anpassningsbart gränssnitt via anpassad CSS |
| Ladda upp anpassade certifikat för tokensignering | Stöds | Anpassade signerings nycklar, inte certifikat, stöds via anpassade principer |
| Anpassa anspråk i token |-Släpp igenom inloggade anspråk från identitets leverantörer<br />– Hämta åtkomsttoken från identitets leverantören som ett anspråk<br />– Utfärda utgående anspråk baserat på värden för indata-anspråk<br />– Utfärda utgående anspråk med konstanta värden |– Kan släppa igenom anspråk från identitets leverantörer. anpassade principer som krävs för vissa anspråk<br />-Det går inte att hämta åtkomsttoken från identitets leverantören som ett anspråk<br />-Kan utfärda utgående anspråk baserat på värden för indata-anspråk via anpassade principer<br />-Kan utfärda utgående anspråk med konstanta värden via anpassade principer |
| **Automation** | | |
| Automatisera konfigurations-och hanterings uppgifter | Stöds via Access Control hanterings tjänst |– Skapa användare som tillåts använda Microsoft Graph API<br />-Det går inte att skapa B2C-klienter, program eller principer program mässigt |

Om du bestämmer att Azure AD B2C är den bästa migreringen för dina program och tjänster börjar du med följande resurser:

- [Azure AD B2C dokumentation](../../active-directory-b2c/overview.md)
- [Anpassade principer i Azure AD B2C](../../active-directory-b2c/custom-policy-overview.md)
- [Azure AD B2C priser](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrera till ping-identitet eller Auth0

I vissa fall kanske du upptäcker att Azure AD och Azure AD B2C inte räcker för att ersätta Access Control i dina webb program utan att göra stora kod ändringar. Några vanliga exempel kan vara:

- Webb program som använder WIF eller WS-Federation för inloggning med sociala identitets leverantörer som Google eller Facebook.
- Webb program som utför direkt Federation till en företags identitets leverantör via WS-Federation-protokollet.
- Webb program som kräver åtkomst-token som utfärdats av en social identitetsprovider (till exempel Google eller Facebook) som ett anspråk i de token som utfärdas av Access Control.
- Webb program med komplex token-omvandlings regler som Azure AD eller Azure AD B2C inte kan återskapa.
- Webb program för flera innehavare som använder ACS för att centralt hantera Federation till många olika identitets leverantörer

I dessa fall kanske du vill överväga att migrera ditt webb program till en annan molnbaserad autentiseringstjänst. Vi rekommenderar att du utforskar följande alternativ. Var och en av följande alternativ erbjuder funktioner som liknar Access Control:

![Den här bilden visar Auth0-logotypen](./media/active-directory-acs-migration/rsz-auth0.png) 

[Auth0](https://auth0.com/acs) är en flexibel moln identitets tjänst som har skapat [vägledning för migrering på hög nivå för kunder i Access Control](https://auth0.com/acs)och har stöd för nästan alla funktioner i ACS.

![Den här bilden visar ping identitet-logo typen](./media/active-directory-acs-migration/rsz-ping.png)

[Ping Identity](https://www.pingidentity.com) erbjuder två lösningar som liknar ACS. PingOne är en moln identitets tjänst som stöder många av samma funktioner som ACS, och PingFederate är en liknande lokal identitets produkt som erbjuder mer flexibilitet. Mer information om hur du använder dessa produkter finns i ping: s indragnings vägledning för ACS.

Vårt mål att arbeta med ping Identity och Auth0 är att se till att alla Access Control kunder har en migrerings Sök väg för sina appar och tjänster som minimerar mängden arbete som krävs för att flytta från Access Control.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webb tjänster som använder aktiv autentisering

För webb tjänster som skyddas med token som utfärdats av Access Control erbjuder Access Control följande funktioner:

- Möjlighet att registrera en eller flera *tjänst identiteter* i Access Control namn området. Tjänst identiteter kan användas för att begära token.
- Stöd för OAuth-omslutning och OAuth 2,0 Draft 13-protokoll för att begära token genom att använda följande typer av autentiseringsuppgifter:
    - Ett enkelt lösen ord som skapas för tjänst identiteten
    - En signerad SWT med hjälp av ett symmetriskt nyckel-eller X509-certifikat
    - En SAML-token som utfärdats av en betrodd identitets leverantör (vanligt vis en AD FS instans)
- Stöd för följande token-format: JWT, SAML 1,1, SAML 2,0 och SWT.
- Transformerings regler för enkel token.

Tjänst identiteter i Access Control används vanligt vis för att implementera server-till-Server-autentisering. 

#### <a name="migrate-to-azure-active-directory"></a>Migrera till Azure Active Directory

Vår rekommendation för den här typen av autentiseringspaket är att migrera till [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD är den molnbaserade identitets leverantören för Microsoft arbets-eller skol konton. Azure AD är identitets leverantören för Microsoft 365, Azure och mycket mer. 

Du kan också använda Azure AD för server-till-Server-autentisering med hjälp av Azure AD-implementeringen av tilldelningen av OAuth-klientautentiseringsuppgifter. I följande tabell jämförs funktionerna i Access Control Server-till-Server-autentisering med de som är tillgängliga i Azure AD.

| Funktion | Access Control support | Support för Azure AD |
| ---------- | ----------- | ---------------- |
| Registrera en webb tjänst | Skapa en förlitande part i Access Control hanterings portalen | Skapa ett Azure AD-webbprogram i Azure Portal |
| Registrera en klient | Skapa en tjänst identitet i Access Control hanterings Portal | Skapa ett annat Azure AD-webbprogram i Azure Portal |
| Protokoll som används |– OAuth-protokollet för att packa<br />– OAuth 2,0 Draft 13-tilldelning av klientautentiseringsuppgifter | Beviljande av autentiseringsuppgifter för OAuth 2.0-klient |
| Metoder för klientautentisering |– Enkelt lösen ord<br />-Signerad SWT<br />– SAML-token från en federerad identitets leverantör |– Enkelt lösen ord<br />-Signerat JWT |
| Token-format |– JWT<br />– SAML 1,1<br />– SAML 2,0<br />– SWT<br /> | Endast JWT |
| Token-transformering |-Lägg till anpassade anspråk<br />– Enkel om-then utfärdande logik för anspråk | Lägg till anpassade anspråk | 
| Automatisera konfigurations-och hanterings uppgifter | Stöds via Access Control hanterings tjänst | Stöds med Microsoft Graph-API: et |

Vägledning om hur du implementerar server-till-Server-scenarier finns i följande resurser:

- Avsnittet tjänst-till-tjänst i [Azure AD Developer Guide](../develop/index.yml)
- [Bakgrunds exempel kod exempel med hjälp av enkla lösen ord klientens autentiseringsuppgifter](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Bakgrunds exempel kod exempel med certifikat klientens autentiseringsuppgifter](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrera till ping-identitet eller Auth0

I vissa fall kanske du upptäcker att autentiseringsuppgifterna för Azure AD-klienten och OAuth Grant-implementeringen inte räcker för att ersätta Access Control i din arkitektur utan större kod ändringar. Några vanliga exempel kan vara:

- Server-till-Server-autentisering med andra token-format än JWTs.
- Server-till-Server-autentisering med hjälp av en token som tillhandahålls av en extern identitets leverantör.
- Server-till-Server-autentisering med token omvandlings regler som Azure AD inte kan återskapa.

I dessa fall kan du överväga att migrera ditt webb program till en annan molnbaserad autentiseringstjänst-tjänst. Vi rekommenderar att du utforskar följande alternativ. Var och en av följande alternativ erbjuder funktioner som liknar Access Control:

![Den här bilden visar Auth0-logotypen](./media/active-directory-acs-migration/rsz-auth0.png)

[Auth0](https://auth0.com/acs) är en flexibel moln identitets tjänst som har skapat [vägledning för migrering på hög nivå för kunder i Access Control](https://auth0.com/acs)och har stöd för nästan alla funktioner i ACS.

![Den här bilden visar ping identity-logo typens ](./media/active-directory-acs-migration/rsz-ping.png)
 [ping-identitet](https://www.pingidentity.com) erbjuder två lösningar som liknar ACS. PingOne är en moln identitets tjänst som stöder många av samma funktioner som ACS, och PingFederate är en liknande lokal identitets produkt som erbjuder mer flexibilitet. Mer information om hur du använder dessa produkter finns i ping: s indragnings vägledning för ACS.

Vårt mål att arbeta med ping Identity och Auth0 är att se till att alla Access Control kunder har en migrerings Sök väg för sina appar och tjänster som minimerar mängden arbete som krävs för att flytta från Access Control.

#### <a name="passthrough-authentication"></a>Genom strömnings autentisering

För genom strömnings autentisering med godtycklig token-transformering finns det ingen motsvarande Microsoft-teknik till ACS. Om det är vad dina kunder behöver, kan Auth0 vara den som ger en närmast ungefärlig lösning.

## <a name="questions-concerns-and-feedback"></a>Frågor, problem och feedback

Vi förstår att många Access Control kunder inte hittar en klar sökväg för migrering efter att ha läst den här artikeln. Du kan behöva hjälp eller vägledning för att fastställa rätt plan. Om du vill diskutera dina migrerings scenarier och frågor kan du lämna en kommentar på den här sidan.