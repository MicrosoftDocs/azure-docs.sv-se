---
title: Konfigurera hantering av autentiseringsprinciper – Azure Active Directory
description: Anpassa konfigurationen för Azure AD-autentisering, inklusive användar inloggnings frekvens och beständighet för webbläsarsessionen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/23/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 205136437fa1a2a33b3b337f5cc9f2dde283faef
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491948"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurera hantering av autentisering med villkorlig åtkomst

I komplexa distributioner kan organisationer ha behov av att begränsa sessionerna för autentisering. Vissa scenarier kan vara:

* Resurs åtkomst från en ohanterad eller delad enhet
* Åtkomst till känslig information från ett externt nätverk
* Användare med hög påverkan
* Kritiska företags program

Med villkorliga åtkomst kontroller kan du skapa principer som riktar sig mot specifika användnings fall i organisationen utan att påverka alla användare.

Innan du simhopp till information om hur du konfigurerar principen ska vi kontrol lera standard konfigurationen.

## <a name="user-sign-in-frequency"></a>Användar inloggnings frekvens

Inloggnings frekvensen definierar den tids period innan en användare uppmanas att logga in igen vid försök att komma åt en resurs.

Standard konfigurationen av Azure Active Directory (Azure AD) för användar inloggnings frekvens är ett rullande fönster på 90 dagar. Att be användare om autentiseringsuppgifter verkar ofta vara lämpligat att göra, men det kan vara refire: användare som har tränats att ange sina autentiseringsuppgifter utan att fundera på att oavsiktligt ange dem till en fråga om obehöriga autentiseringsuppgifter.

Det kan bero på att användaren inte behöver be om en användare att logga in igen, i verkligheten att en överträdelse av IT-principerna återkallar sessionen. Några exempel är (men är inte begränsade till) en lösen ords ändring, en inkompatibel enhet eller inaktivt konto. Du kan även uttryckligen [återkalla användarnas sessioner med hjälp av PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true). Standard konfigurationen för Azure AD kommer inte att be användarna att ange sina autentiseringsuppgifter om säkerhets position inte har ändrats i sina sessioner.

Inställningen för inloggnings frekvens fungerar med appar som har implementerat OAUTH2-eller OIDC-protokoll enligt standarderna. De flesta inbyggda Microsoft-appar för Windows, Mac och Mobile, inklusive följande webb program, följer inställningen.

- Word, Excel, PowerPoint Online
- OneNote online
- Office.com
- Microsoft 365 admin-portalen
- Exchange Online
- SharePoint och OneDrive
- Team webb klient
- Dynamics CRM Online
- Azure Portal

Inställningen för inloggnings frekvens fungerar tillsammans med SAML-program, så länge de inte släpper sina egna cookies och dirigeras tillbaka till Azure AD för autentisering på regelbunden basis.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Användar inloggnings frekvens och Multi-Factor Authentication

Inloggnings frekvensen har tidigare endast tillämpats på den första Factor Authentication på enheter som var Azure AD-ansluten, hybrid Azure AD-ansluten och Azure AD registrerad. Det fanns inget enkelt sätt för våra kunder att genomdriva Multi Factor Authentication (MFA) på dessa enheter. Beroende på kundfeedback kommer inloggnings frekvensen även gälla för MFA.

