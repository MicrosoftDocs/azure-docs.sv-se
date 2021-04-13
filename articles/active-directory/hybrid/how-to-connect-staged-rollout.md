---
title: 'Azure AD Connect: molnbaserad autentisering via mellanlagrad distribution | Microsoft Docs'
description: I den här artikeln förklaras hur du migrerar från federerad autentisering till molnbaserad autentisering med hjälp av en stegvis distribution.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 756e5e96a8040fb3d93273a5521236d46879e60d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306389"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout"></a>Migrera till molnautentisering med mellanlagrad distribution

Med mellanlagrad distribution kan du selektivt testa grupper av användare med funktioner för molnbaserad autentisering, t. ex. Azure AD Multi-Factor Authentication (MFA), villkorlig åtkomst, identitets skydd för läckta autentiseringsuppgifter, identitets styrning och andra, innan du klipper över dina domäner.  Den här artikeln beskriver hur du gör-växeln. Innan du påbörjar den mellanlagrade distributionen bör du ta hänsyn till konsekvenserna om ett eller flera av följande villkor är uppfyllda:
    
-  Du använder för närvarande en lokal Multi-Factor Authentication-Server. 
-  Du använder smartkort för autentisering. 
-  Den aktuella servern erbjuder vissa endast Federations funktioner.

