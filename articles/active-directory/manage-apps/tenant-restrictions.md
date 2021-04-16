---
title: Använda klientbegränsningar för att hantera åtkomst till SaaS-appar – Azure AD
description: Hur du använder klientbegränsningar för att hantera vilka användare som kan komma åt appar baserat på deras Azure AD-klientorganisation.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 4/6/2021
ms.author: iangithinji
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 941bf61f3387abf19be58bdd4d8861ab123e244f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376587"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Använda klientbegränsningar för att hantera åtkomst till SaaS-molnprogram

Stora organisationer som betonar säkerhet vill flytta till molntjänster som Microsoft 365, men behöver veta att deras användare bara kan komma åt godkända resurser. Traditionellt begränsar företag domännamn eller IP-adresser när de vill hantera åtkomst. Den här metoden fungerar inte i en värld där appar för programvara som en tjänst (eller SaaS) finns i ett offentligt moln som körs på delade domännamn som [outlook.office.com](https://outlook.office.com/) [och login.microsoftonline.com](https://login.microsoftonline.com/). Om du blockerar dessa adresser kan användarna inte komma åt Outlook på webben helt, i stället för att bara begränsa dem till godkända identiteter och resurser.

Lösningen Azure Active Directory (Azure AD) för den här utmaningen är en funktion som kallas klientbegränsningar. Med klientbegränsningar kan organisationer styra åtkomsten till SaaS-molnprogram, baserat på Azure AD-klientorganisationen som programmen använder för enkel inloggning. Du kanske till exempel vill tillåta åtkomst till organisationens program Microsoft 365, samtidigt som du förhindrar åtkomst till andra organisationers instanser av samma program.  

Med klientbegränsningar kan organisationer ange en lista över klienter som deras användare har behörighet att komma åt. Azure AD beviljar sedan endast åtkomst till dessa tillåtna klienter.

Den här artikeln fokuserar på klientbegränsningar för Microsoft 365, men funktionen skyddar alla appar som skickar användaren till Azure AD för enkel inloggning. Om du använder SaaS-appar med en annan Azure AD-klientorganisation än den klientorganisation som används av din Microsoft 365 kontrollerar du att alla nödvändiga klienter är tillåtna (t.ex. i B2B-samarbetsscenarier). Mer information om SaaS-molnappar finns i [Active Directory Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps)

Dessutom stöder funktionen för klientbegränsningar nu blockera användningen av [alla Microsoft-konsumentprogram](#blocking-consumer-applications-public-preview) (MSA-appar), till exempel OneDrive, Hotmail och Xbox.com.  Detta använder ett separat sidhuvud `login.live.com` till slutpunkten och beskrivs i slutet av dokumentet.

## <a name="how-it-works"></a>Så här fungerar det

Den övergripande lösningen består av följande komponenter:

1. **Azure AD:** Om `Restrict-Access-To-Tenants: <permitted tenant list>` -huvudet finns utfärdar Azure AD endast säkerhetstoken för de tillåtna klienterna.

2. **Infrastruktur för lokal proxyserver:** Den här infrastrukturen är en proxyenhet som kan Transport Layer Security (TLS)-kontroll. Du måste konfigurera proxyn för att infoga rubriken som innehåller listan över tillåtna klienter i trafik till Azure AD.

3. **Klientprogramvara:** För att stödja klientbegränsningar måste klientprogramvaran begära token direkt från Azure AD, så att proxyinfrastrukturen kan fånga upp trafik. Webbläsarbaserade program Microsoft 365 för närvarande klientbegränsningar, precis som Office-klienter som använder modern autentisering (t.ex. OAuth 2.0).

4. **Modern autentisering:** Molntjänster måste använda modern autentisering för att använda klientbegränsningar och blockera åtkomst till alla icke-tillåtna klienter. Du måste konfigurera Microsoft 365 att använda moderna autentiseringsprotokoll som standard. Den senaste informationen om hur Microsoft 365 för modern autentisering finns i [Uppdaterad modern autentisering för Office 365.](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)

Följande diagram illustrerar trafikflödet på hög nivå. Klientbegränsningar kräver TLS-kontroll endast för trafik till Azure AD, inte Microsoft 365 molntjänster. Den här skillnaden är viktig eftersom trafikvolymen för autentisering till Azure AD vanligtvis är mycket lägre än trafikvolymen till SaaS-program som Exchange Online och SharePoint Online.

![Trafikflöde för klientbegränsningar – diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Konfigurera klientbegränsningar

Det finns två steg för att komma igång med klientbegränsningar. Kontrollera först att klienterna kan ansluta till rätt adresser. Konfigurera sedan proxyinfrastrukturen.

### <a name="urls-and-ip-addresses"></a>URL:er och IP-adresser

Om du vill använda klientbegränsningar måste klienterna kunna ansluta till följande Azure AD-URL:er för att autentisera: [login.microsoftonline.com,](https://login.microsoftonline.com/) [login.microsoft.com](https://login.microsoft.com/) [och login.windows.net](https://login.windows.net/). För att få åtkomst till Office 365 måste klienterna dessutom kunna ansluta till de fullständigt kvalificerade domännamnen (FQDN), URL:er och IP-adresser som definierats i URL:er och IP-adressintervall för [Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 

### <a name="proxy-configuration-and-requirements"></a>Proxykonfiguration och krav

Följande konfiguration krävs för att aktivera klientbegränsningar via proxyinfrastrukturen. Den här vägledningen är allmän, så du bör gå till proxyleverantörens dokumentation för specifika implementeringssteg.

#### <a name="prerequisites"></a>Förutsättningar

- Proxyn måste kunna utföra TLS-skärningspunkt, HTTP-rubrikinfogning och filtermål med hjälp av FQDN/URL:er.

- Klienter måste lita på certifikatkedjan som presenteras av proxyn för TLS-kommunikation. Om certifikat från en intern infrastruktur för offentliga nycklar [(PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) används, måste det interna utfärdande rotcertifikatutfärdarecertifikatet vara betrott.

- Azure AD Premium 1 licenser krävs för användning av klientbegränsningar.

#### <a name="configuration"></a>Konfiguration

För varje utgående begäran till login.microsoftonline.com, login.microsoft.com och login.windows.net infogar du två HTTP-huvuden: *Restrict-Access-To-Tenants* *och Restrict-Access-Context*.

> [!NOTE]
> Inkludera inte underdomäner under i `*.login.microsoftonline.com` proxykonfigurationen. Detta inkluderar device.login.microsoftonline.com och stör klientcertifikatautentisering, som används i scenarier för enhetsregistrering och enhetsbaserad villkorlig åtkomst. Konfigurera proxyservern så att den undantar device.login.microsoftonline.com från TLS-break-and-inspect och rubrikinjektion.

Huvudena bör innehålla följande element:

- För *Begränsa åtkomst till* klienter använder du värdet , som är en kommaavgränsad lista över klienter som du vill ge användare åtkomst \<permitted tenant list\> till. Alla domäner som är registrerade i en klientorganisation kan användas för att identifiera klienten i den här listan, samt själva katalog-ID:t. Ett exempel på alla tre sätt att beskriva en klientorganisation är att namn-/värdeparet för att tillåta Contoso, Fabrikam och Microsoft ser ut så här: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- För *Begränsa åtkomstkontext använder du* ett värde för ett enda katalog-ID och deklarerar vilken klientorganisation som anger klientbegränsningarna. Om du till exempel vill deklarera Contoso som den klientorganisation som anger principen för klientbegränsningar ser namn/värde-paret ut så här: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` .  Du **måste** använda ditt eget katalog-ID på den här platsen för att kunna hämta loggar för dessa autentiseringar.

> [!TIP]
> Du hittar ditt katalog-ID i [Azure Active Directory portalen.](https://aad.portal.azure.com/) Logga in som administratör, välj **Azure Active Directory** och välj sedan **Egenskaper.** 
>
> Om du vill verifiera att ett katalog-ID eller domännamn refererar till samma klientorganisation använder du det ID:t eller domänen i stället för <tenant> i den här URL:en: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` .  Om resultatet med domänen och ID:t är desamma refererar de till samma klientorganisation. 

För att förhindra att användare infogar sina egna HTTP-huvuden med icke-godkända klienter måste proxyn ersätta rubriken *Restrict-Access-To-Tenants* om den redan finns i den inkommande begäran.

Klienter måste tvingas att använda proxyn för alla begäranden till login.microsoftonline.com, login.microsoft.com och login.windows.net. Om PAC-filer till exempel används för att dirigera klienter att använda proxyn, ska slutanvändarna inte kunna redigera eller inaktivera PAC-filerna.

## <a name="the-user-experience"></a>Användarupplevelsen

I det här avsnittet beskrivs upplevelsen för både slutanvändare och administratörer.

### <a name="end-user-experience"></a>Slutanvändarens upplevelse

En exempelanvändare finns i Contoso-nätverket, men försöker komma åt Fabrikam-instansen av ett delat SaaS-program som Outlook Online. Om Fabrikam är en icke-tillåten klientorganisation för Contoso-instansen ser användaren ett meddelande om nekad åtkomst, som säger att du försöker komma åt en resurs som tillhör en organisation som inte godkänns av IT-avdelningen.

![Felmeddelande om klientbegränsningar från april 2021](./media/tenant-restrictions/error-message.png)

### <a name="admin-experience"></a>Administratörsupplevelse

Konfiguration av klientbegränsningar görs i företagets proxyinfrastruktur, men administratörer kan komma åt rapporterna om klientbegränsningar i Azure Portal direkt. Så här visar du rapporterna:

1. Logga in på [Azure Active Directory portalen.](https://aad.portal.azure.com/) Instrumentpanelen **Azure Active Directory administrationscenter** visas.

2. Välj **Azure Active Directory** i den vänstra rutan. Sidan Azure Active Directory översikt visas.

3. På sidan Översikt väljer du **Klientbegränsningar.**

Administratören för den klientorganisation som anges som klientorganisationen Restricted-Access-Context kan använda den här rapporten för att se inloggningar som blockerats på grund av principen för klientbegränsningar, inklusive den identitet som används och målkatalog-ID:t. Inloggningar ingår om klientinställningen begränsningen är antingen användarklientorganisationen eller resursklientorganisationen för inloggningen.

Rapporten kan innehålla begränsad information, till exempel målkatalog-ID, när en användare som är i en annan klient än klientorganisationen Restricted-Access-Context loggar in. I det här fallet maskeras användaridentifierande information, till exempel namn och användarens huvudnamn, för att skydda användardata i andra klienter ("{PII borttagen} @domain.com " eller 00000000-0000-0000-0000-00000000000 i stället för användarnamn och objekt-ID:t efter behov). 

Precis som i andra Azure Portal kan du använda filter för att ange rapportens omfång. Du kan filtrera efter ett visst tidsintervall, användare, program, klient eller status. Om du väljer **knappen Kolumner** kan du välja att visa data med valfri kombination av följande fält:

- **Användare** – det här fältet kan ta bort personliga data, där de anges till `00000000-0000-0000-0000-000000000000` . 
- **Program**
- **Status**
- **Datum**
- **Datum (UTC)** – där UTC är Coordinated Universal Time
- **IP-adress**
- **Klient**
- **Användarnamn** – det här fältet kan ta bort personliga data, där det anges till `{PII Removed}@domain.com`
- **Plats**
- **Målklient-ID**

## <a name="microsoft-365-support"></a>Microsoft 365-support

Microsoft 365 måste uppfylla två kriterier för att ge fullständigt stöd för klientbegränsningar:

1. Klienten som används stöder modern autentisering.
2. Modern autentisering är aktiverat som standardautentiseringsprotokoll för molntjänsten.

Se Uppdaterad [modern Office 365-autentisering för den](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) senaste informationen om vilka Office-klienter som för närvarande stöder modern autentisering. Sidan innehåller också länkar till instruktioner för att aktivera modern autentisering på specifika Exchange Online- och Skype för företag – Online-klienter. SharePoint Online aktiverar redan modern autentisering som standard.

Microsoft 365 webbläsarbaserade program (Office-portalen, Yammer, SharePoint-webbplatser, Outlook på webben med flera) stöder för närvarande klientbegränsningar. Tjocka klienter (Outlook, Skype för företag, Word, Excel, PowerPoint med flera) kan endast framtvinga klientbegränsningar när du använder modern autentisering.  

Outlook- och Skype för företag-klienter som stöder modern autentisering kan fortfarande använda äldre protokoll mot klienter där modern autentisering inte är aktiverat, vilket effektivt kringgår klientbegränsningar. Klientbegränsningar kan blockera program som använder äldre protokoll om de kontaktar login.microsoftonline.com, login.microsoft.com eller login.windows.net under autentiseringen.

För Outlook i Windows kan kunder välja att implementera begränsningar som hindrar slutanvändare från att lägga till icke-godkända e-postkonton i sina profiler. Se till exempel grupprincipinställningen [Förhindra tillägg av icke-standardkonton](https://gpsearch.azurewebsites.net/default.aspx?ref=1) för Exchange.

## <a name="testing"></a>Testning

Om du vill prova klientbegränsningar innan du implementerar det för hela organisationen har du två alternativ: en värdbaserad metod med ett verktyg som Fiddler eller en mellanfasad implementering av proxyinställningar.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler för en värdbaserad metod

Fiddler är en kostnadsfri webbfelsökningsproxy som kan användas för att samla in och ändra HTTP/HTTPS-trafik, inklusive att infoga HTTP-huvuden. Utför följande steg för att konfigurera Fiddler för att testa klientbegränsningar:

1. [Ladda ned och installera Fiddler](https://www.telerik.com/fiddler).

2. Konfigurera Fiddler för att dekryptera HTTPS-trafik enligt [Fiddlers hjälpdokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Konfigurera Fiddler för att infoga *rubrikerna Restrict-Access-To-Tenants* och *Restrict-Access-Context* med hjälp av anpassade regler:

   1. I fiddler-verktyget Web Debugger väljer du **menyn Regler** och sedan **Anpassa regler...** för att öppna CustomRules-filen.

   2. Lägg till följande rader i början av `OnBeforeRequest` funktionen. Ersätt \<List of tenant identifiers\> med en domän registrerad med din klientorganisation (till exempel `contoso.onmicrosoft.com` ). Ersätt \<directory ID\> med din klients Azure AD GUID-identifierare.  Du **måste** inkludera rätt GUID-identifierare för att loggarna ska visas i din klientorganisation. 

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

   Om du behöver tillåta flera klienter använder du ett kommatecken för att avgränsa klientnamnen. Exempel:

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Spara och stäng CustomRules-filen.

När du har konfigurerat Fiddler kan du samla in trafik genom att gå till **menyn Arkiv** och välja **Avbilda trafik.**

### <a name="staged-rollout-of-proxy-settings"></a>Mellanstadiumsdelade proxyinställningar

Beroende på funktionerna i proxyinfrastrukturen kan du mellan olika inställningar mellan olika användare. Här är några avancerade alternativ att överväga:

1. Använd PAC-filer för att peka testanvändare till en testproxyinfrastruktur, medan vanliga användare fortsätter att använda infrastrukturen för produktionsproxy.
2. Vissa proxyservrar kan ha stöd för olika konfigurationer med hjälp av grupper.

Mer information finns i dokumentationen om proxyservern.

## <a name="blocking-consumer-applications-public-preview"></a>Blockera konsumentprogram (offentlig förhandsversion)

Program från Microsoft som stöder både konsumentkonton och organisationskonton, till exempel [OneDrive](https://onedrive.live.com/) [eller Microsoft Learn](/learn/), kan ibland finnas på samma URL.  Det innebär att användare som måste ha åtkomst till url:en i arbetet också har åtkomst till den för personligt bruk, vilket kanske inte tillåts enligt dina riktlinjer för användning.

Vissa organisationer försöker åtgärda detta genom att `login.live.com` blockera för att blockera personliga konton från att autentisera.  Detta har flera nackdelar:

1. Blockering `login.live.com` blockerar användningen av personliga konton i B2B-gästscenarier, som kan störa besökare och samarbete.
1. [Autopilot kräver användning `login.live.com` av](/mem/autopilot/networking-requirements) för att distribuera. Intune- och Autopilot-scenarier kan misslyckas `login.live.com` när blockeras.
1. Organisationstelemetri och Windows-uppdateringar som förlitar sig på login.live.com-tjänsten för enhets-ID:n [slutar att fungera.](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are)

### <a name="configuration-for-consumer-apps"></a>Konfiguration för konsumentappar

Även om huvudet fungerar som en lista över tillåtna fungerar Microsoft-konto-blocket (MSA) som en neka-signal, vilket talar om för Microsoft-konto-plattformen att inte tillåta användare att logga in på `Restrict-Access-To-Tenants` konsumentprogram. För att skicka den här signalen `sec-Restrict-Tenant-Access-Policy` matas -huvudet in i trafik som besöker med `login.live.com` samma företagsproxy eller brandvägg som [ovan](#proxy-configuration-and-requirements). Värdet för -huvudet måste vara `restrict-msa` . När rubriken finns och en konsumentapp försöker logga in en användare direkt blockeras den inloggningen.

För stunden visas inte autentisering till konsumentprogram i administratörsloggarna [eftersom](#admin-experience)login.live.com värdbaserad separat från Azure AD.

### <a name="what-the-header-does-and-does-not-block"></a>Vad rubriken gör och inte blockerar

Principen `restrict-msa` blockerar användningen av konsumentprogram, men tillåter genom flera andra typer av trafik och autentisering:

1. Användar less traffic for devices (Användar-mindre trafik för enheter).  Detta inkluderar trafik för Autopilot, Windows Update och organisationstelemetri.
1. B2B-autentisering av konsumentkonton. Användare med Microsoft-konton som [bjuds in att samarbeta](../external-identities/redemption-experience.md#invitation-redemption-flow) med en klientorganisation autentiseras login.live.com för att få åtkomst till en resursklientorganisation.
    1. Den här åtkomsten styrs med `Restrict-Access-To-Tenants` hjälp av -huvudet för att tillåta eller neka åtkomst till den resursklientorganisationen.
1. "Genomströmningsautentisering" som används av många Azure-appar samt Office.com, där appar använder Azure AD för att logga in konsumentanvändare i en konsumentkontext.
    1. Den här åtkomsten styrs också med hjälp av -huvudet för att tillåta eller neka `Restrict-Access-To-Tenants` åtkomst till den särskilda "genomströmningsklienten" ( `f8cdef31-a31e-4b4a-93e4-5f571e91255a` ).  Om den här klientorganisationen inte visas i listan över tillåtna domäner blockeras konsumentkonton av Azure AD från `Restrict-Access-To-Tenants` att logga in på dessa appar.

## <a name="next-steps"></a>Nästa steg

- Läs mer [om uppdaterad modern Office 365-autentisering](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Granska [URL:er och IP-adressintervall för Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
