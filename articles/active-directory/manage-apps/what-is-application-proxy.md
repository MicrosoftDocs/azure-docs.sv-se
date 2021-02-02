---
title: Publicera lokala appar med Azure AD-programproxy
description: Ta reda på varför du kan använda Application Proxy för att publicera lokala webb program externt för fjärran vändare. Lär dig mer om arkitektur, anslutningar, autentiseringsmetoder och säkerhets förmåner i Application Proxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 12/31/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 1569e577ec535a4680cfaec6f7975c056275ecbe
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258786"
---
# <a name="using-azure-ad-application-proxy-to-publish-on-premises-apps-for-remote-users"></a>Använda Azure AD-programproxy för att publicera lokala appar för fjärranslutna användare

Azure Active Directory (Azure AD) erbjuder många funktioner för att skydda användare, appar och data i molnet och lokalt. I synnerhet kan Azure AD-programproxy-funktionen implementeras av IT-proffs som vill publicera lokala webb program externt. Fjärran vändare som behöver åtkomst till interna appar kan sedan komma åt dem på ett säkert sätt.

Möjligheten att säkert komma åt interna appar utanför nätverket blir ännu mer kritiska på den moderna arbets platsen. Med scenarier som BYOD (ta med din egen enhet) och mobila enheter, utmanas IT-proffs för att uppfylla två mål:

* Gör det möjligt för slutanvändarna att vara produktiva när och var som helst
* Skydda företags till gångar hela tiden

Många organisationer tror att de är i kontroll och skyddade när det finns resurser inom gränserna för företagets nätverk. Men i dagens digitala arbets plats har denna gränser utökats med hanterade mobila enheter och resurser och tjänster i molnet. Nu måste du hantera komplexiteten för att skydda användarnas identiteter och data som lagras på deras enheter och appar.

Kanske använder du redan Azure AD för att hantera användare i molnet som behöver komma åt Microsoft 365 och andra SaaS-program, samt webbappar som finns lokalt. Om du redan har Azure AD kan du utnyttja det som ett enda kontroll plan för att ge sömlös och säker åtkomst till dina lokala program. Eller så kanske du fortfarande överväger en flytt till molnet. I så fall kan du påbörja resan till molnet genom att implementera programproxy och ta det första steget i att skapa en stark identitets grund.

I listan nedan visas några av de saker som du kan aktivera genom att implementera App proxy i ett scenario med hybrid samexistens:

* Publicera lokala webb program externt på ett förenklat sätt utan en DMZ
* Stöd för enkel inloggning (SSO) mellan enheter, resurser och appar i molnet och lokalt
* Stöd för Multi-Factor Authentication för appar i molnet och lokalt
* Utnyttja moln funktioner snabbt med Microsoft Cloudens säkerhet
* Centralisera hanteringen av användar konton
* Centralisera kontrollen av identitet och säkerhet
* Lägg automatiskt till eller ta bort användar åtkomst till program baserat på grupp medlemskap

Den här artikeln förklarar hur Azure AD och Application Proxy ger fjärranslutna användare en enkel inloggning (SSO). Användare ansluter säkert till lokala appar utan en VPN-eller Dual-Home-Server och brand Väggs regler. Den här artikeln hjälper dig att förstå hur programproxyn ger till gång till funktioner och säkerhets fördelarna med molnet i dina lokala webb program. Den beskriver också arkitekturen och topologier som är möjliga.

## <a name="remote-access-in-the-past"></a>Fjärråtkomst tidigare

Tidigare var ditt kontroll plan för att skydda interna resurser från angripare samtidigt som fjärran vändarna lätt kan komma åt dem i DMZ, eller i perimeternätverket. Men VPN-och omvända proxy-lösningar som distribuerats i DMZ som används av externa klienter för att komma åt företags resurser är inte lämpliga för moln världen. De har vanligt vis drabbats av följande nack delar:

