---
title: Tekniska funktioner för säkerhet i Azure – Microsoft Azure
description: Introduktion till säkerhets tjänster i Azure som hjälper dig att skydda data, resurser och program i molnet.
services: security
author: terrylanfear
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 07/13/2020
ms.author: terrylan
ms.openlocfilehash: 353b1708a1c6014f9108ad2be62f22c1e0b0604c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092776"
---
# <a name="azure-security-technical-capabilities"></a>Tekniska funktioner för Azure-säkerhet
Den här artikeln innehåller en introduktion till säkerhets tjänster i Azure som hjälper dig att skydda dina data, resurser och program i molnet och uppfylla verksamhetens behov av säkerhet.

## <a name="azure-platform"></a>Azure-plattformen

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) är en moln plattform som består av infrastruktur-och program tjänster, med integrerade data tjänster och avancerad analys samt utvecklingsverktyg och-tjänster, som finns i Microsofts offentliga moln Data Center. Kunder använder Azure för många olika kapaciteter och scenarier, från grundläggande beräkning, nätverk och lagring till mobila tjänster och webb program tjänster, till fullständiga moln scenarier som Sakernas Internet och kan användas med teknik för öppen källkod, och distribueras som ett hybrid moln eller som finns i en kunds Data Center. Azure tillhandahåller moln teknik som bygg stenar för att hjälpa företag att spara kostnader, förnya snabbt och hantera system proaktivt. När du bygger på, eller migrerar IT-tillgångar till en moln leverantör, förlitar du dig på organisationens möjligheter att skydda dina program och data med tjänsterna och de kontroller som de tillhandahåller för att hantera säkerheten för dina molnbaserade till gångar.

Microsoft Azure är den enda molnbaserad data behandlings leverantören som erbjuder en säker, konsekvent program plattform och infrastruktur som en tjänst för team att arbeta inom deras olika moln-färdighetsuppsättningar och nivåer av projekt komplexitet. med integrerade data tjänster och analyser som avvisar information från data oavsett var de finns, kan du öppna ramverk och verktyg i både Microsoft och andra plattformar, öppna ramverk och verktyg, vilket ger möjlighet att integrera molnet med lokalt och Distribuera Azure Cloud Services i lokala data Center. Som en del av Microsoft Trusted Cloud förlitar sig kunderna på Azure för branschledande säkerhet, tillförlitlighet, efterlevnad, sekretess och det stora nätverket av människor, partners och processer för att stödja organisationer i molnet.

Med Microsoft Azure kan du:

- Påskynda innovationen med molnet.

- Power Business-beslut & appar med insikter.

- Bygg fritt och distribuera var som helst.

- Skydda sin verksamhet.

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>Tekniska funktioner för säkerhet för att uppfylla ditt ansvar

Microsoft Azure tillhandahåller tjänster som hjälper dig att uppfylla dina krav på säkerhet, sekretess och efterlevnad. Följande bild hjälper till att förklara olika Azure-tjänster som är tillgängliga för att bygga en säker och kompatibel program infrastruktur baserad på bransch standarder.

