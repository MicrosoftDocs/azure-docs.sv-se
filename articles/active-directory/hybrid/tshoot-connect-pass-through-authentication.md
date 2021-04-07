---
title: 'Azure AD Connect: Felsök direktautentisering | Microsoft Docs'
description: Den här artikeln beskriver hur du felsöker Azure Active Directory (Azure AD) genom strömnings-autentisering.
services: active-directory
keywords: Felsök Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a014bd5c8f1edbfb00019b8541cef552271d65b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98762851"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Felsöka Azure Active Directory-direktautentisering

Den här artikeln hjälper dig att hitta felsöknings information om vanliga problem i samband med Azure AD-direktautentisering.

>[!IMPORTANT]
>Om du har problem med att logga in användare med direkt autentisering inaktiverar du inte funktionen eller avinstallerar direktautentisering genom att inte ha ett globalt administratörs konto för molnet för att gå vidare. Lär dig mer om [att lägga till ett globalt administratörs konto för molnet](../fundamentals/add-users-azure-active-directory.md). Det här steget är kritiskt och säkerställer att du inte blir utelåst från din klient.

## <a name="general-issues"></a>Allmänna frågor

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Kontrol lera status för funktionen och autentiserings agenter

Se till att funktionen för direkt autentisering fortfarande är **aktive rad** på din klient och statusen för Autentiseringstjänsten visar **aktiv** och inte **inaktiv**. Du kan kontrol lera status genom att gå till bladet **Azure AD Connect** på [Azure Active Directory administrations Center](https://aad.portal.azure.com/).

![Azure Active Directory administrations Center – Azure AD Connect bladet](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Azure Active Directory administrations Center – pass-through Authentication-bladet](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Inloggnings fel meddelanden som riktas mot användare

Om användaren inte kan logga in med hjälp av direktautentisering kan de se något av följande problem med användaren på Azure AD-inloggnings skärmen: 

|Fel|Beskrivning|Lösning
| --- | --- | ---
|AADSTS80001|Det går inte att ansluta till Active Directory|Se till att agent servrar är medlemmar i samma AD-skog som de användare vars lösen ord måste verifieras och att de kan ansluta till Active Directory.  
|AADSTS8002|En timeout uppstod vid anslutning till Active Directory|Kontrol lera att Active Directory är tillgänglig och svarar på begär Anden från agenterna.
|AADSTS80004|Det användar namn som skickades till agenten var inte giltigt|Se till att användaren försöker logga in med rätt användar namn.
|AADSTS80005|Verifieringen påträffade en oförutsägbar WebException|Ett tillfälligt fel. Gör om begäran. Kontakta Microsoft-supporten om den fortsätter att fungera.
|AADSTS80007|Ett fel uppstod vid kommunikation med Active Directory|Mer information finns i agent loggarna och kontrol lera att Active Directory fungerar som förväntat.

### <a name="users-get-invalid-usernamepassword-error"></a>Användare får ett ogiltigt användar namn/lösen ord 

Detta kan inträffa när en användares lokala UserPrincipalName (UPN) skiljer sig från användarens moln-UPN.

För att bekräfta att detta är problemet, måste du först testa att direktautentisering fungerar korrekt:


1. Skapa ett test konto.  
2. Importera PowerShell-modulen på agent datorn:

 ```powershell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\PassthroughAuthPSModule\PassthroughAuthPSModule.psd1"
 ```
3. Kör PowerShell-kommandot anropa: 

 ```powershell
 Invoke-PassthroughAuthOnPremLogonTroubleshooter 
 ``` 
4. När du uppmanas att ange autentiseringsuppgifter anger du samma användar namn och lösen ord som används för att logga in på ( https://login.microsoftonline.com) .

Om du får samma användar namn/lösen ord-fel innebär det att direktautentisering fungerar som den ska och att problemet kan vara att det lokala UPN: et inte är dirigerbart. Mer information finns i [Konfigurera alternativt inloggnings-ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

> [!IMPORTANT]
> Om Azure AD Connect servern inte är domänansluten, ett krav som anges i [Azure AD Connect: krav](./how-to-connect-install-prerequisites.md#installation-prerequisites), uppstår ett ogiltigt användar namn och lösen ord.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Orsaker till inloggnings försök i Azure Active Directory administrations Center (kräver Premium-licens)

Om din klient har en Azure AD Premium licens som är kopplad till den, kan du också titta på [inloggnings aktivitets rapporten](../reports-monitoring/concept-sign-ins.md) i [Azure Active Directory administrations centret](https://aad.portal.azure.com/).

![Rapporten Azure Active Directory administrations center-inloggnings program](./media/tshoot-connect-pass-through-authentication/pta4.png)

Navigera till **Azure Active Directory**  ->  **inloggningar** i [Azure Active Directory administrations Center](https://aad.portal.azure.com/) och klicka på en enskild användares inloggnings aktivitet. Leta efter fältet **fel kod för inloggning** . Mappa värdet i det fältet till en felorsak och lösning med hjälp av följande tabell:

|Felkod för inloggning|Orsak till inloggnings försök|Lösning
| --- | --- | ---
| 50144 | Användarens Active Directory-lösenord har upphört att gälla. | Återställ användarens lösen ord i din lokala Active Directory.
| 80001 | Ingen autentiseringsagent är tillgänglig. | Installera och registrera en agent för autentisering.
| 80002 | Den tillåtna tiden för autentiseringsagentens lösenordsvalidering överskreds. | Kontrol lera om din Active Directory kan kommas åt från Authentication agent.
| 80003 | Ogiltigt svar har tagits emot av autentiseringsagenten. | Om problemet ständigt reproduceras över flera användare, kontrollerar du Active Directory konfiguration.
| 80004 | Felaktig UPN (User Principal Name) används i begäran om inloggning. | Be användaren att logga in med rätt användar namn.
| 80005 | Autentiseringsagent: Fel uppstod. | Tillfälligt fel. Försök igen senare.
| 80007 | Det gick inte att ansluta autentiseringsagenten till Active Directory. | Kontrol lera om din Active Directory kan kommas åt från Authentication agent.
| 80010 | Autentiseringsagenten kan inte dekryptera lösenordet. | Om problemet ständigt är reproducerat, installerar och registrerar du en ny autentiseringstjänst. Och avinstallera den aktuella. 
| 80011 | Autentiseringsagenten kunde inte hämta dekrypteringsnyckeln. | Om problemet ständigt är reproducerat, installerar och registrerar du en ny autentiseringstjänst. Och avinstallera den aktuella.
| 80014 | Verifierings förfrågan har svarat efter att den längsta förfluten tiden har överskridits. | Tids gränsen nåddes för Autentiseringstjänsten. Öppna ett support ärende med felkod, korrelations-ID och tidsstämpel för att få mer information om det här felet

>[!IMPORTANT]
>Direkt autentiserings agenter autentiserar Azure AD-användare genom att verifiera sina användar namn och lösen ord mot Active Directory genom att anropa [Win32 LogonUser-API: et](/windows/win32/api/winbase/nf-winbase-logonusera). Det innebär att om du har ställt in inställningen "logga in på" i Active Directory för att begränsa åtkomsten till arbets stationer måste du lägga till servrar som är värdar för direktautentisering till listan över "inloggningar till"-servrar. Om du inte gör det blockeras användarna från att logga in på Azure AD.

## <a name="authentication-agent-installation-issues"></a>Installations problem för autentiserings agent

### <a name="an-unexpected-error-occurred"></a>Ett oväntat fel har uppstått

[Samla in agent loggar](#collecting-pass-through-authentication-agent-logs) från servern och kontakta Microsoft support med ditt problem.

## <a name="authentication-agent-registration-issues"></a>Registrerings problem för autentiserings agent

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Det gick inte att registrera Authentication agent på grund av blockerade portar

Se till att den server där Autentiseringstjänsten har installerats kan kommunicera med URL: er och portar för tjänsten som anges [här](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Det gick inte att registrera Authentication agent på grund av token eller konto verifierings fel

Se till att du använder ett globalt administratörs konto med endast ett moln för alla Azure AD Connect eller fristående autentiserings-och registrerings åtgärder. Det finns ett känt problem med MFA-aktiverade globala administratörs konton. Stäng av MFA tillfälligt (endast för att slutföra åtgärderna) som en lösning.

### <a name="an-unexpected-error-occurred"></a>Ett oväntat fel har uppstått

[Samla in agent loggar](#collecting-pass-through-authentication-agent-logs) från servern och kontakta Microsoft support med ditt problem.

## <a name="authentication-agent-uninstallation-issues"></a>Problem med avinstallation av autentiseringsfel

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Varnings meddelande när du avinstallerar Azure AD Connect

Om du har direkt autentisering aktive rad på klienten och försöker avinstallera Azure AD Connect, visas följande varnings meddelande: "användarna kommer inte att kunna logga in på Azure AD om du inte har andra direktautentisering installerade på andra servrar."

Se till att installations programmet har [hög tillgänglighet](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) innan du avinstallerar Azure AD Connect för att undvika att dela in användar inloggning.

## <a name="issues-with-enabling-the-feature"></a>Problem med att aktivera funktionen

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Det gick inte att aktivera funktionen eftersom det inte fanns några tillgängliga autentiseringsscheman

Du måste ha minst en aktiv autentiserings agent för att aktivera direktautentisering på din klient. Du kan installera en agent för autentisering genom att antingen installera Azure AD Connect eller en fristående autentiseringstjänst.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Det gick inte att aktivera funktionen på grund av blockerade portar

Se till att den server där Azure AD Connect har installerats kan kommunicera med URL: er och portar för tjänsten som anges [här](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Det gick inte att aktivera funktionen på grund av token eller konto auktoriseringsfel

Se till att du använder ett globalt administratörs konto i molnet när du aktiverar funktionen. Det finns ett känt problem med MFA-aktiverade (Multi-Factor Authentication) globala administratörs konton. Inaktivera MFA tillfälligt (endast för att slutföra åtgärden) som en lösning.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Samla in loggar för strömnings agenter

Beroende på vilken typ av problem du kan ha måste du titta på olika platser för vidarekoppling av loggar med direkt autentisering.

### <a name="azure-ad-connect-logs"></a>Azure AD Connect loggar

Om du har fel som rör installationen kontrollerar du Azure AD Connect loggarna på **%programdata%\AADConnect\trace- \* . log**.

### <a name="authentication-agent-event-logs"></a>Händelse loggar för Authentication agent

Om du har fel som rör Autentiseringstjänsten öppnar du Loggboken-programmet på-servern och kontrollerar under **program-och tjänst Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

För detaljerad analys aktiverar du loggen "session" (Högerklicka i Loggboken programmet för att hitta det här alternativet). Kör inte Authentication agent med den här loggen aktive rad under normal drift; Använd endast för fel sökning. Logg innehållet visas bara när loggen har inaktiverats igen.



### <a name="detailed-trace-logs"></a>Detaljerade spårnings loggar

Felsök användar inloggnings fel genom att leta efter spårnings loggar på **%programdata%\MICROSOFT\AZURE AD Connect Authentication Agent\Trace \\**. Dessa loggar innehåller orsaker till varför en speciell användar inloggning misslyckades med hjälp av funktionen för direkt autentisering. De här felen mappas också till de inloggnings fel orsaker som visas i tabellen närmast föregående fel orsaken till inloggningen. Följande är en exempel logg post:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Du kan få beskrivande information om felet ("1328" i föregående exempel) genom att öppna kommando tolken och köra följande kommando (Obs! Ersätt "1328" med det faktiska fel numret som visas i loggarna:

`Net helpmsg 1328`

![Direktautentisering](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Loggar för domänkontrollant

Om gransknings loggning har Aktiver ATS finns ytterligare information i domän kontrol Lanternas säkerhets loggar. Ett enkelt sätt att fråga inloggnings begär Anden som skickas av direktautentisering är följande:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Prestanda övervaknings räknare

Ett annat sätt att övervaka autentiseringsprinciper är att spåra vissa räknare för prestanda övervakaren på varje server där Authentication agent är installerad. Använd följande globala räknare (**# PTA-autentiseringar**, **#PTA misslyckade autentiseringar** och **#PTA lyckade autentiseringar**) och fel räknare (**# PTA Authentication errors**):

![Prestanda övervaknings räknare för direktautentisering](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Direktautentisering ger hög tillgänglighet med hjälp av flera autentiseringsmekanismer och _inte_ belastnings utjämning. Beroende på din konfiguration får _inte_ alla dina autentiseringsbegäranden ungefär _lika_ många begär Anden. Det är möjligt att en specific Authentication agent inte tar emot någon trafik alls.