* Maskinvarukostnader
* Upprätthålla säkerhet (uppdatering, övervakning av portar osv.)
* Autentisera användare på gränsen
* Autentisera användare till webb servrar i perimeternätverket
* Upprätthålla VPN-åtkomst för fjärran vändare med distribution och konfiguration av VPN-klientprogramvara. Du kan också underhålla domänanslutna servrar i DMZ, som kan vara sårbara för externa attacker.

I dagens moln – första världen är Azure AD bäst lämpad att styra vem och vad som ingår i nätverket. Azure AD-programproxy integreras med modern autentisering och molnbaserade tekniker, t. ex. SaaS program och identitets leverantörer. Den här integrationen gör det möjligt för användare att komma åt appar varifrån som helst. Det är inte bara App proxy som passar bäst för dagens digitala arbets platser, men det är säkrare än VPN-och omvänd proxy-lösning och enklare att implementera. Fjärran vändare kan komma åt dina lokala program på samma sätt som de kommer åt Microsoft och andra SaaS-appar som är integrerade med Azure AD. Du behöver inte ändra eller uppdatera dina program för att fungera med programproxy. Dessutom kräver App proxy inte att du öppnar inkommande anslutningar via brand väggen. Med App proxy ställer du bara in den och glömmer den.

## <a name="the-future-of-remote-access"></a>Framtiden för fjärråtkomst

I dagens digitala arbets plats fungerar användarna var som helst med flera enheter och appar. Den enda konstanten är användar identitet. Det är därför som det första steget i ett säkert nätverk idag är att använda [Azure Ads hanterings funktioner för identiteter](../../security/fundamentals/identity-management-overview.md) som ditt säkerhets kontroll plan. En modell som använder identitet som ditt kontroll plan består vanligt vis av följande komponenter:

* En identitets leverantör för att hålla reda på användare och användar relaterad information.
* Enhets katalog för att underhålla en lista över enheter som har åtkomst till företags resurser. Den här katalogen innehåller motsvarande enhets information (till exempel typ av enhet, integritet osv.).
* Princip utvärderings tjänst för att avgöra om en användare och enhet följer den princip som anges av säkerhets administratörer.
* Möjlighet att bevilja eller neka åtkomst till organisations resurser.

Med Application Proxy håller Azure AD reda på användare som behöver åtkomst till webbappar som publicerats lokalt och i molnet. Den tillhandahåller en central hanterings plats för dessa appar. Även om det inte krävs rekommenderar vi att du även aktiverar villkorlig åtkomst för Azure AD. Genom att definiera villkor för hur användare autentiserar och får åtkomst kan du se till att rätt personer har åtkomst till program.

**Obs:** Det är viktigt att förstå att Azure AD-programproxy är avsedd som en VPN-eller omvänd proxy för nätverks växling (eller fjärranslutna) användare som behöver åtkomst till interna resurser. Den är inte avsedd för interna användare i företags nätverket. Interna användare som i onödan använder Application Proxy kan orsaka oväntade och oönskade prestanda problem.

![Azure Active Directory och alla dina appar](media/what-is-application-proxy/azure-ad-and-all-your-apps.png)

### <a name="an-overview-of-how-app-proxy-works"></a>En översikt över hur App proxy fungerar

Application Proxy är en Azure AD-tjänst som du konfigurerar i Azure Portal. Du kan publicera en extern offentlig HTTP/HTTPS URL-slutpunkt i Azure-molnet, som ansluter till en intern program server-URL i din organisation. Dessa lokala webbappar kan integreras med Azure AD för att stödja enkel inloggning. Slutanvändare kan sedan komma åt lokala webbappar på samma sätt som de kommer åt Microsoft 365 och andra SaaS-appar.

Komponenterna i den här funktionen inkluderar Application Proxy-tjänsten som körs i molnet, Application Proxy Connector, som är en Lightweight-agent som körs på en lokal server och Azure AD, som är identitets leverantören. Alla tre komponenterna fungerar tillsammans för att ge användaren en enkel inloggnings upplevelse för att komma åt lokala webb program.

