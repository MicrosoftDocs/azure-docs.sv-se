---
title: Villkor i princip för villkorlig åtkomst – Azure Active Directory
description: Vad är villkor i en princip för villkorlig åtkomst för Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab840b46503aed1a318e3b39a4e8fe3e4d11735c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579135"
---
# <a name="conditional-access-conditions"></a>Villkorlig åtkomst: villkor

I en princip för villkorlig åtkomst kan en administratör använda signaler från villkor som risk, enhets plattform eller plats för att förbättra sina princip beslut. 

[![Definiera en princip för villkorlig åtkomst och ange villkor](./media/concept-conditional-access-conditions/conditional-access-conditions.png)](./media/concept-conditional-access-conditions/conditional-access-conditions.png#lightbox)

Flera villkor kan kombineras för att skapa detaljerade och detaljerade principer för villkorlig åtkomst.

Vid åtkomst till ett känsligt program kan en administratör till exempel faktor information om inloggnings risker från identitets skydd och plats till sitt åtkomst beslut utöver andra kontroller som Multi-Factor Authentication.

## <a name="sign-in-risk"></a>Inloggningsrisk

För kunder med till gång till [identitets skydd](../identity-protection/overview-identity-protection.md)kan inloggnings risker utvärderas som en del av en princip för villkorlig åtkomst. Inloggnings risken representerar sannolikheten att en begäran om autentisering inte har behörighet av identitets ägaren. Mer information om inloggnings risker finns i artiklarna, [Vad är risk](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) och [hur du kan: Konfigurera och aktivera risk principer](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="user-risk"></a>Användarrisk 

För kunder med till gång till [identitets skydd](../identity-protection/overview-identity-protection.md)kan användar risken utvärderas som en del av en princip för villkorlig åtkomst. Användar risken representerar sannolikheten för att en specifik identitet eller ett konto har komprometterats. Mer information om användar risker finns i artiklarna, [Vad är risk](../identity-protection/concept-identity-protection-risks.md#user-risk) och [hur du kan: Konfigurera och aktivera risk principer](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Enhetsplattformar

Enhets plattformen karakteriseras av operativ systemet som körs på en enhet. Azure AD identifierar plattformen genom att använda information från enheten, till exempel användar Agent strängar. Eftersom användar Agent strängar kan ändras är den här informationen inte verifierad. Enhets plattformen bör användas tillsammans med Microsoft Intune enhetskompatibilitet eller som en del av en block-instruktion. Standardvärdet är att gälla för alla enhets plattformar.

Villkorlig åtkomst för Azure AD stöder följande enhetsspecifika plattformar:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Om du blockerar äldre autentisering med hjälp av **andra klient** villkor kan du även ange enhetens plattforms villkor.

> [!IMPORTANT]
> Microsoft rekommenderar att du har en princip för villkorlig åtkomst för enhets plattformar som inte stöds. Om du t. ex. vill blockera åtkomst till företagets resurser från Linux eller andra klienter som inte stöds, bör du konfigurera en princip med ett villkor för enhets plattformar som inkluderar alla enheter och exkluderar plattformar som stöds och bevilja kontroll uppsättning till blockera åtkomst.

## <a name="locations"></a>Platser

När du konfigurerar plats som ett villkor kan organisationer välja att ta med eller undanta platser. Dessa namngivna platser kan innehålla information om offentliga IPv4-nätverk, land eller region eller till och med okända områden som inte mappas till vissa länder eller regioner. Endast IP-adressintervall kan markeras som betrodda platser.

När du inkluderar **valfri plats** inkluderar det här alternativet alla IP-adresser på Internet som inte precis har konfigurerat namngivna platser. När du väljer **en plats** kan administratörer välja att undanta **alla betrodda** eller **valda platser**.

Vissa organisationer kan till exempel välja att inte kräva Multi-Factor Authentication när deras användare är anslutna till nätverket på en betrodd plats, till exempel deras fysiska huvud kontor. Administratörer kan skapa en princip som inkluderar vilken plats som helst, men undantar de valda platserna för sina huvud kontors nätverk.

Mer information om platser hittar du i artikeln, [vilket är plats villkoret i Azure Active Directory villkorlig åtkomst](location-condition.md).

## <a name="client-apps"></a>Klientappar

Som standard gäller alla nya principer för villkorlig åtkomst för alla typer av klient program även om klient program villkoret inte har kon figurer ATS. 

> [!NOTE]
> Beteendet för klient programs villkoret uppdaterades i augusti 2020. Om du har befintliga principer för villkorlig åtkomst kommer de att förbli oförändrade. Om du klickar på en befintlig princip har växlingen av konfiguration tagits bort och de klient program som principen gäller för är markerade.

> [!IMPORTANT]
> Inloggningar från äldre autentiseringsbegäranden stöder inte MFA och skickar inte enhets tillstånds information till Azure AD, så de kommer att blockeras av kontroller för villkorlig åtkomst, som att kräva MFA-eller kompatibla enheter. Om du har konton som måste använda äldre autentisering måste du antingen undanta dessa konton från principen eller konfigurera principen så att den endast gäller för moderna autentiserings klienter.

Alternativet **Konfigurera** växling när är inställt på **Ja** gäller för markerade objekt, om det är inställt på **Nej** gäller för alla klient program, inklusive moderna och bakåtkompatibla autentiseringsbegäranden. Den här växlingen visas inte i principer som skapats före augusti 2020.

- Moderna autentiserade klienter
   - Webbläsare
      - Detta inkluderar webbaserade program som använder protokoll som SAML, WS-Federation, OpenID Connect eller tjänster som registrerats som en OAuth-konfidentiell klient.
   - Mobilappar och skriv bords klienter
      -  Det här alternativet omfattar program som Office Desktop och Phone-program.
- Äldre autentiserings klienter
   - Exchange ActiveSync-klienter
      - Detta inkluderar all användning av protokollet Exchange ActiveSync (EAS).
      - När principen blockerar användningen av Exchange ActiveSync får den berörda användaren ett enda karantän-e-postmeddelande. Det här e-postmeddelandet med innehåller information om varför de blockeras och ta med reparations instruktioner om det går.
      - Administratörer kan endast tillämpa principer på plattformar som stöds (t. ex. iOS, Android och Windows) via MS-Graph API för villkorlig åtkomst.
   - Övriga klienter
      - Det här alternativet omfattar klienter som använder grundläggande/äldre autentiseringsprotokoll som inte stöder modern autentisering.
         - Autentiserad SMTP – används av POP-och IMAP-klienten för att skicka e-postmeddelanden.
         - Identifiera automatiskt – används av Outlook-och EAS-klienter för att hitta och ansluta till post lådor i Exchange Online.
         - Exchange Online PowerShell – används för att ansluta till Exchange Online med fjärr-PowerShell. Om du blockerar grundläggande autentisering för Exchange Online PowerShell måste du använda Exchange Online PowerShell-modulen för att ansluta. Mer information finns i [ansluta till Exchange Online PowerShell med Multi-Factor Authentication](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Exchange Web Services (EWS) – ett programmerings gränssnitt som används av Outlook, Outlook för Mac och appar från tredje part.
         - IMAP4 – används av IMAP e-postklienter.
         - MAPI över HTTP (MAPI/HTTP) – används av Outlook 2010 och senare.
         - Offlineadressbok (OAB) – en kopia av adress list samlingar som hämtas och används av Outlook.
         - Outlook överallt (RPC över HTTP) – används av Outlook 2016 och tidigare.
         - Outlook service – används av appen e-post och kalender för Windows 10.
         - POP3 – används av POP-e-postklienter.
         - Repor ting Web Services – används för att hämta rapport data i Exchange Online.

Dessa villkor används ofta när du kräver en hanterad enhet, blockerar äldre autentisering och blockerar webb program, men tillåter mobilappar eller skrivbordsappar.

### <a name="supported-browsers"></a>Webbläsare som stöds

Den här inställningen fungerar med alla webbläsare. Men för att uppfylla en enhets princip, som ett krav för en kompatibel enhet, stöds följande operativ system och webbläsare:

| Operativsystem | Webbläsare |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8/8,1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

> [!NOTE]
> Edge 85 + kräver att användaren är inloggad i webbläsaren för att klara enhets identiteten korrekt. Annars fungerar det som Chrome utan tillägg för konton. Den här inloggningen kanske inte sker automatiskt i ett scenario med hybrid Azure AD-anslutning. 

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Varför visas en certifikat varning i webbläsaren

På Windows 7, iOS, Android och macOS Azure AD identifierar enheten med ett klient certifikat som är etablerad när enheten har registrerats med Azure AD.  När en användare först loggar in via webbläsaren uppmanas användaren att välja certifikatet. Användaren måste välja det här certifikatet innan du använder webbläsaren.

#### <a name="chrome-support"></a>Chrome-support

För Chrome-stöd i **Windows 10 Creators Update (version 1703)** eller senare installerar du [tillägget Windows 10-konton](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Det här tillägget krävs när en princip för villkorlig åtkomst kräver enhetsspecifika uppgifter.

Skapa följande register nyckel för att automatiskt distribuera tillägget till Chrome-webbläsare:

- Sökväg HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist
- Namn 1
- Typ REG_SZ (sträng)
- Data ppnbnpeolgkicgegkbkbjmhlideopiji; https \: //clients2.Google.com/service/update2/CRX

Skapa följande register nyckel för Chrome-stöd i **Windows 8,1 och 7**:

- Sökväg HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls
- Namn 1
- Typ REG_SZ (sträng)
- Data {"mönster": " https://device.login.microsoftonline.com ", "filter": {"utfärdare": {"CN": "MS-Organization-Access"}}}

Dessa webbläsare har stöd för enhetsautentisering, så att enheten kan identifieras och verifieras mot en princip. Enhets kontrollen Miss lyckas om webbläsaren körs i privat läge.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Mobila program och skriv bords klienter som stöds

Organisationer kan välja **mobilappar och skriv bords klienter** som klient program.

Den här inställningen påverkar åtkomst försök som görs från följande mobilappar och skriv bords klienter:

| Klientappar | Mål tjänst | Plattform |
| --- | --- | --- |
| Dynamics CRM-App | Dynamics CRM | Windows 10, Windows 8,1, iOS och Android |
| E-post/kalender/Peoples-app, Outlook 2016, Outlook 2013 (med modern autentisering)| Exchange Online | Windows 10 |
| MFA och plats princip för appar. Enhets principer stöds inte.| App Service för Mina appar | Android och iOS |
| Microsoft Teams Services – detta styr alla tjänster som stöder Microsoft-team och alla dess klient program – Windows-skrivbord, iOS, Android, WP och webb klient | Microsoft Teams | Windows 10, Windows 8,1, Windows 7, iOS, Android och macOS |
| Office 2016-appar, Office 2013 (med modern autentisering), [OneDrive-synkronisering-klient](/onedrive/enable-conditional-access) | SharePoint | Windows 8,1, Windows 7 |
| Office 2016-appar, universella Office-appar, Office 2013 (med modern autentisering), [OneDrive-synkroniseringsklient](/onedrive/enable-conditional-access) | sharepoint online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, endast OneNote). | SharePoint | macOS |
| Office 2019| SharePoint | Windows 10, macOS |
| Office Mobile-appar | SharePoint | Android, iOS |
| Office Yammer-app | Yammer | Windows 10, iOS, Android |
| Outlook 2019 | SharePoint | Windows 10, macOS |
| Outlook 2016 (Office för macOS) | Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (med modern autentisering), Skype för företag (med modern autentisering) | Exchange Online | Windows 8,1, Windows 7 |
| Outlook-mobilapp | Exchange Online | Android, iOS |
| Power BI-app | Power BI-tjänst | Windows 10, Windows 8,1, Windows 7, Android och iOS |
| Skype för företag | Exchange Online| Android, iOS |
| Visual Studio Team Services-app | Visual Studio Team Services | Windows 10, Windows 8,1, Windows 7, iOS och Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync-klienter

- Organisationer kan bara välja Exchange ActiveSync-klienter när de tilldelar principer till användare eller grupper. Om du väljer **alla användare**, **alla gäst-och externa användare** eller **katalog roller** , kommer alla användare att omfattas av principen.
- När du skapar en princip som är tilldelad till Exchange ActiveSync-klienter bör **Exchange Online** vara det enda moln program som tilldelats principen. 
- Organisationer kan begränsa omfånget för den här principen till specifika plattformar med hjälp av villkor för **enhets plattformar** .

Om åtkomst kontrollen som tilldelas principen använder **Kräv godkänd klient app**, dirigeras användaren till att installera och använda Outlook Mobile-klienten. Om **Multi-Factor Authentication**, **användningsvillkor** eller **anpassade kontroller** krävs blockeras berörda användare, eftersom grundläggande autentisering inte stöder dessa kontroller.

Mer information finns i följande artiklar:

- [Blockera äldre autentisering med villkorlig åtkomst](block-legacy-authentication.md)
- [Kräver godkända klient program med villkorlig åtkomst](app-based-conditional-access.md)

### <a name="other-clients"></a>Övriga klienter

Genom att välja **andra klienter** kan du ange ett villkor som påverkar appar som använder grundläggande autentisering med e-postprotokoll som IMAP, MAPI, pop, SMTP och äldre Office-appar som inte använder modern autentisering.

## <a name="device-state-preview"></a>Enhets tillstånd (för hands version)

Enhets tillstånds villkoret kan användas för att utesluta enheter som är hybrid Azure AD-anslutna och/eller enheter som marker ATS som kompatibla med en Microsoft Intune efterlevnadsprincip från en organisations principer för villkorlig åtkomst.

Till exempel *alla användare* som har åtkomst till appen för *Microsoft Azure hanterings* moln, inklusive **alla enhets tillstånd** exklusive **enhets hybriden Azure AD** som är ansluten och **enhet markerad som kompatibel** och för *åtkomst kontroller*, **blockera**. 
   - Det här exemplet skulle skapa en princip som endast tillåter åtkomst till Microsoft Azure hantering från enheter som antingen är hybrid Azure AD-anslutna eller enheter som är markerade som kompatibla.

## <a name="next-steps"></a>Nästa steg

- [Villkorlig åtkomst: bevilja](concept-conditional-access-grant.md)

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)