[![Inloggnings frekvens och MFA](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>Användar inloggnings frekvens och enhets identiteter

Om du har Azure AD-ansluten, en hybrid Azure AD-anslutning eller registrerade Azure AD-enheter, kommer den här händelsen att uppfylla principerna för inloggnings frekvens även när en användare låser upp sin enhet eller loggar i interaktivt. I följande två exempel anges användar inloggnings frekvensen till 1 timme:

Exempel 1:

- Vid 00:00 loggar användaren in på Windows 10 Azure AD-anslutna enheter och börjar arbeta med ett dokument som är lagrat på SharePoint Online.
- Användaren fortsätter att arbeta med samma dokument på sina enheter i en timme.
- Vid 01:00 uppmanas användaren att logga in igen baserat på kravet på inloggnings frekvens i principen för villkorlig åtkomst som kon figurer ATS av administratören.

Exempel 2:

- Vid 00:00 loggar användaren in på Windows 10 Azure AD-anslutna enheter och börjar arbeta med ett dokument som är lagrat på SharePoint Online.
- Vid 00:30 hämtar användaren upp och tar en paus låsning av enheten.
- Vid 00:45 återgår användaren från sin paus och låser upp enheten.
- Vid 01:45 uppmanas användaren att logga in igen baserat på kravet på inloggnings frekvens i principen för villkorlig åtkomst som kon figurer ATS av administratören sedan den senaste inloggningen skedde vid 00:45.

## <a name="persistence-of-browsing-sessions"></a>Persistence för att bläddra i sessioner

Med en beständig webbläsarsession kan användarna vara inloggade när de har stängt och öppnat sina webbläsarfönster igen.

Med Azure AD-beständighet för webbläsarsessionen kan användare på personliga enheter välja om de vill spara sessionen genom att visa en "förbli inloggad?" prompt efter slutförd autentisering. Om webb läsar persisten har kon figurer ATS i AD FS med hjälp av rikt linjerna i artikeln [AD FS enskilda Sign-On inställningar](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)kommer vi att följa principen och även behålla Azure AD-sessionen. Du kan också konfigurera om användare i din klient organisation ska se "förbli inloggad?" genom att ändra lämplig inställning i rutan för företags anpassning i Azure Portal att använda vägledningen i artikeln [Anpassa din inloggnings sida för Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurera kontroller för autentisering av sessionen

Villkorlig åtkomst är en Azure AD Premium funktion och kräver en Premium-licens. Om du vill veta mer om villkorlig åtkomst, se [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Observera att vi inte har stöd för att skapa två olika principer för samma användar-eller app-kombination om du använder den [konfigurerbara livs längden för token](../develop/active-directory-configurable-token-lifetimes.md) i en offentlig för hands version. Microsoft har dragit tillbaka den konfigurerbara livs längden för token för uppdatering och token för sessionstoken den 30 januari 2021 och ersatte den med funktionen för hantering av autentisering med villkorlig åtkomst.  
>
> Innan du aktiverar inloggnings frekvens kontrollerar du att andra inställningar för autentisering är inaktiverade i din klient. Om "kom ihåg MFA på betrodda enheter" är aktive rad måste du inaktivera den innan du använder inloggnings frekvensen, eftersom de båda inställningarna kan leda till att användarna uppmanas att vänta. Mer information om omautentiserings-prompter och sessionens livs längd finns i artikeln [optimera omautentiserings-prompter och förstå sessionens livs längd för Azure AD Multi-Factor Authentication](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

### <a name="policy-1-sign-in-frequency-control"></a>Princip 1: kontroll för inloggnings frekvens

1. Skapa ny princip
1. Välj alla nödvändiga villkor för kundens miljö, inklusive mål molnappar.

   > [!NOTE]
   > Vi rekommenderar att du anger samma frekvens för autentiserings-och körnings frekvensen för viktiga Microsoft Office appar som Exchange Online och SharePoint Online för bästa möjliga användar upplevelse.

1. Gå till **Access Controls**-  >  **sessionen** och klicka på **inloggnings frekvens**
1. Ange det obligatoriska värdet för dagar och timmar i den första text rutan
1. Välj ett värde för **timmar** eller **dagar** från List rutan
1. Spara principen

![Princip för villkorlig åtkomst har kon figurer ATS för inloggnings frekvens](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

På Azure AD-registrerade Windows-enheter loggar du in på enheten som en uppfråga. Om du till exempel har konfigurerat inloggnings frekvensen till 24 timmar för Office-appar, så uppfyller användare på Azure AD-registrerade Windows-enheter inloggnings frekvens principen genom att logga in på enheten och kommer inte att tillfrågas igen när Office-appar öppnas.

### <a name="policy-2-persistent-browser-session"></a>Princip 2: beständig webbläsarsession

1. Skapa ny princip
1. Välj alla obligatoriska villkor.

   > [!NOTE]
   > Observera att den här kontrollen kräver att du väljer alla molnappar som ett villkor. Persistence för webbläsarsessionen styrs av autentiseringstoken för autentisering. Alla flikar i en webbläsarsession delar en token för en session och därför måste alla dela beständiga tillstånd.

1. Gå till **Access Controls**-  >  **sessionen** och klicka på **beständig** webbläsarsession
1. Välj ett värde i list rutan
1. Spara principen

![Princip för villkorlig åtkomst har kon figurer ATS för beständig webbläsare](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Beständig konfiguration av webbläsarsessionen i Azure AD villkorlig åtkomst kommer skriva över "förbli inloggad?" inställningen i rutan för företags anpassning i Azure Portal för samma användare om du har konfigurerat båda principerna.

## <a name="validation"></a>Validering

Använd What-If-verktyget för att simulera en inloggning från användaren till mål programmet och andra villkor baserat på hur du har konfigurerat principen. Kontrollerna för hantering av autentisering visas i resultatet av verktyget.

![Resultat för What If verktyget för villkorlig åtkomst](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Distribution av princip

För att se till att principen fungerar som förväntat, är den rekommenderade rekommenderade metoden att testa den innan den distribueras till produktion. Vi rekommenderar att du använder en test klient för att kontrol lera om den nya principen fungerar som den ska. Mer information finns i artikeln [Planera en distribution av villkorlig åtkomst](plan-conditional-access.md).

## <a name="next-steps"></a>Nästa steg

* Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö kan du läsa artikeln [Planera en distribution av villkorlig åtkomst](plan-conditional-access.md).