När du har loggat in kan externa användare komma åt lokala webb program genom att använda en välbekant URL eller [Mina appar](../user-help/my-apps-portal-end-user-access.md) från sina Desktop-eller iOS/Mac-enheter. Till exempel kan App proxy tillhandahålla fjärråtkomst och enkel inloggning till fjärr skrivbord, SharePoint-webbplatser, Tableau, Qlik, Outlook på webben och branschspecifika program (LOB).

![Azure AD-programproxy-arkitektur](media/what-is-application-proxy/azure-ad-application-proxy-architecture.png)

### <a name="authentication"></a>Autentisering

Det finns flera sätt att konfigurera ett program för enkel inloggning och den metod du väljer beror på vilken autentisering som används i programmet. Application Proxy stöder följande typer av program:

* Webbprogram
* Webb-API: er som du vill exponera för rika program på olika enheter
* Program som finns bakom en fjärr skrivbords-Gateway
* Rika klient program som är integrerade med [Microsoft Authentication Library (MSAL)](../develop/v2-overview.md)

App proxy fungerar med appar som använder följande interna autentiseringsprotokoll:

* **[Integrerad Windows-autentisering (IWA)](application-proxy-configure-single-sign-on-with-kcd.md).** För IWA använder Application Proxy-kopplingarna Kerberos-begränsad delegering (KCD) för att autentisera användare till Kerberos-programmet.

App proxy stöder även följande autentiseringsprotokoll med integration från tredje part eller i vissa konfigurations scenarier:

* [**Rubrik baserad autentisering**](./application-proxy-configure-single-sign-on-with-headers.md). Den här inloggnings metoden använder en tredjeparts autentiseringstjänst som kallas PingAccess och används när programmet använder rubriker för autentisering. I det här scenariot hanteras autentiseringen av PingAccess.
* [**Formulär-eller lösenordsbaserad autentisering**](application-proxy-configure-single-sign-on-password-vaulting.md). Med den här autentiseringsmetoden loggar användare in på programmet med ett användar namn och lösen ord första gången de använder den. Efter den första inloggningen skickar Azure AD användar namn och lösen ord till programmet. I det här scenariot hanteras autentiseringen av Azure AD.
* [**SAML-autentisering**](application-proxy-configure-single-sign-on-on-premises-apps.md). SAML-baserad enkel inloggning stöds för program som använder antingen SAML 2,0 eller WS-Federation protokoll. Med enkel inloggning i SAML autentiserar Azure AD programmet med hjälp av användarens Azure AD-konto.