Innan du provar den här funktionen rekommenderar vi att du läser vår guide om hur du väljer rätt autentiseringsmetod. Mer information finns i tabellen "jämförelse metoder" i [Välj rätt autentiseringsmetod för Azure Active Directory hybrid identitets lösning](./choose-ad-authn.md#comparing-methods).

En översikt över funktionen finns i "Azure Active Directory: Vad är mellanlagrad distribution?" grafik

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Förutsättningar

-   Du har en Azure Active Directory-klient (Azure AD) med federerade domäner.

-   Du har valt att flytta till något av två alternativ:
    - **Alternativ A**  -  *hash-synkronisering av lösen ord (synkronisering)*  +  *sömlös enkel inloggning (SSO)*.  Mer information finns i [Vad är lösen ords-hash-synkronisering](whatis-phs.md) och [Vad är sömlös SSO](how-to-connect-sso.md)
    - **Alternativ B**  -  *direktautentisering*  +  *sömlös SSO*.  Mer information finns i [Vad är direktautentisering](how-to-connect-pta.md)  
    
    Även om *sömlös enkel inloggning* är valfritt, rekommenderar vi att du aktiverar det för att få en tyst inloggnings upplevelse för användare som kör domänanslutna datorer inifrån ett företags nätverk.

-   Du har konfigurerat alla lämpliga principer för klient anpassning och villkorlig åtkomst som du behöver för användare som migreras till molnbaserad autentisering.

-   Om du planerar att använda Azure AD Multi-Factor Authentication rekommenderar vi att du använder [kombinerad registrering för självbetjäning för återställning av lösen ord (SSPR) och Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) för att användarna ska kunna registrera sina autentiseringsmetoder en gång. Obs! när du använder SSPR för att återställa lösen ordet eller ändra lösen ord med hjälp av min profil sida under den stegvisa distributionen måste Azure AD Connect synkronisera det nya lösen ordet hash som kan ta upp till 2 minuter efter en återställning.

-   Om du vill använda funktionen för stegvis distribution måste du vara global administratör för din klient.

-   Om du vill aktivera *sömlös SSO* på en speciell Active Directory skog måste du vara domän administratör.

-  Om du distribuerar hybrid Azure AD eller Azure AD Join måste du uppgradera till Windows 10 1903 Update.


## <a name="supported-scenarios"></a>Scenarier som stöds

Följande scenarier stöds för stegvis distribution. Funktionen fungerar endast för:

- Användare som är etablerade i Azure AD med hjälp av Azure AD Connect. Den gäller inte enbart för moln användare.

- Användarens inloggnings trafik för webbläsare och *moderna autentiserings* klienter. Program eller moln tjänster som använder äldre autentisering kommer tillbaka till federerade autentiserings flöden. Ett exempel kan vara Exchange Online med modern autentisering inaktive rad eller Outlook 2010, som inte stöder modern autentisering.

- Grupp storleken är för närvarande begränsad till 50 000 användare.  Om du har grupper som är större än 50 000 användare, rekommenderar vi att du delar upp den här gruppen över flera grupper för mellanlagrad distribution.

- Windows 10 hybrid anslutning eller Azure AD Join-hämtning av primär uppdateringstoken utan detaljerad information till Federations servern för Windows 10 version 1903 och senare, när användarens UPN-värde dirigeras och domänsuffix verifieras i Azure AD.

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier stöds inte för stegvis distribution:

- Äldre autentisering som POP3 och SMTP stöds inte.

- Vissa program skickar frågeparametern "domain_hint" till Azure AD under autentiseringen. Dessa flöden fortsätter och användare som är aktiverade för stegvis distribution fortsätter att använda Federation för autentisering.

<!-- -->

- Administratörer kan distribuera molnbaserad autentisering med hjälp av säkerhets grupper. Vi rekommenderar att du använder moln säkerhets grupper för att undvika synkronisering av svars tider när du använder lokala Active Directory säkerhets grupper. Följande villkor gäller:

    - Du kan använda max 10 grupper per funktion. Det innebär att du kan använda 10 grupper var och en för *lösen ords-hash-synkronisering*, *DIREKTAUTENTISERING* och *sömlös SSO*.
    - Kapslade grupper *stöds inte*. 
    - Dynamiska grupper *stöds inte* för mellanlagrad distribution.
    - Kontakt objekt inuti gruppen kommer att blockera gruppen från att läggas till.

- När du först lägger till en säkerhets grupp för stegvis distribution är du begränsad till 200 användare för att undvika en UX-timeout. När du har lagt till gruppen kan du lägga till fler användare direkt till den, efter behov.

- Medan användarna är i stegvis distribution, när EnforceCloudPasswordPolicyForPasswordSyncedUsers har Aktiver ATS, anges principen för lösen ordets giltighets tid till 90 dagar utan alternativ för att anpassa den. 

- Windows 10 hybrid anslutning eller Azure AD Join-hämtning för primär uppdateringstoken för Windows 10 version som är äldre än 1903. Det här scenariot återgår till WS-Trust slut punkten för Federations servern, även om användaren som loggar in befinner sig inom omfånget för mellanlagrad distribution.

- Windows 10 hybrid anslutning eller Azure AD Join-hämtning av primär uppdateringstoken för alla versioner, när användarens lokala UPN inte är flyttbara. Det här scenariot återgår till WS-Trust slut punkten i läget för mellanlagrad distribution, men slutar att fungera när mellanlagrad migrering är slutförd och användar inloggning inte längre förlitar sig på Federations servern.

  >[!NOTE]
  >Du måste fortfarande göra den slutliga start punkt från federerad till molnbaserad autentisering med hjälp av Azure AD Connect eller PowerShell. Den mellanlagrade distributionen växlar inte domäner från federerade till hanterade.  Mer information om domän-start punkt finns i [Migrera från Federation till hash-synkronisering av lösen ord](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso) och [Migrera från Federation till direktautentisering](plan-migrate-adfs-pass-through-authentication.md#step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso).
  
## <a name="get-started-with-staged-rollout"></a>Kom igång med stegvis distribution

Om du vill testa inloggningen för *lösen ords-hash-synkronisering* med hjälp av stegvis distribution följer du anvisningarna i nästa avsnitt.

Information om vilka PowerShell-cmdletar som ska användas finns i för [hands versionen av Azure AD 2,0](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>För arbete för synkronisering av lösen ords-hash

1. Aktivera *hash-synkronisering av lösen ord* från sidan [valfria funktioner](how-to-connect-install-custom.md#optional-features) i Azure AD Connect. 

   ![Skärm bild av sidan med valfria funktioner i Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Se till att en fullständig *synkronisering av lösen ord för hash* har körts så att alla användares lösen ords-hashar har synkroniserats med Azure AD. Om du vill kontrol lera statusen för *hash-synkronisering av lösen ord* kan du använda PowerShell-diagnostik i [Felsöka lösen ords-hash-synkronisering med Azure AD Connect Sync](tshoot-connect-password-hash-synchronization.md).

   ![Skärm bild av fel söknings loggen AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Om du vill testa *direktautentisering* genom att använda mellanlagrad distribution aktiverar du det genom att följa anvisningarna i nästa avsnitt.

## <a name="pre-work-for-pass-through-authentication"></a>För bearbetning i förväg för direktautentisering

1. Identifiera en server som kör Windows Server 2012 R2 eller senare och där du vill att agenten för *direkt autentisering* ska köras. 

   Välj *inte* Azure AD Connect servern. Kontrol lera att servern är domänansluten, kan autentisera valda användare med Active Directory och kan kommunicera med Azure AD på utgående portar och URL: er. Mer information finns i avsnittet "steg 1: kontrol lera kraven" i [snabb start: Azure AD sömlös enkel inloggning](how-to-connect-sso-quick-start.md).

1. [Ladda ned Azure AD Connect Authentication agent](https://aka.ms/getauthagent)och installera den på servern. 

1. Om du vill aktivera [hög tillgänglighet](how-to-connect-sso-quick-start.md)installerar du ytterligare autentiseringsmetoder på andra servrar.

1. Kontrol lera att du har konfigurerat [inställningarna för smart utelåsning](../authentication/howto-password-smart-lockout.md) på rätt sätt. På så sätt ser du till att dina användares lokala Active Directory-konton inte blir utelåsta av felaktiga aktörer.

Vi rekommenderar att du aktiverar *sömlös SSO* oberoende av inloggnings metoden (*lösen ordets hash-synkronisering* eller *direktautentisering*) som du väljer för stegvis distribution. Om du vill aktivera *sömlös SSO* följer du anvisningarna i nästa avsnitt.

## <a name="pre-work-for-seamless-sso"></a>För hands arbete för sömlös enkel inloggning

Aktivera *sömlös SSO* på Active Directory skogar med hjälp av PowerShell. Om du har mer än en Active Directory skog aktiverar du den för varje skog individuellt. *Sömlös SSO* utlöses endast för användare som har valts för stegvis distribution. Den befintliga Federations konfigurationen påverkas inte.

Aktivera *sömlös SSO* genom att göra följande:

1. Logga in på Azure AD Connect-servern.

2. Gå till mappen *% ProgramFiles% \\ Microsoft Azure Active Directory Connect* .

3. Importera den *sömlösa SSO* PowerShell-modulen genom att köra följande kommando: 

   `Import-Module .\AzureADSSO.psd1`

4. Kör PowerShell som administratör. I PowerShell anropar du `New-AzureADSSOAuthenticationContext` . Det här kommandot öppnar ett fönster där du kan ange klient organisationens autentiseringsuppgifter för global administratör.

5. Anropa `Get-AzureADSSOStatus | ConvertFrom-Json` . Det här kommandot visar en lista över Active Directory skogar (se listan "domäner") där funktionen har Aktiver ATS. Som standard är den inställd på false på klient nivå.

   ![Exempel på Windows PowerShell-utdata](./media/how-to-connect-staged-rollout/sr3.png)

6. Anropa `$creds = Get-Credential` . Ange domän administratörs behörighet för den avsedda Active Directory skogen vid prompten.

7. Anropa `Enable-AzureADSSOForest -OnPremCredentials $creds` . Det här kommandot skapar AZUREADSSOACC-datornamnet från den lokala domänkontrollanten för den Active Directory skog som krävs för *sömlös SSO*.

8. *Sömlös SSO* kräver att URL: er är i zonen Intranät. Om du vill distribuera dessa URL: er med hjälp av grup principer, se [snabb start: Azure AD sömlös enkel inloggning](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. För en fullständig genom gång kan du också hämta våra [distributions planer](https://aka.ms/SeamlessSSODPDownload) för *sömlös SSO*.

## <a name="enable-staged-rollout"></a>Aktivera mellanlagrad distribution

Om du vill distribuera en speciell funktion (*vidarekoppling*, *lösen ords-hash-synkronisering* eller *sömlös SSO*) till en Välj uppsättning användare i en grupp, följer du anvisningarna i nästa avsnitt.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Aktivera en stegvis distribution av en speciell funktion på din klient organisation

Du kan distribuera ett av följande alternativ:

- **Alternativ A**  -  *hash-synkronisering*  +  av lösen ord *sömlös SSO*
- **Alternativ B**  -  *direktautentisering*  +  *sömlös SSO*
- **Stöds inte**  -  *hash-synkronisering*  +  av lösen ord *direktautentisering*  +  *sömlös SSO*

Gör följande:

1. Logga in på [Azure AD-portalen](https://aka.ms/stagedrolloutux)för att få åtkomst till UX.

2. Välj **inloggnings länken Aktivera mellanlagrad installation för hanterad användare** .

   Om du t. ex. vill aktivera *alternativ A*, drar du skjutreglaget för **hash-synkronisering av lösen ord** och **sömlöst enkel inloggning** till **på**, som du ser i följande avbildningar.

   

  

3. Lägg till grupperna i funktionen för att aktivera *direktautentisering* och *sömlös SSO*. För att undvika en UX-timeout kontrollerar du att säkerhets grupperna inte innehåller fler än 200 medlemmar från början.

   

   >[!NOTE]
   >Medlemmarna i en grupp aktive ras automatiskt för mellanlagrad distribution. Kapslade och dynamiska grupper stöds inte för mellanlagrad distribution.
   >När du lägger till en ny grupp kommer användare i gruppen (upp till 200 användare för en ny grupp) att uppdateras för att använda hanterad autentisering direkt. Genom att redigera en grupp (lägga till eller ta bort användare) kan det ta upp till 24 timmar innan ändringarna börjar gälla.
   >Sömlös SSO gäller endast om användarna finns i den sömlösa SSO-gruppen och även i antingen en PTA-eller PHS-grupp.

## <a name="auditing"></a>Granskning

Vi har aktiverat gransknings händelser för de olika åtgärder som vi utför för stegvis distribution:

- Gransknings händelse när du aktiverar en mellanlagrad distribution för *lösen ords-hash-synkronisering*, *DIREKTAUTENTISERING* eller *sömlös SSO*.

  >[!NOTE]
  >En gransknings händelse loggas när *sömlös enkel inloggning* aktive ras med hjälp av mellanlagrad distribution.

  ![Fönstret "skapa distributions princip för funktion" – fliken aktivitet](./media/how-to-connect-staged-rollout/sr7.png)

  ![Fönstret "skapa distributions princip för funktion" – fliken ändrade egenskaper](./media/how-to-connect-staged-rollout/sr8.png)

- Gransknings händelse när en grupp läggs till i *hash-synkronisering av lösen ord*, *DIREKTAUTENTISERING* eller *sömlös SSO*.

  >[!NOTE]
  >En gransknings händelse loggas när en grupp läggs till i *lösen ordets hash-synkronisering* för stegvis distribution.

  ![Fönstret "Lägg till en grupp till funktions distribution" – fliken aktivitet](./media/how-to-connect-staged-rollout/sr9.png)

  ![Fönstret "Lägg till en grupp till funktions distribution" – fliken ändrade egenskaper](./media/how-to-connect-staged-rollout/sr10.png)

- Gransknings händelse när en användare som har lagts till i gruppen har Aktiver ATS för stegvis distribution.

  ![Fönstret "Lägg till användar till funktions distribution" – fliken aktivitet](media/how-to-connect-staged-rollout/sr11.png)

  ![Fönstret "Lägg till användar till funktions distribution" – fliken mål (er)](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Validering

Om du vill testa inloggningen med *hash-synkronisering av lösen ord* eller *direktautentisering* (användar namn och lösen ord) gör du följande:

1. På extra nätet går du till [sidan appar](https://myapps.microsoft.com) i en privat webbläsarsession och anger sedan USERPRINCIPALNAME (UPN) för det användar konto som har valts för stegvis distribution.

   Användare som har varit mål för stegvis distribution omdirigeras inte till din federerade inloggnings sida. De uppmanas istället att logga in på inloggnings sidan för Azure AD-klient.

1. Kontrol lera att inloggningen visas i [rapporten inloggnings aktivitet i Azure AD](../reports-monitoring/concept-sign-ins.md) genom att filtrera med userPrincipalName.

Så här testar du inloggning med *sömlös SSO*:

1. På intranätet går du till [sidan appar](https://myapps.microsoft.com) i en privat webbläsarsession och anger sedan USERPRINCIPALNAME (UPN) för det användar konto som har valts för stegvis distribution.

   Användare som har varit mål för stegvis distribution av *sömlös SSO* visas med ett "försök att logga in dig..." meddelande innan de är inloggade i tyst läge.

1. Kontrol lera att inloggningen visas i [rapporten inloggnings aktivitet i Azure AD](../reports-monitoring/concept-sign-ins.md) genom att filtrera med userPrincipalName.

   Följ instruktionerna i [AD FS fel sökning: händelser och loggning](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)för att spåra användar inloggningar som fortfarande inträffar på Active Directory Federation Services (AD FS) (AD FS) för valda distributions användare. Kontrol lera leverantörens dokumentation om hur du kan kontrol lera detta på tredjeparts Federations leverantörer.

## <a name="remove-a-user-from-staged-rollout"></a>Ta bort en användare från mellanlagrad distribution

Att ta bort en användare från gruppen inaktiverar stegvis distribution för den användaren. Om du vill inaktivera funktionen för stegvis distribution drar du tillbaka kontrollen till **av**.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: kan jag använda den här funktionen i produktionen?**

A: Ja, du kan använda den här funktionen i din produktions klient, men vi rekommenderar att du först provar i test klienten.

**F: kan funktionen användas för att upprätthålla en permanent "samexistens" där vissa användare använder federerad autentisering och andra använder molnbaserad autentisering?**

A: Nej, den här funktionen är utformad för att testa molnbaserad autentisering. När du har testat några få grupper av användare bör du klippa över till molnbaserad autentisering. Vi rekommenderar inte att du använder ett permanent blandat läge, eftersom den här metoden kan leda till oväntade autentiserings flöden.

**F: kan jag använda PowerShell för att utföra stegvis distribution?**

S: Ja. Information om hur du använder PowerShell för att utföra stegvis distribution finns i [Azure AD Preview](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout).

## <a name="next-steps"></a>Nästa steg
- [Azure AD 2,0-förhandsgranskning](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#staged_rollout )
- [Ändra inloggnings metoden till hash-synkronisering av lösen ord](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
- [Ändra inloggnings metod till direktautentisering](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
