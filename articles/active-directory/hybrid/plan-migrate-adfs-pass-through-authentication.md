---
title: 'Azure AD Connect: Migrera från Federation till PTA för Azure AD'
description: Den här artikeln innehåller information om hur du flyttar din hybrid identitets miljö från Federation till direktautentisering.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10c4c0e6e40636e4803e054155d6fdaa12a9366
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96858560"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migrera från Federation till direktautentisering för Azure Active Directory

I den här artikeln beskrivs hur du flyttar organisations domäner från Active Directory Federation Services (AD FS) (AD FS) för att sprida autentisering.

> [!NOTE]
> Att ändra autentiseringsmetoden kräver planering, testning och eventuella stillestånds tider. [Stegvis](how-to-connect-staged-rollout.md) distribution är ett alternativt sätt att testa och gradvis migrera från Federation till molnbaserad autentisering med hjälp av direktautentisering.
> 
> Om du planerar att använda stegvis distribution bör du komma ihåg att inaktivera funktionerna för stegvis distribution när du är klar med att klippa ut.  Mer information finns i [migrera till molnbaserad autentisering med hjälp av mellanlagrad](how-to-connect-staged-rollout.md) distribution


## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Krav för migrering till direktautentisering

Följande förutsättningar måste vara uppfyllda om du vill migrera från att använda AD FS för att använda direktautentisering.

### <a name="update-azure-ad-connect"></a>Uppdatera Azure AD Connect