Mer information om metoder som stöds finns i [välja en metod för enkel inloggning](sso-options.md#choosing-a-single-sign-on-method).

### <a name="security-benefits"></a>Säkerhets förmåner

Lösningen för fjärråtkomst som erbjuds av programproxy och Azure AD stöder flera säkerhets förmåner som kunder kan dra nytta av, inklusive:

* **Autentiserad åtkomst**. Programproxyn lämpar sig bäst för att publicera program med [förautentisering](application-proxy-security.md#authenticated-access) för att säkerställa att endast autentiserade anslutningar når nätverket. För program som publiceras med förautentisering tillåts ingen trafik att passera via App proxy-tjänsten till din lokala miljö utan giltig token. För autentisering är det mycket viktigt att blockera ett stort antal riktade attacker eftersom endast autentiserade identiteter kan komma åt backend-programmet.
* **Villkorlig åtkomst**. Bättre princip kontroller kan tillämpas innan anslutningar till nätverket upprättas. Med villkorlig åtkomst kan du definiera begränsningar för den trafik som du tillåter för att nå Server dels programmet. Du skapar principer som begränsar inloggningar baserat på plats, styrkan för autentisering och användar risk profil. Allteftersom villkorlig åtkomst utvecklas läggs fler kontroller till för att ge ytterligare säkerhet, till exempel integrering med Microsoft Cloud App Security (MCAS). Med MCAS-integrering kan du konfigurera ett lokalt program för [övervakning i real tid](application-proxy-integrate-with-microsoft-cloud-application-security.md) genom att använda villkorlig åtkomst för att övervaka och kontrol lera sessioner i real tid baserat på principer för villkorlig åtkomst.
* **Trafik terminering**. All trafik till Server dels programmet avslutas vid Application Proxy-tjänsten i molnet medan sessionen återupprättas med backend-servern. Den här anslutnings strategin innebär att backend-servrarna inte exponeras för direkt HTTP-trafik. De är bättre skyddade mot riktade DoS-attacker (Denial-of-Service) eftersom brand väggen inte är utsatt för angrepp.
* **All åtkomst är utgående**. Application Proxy-kopplingar använder bara utgående anslutningar till Application Proxy-tjänsten i molnet över portarna 80 och 443. Utan några inkommande anslutningar behöver du inte öppna brand Väggs portar för inkommande anslutningar eller komponenter i DMZ. Alla anslutningar är utgående och via en säker kanal.
* **Security Analytics och Machine Learning (ml) baserad intelligens**. Eftersom den är en del av Azure Active Directory kan programproxyn utnyttja [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) (kräver [Premium P2-licensiering](https://azure.microsoft.com/pricing/details/active-directory/)). Azure AD Identity Protection kombinerar säkerhets information för maskin inlärning med datafeeds från Microsofts [digitala brottslighet-enhet](https://news.microsoft.com/stories/cybercrime/index.html) och [Microsoft Security Response Center](https://www.microsoft.com/msrc) för att proaktivt identifiera komprometterade konton. Identitets skydd ger real tids skydd från högrisk inloggningar. Det tar hänsyn till faktorer som åtkomst från infekterade enheter, via maskera-nätverk eller från ovanlig och osannolika platser för att öka risk profilen för en session. Den här risk profilen används för real tids skydd. Många av dessa rapporter och händelser är redan tillgängliga via ett API för integrering med dina SIEM-system.

* **Fjärråtkomst som en tjänst**. Du behöver inte bekymra dig om att underhålla och uppdatera lokala servrar för att aktivera fjärråtkomst. Application Proxy är en Internet skalnings tjänst som Microsoft äger, så att du alltid får de senaste säkerhets korrigeringarna och-uppgraderingar. Program vara som inte har uppdaterats har fortfarande konton för ett stort antal attacker. Enligt Homeland-säkerhetsavdelningen är det så många som [85 procent av riktade angrepp som kan förhindras](https://www.us-cert.gov/ncas/alerts/TA15-119A). Med den här tjänst modellen behöver du inte ha den stora belastningen på att hantera dina Edge-servrar längre och du kan koda för att korrigera dem efter behov.

* **Intune-integrering**. Med Intune dirigeras företags trafiken separat från personlig trafik. Application Proxy säkerställer att företags trafiken autentiseras. [Programproxyn och Intune Managed browsers](/intune/app-configuration-managed-browser#how-to-configure-application-proxy-settings-for-protected-browsers) förmågan kan också användas tillsammans för att ge fjärran vändare åtkomst till interna webbplatser från iOS-och Android-enheter på ett säkert sätt.

### <a name="roadmap-to-the-cloud"></a>Översikt över molnet

En annan stor fördel med att implementera programproxyn är att utöka Azure AD till din lokala miljö. I själva verket är implementering av App proxy ett viktigt steg i att flytta din organisation och dina appar till molnet. Genom att flytta till molnet och bort från lokal autentisering minskar du ditt lokala utrymme och använder Azure ADs identitets hanterings funktioner som kontroll plan. Med minimala eller inga uppdateringar av befintliga program har du till gång till moln funktioner som enkel inloggning, Multi-Factor Authentication och central hantering. Att installera nödvändiga komponenter till App proxy är en enkel process för att upprätta ett ramverk för fjärråtkomst. Och genom att flytta till molnet har du till gång till de senaste Azure AD-funktionerna, uppdateringarna och funktionerna, till exempel hög tillgänglighet och haveri beredskap.

Mer information om hur du migrerar dina appar till Azure AD finns i [migrera dina program till Azure Active Directory](https://aka.ms/migrateapps/whitepaper) White Paper.

## <a name="architecture"></a>Arkitektur

Följande diagram illustrerar i allmänhet hur Azure AD Authentication Services och Application Proxy arbetar tillsammans för att tillhandahålla enkel inloggning till lokala program för slutanvändare.

![Azure AD-programproxy Authentication Flow](media/what-is-application-proxy/azure-ad-application-proxy-authentication-flow.png)

1. När användaren har åtkomst till programmet via en slut punkt omdirigeras användaren till inloggnings sidan för Azure AD. Om du har konfigurerat principer för villkorlig åtkomst kontrol leras vissa villkor just nu för att säkerställa att du följer organisationens säkerhets krav.
2. Efter en lyckad inloggning skickar Azure AD en token till användarens klient enhet.
3. Klienten skickar token till Application Proxy-tjänsten som hämtar User Principal Name (UPN) och säkerhets huvud namn (SPN) från token.
4. Programproxyn vidarebefordrar begäran som hämtas av Application Proxy [Connector](application-proxy-connectors.md).
5. Anslutningen utför all ytterligare autentisering som krävs för användarens räkning (*valfritt beroende på autentiseringsmetod*), begär den interna slut punkten för program servern och skickar begäran till det lokala programmet.
6. Svaret från program servern skickas via anslutningen till Application Proxy-tjänsten.
7. Svaret skickas från Application Proxy-tjänsten till användaren.

|**Komponent**|**Beskrivning**|
|:-|:-|
|Slutpunkt|Slut punkten är en URL eller en [slut användar Portal](end-user-experiences.md). Användare kan nå program utanför nätverket genom att ha åtkomst till en extern URL. Användare i nätverket kan komma åt programmet via en URL eller en slut användar Portal. När användarna går till någon av dessa slut punkter, autentiseras de i Azure AD och dirigeras sedan via anslutningen till det lokala programmet.|
|Azure AD|Azure AD utför autentiseringen med hjälp av klient katalogen som lagras i molnet.|
|Application Proxy-tjänst|Tjänsten Application Proxy körs i molnet som en del av Azure AD. Den skickar inloggnings-token från användaren till Application Proxy Connector. Programproxyn vidarebefordrar eventuella tillgängliga huvuden på begäran och anger huvudena enligt dess protokoll till klientens IP-adress. Om den inkommande begäran till proxyn redan har den rubriken, läggs klientens IP-adress till i slutet av den kommaseparerade listan som är värdet för rubriken.|
|Application Proxy Connector|Anslutningen är en Lightweight-agent som körs på en Windows-Server i nätverket. Anslutningen hanterar kommunikationen mellan Application Proxy-tjänsten i molnet och det lokala programmet. Anslutningen använder bara utgående anslutningar, så du behöver inte öppna några inkommande portar eller publicera något i DMZ. Anslutningarna är tillstånds lösa och hämtar information från molnet om det behövs. Mer information om anslutningar, t. ex. hur de kan belastningsutjämna och autentiseras, finns i [förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md).|
|Active Directory (AD)|Active Directory körs lokalt för att utföra autentisering för domän konton. När enkel inloggning har kon figurer ATS kommunicerar anslutningen med AD för att utföra ytterligare autentisering krävs.|
|Lokalt program|Slutligen kan användaren komma åt ett lokalt program.|

Azure AD-programproxy består av den molnbaserade tjänsten Application Proxy och en lokal anslutning. Anslutningen lyssnar efter begär Anden från Application Proxy-tjänsten och hanterar anslutningar till de interna programmen. Det är viktigt att Observera att all kommunikation sker över TLS och alltid kommer till-anslutningen till Application Proxy-tjänsten. Det vill säga att kommunikation endast är utgående. Anslutningen använder ett klient certifikat för att autentisera till Application Proxy-tjänsten för alla anrop. Det enda undantaget till anslutnings säkerheten är det första installations steget där klient certifikatet upprättas. Se programproxyn [under huven](application-proxy-security.md#under-the-hood) för mer information.

### <a name="application-proxy-connectors"></a>Application Proxy-kopplingar

[Application Proxy-kopplingar](application-proxy-connectors.md) är lätta distributioner som distribueras lokalt och som underlättar den utgående anslutningen till Application Proxy-tjänsten i molnet. Anslutningarna måste installeras på en Windows-Server som har åtkomst till backend-programmet. Användare ansluter till moln tjänsten för App-proxy som dirigerar sin trafik till apparna via anslutningarna enligt nedan.

![Azure AD-programproxy nätverks anslutningar](media/what-is-application-proxy/azure-ad-application-proxy-network-connections.png)

Installation och registrering mellan en anslutning och App proxy-tjänsten utförs på följande sätt:

1. IT-administratören öppnar portarna 80 och 443 för utgående trafik och ger åtkomst till flera URL: er som behövs för anslutningen, App proxy-tjänsten och Azure AD.
2. Administratören loggar in på Azure Portal och kör en körbar fil för att installera anslutningen på en lokal Windows Server.
3. Anslutningen startar till "lyssna" till App proxy-tjänsten.
4. Administratören lägger till det lokala programmet i Azure AD och konfigurerar inställningar, till exempel de webb adresser som användarna behöver för att ansluta till sina appar.

Mer information finns i [Planera en Azure AD-programproxy-distribution](application-proxy-deployment-plan.md).

Vi rekommenderar att du alltid distribuerar flera anslutningar för redundans och skalning. Anslutningarna, tillsammans med tjänsten, tar hand om alla aktiviteter för hög tillgänglighet och kan läggas till eller tas bort dynamiskt. Varje gång en ny begäran tas emot dirigeras den till en av de tillgängliga anslutningarna. När en anslutning körs förblir den aktiv när den ansluter till tjänsten. Om en koppling är tillfälligt otillgänglig, svarar den inte på den här trafiken. Oanvända kopplingar är taggade som inaktiva och tas bort efter 10 dagars inaktivitet.

Anslutningar avsöker också servern för att ta reda på om det finns en nyare version av anslutningen. Även om du kan göra en manuell uppdatering kommer kopplingarna att uppdateras automatiskt så länge programproxyn Connector Updater tjänsten körs. För klienter med flera anslutningar är de automatiska uppdateringarna riktade till en koppling i taget i varje grupp för att förhindra drift stopp i din miljö.

**Obs!** du kan övervaka [versions historik sidan](application-proxy-release-version-history.md) för programproxyn för att bli meddelad när uppdateringar har släppts genom att prenumerera på dess RSS-flöde.

Varje Application Proxy-koppling är tilldelad till en [kopplings grupp](application-proxy-connector-groups.md). Kopplingar i samma kopplings grupp fungerar som en enskild enhet för hög tillgänglighet och belastnings utjämning. Du kan skapa nya grupper, tilldela kopplingar till dem i Azure Portal och sedan tilldela vissa kopplingar för att betjäna vissa program. Vi rekommenderar att du har minst två kopplingar i varje anslutnings grupp för hög tillgänglighet.

Kopplings grupper är användbara när du behöver stöd för följande scenarier:

* Geografisk publicering av appar
* Program segmentering/isolering
* Publicera webbappar som körs i molnet eller lokalt

Mer information om hur du väljer var du vill installera dina anslutningar och hur du optimerar ditt nätverk finns i [nätverks sto pol faktorer när du använder Azure Active Directory-programproxy](application-proxy-network-topology.md).

## <a name="other-use-cases"></a>Andra användnings fall

Hittills har vi fokuserat på att använda Application Proxy för att publicera lokala appar externt samtidigt som du aktiverar enkel inloggning till alla molnbaserade och lokala appar. Det finns dock andra användnings fall för App proxy som är värda att nämna. De omfattar:

* **Publicera REST-API: er på ett säkert sätt**. När du har affärs logik eller API: er som körs lokalt eller som finns på virtuella datorer i molnet, tillhandahåller Application Proxy en offentlig slut punkt för API-åtkomst. Åtkomst till API-slutpunkt gör att du kan styra autentisering och auktorisering utan att kräva inkommande portar. Den ger ytterligare säkerhet genom Azure AD Premium funktioner som Multi-Factor Authentication och enhets villkorliga åtkomst för Station ära datorer, iOS-, Mac-och Android-enheter med hjälp av Intune. Mer information finns i [så här aktiverar du interna klient program för att interagera med proxy-program](application-proxy-configure-native-client-application.md) och [skydda ett API med hjälp av OAuth 2,0 med Azure Active Directory och API Management](../../api-management/api-management-howto-protect-backend-with-aad.md).
* **Fjärrskrivbordstjänster** **(RDS)**. Standard-RDS-distributioner kräver öppna inkommande anslutningar. Dock har [RDS-distributionen med Application Proxy](application-proxy-integrate-with-remote-desktop-services.md) en permanent utgående anslutning från servern som kör anslutnings tjänsten. På så sätt kan du erbjuda fler program till slutanvändare genom att publicera lokala program via Fjärrskrivbordstjänster. Du kan också minska angrepps ytan för distributionen med en begränsad uppsättning tvåstegsverifiering och villkorliga åtkomst kontroller till fjärr skrivbords tjänster.
* **Publicera program som ansluter med WebSockets**. Support med [Qlik Sense](application-proxy-qlik.md) är i offentlig för hands version och kommer att utökas till andra appar i framtiden.
* **Aktivera interna klient program för att interagera med proxyprogram**. Du kan använda Azure-AD-programproxy för att publicera webbappar, men det kan också användas för att publicera [interna klient program](application-proxy-configure-native-client-application.md) som har kon figurer ATS med Azure AD Authentication Library (ADAL). Interna klient program skiljer sig från webbappar eftersom de är installerade på en enhet, medan webb program nås via en webbläsare.

## <a name="conclusion"></a>Slutsats

Hur vi arbetar och de verktyg som vi använder ändras snabbt. Med fler anställda som använder sina egna enheter för att arbeta och den genomgripande användningen av SaaS-program (program vara som en tjänst), så kan organisationerna hantera och skydda sina data måste också utvecklas. Företag arbetar inte längre enbart inom sina egna väggar som skyddas av en Moat som omger sin gräns. Data överförs till fler platser än förut – i både lokala och molnbaserade miljöer. Den här utvecklingen har hjälpt till att öka användarnas produktivitet och möjlighet att samar beta, men det gör också det svårare att skydda känsliga data.

Oavsett om du använder Azure AD för att hantera användare i ett hybrid scenario eller om du vill starta din resa till molnet kan du med hjälp av Azure AD-programproxy minska storleken på dina lokala utrymmen genom att tillhandahålla fjärråtkomst som en tjänst.

Organisationer ska börja dra nytta av App proxy idag för att dra nytta av följande fördelar:

* Publicera lokala appar externt utan att du behöver ha till gång till traditionell VPN eller andra lokala webb publicerings lösningar och DMZ-metoder
* Enkel inloggning till alla program, var de Microsoft 365 eller andra SaaS-appar och inklusive lokala program
* Säkerhet för moln skala där Azure AD utnyttjar Microsoft 365 telemetri för att förhindra obehörig åtkomst
* Intune-integrering för att säkerställa att företags trafiken autentiseras
* Centralisering för användar konto hantering
* Automatiska uppdateringar för att se till att du har de senaste säkerhets korrigeringarna
* Nya funktioner när de släpps. det senaste stödet för SAML enkel inloggning och mer detaljerad hantering av program cookies

## <a name="next-steps"></a>Nästa steg

* Information om hur du planerar, hanterar och hanterar Azure AD-programproxy finns i [Planera en azure AD-programproxy-distribution](application-proxy-deployment-plan.md).
* Om du vill schemalägga en live-demo eller få en kostnads fri 90-dagars utvärderings version kan du läsa [komma igång med Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial).
