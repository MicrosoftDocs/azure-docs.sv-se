---
title: Kerberos-baserad enkel inloggning (SSO) i Azure Active Directory med Application Proxy
description: Beskriver hur du ger enkel inloggning med hjälp av Azure Active Directory-programproxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: a4fdd8d16854e76cdf20d27a6048694c01de8499
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253891"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-sso-to-your-apps-with-application-proxy"></a>Kerberos-begränsad delegering för enkel inloggning (SSO) till dina appar med Application Proxy

Du kan tillhandahålla enkel inloggning för lokala program som publicerats via programproxyn som skyddas med integrerad Windows-autentisering. Dessa program kräver en Kerberos-biljett för åtkomst. Application Proxy använder Kerberos-begränsad delegering (KCD) för att stödja dessa program. 

Du kan aktivera enkel inloggning till dina program med hjälp av integrerad Windows-autentisering (IWA) genom att ge Application Proxy Connectors behörighet i Active Directory att personifiera användare. Anslutningarna använder den här behörigheten för att skicka och ta emot token för deras räkning.

## <a name="how-single-sign-on-with-kcd-works"></a>Så här fungerar enkel inloggning med KCD
Det här diagrammet förklarar flödet när en användare försöker komma åt ett lokalt program som använder IWA.

![Flödes diagram för Microsoft AAD-autentisering](./media/application-proxy-configure-single-sign-on-with-kcd/authdiagram.png)

1. Användaren anger URL: en för att komma åt den lokala appen via programproxyn.
2. Programproxyn omdirigerar begäran till Azure AD Authentication Services för att förautentisera sig. I det här fallet tillämpar Azure AD alla tillämpliga autentiserings-och Auktoriseringsprinciper, till exempel multifaktorautentisering. Om användaren verifieras skapar Azure AD en token och skickar den till användaren.
3. Användaren skickar token till Application Proxy.
4. Programproxyn verifierar token och hämtar UPN (User Principal Name) från den, och sedan hämtar anslutnings programmet UPN och tjänstens huvud namn (SPN) genom en dubbelriktad säker kanal.
5. -Anslutningen utför en Kerberos-KCD-förhandling med den lokala AD-förhandlingen och imiterar användaren att hämta en Kerberos-token till programmet.
6. Active Directory skickar Kerberos-token för programmet till anslutningen.
7. Anslutningen skickar den ursprungliga begäran till program servern med hjälp av Kerberos-token som den fick från AD.
8. Programmet skickar svaret till anslutningen, som sedan returneras till Application Proxy-tjänsten och slutligen till användaren.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar med enkel inloggning för IWA-program kontrollerar du att din miljö är klar med följande inställningar och konfigurationer:

