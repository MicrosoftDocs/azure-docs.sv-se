---
title: Azure Active Directory djupgående säkerhets djup för autentisering | Microsoft Docs
description: Den här artikeln beskriver hur Azure Active Directory (Azure AD) direktautentisering skyddar dina lokala konton
services: active-directory
keywords: Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a73c2b1be4b17136ba19e7efb71c2b21359fdf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89280153"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory djupgående säkerhets djup

Den här artikeln innehåller en mer detaljerad beskrivning av hur Azure Active Directory (Azure AD) genom strömnings autentisering fungerar. Den fokuserar på säkerhets aspekterna i funktionen. Den här artikeln är för säkerhets-och IT-administratörer, Chief Compliance and Security officer och andra IT-proffs som ansvarar för IT-säkerhet och efterlevnad av små till medel stora organisationer eller stora företag.

De avsnitt som beskrivs är:
- Detaljerad teknisk information om hur du installerar och registrerar agenter för autentisering.
- Detaljerad teknisk information om kryptering av lösen ord under användar inloggning.
- Säkerheten för kanaler mellan lokala autentiserings agenter och Azure AD.
- Detaljerad teknisk information om hur du håller autentiserings agenter säkert.
- Andra säkerhetsrelaterade ämnen.

## <a name="key-security-capabilities"></a>Viktiga säkerhets funktioner

