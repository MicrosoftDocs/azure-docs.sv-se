---
title: 'Azure Active Directory Connect: Felsök sömlösa enkla Sign-On | Microsoft Docs'
description: I det här avsnittet beskrivs hur du felsöker Azure Active Directory sömlösa enkla Sign-On
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bc638eec174a52d501120d5e53bb2dc9e35b688
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591182"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Felsöka Azure Active Directory sömlösa enkla Sign-On

Den här artikeln hjälper dig att hitta felsöknings information om vanliga problem som rör Azure Active Directory (Azure AD) sömlös enkel Sign-On (sömlös SSO).

## <a name="known-issues"></a>Kända problem

- I några fall kan det ta upp till 30 minuter att aktivera sömlös SSO.
- Om du inaktiverar och återaktiverar sömlös SSO på din klient, kommer användarna inte att få enkel inloggning till sina cachelagrade Kerberos-biljetter, vanligt vis giltiga i 10 timmar, ha upphört att gälla.
- Om sömlös SSO lyckas har användaren inte möjlighet att välja **Behåll mig inloggad**. På grund av det här problemet fungerar inte [SharePoint-och OneDrive-mappnings scenarier](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) .
- Microsoft 365 Win32-klienter (Outlook, Word, Excel och andra) med versioner 16.0.8730. xxxx och senare stöds med ett icke-interaktivt flöde. Andra versioner stöds inte. i dessa versioner anger användarna sina användar namn, men inte lösen ord, för inloggning. För OneDrive måste du aktivera [funktionen OneDrive-tyst konfiguration](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) för att få en tyst inloggning.
- Sömlös SSO fungerar inte i privat bläddringsläge på Firefox.
- Sömlös SSO fungerar inte i Internet Explorer när utökat skyddat läge är aktiverat.
- Sömlös enkel inloggning fungerar inte i mobila webbläsare på iOS och Android.
- Om en användare är en del av för många grupper i Active Directory kommer användarens Kerberos-biljett troligen att vara för stor för att kunna bearbetas, och detta leder till att sömlös SSO Miss fungerar. Azure AD HTTPS-begäranden kan ha huvuden med en maximal storlek på 50 KB; Kerberos-biljetter måste vara mindre än den gräns som krävs för att hantera andra Azure AD-artefakter (vanligt vis 2-5 KB) som cookies. Vi rekommenderar att du minskar användarens grupp medlemskap och försöker igen.
- Om du synkroniserar 30 eller fler Active Directory skogar kan du inte aktivera sömlös SSO via Azure AD Connect. Som en lösning kan du [aktivera funktionen manuellt](#manual-reset-of-the-feature) på klienten.
- Om du lägger till Azure AD-tjänstens URL ( `https://autologon.microsoftazuread-sso.com` ) i zonen betrodda platser i stället för zonen Lokalt intranät *blockeras användare från att logga in*.
- Sömlös SSO stöder AES256_HMAC_SHA1, AES128_HMAC_SHA1 och RC4_HMAC_MD5 krypterings typer för Kerberos. Det rekommenderas att krypterings typen för AzureADSSOAcc $-kontot har angetts till AES256_HMAC_SHA1, eller en av AES-typerna vs. RC4 för ytterligare säkerhet. Krypterings typen lagras i attributet msDS-SupportedEncryptionTypes för kontot i din Active Directory.  Om krypterings typen för AzureADSSOAcc $-kontot har angetts till RC4_HMAC_MD5 och du vill ändra den till en av AES-krypterings typerna, se till att du först rullar över Kerberos-dekrypterings nyckeln för AzureADSSOAcc $-kontot enligt beskrivningen i [FAQ-dokumentet](how-to-connect-sso-faq.md) under den aktuella frågan, annars sker ingen sömlös SSO.
-  Om du har mer än en skog med skogs förtroende, aktiverar SSO i en av skogarna, kommer att aktivera SSO i alla betrodda skogar. Om du aktiverar SSO i en skog där SSO redan är aktiverat, får du ett fel meddelande om att SSO redan har Aktiver ATS i skogen.

## <a name="check-status-of-feature"></a>Kontrol lera status för funktionen

Se till att den sömlösa SSO-funktionen fortfarande är **aktive rad** på din klient. Du kan kontrol lera statusen genom att gå till **Azure AD Connects** fönstret i [Azure Active Directory administrations Center](https://aad.portal.azure.com/).

![Azure Active Directory administrations Center: Azure AD Connects fönstret](./media/tshoot-connect-sso/sso10.png)

Klicka här för att se alla AD-skogar som har Aktiver ATS för sömlös SSO.

![Azure Active Directory administrations Center: sömlöst SSO-fönster](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Orsaker till inloggnings försök i Azure Active Directory administrations Center (behöver en Premium-licens)

Om din klient har en Azure AD Premium licens som är kopplad till den, kan du också titta på [inloggnings aktivitets rapporten](../reports-monitoring/concept-sign-ins.md) i [Azure Active Directory administrations centret](https://aad.portal.azure.com/).

![Azure Active Directory administrations Center: inloggnings rapport](./media/tshoot-connect-sso/sso9.png)

Bläddra till **Azure Active Directory**  >  **inloggningar** i [Azure Active Directory administrations Center](https://aad.portal.azure.com/)och välj sedan en speciell användares inloggnings aktivitet. Leta efter fältet **fel kod för inloggning** . Mappa värdet i fältet till en felorsak och lösning med hjälp av följande tabell:

|Felkod för inloggning|Orsak till inloggnings försök|Lösning
| --- | --- | ---
| 81001 | Användarens Kerberos-biljett är för stor. | Minska användarens gruppmedlemskap och försök igen.
| 81002 | Det gick inte att verifiera användarens Kerberos-biljett. | Se [Check lista för fel sökning](#troubleshooting-checklist).
| 81003 | Det gick inte att verifiera användarens Kerberos-biljett. | Se [Check lista för fel sökning](#troubleshooting-checklist).
| 81004 | Kerberos-autentiseringsförsök misslyckades. | Se [Check lista för fel sökning](#troubleshooting-checklist).
| 81008 | Det gick inte att verifiera användarens Kerberos-biljett. | Se [Check lista för fel sökning](#troubleshooting-checklist).
| 81009 | Det gick inte att verifiera användarens Kerberos-biljett. | Se [Check lista för fel sökning](#troubleshooting-checklist).
| 81010 | Sömlös SSO misslyckades eftersom användarens Kerberos-biljett har upphört att gälla eller är ogiltig. | Användaren måste logga in från en domänansluten enhet i företags nätverket.
| 81011 | Det gick inte att hitta användarobjektet baserat på informationen i användarens Kerberos-biljett. | Använd Azure AD Connect för att synkronisera användarens information i Azure AD.
| 81012 | Användaren som försöker logga in på Azure AD skiljer sig från den användare som är inloggad på enheten. | Användaren måste logga in från en annan enhet.
| 81013 | Det gick inte att hitta användarobjektet baserat på informationen i användarens Kerberos-biljett. |Använd Azure AD Connect för att synkronisera användarens information i Azure AD. 

## <a name="troubleshooting-checklist"></a>Checklista för felsökning

Använd följande check lista för att felsöka sömlösa SSO-problem:

- Se till att funktionen sömlös SSO är aktive rad i Azure AD Connect. Om du inte kan aktivera funktionen (till exempel på grund av en blockerad port) bör du kontrol lera att du har alla [krav](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) på plats.
- Om du har aktiverat både [Azure AD Join](../devices/overview.md) och sömlös SSO på din klient organisation kontrollerar du att problemet inte är med Azure AD Join. SSO från Azure AD Join prioriteras över sömlös SSO om enheten både är registrerad med Azure AD och domänanslutna. Med SSO från Azure AD Join ser användaren en inloggnings panel som säger "ansluten till Windows".
- Kontrol lera att Azure AD-URL: en ( `https://autologon.microsoftazuread-sso.com` ) är en del av användarens intranät zon inställningar.
- Se till att företags enheten är ansluten till Active Directorys domänen. Enheten behöver _inte_ vara [Azure AD-ansluten](../devices/overview.md) för att sömlös inloggning ska fungera.
- Se till att användaren är inloggad på enheten via ett Active Directory domän konto.
- Se till att användarens konto är från en Active Directory skog där sömlös SSO har kon figurer ATS.
- Kontrol lera att enheten är ansluten till företags nätverket.
- Kontrol lera att enhetens tid är synkroniserad med tiden i både Active Directory och domän kontrol Lanterna och att de är inom fem minuter från varandra.
- Se till att `AZUREADSSOACC` dator kontot finns och är aktiverat i varje AD-skog som du vill ha sömlös SSO aktiverat. Om dator kontot har tagits bort eller saknas kan du skapa dem på nytt med [PowerShell-cmdletar](#manual-reset-of-the-feature) .
- Lista de befintliga Kerberos-biljetterna på enheten med hjälp av `klist` kommandot från en kommando tolk. Se till att det finns biljetter som utfärdats för `AZUREADSSOACC` dator kontot. Användarens Kerberos-biljetter är vanligt vis giltiga i 10 timmar. Du kan ha olika inställningar i Active Directory.
- Om du har inaktiverat och återaktiverat sömlös SSO på klienten, kommer användarna inte att få enkel inloggning till sina cachelagrade Kerberos-biljetter.
- Rensa befintliga Kerberos-biljetter från enheten med hjälp av `klist purge` kommandot och försök igen.
- Om du vill ta reda på om det finns JavaScript-relaterade problem granskar du konsolens loggar i webbläsaren (under **utvecklarverktyg**).
- Granska domänkontrollantens [loggar](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Loggar för domänkontrollant

Om du aktiverar lyckad granskning på domänkontrollanten registreras en säkerhets post i händelse loggen varje gång en användare loggar in via sömlös SSO. Du kan hitta dessa säkerhets händelser med hjälp av följande fråga. (Leta efter händelse **4769** som är associerad med dator kontot **AzureADSSOAcc $**.)

```
  <QueryList>
    <Query Id="0" Path="Security">
      <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
    </Query>
  </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Manuell återställning av funktionen

Om fel sökningen inte hjälper kan du manuellt återställa funktionen på klienten. Följ de här stegen på den lokala server där du kör Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Steg 1: importera sömlös SSO PowerShell-modul

1. Börja med att ladda ned och installera [Azure AD PowerShell](/powershell/azure/active-directory/overview).
2. Bläddra till mappen `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importera den sömlösa SSO PowerShell-modulen med hjälp av följande kommando: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Steg 2: hämta listan över Active Directory skogar där sömlös enkel inloggning har Aktiver ATS

1. Kör PowerShell som administratör. I PowerShell anropar du `New-AzureADSSOAuthenticationContext` . När du uppmanas till det anger du klient organisationens autentiseringsuppgifter för global administratör.
2. Anropa `Get-AzureADSSOStatus` . Med det här kommandot får du en lista över Active Directory skogar (se listan "domäner") där funktionen har Aktiver ATS.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Steg 3: inaktivera sömlös SSO för varje Active Directory skog där du har konfigurerat funktionen

1. Anropa `$creds = Get-Credential` . När du uppmanas till det anger du autentiseringsuppgifter för domän administratören för den avsedda Active Directory skogen.

   > [!NOTE]
   >Användar namnet för domän administratörs behörighet måste anges i formatet SAM-kontonamn (contoso\johndoe eller contoso. com\johndoe). Vi använder domän delen av användar namnet för att hitta domänkontrollanten i domän administratören med hjälp av DNS.

   >[!NOTE]
   >Det domän administratörs konto som används får inte vara medlem i gruppen för skyddade användare. I så fall kommer åtgärden att Miss Miss läge.

2. Anropa `Disable-AzureADSSOForest -OnPremCredentials $creds` . Det här kommandot tar bort `AZUREADSSOACC` dator kontot från den lokala domänkontrollanten för den här aktuella Active Directory skogen.
3. Upprepa föregående steg för varje Active Directory skog där du har konfigurerat funktionen.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Steg 4: Aktivera sömlös SSO för varje Active Directory skog

1. Anropa `Enable-AzureADSSOForest` . När du uppmanas till det anger du autentiseringsuppgifter för domän administratören för den avsedda Active Directory skogen.

   > [!NOTE]
   >Användar namnet för domän administratörs behörighet måste anges i formatet SAM-kontonamn (contoso\johndoe eller contoso. com\johndoe). Vi använder domän delen av användar namnet för att hitta domänkontrollanten i domän administratören med hjälp av DNS.

   >[!NOTE]
   >Det domän administratörs konto som används får inte vara medlem i gruppen för skyddade användare. I så fall kommer åtgärden att Miss Miss läge.

2. Upprepa föregående steg för varje Active Directory skog där du vill ställa in funktionen.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Steg 5. Aktivera funktionen på din klient

Ring om du vill aktivera funktionen på klienten `Enable-AzureADSSO -Enable $true` .