![Tillgängliga tekniska funktioner för säkerhet – stor bild](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Hantera och kontrol lera identitet och användar åtkomst

Azure hjälper dig att skydda företags information och personlig information genom att göra det möjligt att hantera användar identiteter och autentiseringsuppgifter och kontrol lera åtkomst.

### <a name="azure-active-directory"></a>Azure Active Directory

Microsofts identitets-och åtkomst hanterings lösningar hjälper till att skydda åtkomsten till program och resurser i företagets data Center och till molnet, vilket möjliggör ytterligare verifierings nivåer, till exempel Multi-Factor Authentication och principer för villkorlig åtkomst. Övervakning av misstänkt aktivitet genom avancerad säkerhetsrapportering, -granskning och -avisering hjälper till att minska potentiella säkerhetsproblem. [Azure Active Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) ger enkel inloggning till tusentals molnappar och åtkomst till webbappar som du kör lokalt.

Säkerhets fördelarna med Azure Active Directory (Azure AD) inkluderar möjligheten att:

- Skapa och hantera en enda identitet för varje användare i hybridföretaget och synkronisera användare, grupper och enheter.

- Ge åtkomst till enkel inloggning till dina program, inklusive tusentals förintegrerade SaaS-appar.

- Skydda programåtkomsten genom att tillämpa regelbaserad multifaktorautentisering får både lokala program och molnprogram.

- Etablera säker fjärråtkomst till lokala webb program via Azure AD-programproxy.

[Azure Active Directory Portal](https://aad.portal.azure.com/) är tillgänglig som en del av Azure Portal. Från den här instrument panelen kan du få en översikt över organisationens tillstånd och enkelt hantera katalogen, användare eller program åtkomst.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

Följande är grundläggande funktioner för Azure Identity Management:

- Enkel inloggning

- Multifaktorautentisering

- Säkerhetsövervakning, varningar och Machine Learning-baserade rapporter

- Konsument identitets-och åtkomst hantering

- Enhetsregistrering

- Privileged Identity Management

- Identity Protection

#### <a name="single-sign-on"></a>Enkel inloggning

[Enkel inloggning (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) innebär att kunna komma åt alla program och resurser som du behöver för att göra affärer, genom att bara logga in en gång med ett enda användar konto. När du har loggat in kan du komma åt alla program som du behöver utan att behöva autentisera (till exempel skriva ett lösen ord) en gång till.

Många organisationer förlitar sig på SaaS-program (program vara som en tjänst) som Microsoft 365, Box och Salesforce för slutanvändarens produktivitet. Tidigare var IT-personal tvungen att individuellt skapa och uppdatera användar konton i varje SaaS-program, och användarna var tvungna att komma ihåg ett lösen ord för varje SaaS-program.

[Azure AD utökar lokala Active Directory till molnet](../../active-directory/manage-apps/what-is-single-sign-on.md), vilket gör det möjligt för användare att använda sitt primära organisations konto för att inte bara logga in på sina domänanslutna enheter och företags resurser, men även alla webb-och SaaS-program som krävs för sitt arbete.

Användare behöver inte bara hantera flera uppsättningar användar namn och lösen ord, program åtkomsten kan automatiskt etableras eller avetableras baserat på organisations grupper och deras status som anställd. [Azure AD introducerar säkerhets-och åtkomst styrnings kontroller](../../active-directory/manage-apps/view-applications-portal.md) som gör att du centralt kan hantera användarnas åtkomst i SaaS-program.

#### <a name="multi-factor-authentication"></a>Multifaktorautentisering

[Azure AD Multi-Factor Authentication (MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) är en autentiseringsmetod som kräver användning av mer än en verifieringsmetod och lägger till ett kritiskt andra säkerhets skikt för användar inloggningar och transaktioner. [MFA hjälper](../../active-directory/authentication/concept-mfa-howitworks.md) till att skydda åtkomsten till data och program samtidigt som du kan möta användarnas behov av en enkel inloggnings process. Den ger stark autentisering via ett antal verifierings alternativ – telefonsamtal, textmeddelande eller meddelande från mobilapp eller verifierings kod och OAuth-token från tredje part.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Säkerhetsövervakning, varningar och Machine Learning-baserade rapporter

Säkerhetsövervakning och aviseringar och Machine Learning-baserade rapporter som identifierar inkonsekventa åtkomst mönster kan hjälpa dig att skydda ditt företag. Du kan använda Azure Active Directory åtkomst-och användnings rapporter för att få insyn i integriteten och säkerheten i din organisations katalog. Med den här informationen kan en katalog administratör bättre avgöra var möjliga säkerhets risker kan vara så att de kan planera för att minska riskerna.

I Azure Portal eller via [Azure Active Directory portalen](https://aad.portal.azure.com/)kategoriseras [rapporterna](../../active-directory/reports-monitoring/overview-reports.md) på följande sätt:

- Avvikelse rapporter – innehåller inloggnings händelser som vi identifierade som avvikande. Vårt mål är att göra dig uppmärksam på sådan aktivitet och gör att du kan bestämma om en händelse är misstänkt.

- Integrerade program rapporter – ger insikter om hur moln program används i din organisation. Azure Active Directory erbjuder integrering med tusentals moln program.

- Fel rapporter – ange fel som kan uppstå när du konfigurerar konton till externa program.

- Användarspecifika rapporter – Visa enhets-och inloggnings aktivitets data för en enskild användare.

- Aktivitets loggar – innehåller en lista över alla granskade händelser under de senaste 24 timmarna, senaste 7 dagarna eller de senaste 30 dagarna, samt ändringar i grupp aktivitet samt återställning av lösen ord och registrerings aktivitet.

#### <a name="consumer-identity-and-access-management"></a>Konsument identitets-och åtkomst hantering

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) är en global, identitets hanterings tjänst med hög tillgänglighet för klientbaserade program som kan skalas till hundratals miljoner identiteter. Den kan integreras över mobila plattformar och webbaserade plattformar. Konsumenterna kan logga in på alla program via anpassningsbara upplevelser genom att använda sina befintliga sociala konton eller genom att skapa nya autentiseringsuppgifter.

Tidigare hade programutvecklare som ville [registrera sig och logga in konsumenter](../../active-directory-b2c/overview.md) i sina program skrivit sin egen kod. Och de använde lokala databaser eller system för att lagra användarnamn och lösenord. Azure Active Directory B2C ger organisationen ett bättre sätt att integrera konsument identitets hantering i program med hjälp av en säker, standard-baserad plattform och en stor uppsättning utöknings bara principer.

När du använder Azure Active Directory B2C kan dina konsumenter registrera sig för dina program genom att använda sina befintliga sociala konton (Facebook, Google, Amazon, LinkedIn) eller genom att skapa nya autentiseringsuppgifter (e-postadress och lösen ord eller användar namn och lösen ord).

#### <a name="device-registration"></a>Enhetsregistrering

[Azure AD Device Registration](../../active-directory/devices/overview.md) är grunden för enhets scenarier för [villkorlig åtkomst](../../active-directory/devices/overview.md) . När en enhet registreras tillhandahåller Azure AD Device Registration enheten med en identitet som används för att autentisera enheten när användaren loggar in. Den autentiserade enheten och attributen för enheten kan sedan användas för att tillämpa principer för villkorlig åtkomst för program som finns i molnet och lokalt.

I kombination med en [hanterings lösning för mobila enheter (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) , till exempel Intune, uppdateras enhetens attribut i Azure Active Directory med ytterligare information om enheten. På så sätt kan du skapa regler för villkorlig åtkomst som tvingar åtkomst från enheter att uppfylla dina standarder för säkerhet och efterlevnad.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Azure Active Directory (AD) Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) låter dig hantera, kontrol lera och övervaka dina privilegierade identiteter och åtkomst till resurser i Azure AD samt andra Microsoft-onlinetjänster som Microsoft 365 eller Microsoft Intune.

Ibland behöver användare utföra privilegierade åtgärder i Azure eller Microsoft 365 resurser eller andra SaaS-appar. Detta innebär ofta att organisationer måste ge dem permanent privilegie rad åtkomst i Azure AD. Detta är en växande säkerhets risk för resurser som är värd för molnet eftersom organisationer inte kan övervaka vad dessa användare gör med sina administratörs privilegier. Om ett användar konto med privilegie rad åtkomst komprometteras, kan det dessutom orsaka att en överträdelse påverkar den övergripande moln säkerheten. Azure AD Privileged Identity Management hjälper till att lösa den här risken.

Med Azure AD Privileged Identity Management kan du:

- Se vilka användare som är Azure AD-administratörer

- Aktivera administrativ åtkomst på begäran, precis i tiden, till Microsoft Online Services som Microsoft 365 och Intune

- Hämta rapporter om administratörs åtkomst historik och ändringar i administratörs tilldelningar

- Få aviseringar om åtkomst till en privilegie rad roll

#### <a name="identity-protection"></a>Identity Protection

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) är en säkerhets tjänst som tillhandahåller en samlad vy över risk identifieringar och potentiella sårbarheter som påverkar organisationens identiteter. Identitets skydd använder befintliga Azure Active Directorys avvikelse identifierings funktioner (tillgängliga via Azure ADs avvikande aktivitets rapporter) och introducerar nya risk identifierings typer som kan identifiera avvikelser i real tid.

## <a name="secure-resource-access"></a>Säker resurs åtkomst

Åtkomst kontroll i Azure börjar från ett fakturerings perspektiv. Ägaren av ett Azure-konto, som nås genom att besöka [Azure-kontocenter](https://account.windowsazure.com/subscriptions), är konto administratören (AA). Prenumerationer är en behållare för fakturering, men de fungerar också som en säkerhets gränser: varje prenumeration har en tjänst administratör (SA) som kan lägga till, ta bort och ändra Azure-resurser i den prenumerationen med hjälp av Azure Portal. Standard-SA för en ny prenumeration är AA, men AA kan ändra SA i Azure-kontocenter.

![Säker åtkomst till resurser i Azure](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

Prenumerationer har också en Association med en katalog. Katalogen definierar en uppsättning användare. Det kan vara användare från arbets plats eller skola som skapade katalogen, eller så kan de vara externa användare (dvs. Microsoft-konton). Prenumerationer är tillgängliga med en delmängd av de katalog användare som har tilldelats antingen tjänst administratör (SA) eller Co-Administrator (CA). Det enda undantaget är att Microsoft-konton (tidigare Windows Live ID) kan tilldelas som SA eller CA utan att finnas i katalogen, av äldre skäl.

Säkerhetsorienterade företag bör fokuseras på att ge anställda de exakta behörigheter de behöver. För många behörigheter kan exponera ett konto för angripare. För få behörigheter innebär det att anställda inte kan få jobbet gjort effektivt. [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) hjälper till att lösa det här problemet genom att erbjuda detaljerad åtkomst hantering för Azure.

![Skyddad resurs åtkomst](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Med hjälp av Azure RBAC kan du hålla isär uppgifter i ditt team och bevilja endast den omfattning av åtkomst till användare som de behöver för att utföra sitt arbete. I stället för att ge alla obegränsade behörigheter i din Azure-prenumeration eller resurser kan du bara tillåta vissa åtgärder. Använd till exempel Azure RBAC för att låta en medarbetare hantera virtuella datorer i en prenumeration, medan en annan kan hantera SQL-databaser inom samma prenumeration.

![Skyddad resurs åtkomst med Azure RBAC](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>Datasäkerhet och kryptering

En av nycklarna till data skydd i molnet är redovisning av de möjliga tillstånd som dina data kan inträffa i och vilka kontroller som är tillgängliga för det tillståndet. Rekommendationerna för Azure Data Security och kryptering är rekommendationerna kring följande data tillstånd.

- I vila: Detta omfattar alla informations lagrings objekt, behållare och typer som finns statiskt på fysiska media, som är magnetiska eller optiska diskar.
- Under överföring: när data överförs mellan komponenter, platser eller program, t. ex. över nätverket, via en Service Bus (från lokalt till molnet och vice versa, inklusive hybrid anslutningar som ExpressRoute) eller under en indata/utdata-process, betraktas den som rörelse.

### <a name="encryption-at-rest"></a>Kryptering i vila

Kryptering i vila diskuteras i detalj i [Azure Data Encryption – i vila](encryption-atrest.md).

### <a name="encryption-in-transit"></a>Kryptering under överföring

Att skydda data i överföring bör vara en viktig del av din strategi för data skydd. Eftersom data flyttas fram och tillbaka från många platser är den allmänna rekommendationen att du alltid använder SSL/TLS-protokoll för att utbyta data mellan olika platser. I vissa fall kanske du vill isolera hela kommunikations kanalen mellan din lokala och moln infrastruktur med hjälp av ett virtuellt privat nätverk (VPN).

För data som flyttas mellan din lokala infrastruktur och Azure bör du överväga lämpliga skydd som HTTPS eller VPN.

För organisationer som behöver säker åtkomst från flera arbets stationer som finns lokalt till Azure använder du [Azure plats-till-plats-VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

För organisationer som behöver säker åtkomst från en arbets Station lokalt till Azure använder du [punkt-till-plats-VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Större data uppsättningar kan flyttas över en dedikerad höghastighets-WAN-länk, till exempel [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Om du väljer att använda ExpressRoute kan du också kryptera data på program nivå med hjälp av [SSL/TLS](https://web.archive.org/web/20150221085231/http://support.microsoft.com:80/kb/257591) eller andra protokoll för ytterligare skydd.

Om du interagerar med Azure Storage via Azure Portal sker alla transaktioner via HTTPS. [Lagrings REST API](/rest/api/storageservices/) över https kan också användas för att interagera med [Azure Storage](https://azure.microsoft.com/services/storage/) och [Azure SQL Database](https://azure.microsoft.com/services/sql-database/).

Organisationer som inte kan skydda data under överföringen är mer känsliga för [man-in-the-The-the-Middle-attack](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14)), [avlyssning](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14))och kapning av sessioner. Sådana attacker kan vara ett första steg i att få åtkomst till konfidentiella data.

Du kan läsa mer om alternativet för Azure VPN genom att läsa artikeln [planering och design för VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="enforce-file-level-data-encryption"></a>Tvinga data kryptering på filnivå

[Azure RMS](/azure/information-protection/what-is-azure-rms) använder krypterings-, identitets-och Auktoriseringsprinciper för att skydda dina filer och e-postmeddelanden. Azure RMS fungerar på flera enheter – telefoner, surfplattor och datorer genom att skydda både i din organisation och utanför organisationen. Den här funktionen är möjlig eftersom Azure RMS lägger till en skydds nivå som är kvar med data, även om den lämnar din organisations gränser.

När du använder Azure RMS för att skydda dina filer använder du branschstandardiserade kryptografi med fullständigt stöd för [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf). När du använder Azure RMS för data skydd kan du vara säker på att skyddet ligger kvar i filen, även om den kopieras till lagring som inte är underordnad, till exempel en moln lagrings tjänst. Samma sak gäller filer som delas via e-post, filen skyddas som en bifogad fil i ett e-postmeddelande med anvisningar för hur du öppnar den skyddade bilagan.
När du planerar för Azure RMS antar vi att du rekommenderar följande:

- Installera [RMS-delning-appen](/azure/information-protection/rms-client/sharing-app-windows). Den här appen integreras med Office-program genom att installera ett Office-tillägg så att användarna enkelt kan skydda filer direkt.

- Konfigurera program och tjänster för att stödja Azure RMS

- Skapa [anpassade mallar](/azure/information-protection/configure-policy-templates) som återspeglar dina affärs behov. Exempel: en mall för Top Secret-data som ska användas i alla Top Secret-relaterade e-postmeddelanden.

Organisationer som är svaga på [data klassificering](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) och Filskydd kan vara mer känsliga för data läckage. Utan rätt Filskydd kan organisationer inte erhålla affärs insikter, övervaka för missbruk och förhindra skadlig åtkomst till filer.

> [!Note]
> Du kan lära dig mer om Azure RMS genom att läsa artikeln [komma igång med Azure Rights Management](/azure/information-protection/requirements).

## <a name="secure-your-application"></a>Skydda ditt program
Även om Azure ansvarar för att skydda den infrastruktur och plattform som ditt program körs på, är det ditt ansvar att skydda själva programmet. Med andra ord måste du utveckla, distribuera och hantera din program kod och ditt innehåll på ett säkert sätt. Utan detta kan program koden eller innehållet fortfarande vara sårbart för hot.

### <a name="web-application-firewall"></a>Brandvägg för webbaserade program
[Brand vägg för webbaserade program (WAF)](../../web-application-firewall/ag/ag-overview.md) är en funktion i [Application Gateway](../../application-gateway/overview.md) som tillhandahåller centraliserat skydd av dina webb program från vanliga sårbarheter och sårbarheter.

Brandväggen använder regler från [OWASP Core Rule Sets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 eller 2.2.9. Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en Application Gateway med brandväggen för webbprogram.

Några vanliga säkerhetshot som brandväggen för webbaserade program skyddar mot:

- Skydd mot SQL-inmatning

- Skydd mot skriptkörning över flera webbplatser

- Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil

- Skydd mot åtgärder som inte följer HTTP-protokollet

- Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas

- Skydd mot robotar, crawlers och skannrar

- Identifiering av vanliga program konfigurationer (dvs. Apache, IIS osv.)

> [!Note]
> En mer detaljerad lista över regler och deras skydd finns i följande [kärn regel uppsättningar](../../web-application-firewall/ag/ag-overview.md):

Azure tillhandahåller också flera lättanvända funktioner som hjälper dig att säkra både inkommande och utgående trafik för din app. Azure hjälper också kunderna att skydda sin program kod genom att tillhandahålla externt tillhandahållna funktioner för att söka igenom webb programmet efter sårbarheter.

- [Konfigurera Azure Active Directory autentisering för din app](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [Skydda trafik till din app genom att aktivera Transport Layer Security (TLS/SSL)-HTTPS](../../app-service/configure-ssl-bindings.md)

  - [Tvinga all inkommande trafik över HTTPS-anslutning](http://microsoftazurewebsitescheatsheet.info/)

  - [Aktivera strikt transport säkerhet (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [Begränsa åtkomsten till din app via klientens IP-adress](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [Begränsa åtkomsten till din app genom klientens beteende – begär ande frekvens och samtidighet](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Sök igenom din webbapp för sårbarheter med TINFOIL Security scanning](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [Konfigurera ömsesidig TLS-autentisering så att klient certifikat måste anslutas till din webbapp](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [Konfigurera ett klient certifikat för användning från din app för att på ett säkert sätt ansluta till externa resurser](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [Ta bort standard server huvuden för att undvika verktyg från att använda finger avtryck för din app](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [Anslut appen på ett säkert sätt med resurser i ett privat nätverk med punkt-till-plats-VPN](../../app-service/web-sites-integrate-with-vnet.md)

- [Anslut appen på ett säkert sätt med resurser i ett privat nätverk med hjälp av Hybridanslutningar](../../app-service/app-service-hybrid-connections.md)

Azure App Service använder samma lösning för program mot skadlig kod som används av Azure Cloud Services och Virtual Machines. Mer information om detta hittar du i vår [dokumentation om program mot skadlig kod](antimalware.md).

## <a name="secure-your-network"></a>Skydda nätverket
Microsoft Azure innehåller en robust nätverks infrastruktur som stöder dina anslutnings krav för program och tjänster. Nätverks anslutningen är möjlig mellan resurser som finns i Azure, mellan lokala och Azure-värdbaserade resurser och till och från Internet och Azure.

Med [Azures nätverks infrastruktur](/previous-versions/azure/virtual-machines/windows/infrastructure-example) kan du på ett säkert sätt ansluta Azure-resurser till varandra med [virtuella nätverk (virtuella nätverk)](../../virtual-network/virtual-networks-overview.md). Ett VNet är en representation av ditt eget nätverk i molnet. Ett VNet är en logisk isolering av Azure Cloud Network dedikerad till din prenumeration. Du kan ansluta virtuella nätverk till dina lokala nätverk.

![Skydda nätverket (skydda)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

Om du behöver grundläggande åtkomst kontroll på nätverks nivå (baserat på IP-adress och TCP-eller UDP-protokoll) kan du använda [nätverks säkerhets grupper](../../virtual-network/virtual-network-vnet-plan-design-arm.md). En nätverks säkerhets grupp (NSG) är en grundläggande tillstånds känslig brand vägg för paket filtrering och du kan styra åtkomsten baserat på en [5-tupel](https://www.techopedia.com/definition/28190/5-tuple).

Azure-nätverk stöder möjligheten att anpassa routnings beteendet för nätverks trafik i virtuella Azure-nätverk. Det kan du göra genom att konfigurera [användardefinierade vägar](../../virtual-network/virtual-networks-udr-overview.md) i Azure.

[Tvingad tunnel trafik](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att se till att dina tjänster inte har tillåtelse att initiera en anslutning till enheter på Internet.

Azure har stöd för dedikerad WAN-anslutning till ditt lokala nätverk och en Azure-Virtual Network med [ExpressRoute](../../expressroute/expressroute-introduction.md). Länken mellan Azure och platsen använder en dedikerad anslutning som inte går via det offentliga Internet. Om ditt Azure-program körs i flera data Center kan du använda [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) för att dirigera begär Anden från användare intelligent över instanser av programmet. Du kan också dirigera trafik till tjänster som inte körs i Azure om de är tillgängliga från Internet.

## <a name="virtual-machine-security"></a>Säkerhet för virtuella datorer

Med [Azure Virtual Machines](../../virtual-machines/index.yml) kan du distribuera en mängd olika data behandlings lösningar på ett flexibelt sätt. Med stöd för Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP och Azure BizTalk Services kan du distribuera alla arbets belastningar och språk på nästan alla operativ system.

Med Azure kan du använda [program mot skadlig kod](antimalware.md) från säkerhets leverantörer som Microsoft, Symantec, Trend Micro och Kasper Sky för att skydda dina virtuella datorer från skadliga filer, annons program och andra hot.

Microsoft Antimalware för Azure Cloud Services och Virtual Machines är en real tids skydds funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara. Microsoft Antimalware tillhandahåller konfigurerbara aviseringar när kända skadlig eller oönskad program vara försöker installera sig själv eller köra på dina Azure-system.

[Azure Backup](../../backup/backup-overview.md) är en skalbar lösning som skyddar dina program data med noll kapital investeringar och minimala drifts kostnader. Program fel kan skada dina data och mänskliga fel kan leda till buggar i dina program. Med Azure Backup skyddas dina virtuella datorer som kör Windows och Linux.

[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) hjälper till att dirigera replikering, redundans och återställning av arbets belastningar och appar så att de är tillgängliga från en annan plats om din primära plats slutar fungera.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>Se till att efterlevnad: moln tjänster förfaller noggrannhet check lista

Microsoft har [Cloud Services utvecklat noggrannhet check lista](https://aka.ms/cloudchecklist.download) för att hjälpa organisationer att använda noggrannhet när de funderar på att flytta till molnet. Det ger en struktur för en organisation av valfri storlek och typ – privata företag och offentliga organisationer, inklusive myndigheter på alla nivåer och icke-täckningar, för att identifiera sina egna prestanda, tjänster, data hantering och styrnings mål och krav. Detta gör det möjligt för dem att jämföra erbjudandena med olika moln tjänst leverantörer, vilket i slut ändan utgör grunden för ett moln tjänst avtal.

Check listan innehåller ett ramverk som justerar sats-för-sats med en ny internationell standard för moln tjänst avtal, ISO/IEC 19086. Den här standarden erbjuder en enhetlig uppsättning överväganden för organisationer som hjälper dem att fatta beslut om moln införande och skapa ett gemensamt underlag för att jämföra moln tjänst erbjudanden.

Check listan befordrar en grundlig testats till molnet, vilket ger strukturerad vägledning och en konsekvent, upprepnings bar metod för att välja en moln tjänst leverantör.

Moln införande är inte längre ett tekniskt beslut. Eftersom krav på check lista rör varje aspekt i en organisation, kan de sammankalla alla viktiga interna besluts fattare, CHEFs-och IT samt juridiska, riskhantering, tillvaratagande och efterlevnad av personal. Detta ökar effektiviteten i besluts fattandet och på grund av besluts fattandet, vilket minskar sannolikheten för att oförutsedda hindren ska kunna antas.

Dessutom visas check listan:

- Visar viktiga diskussions ämnen för besluts fattare i början av moln införande processen.

- Har stöd för noggranna affärs diskussioner om regler och organisationens egna mål för sekretess, personligt identifierbar information (PII) och data säkerhet.

- Hjälper organisationer att identifiera eventuella eventuella problem som kan påverka ett moln projekt.

- Innehåller en konsekvent uppsättning frågor, med samma villkor, definitioner, mått och slut produkter för varje provider, för att förenkla processen med att jämföra erbjudanden från olika moln tjänst leverantörer.

## <a name="azure-infrastructure-and-application-security-validation"></a>Azure-infrastruktur och program säkerhets validering

[Azures drift säkerhet](operational-security.md) syftar på tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda data, program och andra till gångar i Microsoft Azure.

![säkerhets verifiering (identifiera)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azures drift säkerhet bygger på ett ramverk som införlivar den kunskap som du har fått via en mängd olika funktioner som är unika för Microsoft, inklusive Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Centre-programmet och djupgående medvetenhet om cybersäkerhet Threat landskap.

### <a name="microsoft-azure-monitor"></a>Övervakare för Microsoft Azure

[Azure Monitor](../../azure-monitor/index.yml) är IT-hanterings lösningen för Hybrid molnet. Azure Monitor loggar som används separat eller för att utöka din befintliga System Center-distribution ger dig maximal flexibilitet och kontroll för molnbaserad hantering av infrastrukturen.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Med Azure Monitor kan du hantera vilken instans som helst i alla moln, inklusive lokalt, Azure, AWS, Windows Server, Linux, VMware och OpenStack, till en lägre kostnad än konkurrens kraftigare lösningar. Azure Monitor har byggts för den molnbaserade världen, erbjuder en ny metod för att hantera företaget som är det snabbaste och mest kostnads effektiva sättet att möta nya affärs utmaningar och hantera nya arbets belastningar, program och moln miljöer.

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor-loggar](https://azure.microsoft.com/documentation/services/log-analytics) tillhandahåller övervaknings tjänster genom att samla in data från hanterade resurser till en central lagrings plats. Dessa data kan omfatta händelser, prestandadata eller anpassade data via API:t. När data har samlats in är de tillgängliga för avisering, analys och export.

![Azure Monitor-loggar](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

Med den här metoden kan du konsolidera data från olika källor, så att du kan kombinera data från dina Azure-tjänster med din befintliga lokala miljö. Metoden innebär också en tydlig separation mellan insamling av data från de åtgärder som vidtas för dessa data, vilket gör att alla åtgärder är tillgängliga för alla typer av data.

### <a name="azure-security-center"></a>Azure Security Center

Med hjälp av [Azure Security Center](../../security-center/security-center-introduction.md) kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla Azureprenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Säkerhetstillståndet på resurserna i Azure analyseras i Security Center för upptäckt av eventuella säkerhetsrisker. Genom en lista med rekommendationer får du hjälp att ställa in de kontrollfunktioner som behövs.

Exempel:

- att installera program som kan hitta och ta bort skadlig kod

- Konfigurera nätverks säkerhets grupper och regler för att styra trafik till virtuella datorer

- att installera brandväggar för webbaserade program för skydd mot angrepp riktade mot webbaserade program

- genomföra alla systemuppdateringar som fattas

- se till att operativsystemen är konfigurerade enligt rekommenderade baslinjer

Loggdata från resurserna i Azure, nätverket och partnerlösningarna, till exempel program mot skadlig kod och brandväggar, samlas in, analyseras och integreras automatiskt i Security Center. Om hot upptäcks skapas en säkerhetsavisering. Exempel på hot:

- Komprometterade virtuella datorer som kommunicerar med kända skadliga IP-adresser

- avancerad skadlig kod upptäckt genom felrapporteringen i Windows

- Brute force-attacker mot virtuella datorer

- säkerhetsaviseringar från integrerade brandväggar och program mot skadlig kod

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../../azure-monitor/overview.md) tillhandahåller länkar till information om vissa typer av resurser. Den erbjuder visualisering, frågor, routning, avisering, automatisk skalning och automatisering av data både från Azure-infrastrukturen (aktivitets loggen) och varje enskild Azure-resurs (diagnostikloggar).

Moln program är komplexa med många rörliga delar. Övervakning tillhandahåller data för att säkerställa att programmet hålls igång i felfritt tillstånd. Det hjälper dig också att Stave av potentiella problem eller att felsöka tidigare.

![Diagram som visar hur du kan använda övervaknings data för att få djupgående insikter om ditt program.](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png)
Dessutom kan du använda övervaknings data för att få djupgående insikter om ditt program. Den kunskapen kan hjälpa dig att förbättra programmets prestanda eller hanterbarhet, eller automatisera åtgärder som annars skulle kräva manuell åtgärd.

Det är viktigt att granska nätverks säkerheten för att identifiera nätverks sårbarheter och säkerställa efterlevnaden av din IT-säkerhet och reglerande styrnings modell. Med vyn säkerhets grupp kan du hämta den konfigurerade nätverks säkerhets gruppen och säkerhets reglerna, samt de effektiva säkerhets reglerna. Med listan över regler som tillämpas kan du fastställa vilka portar som är öppna och SS-nätverks sårbarhet.

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på en nätverks nivå i, till och från Azure. Diagnostikverktyg för nätverk och visualiserings verktyg som är tillgängliga med Network Watcher hjälpa dig att förstå, diagnostisera och få insikter om ditt nätverk i Azure. Den här tjänsten omfattar paket insamling, nästa hopp, kontrol lera IP-flöde, vyn säkerhets grupp, NSG Flow-loggar. Övervakning av scenario nivå ger en heltäckande vy över nätverks resurser i motsats till övervakning av enskilda nätverks resurser.

### <a name="storage-analytics"></a>Lagringsanalys

[Lagringsanalys](/rest/api/storageservices/fileservices/storage-analytics) kan lagra mått som innehåller aggregerad transaktions statistik och kapacitets data om begär anden till en lagrings tjänst. Transaktioner rapporteras både på API-nivå och på lagrings tjänst nivå, och kapaciteten rapporteras på lagrings tjänst nivå. Mät data kan användas för att analysera användningen av lagrings tjänster, diagnostisera problem med begär Anden som gjorts mot lagrings tjänsten och förbättra prestanda för program som använder en tjänst.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) är en utöknings bar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Den identifierar automatiskt prestandaavvikelser. Den innehåller kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå vad användarna gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet. Det fungerar för appar på en mängd olika plattformar, inklusive .NET, Node.js och Java-EE, som finns lokalt eller i molnet. Den integreras med din devOps-process och har anslutnings punkter till olika utvecklingsverktyg.

Tjänsten övervakar:

- **Begärandefrekvens, svarstider och felfrekvens** – Ta reda på vilka sidor som är mest populära, vid vilka tidpunkter på dagen och var dina användare finns. Se vilka sidor som fungerar bäst. Om svarstiden och felfrekvensen är hög när det finns många begäranden kan det bero på ett resurstilldelningsproblem.

- **Beroendefrekvens, svarstider och felfrekvens** – Ta reda på om externa tjänster gör systemet långsammare.

- **Undantag** – Analysera aggregerad statistik eller välj specifika instanser och visa detaljerad information om stackspårningen och relaterade begäranden. Både server- och webbläsarundantag rapporteras.

- **Sidvyer och inläsningsprestanda** – Rapporteras av användarnas webbläsare.

- **AJAX-anrop från webb sidor** – hastigheter, svars tider och felpriser.

- **Antal användare och sessioner.**

- **Prestandaräknare** från dina Windows- eller Linux-serverdatorer, till exempel processor, minne och nätverksanvändning.

- **Värddiagnostik** från Docker eller Azure.

- **Diagnostikspårningsloggar** från din app – så att du kan jämföra spårningshändelser med begäranden.

- **Anpassade händelser och mät värden** som du skriver själv i klient-eller Server koden för att spåra affärs händelser, t. ex. sålda artiklar eller vunna spel.

Infrastrukturen för ditt program består normalt av många komponenter – kanske en virtuell dator, ett lagringskonto och ett virtuellt nätverk eller en webbapp, en databas, en databasserver och tjänster från tredje part. Du ser inte de här komponenterna som separata entiteter, utan som relaterade delar av samma enhet som är beroende av varandra. Du vill distribuera, hantera och övervaka dem som en grupp. Med [Azure Resource Manager](../../azure-resource-manager/management/overview.md) kan du arbeta med resurserna i din lösning som en grupp.

Du kan distribuera, uppdatera eller ta bort alla resurser i lösningen i en enda, samordnad åtgärd. Du använder en mall för distributionen. Mallen kan användas i olika miljöer, till exempel för testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper dig att hantera dina resurser efter distributionen.

**Fördelarna med att använda Resource Manager**

Resource Manager har flera fördelar:

- Du kan distribuera, hantera och övervaka alla resurserna för din lösning som en grupp i stället för att hantera resurserna separat.

- Du kan distribuera lösningen flera gånger genom utvecklingslivscykeln och vara säker på att dina resurser distribueras på ett enhetligt sätt.

- Du kan hantera infrastrukturen med hjälp av deklarativa mallar i stället för skript.

- Du kan definiera beroenden mellan resurser så att de distribueras i rätt ordning.

- Du kan använda åtkomst kontroll för alla tjänster i din resurs grupp eftersom Azure-rollbaserad åtkomst kontroll (Azure RBAC) är inbyggt integrerat i hanterings plattformen.

- Du kan lägga till taggar för resurser och organisera alla logiskt i din prenumeration.

- Du kan tydliggöra din organisations fakturering genom att visa kostnaderna för en grupp av resurser som delar samma tagg.

> [!Note]
> Resource Manager erbjuder ett nytt sätt att distribuera och hantera lösningar. Om du använde den tidigare distributions modellen och vill lära dig mer om ändringarna, se [förstå Resource Manager-distribution och klassisk distribution](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om säkerhet genom att läsa några av våra djupgående säkerhets ämnen:

- [Granskning och loggning](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [Cyberbrott](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [Design-och drift säkerhet](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [Kryptering](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [Identitets- och åtkomsthantering](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [Nätverkssäkerhet](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [Hothantering](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)