För att kunna slutföra de steg som krävs för att migrera till att använda direktautentisering måste du ha [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 eller en senare version. I Azure AD Connect 1.1.819.0 utförs inloggningen på ett mycket bra sätt. Den totala tiden för migrering från AD FS till molnbaserad autentisering i den här versionen minskas från potentiella timmar till minuter.

> [!IMPORTANT]
> Du kan läsa i inaktuell dokumentation, verktyg och Bloggar som användar konvertering krävs när du konverterar domäner från federerad identitet till hanterad identitet. Det krävs inte längre att *konvertera användare* . Microsoft arbetar med att uppdatera dokumentation och verktyg för att avspegla den här ändringen.

Om du vill uppdatera Azure AD Connect slutför du stegen i [Azure AD Connect: uppgradera till den senaste versionen](./how-to-upgrade-previous-version.md).

### <a name="plan-authentication-agent-number-and-placement"></a>Planera Authentication agent-nummer och placering

Direktautentisering kräver att du distribuerar enkla agenter på Azure AD Connect-servern och på den lokala dator som kör Windows Server. Om du vill minska svars tiden installerar du agenterna så nära som möjligt Active Directory domän kontrol Lanterna.

För de flesta kunder räcker det två eller tre autentiseringsmekanismer för att ge hög tillgänglighet och den kapacitet som krävs. En klient organisation kan ha högst 12 agenter registrerade. Den första agenten installeras alltid på själva Azure AD Connect servern. Information om agent begränsningar och agent distributions alternativ finns i [Azure AD-direktautentisering: aktuella begränsningar](./how-to-connect-pta-current-limitations.md).

### <a name="plan-the-migration-method"></a>Planera migrations metoden

Du kan välja mellan två metoder för att migrera från federerad identitets hantering till vidarekoppling och sömlös enkel inloggning (SSO). Vilken metod du använder beror på hur AD FS-instansen ursprungligen konfigurerades.

* **Azure AD Connect**. Om du ursprungligen konfigurerade AD FS med Azure AD Connect *måste* du ändra till direktautentisering genom att använda Azure AD Connect guiden.

   Azure AD Connect kör automatiskt cmdleten **set-MsolDomainAuthentication** när du ändrar inloggnings metoden för användaren. Azure AD Connect automatiskt federera alla verifierade federerade domäner i Azure AD-klienten.

   > [!NOTE]
   > Om du för närvarande har använt Azure AD Connect för att konfigurera AD FS kan du undvika att inte federera alla domäner i din klient organisation när du ändrar inloggningen för användare till vidarekoppling.
‎
* **Azure AD Connect med PowerShell**. Du kan bara använda den här metoden om du inte ursprungligen konfigurerade AD FS med hjälp av Azure AD Connect. För det här alternativet måste du fortfarande ändra användar inloggnings metoden via guiden Azure AD Connect. Core-skillnaden med det här alternativet är att guiden inte kör cmdleten **set-MsolDomainAuthentication** automatiskt. Med det här alternativet har du fullständig kontroll över vilka domäner som konverteras och i vilken ordning.

För att förstå vilken metod du ska använda, slutför stegen i följande avsnitt.

#### <a name="verify-current-user-sign-in-settings"></a>Verifiera de aktuella användar inloggnings inställningarna

1. Logga in på [Azure AD-portalen](https://aad.portal.azure.com/) med ett globalt administratörs konto.
2. I avsnittet **användar inloggning** kontrollerar du följande inställningar:
   * **Federation** har angetts till **aktive rad**.
   * **Sömlös enkel inloggning** är inställt på **inaktive rad**.
   * **Direktautentisering** är **inaktive rad**.

   ![Skärm bild av inställningarna i avsnittet Azure AD Connect användar inloggning](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Verifiera hur federationen konfigurerades

1. Öppna Azure AD Connect på Azure AD Connect-servern. Välj **Konfigurera**.
2. På sidan **Ytterligare aktiviteter** väljer du **Visa aktuell konfiguration** och väljer sedan **Nästa**.<br />
 
   ![Skärm bild av alternativet Visa nuvarande konfiguration på sidan Ytterligare aktiviteter](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. Under **Ytterligare aktiviteter > hanterar federationen**, bläddrar du till **Active Directory Federation Services (AD FS) (AD FS)**.<br />

   * Om AD FS-konfigurationen visas i det här avsnittet kan du på ett säkert sätt anta att AD FS ursprungligen konfigurerades med hjälp av Azure AD Connect. Du kan konvertera dina domäner från federerad identitet till hanterad identitet med hjälp av alternativet Azure AD Connect **ändra användar inloggning** . Mer information om processen finns i avsnittet **A: Konfigurera direktautentisering genom att använda Azure AD Connect**.
   * Om AD FS inte visas i de aktuella inställningarna måste du manuellt konvertera dina domäner från federerad identitet till hanterad identitet med hjälp av PowerShell. Mer information om den här processen finns i avsnittet **alternativ B: växla från Federation till direktautentisering genom att använda Azure AD Connect och PowerShell**.

### <a name="document-current-federation-settings"></a>Dokumentera aktuella Federations inställningar

Du hittar de aktuella Federations inställningarna genom att köra cmdleten **Get-MsolDomainFederationSettings** :

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Exempel:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Kontrol lera alla inställningar som kan ha anpassats för din dokumentation om Federations design och distribution. Mer specifikt kan du söka efter anpassningar i **PreferredAuthenticationProtocol**, **SupportsMfa** och **PromptLoginBehavior**.

Mer information finns i de här artiklarna:

* [AD FS prompt = stöd för inloggnings parameter](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

> [!NOTE]
> Om **SupportsMfa** är inställt på **Sant** använder du en lokal Multi-Factor Authentication-lösning för att mata in en andra faktor i flödet för användarautentisering. Den här installationen fungerar inte längre för Azure AD-autentiserings scenarier. 
>
> Använd i stället den molnbaserade Azure AD Multi-Factor Authentication-baserade tjänsten för att utföra samma funktion. Utvärdera dina Multi-Factor Authentication-krav noggrant innan du fortsätter. Innan du konverterar domänerna bör du se till att du förstår hur du använder Azure AD Multi-Factor Authentication, licens konsekvenserna och användar registrerings processen.

#### <a name="back-up-federation-settings"></a>Säkerhetskopiera Federations inställningar

Även om inga ändringar görs i andra förlitande parter i AD FS-servergruppen under de processer som beskrivs i den här artikeln, rekommenderar vi att du har en aktuell giltig säkerhets kopia av din AD FS server grupp som du kan återställa från. Du kan skapa en aktuell giltig säkerhets kopia genom att använda det kostnads fria [verktyget Microsoft AD FS Rapid Restore](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Du kan använda verktyget för att säkerhetskopiera AD FS och återställa en befintlig Server grupp eller skapa en ny server grupp.

Om du väljer att inte använda AD FS Rapid Restore-verktyget bör du exportera Microsoft 365 identitets plattformens förtroende för förlitande part och eventuella associerade anpassade anspråks regler som du har lagt till. Du kan exportera förtroende för förlitande part och associerade anspråks regler med hjälp av följande PowerShell-exempel:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Distributions överväganden och användning av AD FS

I det här avsnittet beskrivs överväganden för distribution och information om hur du använder AD FS.

### <a name="current-ad-fs-use"></a>Nuvarande AD FS användning

Innan du konverterar från federerad identitet till hanterad identitet kan du titta närmare på hur du för närvarande använder AD FS för Azure AD, Microsoft 365 och andra program (förtroenden för förlitande part). Mer specifikt bör du tänka på de scenarier som beskrivs i följande tabell:

| Om | Dra |
|-|-|
| Du planerar att fortsätta använda AD FS med andra program (andra än Azure AD och Microsoft 365). | När du har konverterat dina domäner använder du både AD FS och Azure AD. Överväg användar upplevelsen. I vissa fall kan användare behöva autentisera två gånger: en gång till Azure AD (där en användare får SSO-åtkomst till andra program, t. ex. Microsoft 365), och igen för program som fortfarande är kopplade till AD FS som förtroende för en förlitande part. |
| AD FS-instansen är kraftigt anpassad och förlitar sig på särskilda anpassnings inställningar i onload.js-filen (till exempel om du ändrade inloggnings upplevelsen så att användarna endast använder ett **sAMAccountName** -format för sitt användar namn i stället för ett UPN-format eller om din organisation har stor varumärkes inloggnings upplevelsen). onload.js-filen kan inte dupliceras i Azure AD. | Innan du fortsätter måste du kontrol lera att Azure AD kan uppfylla dina aktuella anpassnings krav. Mer information och anvisningar finns i avsnitten om AD FS anpassning och AD FS anpassning.|
| Du använder AD FS för att blockera tidigare versioner av autentiseringsbegäranden.| Överväg att ersätta AD FS kontroller som blockerar tidigare versioner av autentiseringsbegäranden genom att använda en kombination av [villkorliga åtkomst kontroller](../conditional-access/concept-conditional-access-conditions.md) och [åtkomst regler för Exchange Online-klienter](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules). |
| Du kräver att användare utför Multi-Factor Authentication mot en lokal Multi-Factor Authentication Server-lösning när användare autentiserar till AD FS.| I en hanterad identitets domän kan du inte mata in en Multi-Factor Authentication-utmaning via den lokala Multi-Factor Authentication-lösningen i autentiseringsschemat. Du kan dock använda Azure AD Multi-Factor Authentication-tjänsten för Multi-Factor Authentication när domänen har konverterats.<br /><br /> Om användarna inte använder Azure AD Multi-Factor Authentication krävs ett steg för att registrera Databasmigrering-användare. Du måste förbereda för och förmedla den planerade registreringen till dina användare. |
| Du använder för närvarande principer för åtkomst kontroll (AuthZ-regler) i AD FS för att kontrol lera åtkomsten till Microsoft 365.| Överväg att ersätta principerna med motsvarande [principer för villkorlig åtkomst](../conditional-access/overview.md) för Azure AD och [åtkomst regler för Exchange Online-klienter](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules).|

### <a name="common-ad-fs-customizations"></a>Vanliga AD FS anpassningar

I det här avsnittet beskrivs vanliga AD FS anpassningar.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork-anspråk

AD FS utfärdar **InsideCorporateNetwork** -anspråk om den användare som autentiserar finns i företags nätverket. Detta anspråk kan sedan skickas till Azure AD. Anspråket används för att kringgå Multi-Factor Authentication baserat på användarens nätverks plats. Information om hur du avgör om den här funktionen för närvarande är tillgänglig i AD FS finns i [betrodda IP-adresser för federerade användare](../authentication/howto-mfa-adfs.md).

**InsideCorporateNetwork** -anspråket är inte tillgängligt efter att dina domäner har konverterats till direktautentisering. Du kan använda [namngivna platser i Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) för att ersätta den här funktionen.

När du har konfigurerat namngivna platser måste du uppdatera alla principer för villkorlig åtkomst som har kon figurer ATS för att antingen inkludera eller exkludera nätverket **alla betrodda platser** eller **MFA-betrodda IP-adresser** för att avspegla de nya namngivna platserna.

Mer information om **plats** villkor i villkorlig åtkomst finns i [Active Directory villkorliga åtkomst platser](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

När du ansluter en enhet till Azure AD kan du skapa regler för villkorlig åtkomst som tvingar enheterna att uppfylla dina åtkomst standarder för säkerhet och efterlevnad. Användare kan också logga in på en enhet genom att använda ett organisations arbets-eller skol konto i stället för ett personligt konto. När du använder hybrid Azure AD-anslutna enheter kan du ansluta dina Active Directory domänanslutna enheter till Azure AD. Den federerade miljön kan ha kon figurer ATS för att använda den här funktionen.

För att säkerställa att hybrid kopplingen fortsätter att fungera för alla enheter som är anslutna till domänen när domänerna har konverterats till direktautentisering, för Windows 10-klienter, måste du använda Azure AD Connect för att synkronisera Active Directory dator konton till Azure AD.

För Windows 8-och Windows 7-dator konton använder hybrid anslutning sömlös SSO för att registrera datorn i Azure AD. Du behöver inte synkronisera Windows 8-och Windows 7-dator konton som du gör för Windows 10-enheter. Du måste dock distribuera en uppdaterad workplacejoin.exe-fil (via en MSI-fil) till Windows 8-och Windows 7-klienter så att de kan registrera sig själva med hjälp av sömlös SSO. [Ladda ned MSI-filen](https://www.microsoft.com/download/details.aspx?id=53554).

Mer information finns i [Konfigurera hybrid Azure AD-anslutna enheter](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Anpassning

Om din organisation har [anpassat dina AD FS inloggnings sidor](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) för att visa information som är mer relevant för organisationen, kan du överväga att göra liknande [anpassningar till inloggnings sidan för Azure AD](../fundamentals/customize-branding.md).

Även om liknande anpassningar är tillgängliga, ska vissa visuella ändringar på inloggnings sidor förväntas efter konverteringen. Du kanske vill ange information om förväntade ändringar i din kommunikation till användare.

> [!NOTE]
> Organisations anpassning är bara tillgängligt om du köper Premium-eller Basic-licensen för Azure Active Directory eller om du har en Microsoft 365 licens.

## <a name="plan-for-smart-lockout"></a>Planera för smart utelåsning

Azure AD Smart utelåsning skyddar mot brute-framtvinga lösen ords attacker. Smart utelåsning förhindrar att ett lokalt Active Directory-konto blir utelåst när direktautentisering används och en grup princip för konto utelåsning anges i Active Directory.

Mer information finns i [Azure Active Directory Smart utelåsning](../authentication/howto-password-smart-lockout.md).

## <a name="plan-deployment-and-support"></a>Planera distribution och support

Slutför de uppgifter som beskrivs i det här avsnittet för att hjälpa dig att planera för distribution och support.

### <a name="plan-the-maintenance-window"></a>Planera underhålls perioden

Även om domän konverterings processen är relativt snabb, kan Azure AD fortsätta att skicka vissa autentiseringsbegäranden till dina AD FS-servrar i upp till fyra timmar efter det att domän konverteringen är färdig. I detta fyra timmars fönster, och beroende på olika cacheminnen på tjänst sidan, kanske inte Azure AD accepterar dessa autentiseringar. Användare kan få ett fel meddelande. Användaren kan fortfarande autentisera mot AD FS, men Azure AD accepterar inte längre användarens utfärdade token eftersom Federations förtroendet nu har tagits bort.

Endast användare som har åtkomst till tjänsterna via en webbläsare under den här post konverterings perioden innan cacheminnet för service sidan tas bort påverkas. Äldre klienter (Exchange ActiveSync, Outlook 2010/2013) förväntas inte påverkas eftersom Exchange Online behåller sina autentiseringsuppgifter under en angiven tids period. Cachen används för att autentisera användaren tyst. Användaren behöver inte gå tillbaka till AD FS. Autentiseringsuppgifterna som lagras på enheten för dessa klienter används för att tyst autentisera sig själva när den cachelagrade informationen har rensats. Användare förväntas inte ta emot några lösen ords meddelanden som ett resultat av domän konverterings processen.

Moderna autentiserings klienter (Office 2016 och Office 2013, iOS och Android-appar) använder en giltig uppdateringstoken för att hämta nya åtkomsttoken för fortsatt åtkomst till resurser i stället för att återgå till AD FS. De här klienterna är immun till eventuella lösen ords meddelanden som uppstår till följd av domän konverterings processen. Klienterna kommer att fortsätta att fungera utan ytterligare konfiguration.

> [!IMPORTANT]
> Stäng inte av AD FS miljön eller ta bort Microsoft 365 förlitande part-förtroendet tills du har kontrollerat att alla användare kan autentiseras med hjälp av molnbaserad autentisering.

### <a name="plan-for-rollback"></a>Planera för återställning

Om du stöter på ett större problem som du inte kan lösa snabbt kan du välja att återställa lösningen till federationen. Det är viktigt att planera vad du ska göra om distributionen inte distribueras som avsett. Om konverteringen av domänen eller användare Miss lyckas under distributionen, eller om du behöver återställa till federationen, måste du förstå hur du minimerar eventuella avbrott och minskar effekterna på användarna.

#### <a name="to-roll-back"></a>Återställa

Om du vill planera för återställning kontrollerar du dokumentationen för Federations design och distribution för din distributions information. Processen bör omfatta följande uppgifter:

* Konvertera hanterade domäner till federerade domäner med hjälp av cmdleten **Convert-MsolDomainToFederated** .
* Vid behov kan du konfigurera ytterligare anspråks regler.

### <a name="plan-communications"></a>Planera kommunikation

En viktig del av att planera distribution och support är att se till att användarna proaktivt informeras om kommande ändringar. Användarna bör känna i förväg vad de kan uppleva och vad som krävs för dem.

När både direktautentisering och sömlös SSO har distribuerats, är användar inloggnings upplevelsen för att få åtkomst till Microsoft 365 och andra resurser som autentiseras via Azure AD-ändringar. Användare som är utanför nätverket ser bara inloggnings sidan för Azure AD. Dessa användare omdirigeras inte till den formulärbaserade sidan som presenteras av externa webb program proxyservrar.

Inkludera följande element i din kommunikations strategi:

* Meddela användare om kommande och utgivna funktioner med hjälp av:
   * E-post och andra interna kommunikations kanaler.
   * Visuella objekt, till exempel affischer.
   * Executive, Live eller annan kommunikation.
* Bestäm vem som ska anpassa kommunikationen och vem som ska skicka kommunikationen och när.

## <a name="implement-your-solution"></a>Implementera din lösning

Du har planerat din lösning. Nu kan du implementera det. Implementeringen omfattar följande komponenter:

* Förbereder för sömlös SSO.
* Ändra inloggnings metoden till vidarekoppling och aktivera sömlös SSO.

### <a name="step-1-prepare-for-seamless-sso"></a>Steg 1: Förbered för sömlös SSO

För att enheterna ska kunna använda sömlös SSO måste du lägga till en Azure AD-URL till användarnas intranät zons inställningar med hjälp av en grup princip i Active Directory.

Som standard beräknar webbläsare automatiskt rätt zon, antingen Internet eller intranät, från en URL. Till exempel **http: \/ \/ contoso/** Maps till zonen intranät och **http: \/ \/ intranet.contoso.com** Maps till zonen Internet (eftersom URL: en innehåller en punkt). Webbläsare skickar Kerberos-biljetter till en moln slut punkt, t. ex. Azure AD-URL, endast om du lägger till URL: en i webbläsarens intranät zon.

Slutför stegen för att [distribuera](./how-to-connect-sso-quick-start.md) de nödvändiga ändringarna på enheterna.

> [!IMPORTANT]
> Om du gör den här ändringen ändras inte hur användarna loggar in på Azure AD. Det är dock viktigt att du tillämpar den här konfigurationen på alla dina enheter innan du fortsätter. Användare som loggar in på enheter som inte har tagit emot den här konfigurationen krävs bara för att ange ett användar namn och lösen ord för att logga in på Azure AD.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Steg 2: ändra inloggnings metoden till vidarekoppling och aktivera sömlös SSO

Du har två alternativ för att ändra inloggnings metod till direktautentisering och aktivera sömlös SSO.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Alternativ A: Konfigurera direktautentisering genom att använda Azure AD Connect

Använd den här metoden om du ursprungligen konfigurerade AD FS miljön med Azure AD Connect. Du kan inte använda den här metoden om du *inte* ursprungligen konfigurerade din AD FS-miljö med hjälp av Azure AD Connect.

> [!IMPORTANT]
> När du har slutfört följande steg konverteras alla domäner från federerad identitet till hanterad identitet. Mer information finns [i Planera migrations metoden](#plan-the-migration-method).

Ändra först inloggnings metoden:

1. Öppna guiden Azure AD Connect på Azure AD Connect-servern.
2. Välj **ändra användar inloggning** och välj sedan **Nästa**. 
3. På sidan **Anslut till Azure AD** anger du användar namn och lösen ord för ett globalt administratörs konto.
4. På sidan **användar inloggning** väljer du knappen **vidarekoppling** , väljer **aktivera enkel inloggning** och väljer sedan **Nästa**.
5. På sidan **aktivera enkel inloggning** anger du autentiseringsuppgifterna för ett domän administratörs konto och väljer sedan **Nästa**.

   > [!NOTE]
   > Autentiseringsuppgifter för domän administratörs kontot krävs för att aktivera sömlös SSO. Processen utför följande åtgärder, som kräver dessa utökade behörigheter. Autentiseringsuppgifterna för domän administratörs kontot lagras inte i Azure AD Connect eller i Azure AD. Autentiseringsuppgifterna för domän administratörs kontot används bara för att aktivera funktionen. Autentiseringsuppgifterna tas bort när processen har slutförts.
   >
   > 1. Ett dator konto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i den lokala Active Directory-instansen.
   > 2. Dator kontots Kerberos-dekrypterings nyckel delas på ett säkert sätt med Azure AD.
   > 3. Två huvud namn för tjänsten Kerberos (SPN) skapas för att representera två URL: er som används vid inloggning i Azure AD.

6. På sidan **klar att konfigurera** kontrollerar du att kryss rutan **starta synkroniseringen När konfigurationen är klar** är markerad. Välj sedan **Konfigurera**.<br />

   ![Skärm bild av sidan redo att konfigurera](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. I Azure AD-portalen väljer du **Azure Active Directory** och väljer sedan **Azure AD Connect**.
8. Verifiera följande inställningar:
   * **Federationen** är inställd på **inaktive rad**.
   * **Sömlös enkel inloggning** har angetts till **aktive rad**.
   * **Direktautentisering** är inställt på **aktive rad**.<br />

   ![Skärm bild som visar inställningarna i användar inloggnings avsnittet](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Nästa. distribuera ytterligare autentiseringsmetoder:

1. I Azure Portal går du till **Azure Active Directory**  >  **Azure AD Connect** och väljer sedan **direktautentisering**.
2. På sidan **direkt autentisering** väljer du knappen **Hämta** .
3. På sidan **Ladda ned agent** väljer du **Godkänn villkor och hämta**.

   Ytterligare autentiseringspaket börjar laddas ned. Installera den sekundära Autentiseringstjänsten på en domänansluten Server. 

   > [!NOTE]
   > Den första agenten installeras alltid på Azure AD Connect själva servern som en del av konfigurations ändringarna som gjorts i avsnittet **användar inloggning** i Azure AD Connect-verktyget. Installera eventuella ytterligare autentiseringsmekanismer på en separat server. Vi rekommenderar att du har två eller tre ytterligare autentiseringsscheman tillgängliga. 

4. Kör installations agenten för autentisering. Under installationen måste du ange autentiseringsuppgifterna för ett globalt administratörs konto.

   ![Skärm bild som visar knappen Installera som du använder för att köra Microsoft Azure AD Connect Authentication agent-paketet.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Skärm bild som visar Microsoft-inloggnings sidan.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. När du har installerat Authentication agent kan du gå tillbaka till sidan hälso tillstånd för direktautentisering för att kontrol lera statusen för de ytterligare agenterna.

Hoppa till [test och nästa steg](#testing-and-next-steps).

> [!IMPORTANT]
> Hoppa över avsnittet **alternativ B: växla från Federation till direktautentisering genom att använda Azure AD Connect och PowerShell**. Stegen i det här avsnittet gäller inte om du väljer alternativ A för att ändra inloggnings metoden till direktautentisering och aktivera sömlös SSO. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Alternativ B: växla från Federation till direktautentisering genom att använda Azure AD Connect och PowerShell

Använd det här alternativet om du inte ursprungligen konfigurerade de federerade domänerna med hjälp av Azure AD Connect.

Börja med att aktivera direktautentisering:

1. Öppna guiden Azure AD Connect på Azure AD Connect-servern.
2. Välj **ändra användar inloggning** och välj sedan **Nästa**.
3. På sidan **Anslut till Azure AD** anger du användar namn och lösen ord för ett globalt administratörs konto.
4. På sidan **användar inloggning** väljer du knappen genom **strömnings autentisering** . Välj **aktivera enkel inloggning** och välj sedan **Nästa**.
5. På sidan **aktivera enkel inloggning** anger du autentiseringsuppgifterna för ett domän administratörs konto och väljer sedan **Nästa**.

   > [!NOTE]
   > Autentiseringsuppgifter för domän administratörs kontot krävs för att aktivera sömlös SSO. Processen utför följande åtgärder, som kräver dessa utökade behörigheter. Autentiseringsuppgifterna för domän administratörs kontot lagras inte i Azure AD Connect eller i Azure AD. Autentiseringsuppgifterna för domän administratörs kontot används bara för att aktivera funktionen. Autentiseringsuppgifterna tas bort när processen har slutförts.
   > 
   > 1. Ett dator konto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i den lokala Active Directory-instansen.
   > 2. Dator kontots Kerberos-dekrypterings nyckel delas på ett säkert sätt med Azure AD.
   > 3. Två huvud namn för tjänsten Kerberos (SPN) skapas för att representera två URL: er som används vid inloggning i Azure AD.

6. På sidan **klar att konfigurera** kontrollerar du att kryss rutan **starta synkroniseringen När konfigurationen är klar** är markerad. Välj sedan **Konfigurera**.<br />

   ![Skärm bild som visar sidan klar att konfigurera och knappen Konfigurera](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   Följande steg inträffar när du väljer **Konfigurera**:

   1. Den första genom strömnings verifierings agenten är installerad.
   2. Direkt lagrings funktionen är aktive rad.
   3. Sömlös SSO är aktiverat.

7. Verifiera följande inställningar:
   * **Federation** har angetts till **aktive rad**.
   * **Sömlös enkel inloggning** har angetts till **aktive rad**.
   * **Direktautentisering** är inställt på **aktive rad**.
   
   ![Skärm bild som visar de inställningar som ska verifieras i användar inloggnings avsnittet.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Välj **direktautentisering** och kontrol lera att statusen är **aktiv**.<br />
   
   Om Autentiseringstjänsten inte är aktiv slutför du vissa [fel söknings steg](./tshoot-connect-pass-through-authentication.md) innan du fortsätter med domän konverteringen i nästa steg. Du riskerar att orsaka autentiserings avbrott om du konverterar dina domäner innan du verifierar att dina direktautentisering har installerats och att deras status är **aktiv** i Azure Portal.

Sedan distribuerar du ytterligare autentiseringsmetoder:

1. I Azure Portal går du till **Azure Active Directory**  >  **Azure AD Connect** och väljer sedan **direktautentisering**.
2. På sidan **direkt autentisering** väljer du knappen **Hämta** . 
3. På sidan **Ladda ned agent** väljer du **Godkänn villkor och hämta**.
 
   Autentiseringstjänsten börjar laddas ned. Installera den sekundära Autentiseringstjänsten på en domänansluten Server.

   > [!NOTE]
   > Den första agenten installeras alltid på Azure AD Connect själva servern som en del av konfigurations ändringarna som gjorts i avsnittet **användar inloggning** i Azure AD Connect-verktyget. Installera eventuella ytterligare autentiseringsmekanismer på en separat server. Vi rekommenderar att du har två eller tre ytterligare autentiseringsscheman tillgängliga.
 
4. Kör installations agenten för autentisering. Under installationen måste du ange autentiseringsuppgifterna för ett globalt administratörs konto.<br />

   ![Skärm bild som visar knappen Installera på sidan Microsoft Azure AD Connect Authentication agent Package](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Skärm bild som visar inloggnings Sidan](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. När du har installerat Authentication agent kan du gå tillbaka till sidan hälso tillstånd för direktautentisering för att kontrol lera statusen för de ytterligare agenterna.

I det här läget är federerade autentisering fortfarande aktiv och fungerar för dina domäner. För att fortsätta med distributionen måste du konvertera varje domän från federerad identitet till hanterad identitet så att direktautentisering börjar betjäna autentiseringsbegäranden för domänen.

Du behöver inte konvertera alla domäner på samma tid. Du kan välja att starta med en test domän på din produktions klient eller börja med din domän som har det lägsta antalet användare.

Slutför konverteringen med hjälp av Azure AD PowerShell-modulen:

1. I PowerShell loggar du in på Azure AD med ett globalt administratörs konto.
2. Om du vill konvertera den första domänen kör du följande kommando:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. I Azure AD-portalen väljer du **Azure Active Directory**  >  **Azure AD Connect**.
4. När du har konverterat alla federerade domäner kontrollerar du följande inställningar:
   * **Federationen** är inställd på **inaktive rad**.
   * **Sömlös enkel inloggning** har angetts till **aktive rad**.
   * **Direktautentisering** är inställt på **aktive rad**.<br />

   ![Skärm bild som visar inställningarna i avsnittet användar inloggning i Azure AD-portalen.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Testning och nästa steg

Utför följande uppgifter för att verifiera direktautentisering och slutföra konverterings processen.

### <a name="test-pass-through-authentication"></a>Testa direktautentisering 

När din klient använder federerade identiteter omdirigerades användarna från inloggnings sidan för Azure AD till din AD FS-miljö. Nu när klienten har kon figurer ATS för att använda direktautentisering i stället för federerad autentisering, omdirigeras inte användarna till AD FS. I stället loggar användarna in direkt på inloggnings sidan för Azure AD.

Så här testar du direktautentisering:

1. Öppna Internet Explorer i InPrivate-läge så att sömlös inloggning inte loggar in automatiskt.
2. Gå till Office 365-inloggnings sidan ( [https://portal.office.com](https://portal.office.com/) ).
3. Ange ett UPN för användare och välj sedan **Nästa**. Se till att du anger UPN för en hybrid användare som har synkroniserats från din lokala Active Directory-instans och som tidigare använde federerad autentisering. En sida där du anger användar namn och lösen ord visas:

   ![Skärm bild som visar inloggnings sidan där du anger ett användar namn](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Skärm bild som visar inloggnings sidan där du anger ett lösen ord](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. När du har angett lösen ordet och väljer **Logga** in omdirigeras du till Office 365-portalen.

   ![Skärm bild som visar Office 365-portalen](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Testa sömlös SSO

Så här testar du sömlös SSO:

1. Logga in på en domänansluten dator som är ansluten till företags nätverket.
2. I Internet Explorer eller Chrome går du till någon av följande URL: er (Ersätt "contoso" med din domän):

   * https: \/ \/ myapps.Microsoft.com/contoso.com
   * https: \/ \/ myapps.Microsoft.com/contoso.onmicrosoft.com

   Användaren omdirigeras en kort stund till inloggnings sidan för Azure AD, som visar meddelandet "försöker logga in dig". Användaren behöver inte ange något användar namn eller lösen ord.<br />

   ![Skärm bild som visar inloggnings sidan för Azure AD och meddelandet](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
3. Användaren omdirigeras och har loggat in på åtkomst panelen:

   > [!NOTE]
   > Sömlös SSO fungerar på Microsoft 365 tjänster som har stöd för domän tips (till exempel myapps.microsoft.com/contoso.com). Microsoft 365-portalen (portal.office.com) stöder för närvarande inte domän tips. Användare måste ange ett UPN. När ett UPN har angetts hämtar sömlös SSO Kerberos-biljetten för användarens räkning. Användaren är inloggad utan att ange ett lösen ord.

   > [!TIP]
   > Överväg att distribuera [Azure AD hybrid Join på Windows 10](../devices/overview.md) för en förbättrad SSO-upplevelse.

### <a name="remove-the-relying-party-trust"></a>Ta bort förtroende för förlitande part

När du har kontrollerat att alla användare och klienter har autentiserats via Azure AD är det säkert att ta bort den Microsoft 365 förlitande partens förtroende.

Om du inte använder AD FS för andra orsaker (det vill säga för andra förtroenden för förlitande part), är det säkert att inaktivera AD FS i det här läget.

### <a name="rollback"></a>Återställning

Om du upptäcker ett större problem och inte kan lösa det snabbt kan du välja att återställa lösningen till Federation.

Läs dokumentationen om Federations design och distribution för din distributions information. Processen bör omfatta följande uppgifter:

* Konvertera hanterade domäner till federerad autentisering med hjälp av cmdleten **Convert-MsolDomainToFederated** .
* Om det behövs konfigurerar du ytterligare anspråks regler.

### <a name="sync-userprincipalname-updates"></a>Synkronisera userPrincipalName-uppdateringar

Tidigare blockeras uppdateringar av **userPrincipalName** -attributet, som använder synkroniseringstjänsten från den lokala miljön, om inte båda dessa villkor är uppfyllda:

* Användaren är i en hanterad (icke-federerad) identitets domän.
* Användaren har inte tilldelats någon licens.

Information om hur du kontrollerar eller aktiverar den här funktionen finns i [Synkronisera userPrincipalName-uppdateringar](./how-to-connect-syncservice-features.md).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rulla över sömlös SSO Kerberos-dekrypterings nyckel

Det är viktigt att ofta rulla över Kerberos-dekrypteringsnyckeln för AZUREADSSOACC-dator kontot (som representerar Azure AD). Dator kontot AZUREADSSOACC skapas i din lokala Active Directory skog. Vi rekommenderar starkt att du går igenom Kerberos-dekrypteringsnyckeln minst var 30: e dag för att anpassas till det sätt som Active Directory domän medlemmar skickar lösen ords ändringar. Det finns ingen kopplad enhet kopplad till AZUREADSSOACC-datorns konto objekt, så du måste utföra förnyelsen manuellt.

Initiera förnyelsen av den sömlösa Kerberos-krypteringsnyckeln på den lokala server som kör Azure AD Connect.

Mer information finns i [Hur gör jag för att över Kerberos-dekrypteringsnyckeln för dator kontot för AZUREADSSOACC?](./how-to-connect-sso-faq.md).

## <a name="monitoring-and-logging"></a>Övervakning och loggning

Övervaka de servrar som kör autentiseringstjänsten för att underhålla lösningens tillgänglighet. Förutom allmänna Server prestanda räknare exponerar autentiseringsinställningarna prestanda objekt som kan hjälpa dig att förstå statistik och fel i autentiseringen.

Autentiserings agenter loggar åtgärder i Windows-händelseloggen som finns under program-och Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin.

Du kan också aktivera loggning för fel sökning.

Mer information finns i [felsöka Azure Active Directory direktautentisering](./tshoot-connect-pass-through-authentication.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att [Azure AD Connect design koncept](plan-connect-design-concepts.md).
* Välj [rätt autentisering](./choose-ad-authn.md).
* Lär dig mer om [topologier som stöds](plan-connect-design-concepts.md).