* Dina appar, t. ex. SharePoint-webbappar, är inställda på att använda integrerad Windows-autentisering. Mer information finns i [Aktivera stöd för Kerberos-autentisering](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759186(v=ws.11)), eller för SharePoint se [plan för Kerberos-autentisering i SharePoint 2013](/SharePoint/security-for-sharepoint-server/kerberos-authentication-planning).
* Alla dina appar har SPN [-namn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* Servern som kör anslutningen och servern som kör appen är domänanslutna och delar av samma domän eller domäner med förtroende. Mer information om domän anslutning finns i [ansluta en dator till en domän](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807102(v=ws.11)).
* Servern som kör anslutningen har åtkomst att läsa TokenGroupsGlobalAndUniversal-attributet för användare. Den här standardinställningen kan ha påverkats av säkerhets härdning av miljön.

### <a name="configure-active-directory"></a>Konfigurera Active Directory
Active Directory-konfigurationen varierar beroende på om din Application Proxy Connector och program servern finns i samma domän eller inte.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Anslutnings-och program server i samma domän
1. I Active Directory går du till **verktyg**  >  **användare och datorer**.
2. Välj den server som kör anslutningen.
3. Högerklicka och välj **Egenskaper**  >  **delegering**.
4. Välj **lita på den här datorn enbart för delegering till angivna tjänster**. 
5. Välj **Använd valfritt autentiseringsprotokoll**.
6. Under **tjänster som det här kontot kan presentera delegerade autentiseringsuppgifter** för lägger du till värdet för program SERVERns SPN-identitet. Detta gör att Application Proxy Connector kan personifiera användare i AD mot de program som definierats i listan.

   ![Koppling – Server instanser Fönstret Egenskaper skärm bild](./media/application-proxy-configure-single-sign-on-with-kcd/properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Anslutnings-och program server i olika domäner
1. En lista över krav för att arbeta med KCD över domäner finns i Kerberos- [begränsad delegering över domäner](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831477(v=ws.11)).
2. Använd `principalsallowedtodelegateto` egenskapen för tjänst kontot (dator eller dedikerat domän användar konto) för webb programmet för att aktivera delegering av Kerberos-autentisering från programproxyn (anslutnings programmet). Program servern körs i kontexten `webserviceaccount` och den delegerande servern är `connectorcomputeraccount` . Kör kommandona nedan på en domänkontrollant (som kör Windows Server 2012 R2 eller senare) i domänen för `webserviceaccount` . Använd fasta namn (icke-UPN) för båda kontona.

   Om `webserviceaccount` är ett dator konto använder du följande kommandon:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Om `webserviceaccount` är ett användar konto använder du följande kommandon:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning 
1. Publicera programmet enligt instruktionerna som beskrivs i [Publicera program med programproxy](application-proxy-add-on-premises-application.md). Se till att välja **Azure Active Directory** som **Förautentiserings metod**.
2. När ditt program visas i listan över företags program markerar du det och klickar på **enkel inloggning**.
3. Ställ in läget för enkel inloggning på **integrerad Windows-autentisering**.  
4. Ange applikations serverns **interna program-SPN** . I det här exemplet är SPN för vårt publicerade program http/www. contoso. com. Detta SPN måste finnas i listan över tjänster som anslutnings programmet kan presentera delegerade autentiseringsuppgifter för. 
5. Välj **Delegerad inloggningsidentitet** för anslutningen som ska användas för användarnas räkning. Mer information finns i [arbeta med olika lokala och molnbaserade identiteter](#working-with-different-on-premises-and-cloud-identities)

   ![Avancerad program konfiguration](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  

## <a name="sso-for-non-windows-apps"></a>SSO för appar som inte kommer från Windows

Kerberos Delegerings flödet i Azure AD-programproxy startar när Azure AD autentiserar användaren i molnet. När begäran har mottagits lokalt utfärdar Azure AD-programproxy Connector en Kerberos-biljett åt användaren genom att interagera med den lokala Active Directory. Den här processen kallas för Kerberos-begränsad delegering (KCD). 

I nästa fas skickas en begäran till Server dels programmet med denna Kerberos-biljett. 

Det finns flera mekanismer som definierar hur du skickar Kerberos-biljetten i sådana begär Anden. De flesta icke-Windows-servrar förväntar sig att ta emot dem i form av SPNEGO-token. Den här mekanismen stöds på Azure AD-programproxy, men är inaktive rad som standard. En anslutning kan konfigureras för SPNEGO eller standard-Kerberos-token, men inte båda.

Om du konfigurerar en anslutnings dator för SPNEGO bör du kontrol lera att alla andra anslutningar i den anslutnings gruppen också är konfigurerade med SPNEGO. Program som förväntar sig Kerberos-token ska dirigeras via andra anslutningar som inte har kon figurer ATS för SPNEGO. Vissa webb program accepterar båda formaten utan att det krävs några ändringar i konfigurationen. 
 

Så här aktiverar du SPNEGO:

1. Öppna en kommando tolk som kör som administratör.
2. Från kommando tolken kör du följande kommandon på de anslutnings servrar som behöver SPNEGO.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Appar som inte är Windows-appar vanligt vis användar namn eller SAM-kontonamn i stället för domän-e-postadresser. Om den situationen gäller för dina program måste du konfigurera fältet delegerad inloggnings identitet för att ansluta dina moln identiteter till dina program identiteter. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Arbeta med olika lokala och molnbaserade identiteter
Application Proxy förutsätter att användarna har exakt samma identitet i molnet och lokalt. Men i vissa miljöer, på grund av företagets principer eller program beroenden, kan organisationer behöva använda alternativa ID: n för inloggning. I sådana fall kan du fortfarande använda KCD för enkel inloggning. Konfigurera en **delegerad inloggnings identitet** för varje program för att ange vilken identitet som ska användas vid enkel inloggning.  

Den här funktionen gör det möjligt för många organisationer som har olika lokala och molnbaserade identiteter att ha SSO från molnet till lokala appar utan att användarna måste ange olika användar namn och lösen ord. Detta omfattar organisationer som:

* Ha flera domäner internt ( joe@us.contoso.com , joe@eu.contoso.com ) och en enda domän i molnet ( joe@contoso.com ).
* Ha ett icke-dirigerbart domän namn internt ( joe@contoso.usa ) och ett giltigt i molnet.
* Använd inte domän namn internt (Johan)
* Använd olika alias lokalt och i molnet. Till exempel joe-johns@contoso.com vs. joej@contoso.com  

Med Application Proxy kan du välja vilken identitet som ska användas för att hämta Kerberos-biljetten. Den här inställningen är per program. Några av dessa alternativ är lämpliga för system som inte accepterar e-postadresser, och andra är utformade för alternativa inloggningar.

![Skärm bild av delegerad inloggnings identitets parameter](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Om delegerad inloggnings identitet används kanske värdet inte är unikt för alla domäner eller skogar i din organisation. Du kan undvika det här problemet genom att publicera programmen två gånger med två olika anslutnings grupper. Eftersom varje program har en annan användar mål grupp kan du ansluta dess kopplingar till en annan domän.

Om **namnet på den lokala sam-kontot** används för inloggnings identiteten måste datorn som är värd för anslutningen läggas till i domänen där användar kontot finns.

### <a name="configure-sso-for-different-identities"></a>Konfigurera SSO för olika identiteter
1. Konfigurera Azure AD Connect inställningar så att huvud identiteten är e-postadressen (e-post). Detta görs som en del av anpassnings processen genom att ändra fältet för **användarens huvud namn** i synkroniseringsinställningarna. De här inställningarna avgör också hur användare loggar in på Office365, windows10-enheter och andra program som använder Azure AD som identitets lager.  
   ![Identifiera användare skärm bild – List rutan för användarens huvud namn](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. I programmets konfigurations inställningar för det program som du vill ändra väljer du den **delegerade inloggnings identitet** som ska användas:

   * Användarens huvud namn (till exempel joe@contoso.com )
   * Alternativt huvud namn för användare (till exempel joed@contoso.local )
   * Användar namn del av användarens huvud namn (till exempel Johan)
   * Användar namn som är en del av alternativt huvud namn för användare (till exempel joed)
   * Namn på lokal SAM-konto (beroende på domänkontrollantens konfiguration)

### <a name="troubleshooting-sso-for-different-identities"></a>Felsöka SSO för olika identiteter
Om det uppstår ett fel i SSO-processen visas den i händelse loggen för anslutnings datorn enligt beskrivningen i [fel sökning](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Men i vissa fall skickas begäran till Server dels programmet medan det här programmet svarar på olika andra HTTP-svar. Fel sökning av dessa fall bör börja genom att undersöka händelse numret 24029 på anslutnings datorn i händelse loggen för programproxy-sessionen. Den användar identitet som användes för delegering visas i fältet "användare" i händelse informationen. Om du vill aktivera sessionsinformation väljer du **Visa analytiska loggar och fel söknings loggar** i visnings menyn för logg boken.

## <a name="next-steps"></a>Nästa steg

* [Så här konfigurerar du ett Application Proxy-program för att använda Kerberos-begränsad delegering](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Felsöka problem med Application Proxy](application-proxy-troubleshoot.md)