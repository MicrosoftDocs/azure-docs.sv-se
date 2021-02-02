---
title: Använd klient begränsningar för att hantera åtkomst till SaaS-appar – Azure AD
description: Så här använder du klient begränsningar för att hantera vilka användare som kan komma åt appar baserade på deras Azure AD-klient.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: kenwith
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: f605b2bb48855d70ea305dcda194b26da71ee9ec
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252482"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Använd klient begränsningar för att hantera åtkomst till SaaS-molnprogram

Stora organisationer som betonar säkerheten vill flytta till moln tjänster som Microsoft 365, men behöver veta att deras användare bara kan komma åt godkända resurser. Traditionellt begränsar företagen domän namn eller IP-adresser när de vill hantera åtkomst. Den här metoden fungerar inte i en värld där program vara som en tjänst (eller SaaS)-appar finns i ett offentligt moln, som körs på delade domän namn som [Outlook.Office.com](https://outlook.office.com/) och [login.microsoftonline.com](https://login.microsoftonline.com/). Genom att blockera de här adresserna kan användarna få åtkomst till Outlook på webben helt, i stället för att bara begränsa dem till godkända identiteter och resurser.

Azure Active Directory-lösningen (Azure AD) till denna utmaning är en funktion som kallas klient begränsningar. Med klient begränsningar kan organisationer styra åtkomsten till SaaS-molnprogram, baserat på Azure AD-klienten som programmen använder för enkel inloggning. Till exempel kanske du vill tillåta åtkomst till din organisations Microsoft 365 program, samtidigt som du förhindrar åtkomst till andra organisationers instanser av samma program.  

Med klient begränsningar kan organisationer ange listan över klienter som användarna får åtkomst till. Azure AD beviljar sedan bara åtkomst till dessa tillåtna klienter.

Den här artikeln fokuserar på klient begränsningar för Microsoft 365, men funktionen bör fungera med alla SaaS-molnappar som använder moderna autentiseringsprotokoll med Azure AD för enkel inloggning. Om du använder SaaS-appar med en annan Azure AD-klient än den klient organisation som används av Microsoft 365, se till att alla begärda klienter är tillåtna. Mer information om SaaS-molnappar finns på [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

## <a name="how-it-works"></a>Så här fungerar det

Den övergripande lösningen består av följande komponenter:

1. **Azure AD**: om `Restrict-Access-To-Tenants: <permitted tenant list>` huvudet finns utfärdar bara säkerhetstoken för de tillåtna klienterna i Azure AD.

2. **Lokal Proxy Server-infrastruktur**: den här infrastrukturen är en proxy-enhet som kan Transport Layer Security (TLS). Du måste konfigurera proxyn så att den infogar rubriken som innehåller listan över tillåtna klienter i trafik som är avsedda för Azure AD.

3. **Klient program vara**: för att ge stöd för klient begränsningar måste klient programmet begära token direkt från Azure AD, så att proxyn kan fånga trafik. Webbläsarbaserade Microsoft 365-program har för närvarande stöd för klient begränsningar, som Office-klienter som använder modern autentisering (t. ex. OAuth 2,0).

4. **Modern autentisering**: moln tjänster måste använda modern autentisering för att använda klient begränsningar och blockera åtkomst till alla icke-tillåtna klienter. Du måste konfigurera Microsoft 365 moln tjänster för att använda moderna autentiseringsprotokoll som standard. Den senaste informationen om Microsoft 365 stöd för modern autentisering finns i [uppdaterad Office 365 modern Authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Följande diagram illustrerar trafikflödet på hög nivå. Klient begränsningar kräver endast TLS-inspektion på trafik till Azure AD, inte för Microsoft 365 moln tjänster. Den här skillnaden är viktig eftersom trafik volymen för autentisering till Azure AD vanligt vis är mycket lägre än trafik volym till SaaS-program som Exchange Online och SharePoint Online.

![Trafik flöde för klient begränsningar – diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Konfigurera klient begränsningar

Det finns två steg för att komma igång med klient begränsningar. Se först till att klienterna kan ansluta till rätt adresser. Sedan konfigurerar du din proxy-infrastruktur.

### <a name="urls-and-ip-addresses"></a>URL: er och IP-adresser

Om du vill använda klient begränsningar måste klienterna kunna ansluta till följande Azure AD-URL: er för att autentisera: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.Microsoft.com](https://login.microsoft.com/)och [login.Windows.net](https://login.windows.net/). För att få åtkomst till Office 365 måste dessutom klienterna också kunna ansluta till de fullständigt kvalificerade domän namnen (FQDN), URL: er och IP-adresser som definierats i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Konfiguration och krav för proxy

Följande konfiguration krävs för att aktivera klient begränsningar via proxyservern för infrastrukturen. Den här vägledningen är generisk, så du bör läsa dokumentationen för proxy-leverantören för att få detaljerade implementerings steg.

#### <a name="prerequisites"></a>Förutsättningar

- Proxyn måste kunna utföra TLS-avlyssning, infoga HTTP-huvud och filtrera mål med hjälp av FQDN/URL: er.

- Klienter måste lita på certifikat kedjan som presenteras av proxyn för TLS-kommunikation. Om till exempel certifikat från en intern [offentlig nyckel infrastruktur (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) används, måste det interna utfärdande rot certifikat utfärdarens certifikat vara betrott.

- Azure AD Premium 1-licenser krävs för att använda klient begränsningar. 

#### <a name="configuration"></a>Konfiguration

Infoga två HTTP-huvuden för varje inkommande begäran till login.microsoftonline.com, login.microsoft.com och login.windows.net: *begränsning-åtkomst-till-innehavare* och *begränsa åtkomst-kontext*.

> [!NOTE]
> När du konfigurerar SSL-avlyssning och rubrik inmatning, se till att trafik till https://device.login.microsoftonline.com undantas. Den här URL: en används för enhetsautentisering och för att utföra TLS-och-inspektera kan störa autentisering av klient certifikat, vilket kan orsaka problem med enhets registrering och enhets-baserad villkorlig åtkomst.



Rubrikerna måste innehålla följande element:

- För att *begränsa åtkomst till klienter* använder du ett värde av \<permitted tenant list\> , som är en kommaavgränsad lista över klienter som du vill att användarna ska kunna komma åt. Alla domäner som är registrerade med en klient kan användas för att identifiera klienten i listan, samt själva katalog-ID: t. Ett exempel på alla tre sätt att beskriva en klient, namn/värde-paret som tillåter contoso, Fabrikam och Microsoft ser ut så här: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- För *begränsning av åtkomst kontexten* använder du ett värde för ett enda katalog-ID och anger vilken klient som ska ange innehavarens begränsningar. Om du till exempel vill deklarera contoso som den klient som anger principen för klient begränsningar, ser namn/värde-paret ut så här: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` .  Du **måste** använda ditt eget katalog-ID på den här platsen.

> [!TIP]
> Du kan hitta ditt katalog-ID i [Azure Active Directory portalen](https://aad.portal.azure.com/). Logga in som administratör, Välj **Azure Active Directory** och välj sedan **Egenskaper**. 
>
> Om du vill kontrol lera att ett katalog-ID eller domän namn refererar till samma klient använder du detta ID eller domän i stället för <tenant> i denna URL: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` .  Om resultatet med domänen och ID: t är detsamma, refererar de till samma klient organisation. 

För att förhindra att användare infogar sin egen HTTP-rubrik med icke-godkända innehavare, måste proxyn ersätta huvudet *begränsning-åtkomst-till-innehavare* om den redan finns i den inkommande begäran.

Klienter måste tvingas att använda proxyn för alla begär anden till login.microsoftonline.com, login.microsoft.com och login.windows.net. Om till exempel PAC-filer används för att dirigera klienter till att använda proxyn, bör slutanvändare inte kunna redigera eller inaktivera PAC-filerna.

> [!NOTE]
> Ta inte med under domäner under *. login.microsoftonline.com i proxykonfigurationen. Detta inkluderar Device.login.microsoftonline.com och kan störa autentisering av klient certifikat, som används i enhets registrering och enhets villkorliga åtkomst scenarier. Konfigurera proxyservern så att den utesluter device.login.microsoftonline.com från TLS-och-granska och rubrik inmatning.

## <a name="the-user-experience"></a>Användar upplevelsen

I det här avsnittet beskrivs upplevelsen för både slutanvändare och administratörer.

### <a name="end-user-experience"></a>Slutanvändarens upplevelse

Ett exempel på en användare finns i Contoso-nätverket, men försöker komma åt Fabrikam-instansen av ett delat SaaS-program som Outlook online. Om Fabrikam är en icke-tillåten klient för Contoso-instansen ser användaren ett nekat åtkomst meddelande, som säger att du försöker få åtkomst till en resurs som tillhör en organisation som inte har godkänts av IT-avdelningen.

### <a name="admin-experience"></a>Administratörsupplevelse

Medan konfigurationen av klient begränsningar görs i infrastrukturen för företags proxy kan administratörerna komma åt rapporterna för klient begränsningar i Azure Portal direkt. Så här visar du rapporterna:

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com/). Instrument panelen för **Azure Active Directory administrations Center** visas.

2. Välj **Azure Active Directory** i den vänstra rutan. Sidan Azure Active Directory översikt visas.

3. På sidan Översikt väljer du **begränsningar för innehavare**.

Administratören för den klient som anges som den begränsade åtkomst kontext klienten kan använda den här rapporten för att se vilka inloggningar som blockeras på grund av principen för klient begränsningar, inklusive den identitet som används och mål katalog-ID: t. Inloggningar inkluderas om klient inställningen begränsningen är antingen användar klienten eller resurs klienten för inloggningen.

> [!NOTE]
> Rapporten kan innehålla begränsad information, till exempel mål katalog-ID, när en användare som finns i en annan klient än den begränsade åtkomst kontexten loggar in. I det här fallet maskeras identifierbar information om användare, till exempel namn och User Principal Name, för att skydda användar data i andra klienter (" 00000000-0000-0000-0000-00000000@domain.com ") 

Precis som med andra rapporter i Azure Portal kan du använda filter för att ange omfattningen för rapporten. Du kan filtrera efter ett angivet tidsintervall, användare, program, klient eller status. Om du väljer knappen **kolumner** kan du välja att visa data med valfri kombination av följande fält:

- **Användare**
- **Program**
- **Status**
- **Datum**
- **Datum (UTC)** (där UTC är UTC Universal Time)
- **MFA auth-metod** (multifaktorautentisering)
- **MFA auth-information** (information om multifaktorautentisering)
- **MFA-resultat**
- **IP-adress**
- **Klient**
- **Användarnamn**
- **Plats**
- **Mål klient-ID**

## <a name="microsoft-365-support"></a>Microsoft 365-support

Microsoft 365 program måste uppfylla två villkor för att fullständigt stödja klient begränsningar:

1. Klienten har stöd för modern autentisering.
2. Modern autentisering är aktiverat som standard protokoll för autentisering för moln tjänsten.

Se [uppdaterad office 365 modern-autentisering](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) för den senaste informationen om vilka Office-klienter som för närvarande stöder modern autentisering. Sidan innehåller också länkar till instruktioner för att aktivera modern autentisering på specifika Exchange Online-och Skype for Business Online-klienter. SharePoint Online aktiverar redan modern autentisering som standard.

Microsoft 365 webbläsarbaserade program (Office-portalen, Yammer, SharePoint-webbplatser, Outlook på webben och mer) har stöd för klient begränsningar. Tjocka klienter (Outlook, Skype för företag, Word, Excel, PowerPoint med flera) kan endast tillämpa klient begränsningar när modern autentisering används.  

Outlook-och Skype för företag-klienter som stöder modern autentisering kan fortfarande använda äldre protokoll mot klienter där modern autentisering inte är aktive rad, vilket effektivt kringgår klient begränsningarna. Klient begränsningar kan blockera program som använder äldre protokoll om de kontaktar login.microsoftonline.com, login.microsoft.com eller login.windows.net under autentisering.

För Outlook i Windows kan kunder välja att implementera begränsningar som hindrar slutanvändare från att lägga till icke-godkända e-postkonton i sina profiler. Se till exempel alternativet förhindra att lägga till en grup princip inställning för [Exchange-konton som inte är standard](https://gpsearch.azurewebsites.net/default.aspx?ref=1) .

## <a name="testing"></a>Testning

Om du vill testa klient begränsningar innan du implementerar den för hela organisationen har du två alternativ: en värdbaserad metod med hjälp av ett verktyg som Fiddler eller en mellanlagrad installation av proxyinställningar.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler för en värd baserad metod

Fiddler är en kostnads fri webb fel söknings proxy som kan användas för att avbilda och ändra HTTP/HTTPS-trafik, inklusive infogning av HTTP-huvuden. Utför följande steg för att konfigurera Fiddler för att testa klient begränsningar:

1. [Hämta och installera Fiddler](https://www.telerik.com/fiddler).

2. Konfigurera Fiddler för att dekryptera HTTPS-trafik, per [Fiddler hjälp dokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Konfigurera Fiddler för att infoga *begränsnings åtkomst till klient organisationer* och *begränsa åtkomst kontext* rubriker med anpassade regler:

   1. I verktyget Fiddler Web-Felsökning väljer du menyn **regler** och väljer **Anpassa regler...** för att öppna CustomRules-filen.

   2. Lägg till följande rader i början av `OnBeforeRequest` funktionen. Ersätt \<tenant domain\> med en domän som är registrerad hos din klient (till exempel `contoso.onmicrosoft.com` ). Ersätt \<directory ID\> med klient organisationens GUID-identifierare för Azure AD.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Om du behöver tillåta flera klienter kan du använda ett kommatecken för att avgränsa klient namnen. Exempel:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Spara och Stäng filen CustomRules.

När du har konfigurerat Fiddler kan du samla in trafik genom att gå till **Arkiv** -menyn och välja **fånga trafik**.

### <a name="staged-rollout-of-proxy-settings"></a>Stegvis distribution av proxyinställningar

Beroende på funktionerna i din proxy-infrastruktur kan du gå vidare med att distribuera inställningar till dina användare. Här följer några alternativ på hög nivå som du bör tänka på:

1. Använd PAC-filer för att peka test användare till en infrastruktur för redundanstest, medan normala användare fortsätter att använda infrastrukturen för produktions proxy.
2. Vissa proxyservrar kan ha stöd för olika konfigurationer med hjälp av grupper.

Mer information finns i Proxy Server-dokumentationen.

## <a name="next-steps"></a>Nästa steg

- Läs om [uppdaterad Office 365 modern autentisering](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Granska [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