Dessa är viktiga säkerhets aspekter av den här funktionen:
- Den bygger på en säker arkitektur för flera innehavare som tillhandahåller isolering av inloggnings begär Anden mellan klienter.
- Lokala lösen ord lagras aldrig i molnet i något formulär.
- Lokala autentiseringsbegäranden som lyssnar efter och svarar på begär Anden om lösen ords validering bara gör utgående anslutningar inifrån nätverket. Det finns inget krav på att installera dessa autentiseringsprinciper i ett perimeternätverk (DMZ). Som bästa praxis ska du behandla alla servrar som kör autentiseringsprinciper som system på nivå 0 (se [referens](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Endast standard portar (80 och 443) används för utgående kommunikation från Authentication-agenter till Azure AD. Du behöver inte öppna inkommande portar i brand väggen. 
  - Port 443 används för all autentiserad utgående kommunikation.
  - Port 80 används bara för att ladda ned listor över återkallade certifikat (CRL) för att säkerställa att inga av de certifikat som används av den här funktionen har återkallats.
  - En fullständig lista över nätverks kraven finns i [Azure Active Directory direktautentisering: snabb start](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- Lösen ord som användare anger under inloggningen krypteras i molnet innan de lokala autentiserings agenter accepterar dem för verifiering mot Active Directory.
- HTTPS-kanalen mellan Azure AD och den lokala Autentiseringstjänsten skyddas med hjälp av ömsesidig autentisering.
- Skyddar dina användar konton genom att arbeta sömlöst med [villkorliga åtkomst principer i Azure AD](../conditional-access/overview.md), inklusive Multi-Factor Authentication (MFA), [blockera äldre autentisering](../conditional-access/concept-conditional-access-conditions.md) och genom att [filtrera bort lösen ords attacker med brute force](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Komponenter som ingår

Allmän information om drift, tjänster och data säkerhet i Azure AD finns i säkerhets [Center](https://azure.microsoft.com/support/trust-center/). Följande komponenter ingår när du använder direktautentisering för användar inloggning:
- **Azure AD STS**: en tillstånds lös säkerhetstokentjänst som bearbetar inloggnings begär Anden och utfärdar säkerhetstoken till användarnas webbläsare, klienter eller tjänster efter behov.
- **Azure Service Bus**: tillhandahåller molnbaserad kommunikation med företags meddelanden och vidarebefordrar kommunikation som hjälper dig att ansluta lokala lösningar med molnet.
- **Azure AD Connect Authentication agent**: en lokal komponent som lyssnar efter och svarar på begär Anden om lösen ords verifiering.
- **Azure SQL Database**: innehåller information om klientens autentiseringskrav, inklusive deras metadata och krypterings nycklar.
- **Active Directory**: lokala Active Directory där dina användar konton och deras lösen ord lagras.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Installation och registrering av autentiseringsprinciper

Autentiseringsprinciper installeras och registreras med Azure AD när du använder något av följande:
   - [Aktivera direkt autentisering genom Azure AD Connect](./how-to-connect-pta-quick-start.md#step-2-enable-the-feature)
   - [Lägg till fler autentiseringsmetoder för att säkerställa hög tillgänglighet för inloggnings begär Anden](./how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) 
   
Att hämta en autentiserings agent arbetar omfattar tre huvud faser:

1. Installation av autentiseringstjänst
2. Registrering av autentiseringsprovider
3. Initiering av autentiserings agent

I följande avsnitt beskrivs de här faserna i detalj.

### <a name="authentication-agent-installation"></a>Installation av autentiseringstjänst

Endast globala administratörer kan installera en autentiseringstjänst (med Azure AD Connect eller fristående) på en lokal server. Vid installationen läggs två nya poster till i program-  >    >  **och funktions** listan i kontroll panelen:
- Själva appen för autentisering av agent. Det här programmet körs med [NetworkService](/windows/win32/services/networkservice-account) -behörigheter.
- Uppdaterings programmet som används för att automatiskt uppdatera Autentiseringstjänsten. Det här programmet körs med [LocalSystem](/windows/win32/services/localsystem-account) -behörighet.

>[!IMPORTANT]
>Av säkerhets synpunkt bör administratören behandla servern som kör PTA-agenten som om den vore en domänkontrollant.  PTA agent-servrarna bör vara härdade längs samma rader som beskrivs i [Skydda domänkontrollanter mot angrepp](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)

### <a name="authentication-agent-registration"></a>Registrering av autentiseringsprovider

När du har installerat Autentiseringstjänsten måste den registreras med Azure AD. Azure AD tilldelar varje Authentication agent ett unikt digitalt identitets certifikat som kan användas för säker kommunikation med Azure AD.

Registrerings proceduren binder även autentiserings agenten med din klient. Detta säkerställer att Azure AD vet att den här särskilda Autentiseringstjänsten är den enda behörighet som krävs för att hantera begär Anden om lösen ords validering för din klient. Den här proceduren upprepas för varje ny autentiseringstjänst som du registrerar.

Autentiserings agenter använder följande steg för att registrera sig för Azure AD:

![Agent registrering](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Azure AD begär första gången en global administratör loggar in på Azure AD med sina autentiseringsuppgifter. Vid inloggningen hämtar Autentiseringstjänsten en åtkomsttoken som den kan använda för den globala administratörens räkning.
2. Authentication agent genererar sedan ett nyckel par: en offentlig nyckel och en privat nyckel.
    - Nyckel paret genereras via standard-RSA 2048-bitars kryptering.
    - Den privata nyckeln är kvar på den lokala server där Autentiseringstjänsten finns.
3. Authentication-agenten gör en "registreringsbegäran"-begäran till Azure AD över HTTPS, med följande komponenter som ingår i begäran:
    - Den åtkomsttoken som hämtades i steg 1.
    - Den offentliga nyckeln som genererades i steg 2.
    - En certifikat signerings förfrågan (CSR eller certifikat förfrågan). Den här begäran gäller för ett digitalt identitets certifikat, med Azure AD som sin certifikat utfärdare (CA).
4. Azure AD validerar åtkomst-token i registreringsbegäran och kontrollerar att begäran kom från en global administratör.
5. Azure AD loggar och skickar sedan ett digitalt identitets certifikat tillbaka till Authentication-agenten.
    - Rot certifikat utfärdaren i Azure AD används för att signera certifikatet. 

      > [!NOTE]
      > Den här certifikat utfärdaren finns _inte_ i arkivet för betrodda rot certifikat utfärdare i Windows.
    - CA: n används endast av funktionen för direkt autentisering. CA: n används bara för att signera kund service representanter under registreringen av Autentiseringstjänsten.
    -  Ingen av de andra Azure AD-tjänsterna använder den här certifikat utfärdaren.
    - Certifikatets ämne (unikt namn eller DN) har angetts till ditt klient-ID. Detta unika namn är ett GUID som unikt identifierar din klient. Detta DN omfångerar certifikatet för användning endast med din klient.
6. Azure AD lagrar Authentication agentens offentliga nyckel i en databas i Azure SQL Database, som bara Azure AD har åtkomst till.
7. Certifikatet (utfärdat i steg 5) lagras på den lokala servern i Windows certifikat arkiv (särskilt på [CERT_SYSTEM_STORE_LOCAL_MACHINE](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_LOCAL_MACHINE) plats). Den används av både autentiseringstjänsten och uppdaterings programmen.

### <a name="authentication-agent-initialization"></a>Initiering av autentiserings agent

När Autentiseringstjänsten startar, antingen för första gången efter registreringen eller efter en omstart av servern, måste det finnas ett sätt att kommunicera säkert med Azure AD-tjänsten och börja godkänna begär Anden om lösen ords validering.

![Agent initiering](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Så här initieras autentiserings agenter:

1. Authentication agent gör en utgående bootstrap-begäran till Azure AD. 
    - Den här begäran görs via port 443 och är över en ömsesidigt autentiserad HTTPS-kanal. Begäran använder samma certifikat som utfärdades under registreringen av Autentiseringstjänsten.
2. Azure AD svarar på begäran genom att tillhandahålla en åtkomst nyckel till en Azure Service Bus kö som är unik för din klient organisation och som identifieras av klient-ID: t.
3. Autentiseringstjänsten gör en permanent utgående HTTPS-anslutning (via port 443) till kön. 
    - Authentication agent är nu redo att hämta och hantera begär Anden om verifiering av lösen ord.

Om du har flera autentiseringspaket registrerade för din klient, ser initierings proceduren till att varje anslutning till samma Service Bus-kö. 

## <a name="process-sign-in-requests"></a>Bearbeta inloggnings begär Anden 

Följande diagram visar hur direktautentisering behandlar användar inloggnings begär Anden.

![Process inloggning](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Direktautentisering hanterar en användar inloggnings förfrågan enligt följande: 

1. En användare försöker komma åt ett program, till exempel [Outlook Web App](https://outlook.office365.com/owa).
2. Om användaren inte redan är inloggad omdirigerar programmet webbläsaren till inloggnings sidan för Azure AD.
3. Azure AD STS-tjänsten svarar på **användarens inloggnings** sida.
4. Användaren anger sitt användar namn i **inloggnings** sidan för användaren och väljer sedan knappen **Nästa** .
5. Användaren anger sitt lösen ord på **inloggnings** sidan för användaren och väljer sedan knappen **Logga** in.
6. Användar namnet och lösen ordet skickas till Azure AD STS i en HTTPS POST-begäran.
7. Azure AD STS hämtar offentliga nycklar för alla autentiserings agenter som är registrerade på din klient organisation från Azure SQL Database och krypterar lösen ordet med hjälp av dem.
    - Den genererar "N" krypterade lösen ords värden för "N"-autentiseringspaket som registrerats på din klient organisation.
8. Azure AD STS placerar begäran om lösen ords verifiering, som består av användar namnet och de krypterade lösen ords värdena, till den Service Bus Queue som är unik för din klient.
9. Eftersom de initierade autentiseringsinställningarna är permanenta anslutna till Service Bus kön, hämtar en av de tillgängliga autentiseringsmetoderna en begäran om lösen ords validering.
10. Authentication-agenten hittar det krypterade lösen ordet som är särskilt för dess offentliga nyckel, genom att använda en identifierare och dekrypterar den med hjälp av dess privata nyckel.
11. Autentiseringstjänsten försöker verifiera användar namnet och lösen ordet mot lokala Active Directory med hjälp av [Win32 LogonUser API: et](/windows/win32/api/winbase/nf-winbase-logonusera) med parametern **dwLogonType** inställd på **LOGON32_LOGON_NETWORK**. 
    - Detta API är samma API som används av Active Directory Federation Services (AD FS) (AD FS) för att logga in användare i ett federerat inloggnings scenario.
    - Detta API är beroende av standard lösnings processen i Windows Server för att hitta domänkontrollanten.
12. Autentiseringstjänsten tar emot resultatet från Active Directory, till exempel lyckad, användar namn eller lösen ord eller lösen ordet har upphört att gälla.

   > [!NOTE]
   > Om verifierings agenten Miss lyckas under inloggnings processen, tas hela inloggnings förfrågan bort. Det finns ingen manuell inloggnings begär Anden från en autentiseringstjänst till en annan autentiseringstjänst lokalt. Dessa agenter kommunicerar bara med molnet och inte med varandra.
   
13. Autentiseringstjänsten vidarebefordrar tillbaka resultatet till Azure AD STS över en utgående ömsesidigt autentiserad HTTPS-kanal över port 443. Ömsesidig autentisering använder certifikatet som tidigare utfärdats för Autentiseringstjänsten under registreringen.
14. Azure AD STS verifierar att det här resultatet motsvarar den särskilda inloggnings förfrågan på din klient organisation.
15. Azure AD STS fortsätter med inloggnings proceduren enligt konfigurationen. Om lösen ords verifieringen till exempel lyckades, kan användaren bli tillfrågad om Multi-Factor Authentication eller omdirigeras tillbaka till programmet.

## <a name="operational-security-of-the-authentication-agents"></a>Drift säkerhet för autentiseringsprinciper

För att säkerställa att direktautentisering fortsätter att fungera säkert kan Azure AD regelbundet förnya autentiserings agenternas certifikat. Azure AD utlöser förnyelserna. Förnyelserna regleras inte av själva Autentiseringstjänsten.

![Driftsäkerhet](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Förnya en Autentiseringstyps förtroende med Azure AD:

1. Autentiseringstjänsten pingar regelbundet Azure AD med några timmar för att kontrol lera om det är dags att förnya certifikatet. Certifikatet förnyas 30 dagar innan det upphör att gälla.
    - Den här kontrollen görs via en ömsesidigt autentiserad HTTPS-kanal och använder samma certifikat som utfärdades under registreringen.
2. Om tjänsten indikerar att det är dags att förnya, genererar Autentiseringstjänsten ett nytt nyckel par: en offentlig nyckel och en privat nyckel.
    - Dessa nycklar genereras via standard-RSA 2048-bitars kryptering.
    - Den privata nyckeln lämnar aldrig den lokala servern.
3. Authentication agent gör sedan en begäran om certifikat förnyelse till Azure AD över HTTPS, med följande komponenter som ingår i begäran:
    - Det befintliga certifikatet som hämtades från CERT_SYSTEM_STORE_LOCAL_MACHINE plats i Windows certifikat arkiv. Det finns ingen global administratör som är involverad i den här proceduren, så det finns ingen åtkomsttoken som krävs för den globala administratörens räkning.
    - Den offentliga nyckeln som genererades i steg 2.
    - En certifikat signerings förfrågan (CSR eller certifikat förfrågan). Den här begäran gäller för ett nytt digitalt identitets certifikat, med Azure AD som certifikat utfärdare.
4. Azure AD validerar det befintliga certifikatet i begäran om förnyelse av certifikat. Sedan verifierar den att begäran kom från en autentiseringsnyckel som registrerats på din klient organisation.
5. Om det befintliga certifikatet fortfarande är giltigt signerar Azure AD ett nytt digitalt identitets certifikat och utfärdar det nya certifikatet tillbaka till Authentication agent. 
6. Om det befintliga certifikatet har upphört att gälla tar Azure AD bort Autentiseringstjänsten från klientens lista över registrerade autentiseringsprinciper. Sedan måste en global administratör installera och registrera en ny autentiseringstjänst manuellt.
    - Använd Azure AD-rot certifikat utfärdaren för att signera certifikatet.
    - Ange certifikatets ämne (unikt namn eller DN) till klient-ID: t, ett GUID som unikt identifierar din klient. DN omfångerar certifikatet enbart till din klient organisation.
6. Azure AD lagrar den nya offentliga nyckeln för Autentiseringstjänsten i en databas i Azure SQL Database att den bara har åtkomst till. Det gör också att den gamla offentliga nyckeln som är kopplad till Authentication agent ogiltig förklaras.
7. Det nya certifikatet (utfärdat i steg 5) lagras sedan på servern i Windows certifikat arkiv (särskilt på [CERT_SYSTEM_STORE_CURRENT_USER](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_CURRENT_USER) plats).
    - Eftersom processen för förnyelse av förtroende inträffar icke-interaktivt (utan den globala administratörens närvaro), har Autentiseringstjänsten inte längre åtkomst för att uppdatera det befintliga certifikatet på den CERT_SYSTEM_STORE_LOCAL_MACHINE platsen. 
    
   > [!NOTE]
   > Den här proceduren tar inte bort själva certifikatet från CERT_SYSTEM_STORE_LOCAL_MACHINE plats.
8. Det nya certifikatet används för autentisering från det här läget på. Varje efterföljande förnyelse av certifikatet ersätter certifikatet på CERT_SYSTEM_STORE_LOCAL_MACHINE plats.

## <a name="auto-update-of-the-authentication-agents"></a>Automatisk uppdatering av autentiseringsinställningarna

Uppdaterings programmet uppdaterar automatiskt Autentiseringstjänsten när en ny version (med fel korrigeringar eller prestanda förbättringar) släpps. Uppdaterings programmet hanterar inte några förfrågningar om lösen ords verifiering för din klient.

Azure AD är värd för den nya versionen av program varan som ett signerat **Windows Installer paket (MSI)**. MSI signeras med hjälp av [Microsoft Authenticode](/previous-versions/windows/internet-explorer/ie-developer/platform-apis/ms537359(v=vs.85)) med SHA256 som Digest-algoritm. 

![Automatisk uppdatering](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Så här uppdaterar du en autentiseringsnyckel automatiskt:

1. Uppdaterings programmet pingar Azure AD varje timme för att kontrol lera om det finns en ny version av Authentication agent. 
    - Den här kontrollen görs via en ömsesidigt autentiserad HTTPS-kanal genom att använda samma certifikat som utfärdades under registreringen. Autentiseringstjänsten och uppdateraren delar certifikatet som lagras på servern.
2. Om en ny version är tillgänglig returnerar Azure AD den signerade MSI-filen till Updater.
3. Updater kontrollerar att MSI är signerat av Microsoft.
4. Uppdaterings tjänsten kör MSI. Den här åtgärden omfattar följande steg:

   > [!NOTE]
   > Uppdaterings tjänsten körs med [lokala system](/windows/win32/services/localsystem-account) privilegier.

    - Stoppar tjänsten Authentication agent
    - Installerar den nya versionen av Autentiseringstjänsten på servern
    - Startar om tjänsten Authentication agent

>[!NOTE]
>Om du har flera autentiseringspaket registrerade för din klient förnyar inte Azure AD sina certifikat eller uppdaterar dem på samma tidpunkt. Azure AD gör i stället en i taget för att säkerställa hög tillgänglighet för inloggnings begär Anden.
>


## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md): Lär dig vilka scenarier som stöds och vilka som inte är det.
- [Snabb start](how-to-connect-pta-quick-start.md): kom igång med Azure AD-direktautentisering.
- [Migrera från AD FS till vidarekoppling](https://aka.ms/adfstoptadpdownload) – en detaljerad guide för att migrera från AD FS (eller andra Federations tekniker) till vidarekoppling.
- [Smart utelåsning](../authentication/howto-password-smart-lockout.md): Konfigurera funktionen för smart utelåsning på klienten för att skydda användar konton.
- [Så här fungerar det](how-to-connect-pta-how-it-works.md): Lär dig grunderna för hur Azure AD-direktautentisering fungerar.
- [Vanliga frågor](how-to-connect-pta-faq.md)och svar: få svar på vanliga frågor.
- [Felsöka](tshoot-connect-pass-through-authentication.md): Lär dig hur du löser vanliga problem med funktionen för direkt autentisering.
- [Azure AD sömlös SSO](how-to-connect-sso.md): Lär dig mer om den här kompletterande funktionen.