---
title: 'Azure AD Connect: Migrera från Federation till PHS för Azure AD | Microsoft Docs'
description: Den här artikeln innehåller information om hur du flyttar din hybrid identitets miljö från Federation till hash-synkronisering av lösen ord.
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
ms.openlocfilehash: dca888bf9e3dc75e80764949a11d95efe3514635
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861824"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migrera från Federation till hash-synkronisering för lösen ord för Azure Active Directory

Den här artikeln beskriver hur du flyttar organisations domäner från Active Directory Federation Services (AD FS) (AD FS) till hash-synkronisering av lösen ord.

> [!NOTE]
> Att ändra autentiseringsmetoden kräver planering, testning och eventuella stillestånds tider. [Mellanlagrad](how-to-connect-staged-rollout.md) distribution är ett alternativt sätt att testa och gradvis migrera från Federation till molnbaserad autentisering med hjälp av hash-synkronisering av lösen ord.
>
> Om du planerar att använda stegvis distribution bör du komma ihåg att inaktivera funktionerna för stegvis distribution när du är klar med att klippa ut.  Mer information finns i [migrera till molnbaserad autentisering med hjälp av mellanlagrad](how-to-connect-staged-rollout.md) distribution


## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Krav för migrering till hash-synkronisering av lösen ord

Följande förutsättningar måste vara uppfyllda för att du ska kunna migrera från att använda AD FS att använda Lösenordssynkronisering.


### <a name="update-azure-ad-connect"></a>Uppdatera Azure AD Connect

För att kunna utföra stegen för att migrera till hash-synkronisering av lösen ord bör du ha [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) -1.1.819.0. Den här versionen innehåller betydande ändringar i hur inloggnings konverteringen utförs och minskar den totala tiden för migrering från Federation till molnbaserad autentisering från potentiella timmar till minuter.


> [!IMPORTANT]
> Du kan läsa i inaktuell dokumentation, verktyg och Bloggar som användar konvertering krävs när du konverterar domäner från federerad identitet till hanterad identitet. Det krävs inte längre att *konvertera användare* . Microsoft arbetar med att uppdatera dokumentation och verktyg för att avspegla den här ändringen.

Om du vill uppdatera Azure AD Connect slutför du stegen i [Azure AD Connect: uppgradera till den senaste versionen](./how-to-upgrade-previous-version.md).

### <a name="password-hash-synchronization-required-permissions"></a>Password-hash-synkronisering krävs behörigheter

Du kan konfigurera Azure AD Connect med hjälp av Express inställningar eller en anpassad installation. Om du har använt alternativet för anpassad installation kanske de [behörigheter som krävs](./reference-connect-accounts-permissions.md) för att synkronisera lösen ord inte är på plats.

Tjänst kontot för Azure AD Connect Active Directory Domain Services (AD DS) kräver följande behörigheter för att synkronisera hashar för lösen ord:

* Replikera katalog ändringar
* Replikera katalog ändringar alla

Nu är det en bra idé att kontrol lera att behörigheterna finns på plats för alla domäner i skogen.

### <a name="plan-the-migration-method"></a>Planera migrations metoden

Du kan välja mellan två metoder för att migrera från federerad identitets hantering till hash-synkronisering av lösen ord och sömlös enkel inloggning (SSO). Vilken metod du använder beror på hur AD FS-instansen ursprungligen konfigurerades.

* **Azure AD Connect**. Om du ursprungligen konfigurerade AD FS med Azure AD Connect *måste* du ändra till hash-synkronisering av lösen ord med hjälp av guiden Azure AD Connect.

   Azure AD Connect kör automatiskt cmdleten **set-MsolDomainAuthentication** när du ändrar inloggnings metoden för användaren. Azure AD Connect automatiskt federera alla verifierade federerade domäner i Azure AD-klienten.

   > [!NOTE]
   > Om du för närvarande har använt Azure AD Connect för att konfigurera AD FS kan du undvika att inte federera alla domäner i din klient organisation när du ändrar användar inloggningen till hash-synkronisering av lösen ord. ‎
* **Azure AD Connect med PowerShell**. Du kan bara använda den här metoden om du inte ursprungligen konfigurerade AD FS med hjälp av Azure AD Connect. För det här alternativet måste du fortfarande ändra användar inloggnings metoden via guiden Azure AD Connect. Core-skillnaden med det här alternativet är att guiden inte kör cmdleten **set-MsolDomainAuthentication** automatiskt. Med det här alternativet har du fullständig kontroll över vilka domäner som konverteras och i vilken ordning.

För att förstå vilken metod du ska använda, slutför stegen i följande avsnitt.

#### <a name="verify-current-user-sign-in-settings"></a>Verifiera de aktuella användar inloggnings inställningarna

Verifiera dina aktuella användar inloggnings inställningar:

1. Logga in på [Azure AD-portalen](https://aad.portal.azure.com/) med ett globalt administratörs konto.
2. I avsnittet **användar inloggning** kontrollerar du följande inställningar:
   * **Federation** har angetts till **aktive rad**.
   * **Sömlös enkel inloggning** är inställt på **inaktive rad**.
   * **Direktautentisering** är **inaktive rad**.

   ![Skärm bild av inställningarna i avsnittet Azure AD Connect användar inloggning](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Verifiera Azure AD Connect-konfigurationen

1. Öppna Azure AD Connect på Azure AD Connect-servern. Välj **Konfigurera**.
2. På sidan **Ytterligare aktiviteter** väljer du **Visa aktuell konfiguration** och väljer sedan **Nästa**.<br />

   ![Skärm bild av alternativet Visa aktuell konfiguration som valts på sidan Ytterligare aktiviteter](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. På sidan **Granska din lösning** noterar du **synkroniseringen av lösen ordets hash** -status.<br /> 

   * Om **lösen ordets hash-synkronisering** är **inaktive rad**, slutför du stegen i den här artikeln för att aktivera det.
   * Om **Lösenordssynkronisering** är inställt på **aktive rad** kan du hoppa över avsnittet **steg 1: Aktivera** Lösenordssynkronisering för hash i den här artikeln.
4. På sidan **Granska din lösning** bläddrar du till **Active Directory Federation Services (AD FS) (AD FS)**.<br />

   * Om AD FS-konfigurationen visas i det här avsnittet kan du på ett säkert sätt anta att AD FS ursprungligen konfigurerades med hjälp av Azure AD Connect. Du kan konvertera dina domäner från federerad identitet till hanterad identitet med hjälp av alternativet Azure AD Connect **ändra användar inloggning** . Processen beskrivs i avsnittet **A: växla från Federation till hash-synkronisering av lösen ord med hjälp av Azure AD Connect**.
   * Om AD FS inte visas i de aktuella inställningarna måste du manuellt konvertera dina domäner från federerad identitet till hanterad identitet med hjälp av PowerShell. Mer information om den här processen finns i avsnittet **alternativ B: växla från Federation till hash-synkronisering av lösen ord med hjälp av Azure AD Connect och PowerShell**.

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
> Om **SupportsMfa** är inställt på **Sant** använder du en lokal Multi-Factor Authentication-lösning för att mata in en andra faktor i flödet för användarautentisering. Den här installationen fungerar inte längre för Azure AD-autentiseringar när du har konverterat den här domänen från federerad till hanterad autentisering. När du har inaktiverat federationen kan du använda relationen till din lokala Federation och detta inkluderar lokala MFA-kort. 
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

AD FS utfärdar **InsideCorporateNetwork** -anspråk om den användare som autentiserar finns i företags nätverket. Detta anspråk kan sedan skickas till Azure AD. Anspråket används för att kringgå Multi-Factor Authentication baserat på användarens nätverks plats. Information om hur du avgör om den här funktionen för närvarande är aktive rad i AD FS finns i [betrodda IP-adresser för federerade användare](../authentication/howto-mfa-adfs.md).

**InsideCorporateNetwork** -anspråket är inte tillgängligt efter att dina domäner har konverterats till hash-synkronisering av lösen ord. Du kan använda [namngivna platser i Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) för att ersätta den här funktionen.

När du har konfigurerat namngivna platser måste du uppdatera alla principer för villkorlig åtkomst som har kon figurer ATS för att antingen inkludera eller exkludera nätverket **alla betrodda platser** eller **MFA-betrodda IP-adresser** för att avspegla de nya namngivna platserna.

Mer information om **plats** villkor i villkorlig åtkomst finns i [Active Directory villkorliga åtkomst platser](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

När du ansluter en enhet till Azure AD kan du skapa regler för villkorlig åtkomst som tvingar enheterna att uppfylla dina åtkomst standarder för säkerhet och efterlevnad. Användare kan också logga in på en enhet genom att använda ett organisations arbets-eller skol konto i stället för ett personligt konto. När du använder hybrid Azure AD-anslutna enheter kan du ansluta dina Active Directory domänanslutna enheter till Azure AD. Den federerade miljön kan ha kon figurer ATS för att använda den här funktionen.

För att säkerställa att hybrid kopplingen fortsätter att fungera för alla enheter som är anslutna till domänen efter att domänerna har konverterats till hash-synkronisering för lösen ord, för Windows 10-klienter, måste du använda Azure AD Connect enhets alternativ för att synkronisera Active Directory dator konton till Azure AD. 

För Windows 8-och Windows 7-dator konton använder hybrid anslutning sömlös SSO för att registrera datorn i Azure AD. Du behöver inte synkronisera Windows 8-och Windows 7-dator konton som du gör för Windows 10-enheter. Du måste dock distribuera en uppdaterad workplacejoin.exe-fil (via en MSI-fil) till Windows 8-och Windows 7-klienter så att de kan registrera sig själva med hjälp av sömlös SSO. [Ladda ned MSI-filen](https://www.microsoft.com/download/details.aspx?id=53554).

Mer information finns i [Konfigurera hybrid Azure AD-anslutna enheter](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Anpassning

Om din organisation har [anpassat dina AD FS inloggnings sidor](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) för att visa information som är mer relevant för organisationen, kan du överväga att göra liknande [anpassningar till inloggnings sidan för Azure AD](../fundamentals/customize-branding.md).

Även om liknande anpassningar är tillgängliga, ska vissa visuella ändringar på inloggnings sidor förväntas efter konverteringen. Du kanske vill ange information om förväntade ändringar i din kommunikation till användare.

> [!NOTE]
> Organisations anpassning är bara tillgängligt om du köper Premium-eller Basic-licensen för Azure Active Directory eller om du har en Microsoft 365-licens.

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

När både hash-synkronisering av lösen ord och sömlös SSO distribueras, är användar inloggnings upplevelsen för att få åtkomst till Microsoft 365 och andra resurser som autentiseras via Azure AD-ändringar. Användare som är utanför nätverket ser bara inloggnings sidan för Azure AD. Dessa användare omdirigeras inte till den formulärbaserade sidan som presenteras av externa webb program proxyservrar.

Inkludera följande element i din kommunikations strategi:

* Meddela användare om kommande och utgivna funktioner med hjälp av:
   * E-post och andra interna kommunikations kanaler.
   * Visuella objekt, till exempel affischer.
   * Executive, Live eller annan kommunikation.
* Bestäm vem som ska anpassa kommunikationen och vem som ska skicka kommunikationen och när.

## <a name="implement-your-solution"></a>Implementera din lösning

Du har planerat din lösning. Nu kan du implementera det. Implementeringen omfattar följande komponenter:

* Aktiverar synkronisering av lösen ords-hash.
* Förbereder för sömlös SSO.
* Ändra inloggnings metoden till hash-synkronisering av lösen ord och aktivera sömlös SSO.

### <a name="step-1-enable-password-hash-synchronization"></a>Steg 1: aktivera hash-synkronisering av lösen ord

Det första steget för att implementera den här lösningen är att aktivera hash-synkronisering av lösen ord med hjälp av guiden Azure AD Connect. Hash-synkronisering av lösen ord är en valfri funktion som du kan aktivera i miljöer som använder Federation. Det finns ingen inverkan på autentiserings flödet. I det här fallet kommer Azure AD Connect att starta synkronisering av lösen ords-hashar utan att påverka användare som loggar in med hjälp av Federation.

Därför rekommenderar vi att du slutför det här steget som en förberedande åtgärds källa innan du ändrar din domäns inloggnings metod. Sedan har du gott om tid för att kontrol lera att lösen ordets hash-synkronisering fungerar korrekt.

Så här aktiverar du Lösenordssynkronisering för hash:

1. Öppna guiden Azure AD Connect på Azure AD Connect-servern och välj sedan **Konfigurera**.
2. Välj **Anpassa synkroniseringsalternativ** och välj sedan **Nästa**.
3. På sidan **Anslut till Azure AD** anger du användar namn och lösen ord för ett globalt administratörs konto.
4. På sidan **Anslut dina kataloger** väljer du **Nästa**.
5. På sidan **domän-och OU-filtrering** väljer du **Nästa**.
6. På sidan **valfria funktioner** väljer du **Lösenordssynkronisering** och väljer sedan **Nästa**.
 
   ![Skärm bild av alternativet för Lösenordssynkronisering som valts på sidan valfria funktioner](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Välj **Nästa** på de återstående sidorna. Välj **Konfigurera** på sista sidan.
8. Azure AD Connect börjar synkronisera lösen ords-hashar vid nästa synkronisering.

När lösen ordets hash-synkronisering har Aktiver ATS hash-kodas lösen ordet för alla användare i omfånget Azure AD Connect-synkronisering och skrivs till Azure AD. Den här åtgärden kan ta några minuter eller flera timmar beroende på antalet användare.

I planerings syfte bör du uppskatta att cirka 20 000 användare bearbetas om 1 timme.

För att kontrol lera att hash-synkroniseringen av lösen ord fungerar korrekt, slutför du **fel söknings** aktiviteten i guiden Azure AD Connect:

1. Öppna en ny Windows PowerShell-session på din Azure AD Connect Server genom att använda alternativet Kör som administratör.
2. Kör `Set-ExecutionPolicy RemoteSigned` eller `Set-ExecutionPolicy Unrestricted` .
3. Starta guiden Azure AD Connect.
4. Gå till sidan **Ytterligare aktiviteter** , Välj **Felsök** och välj sedan **Nästa**.
5. På sidan **fel sökning** väljer du **Starta** för att starta fel söknings menyn i PowerShell.
6. På huvud menyn väljer du **Felsök Password hash-synkronisering**.
7. Välj Password hash-synkronisering på undermenyn **inte fungerar alls**.

Information om fel sökning finns i [Felsöka Password-hash-synkronisering med Azure AD Connect Sync](./tshoot-connect-password-hash-synchronization.md).

### <a name="step-2-prepare-for-seamless-sso"></a>Steg 2: förbereda för sömlös SSO

För att enheterna ska kunna använda sömlös SSO måste du lägga till en Azure AD-URL till användarnas intranät zons inställningar med hjälp av en grup princip i Active Directory.

Som standard beräknar webbläsare automatiskt rätt zon, antingen Internet eller intranät, från en URL. Till exempel **http: \/ \/ contoso/** Maps till zonen intranät och **http: \/ \/ intranet.contoso.com** Maps till zonen Internet (eftersom URL: en innehåller en punkt). Webbläsare skickar Kerberos-biljetter till en moln slut punkt, t. ex. Azure AD-URL, endast om du lägger till URL: en i webbläsarens intranät zon.

Slutför stegen för att [distribuera](./how-to-connect-sso-quick-start.md) de nödvändiga ändringarna på enheterna.

> [!IMPORTANT]
> Om du gör den här ändringen ändras inte hur användarna loggar in på Azure AD. Det är dock viktigt att du tillämpar den här konfigurationen på alla dina enheter innan du fortsätter. Användare som loggar in på enheter som inte har tagit emot den här konfigurationen krävs bara för att ange ett användar namn och lösen ord för att logga in på Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Steg 3: ändra inloggnings metoden till hash-synkronisering av lösen ord och aktivera sömlös SSO

Du har två alternativ för att ändra inloggnings metod till hash-synkronisering av lösen ord och aktivera sömlös SSO.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Alternativ A: växla från Federation till hash-synkronisering av lösen ord med hjälp av Azure AD Connect

Använd den här metoden om du ursprungligen konfigurerade AD FS miljön med Azure AD Connect. Du kan inte använda den här metoden om du *inte* ursprungligen konfigurerade din AD FS-miljö med hjälp av Azure AD Connect.

Ändra först inloggnings metoden:

1. Öppna guiden Azure AD Connect på Azure AD Connect-servern.
2. Välj **ändra användar inloggning** och välj sedan **Nästa**. 

   ![Skärm bild av alternativet ändra användar inloggning på sidan Ytterligare aktiviteter](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. På sidan **Anslut till Azure AD** anger du användar namn och lösen ord för ett globalt administratörs konto.
4. På sidan **användar inloggning** väljer du **knappen lösen ords-hash-synkronisering**. Se till att markera kryss rutan **Konvertera inte användar konton** . Alternativet är föråldrat. Välj **aktivera enkel inloggning** och välj sedan **Nästa**.

   ![Skärm bild av sidan aktivera enkel inloggning](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Från och med Azure AD Connect version 1.1.880.0 är kryss rutan **sömlös enkel inloggning** markerad som standard.

   > [!IMPORTANT]
   > Du kan ignorera varningarna som anger att användar konvertering och fullständig hash-synkronisering av lösen ord krävs för att konvertera från Federation till molnbaserad autentisering. Observera att de här stegen inte behövs längre. Om du fortfarande ser de här varningarna kontrollerar du att du kör den senaste versionen av Azure AD Connect och att du använder den senaste versionen av den här guiden. Mer information finns i avsnittet [uppdatera Azure AD Connect](#update-azure-ad-connect).

5. På sidan **aktivera enkel inloggning** anger du autentiseringsuppgifterna för domän administratörs kontot och väljer sedan **Nästa**.

   ![Skärm bild av sidan aktivera enkel inloggning där du kan ange autentiseringsuppgifter för domän administratörs kontot.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Autentiseringsuppgifter för domän administratörs kontot krävs för att aktivera sömlös SSO. Processen utför följande åtgärder, som kräver dessa utökade behörigheter. Autentiseringsuppgifterna för domän administratörs kontot lagras inte i Azure AD Connect eller i Azure AD. Autentiseringsuppgifterna för domän administratörs kontot används bara för att aktivera funktionen. Autentiseringsuppgifterna tas bort när processen har slutförts.
   >
   > 1. Ett dator konto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i den lokala Active Directory-instansen.
   > 2. Dator kontots Kerberos-dekrypterings nyckel delas på ett säkert sätt med Azure AD.
   > 3. Två huvud namn för tjänsten Kerberos (SPN) skapas för att representera två URL: er som används vid inloggning i Azure AD.

6. På sidan **klar att konfigurera** kontrollerar du att kryss rutan **starta synkroniseringen När konfigurationen är klar** är markerad. Välj sedan **Konfigurera**.

      ![Skärm bild av sidan redo att konfigurera](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > I det här läget kommer alla federerade domäner att ändras till hanterad autentisering. Hash-synkronisering av lösen ord är den nya autentiseringsmetoden.

7. I Azure AD-portalen väljer du **Azure Active Directory**  >  **Azure AD Connect**.
8. Verifiera följande inställningar:
   * **Federationen** är inställd på **inaktive rad**.
   * **Sömlös enkel inloggning** har angetts till **aktive rad**.
   * **Lösenordssynkronisering** är **aktiverat**.<br /> 

   ![Skärm bild som visar inställningarna i avsnittet användar inloggning i Azure AD-portalen.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Hoppa till [test och nästa steg](#testing-and-next-steps).

   > [!IMPORTANT]
   > Hoppa över avsnittet **alternativ B: växla från Federation till hash-synkronisering av lösen ord med hjälp av Azure AD Connect och PowerShell**. Stegen i det här avsnittet gäller inte om du väljer alternativ A för att ändra inloggnings metod till hash-synkronisering av lösen ord och aktivera sömlös SSO.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Alternativ B: växla från Federation till hash-synkronisering av lösen ord med hjälp av Azure AD Connect och PowerShell

Använd det här alternativet om du inte ursprungligen konfigurerade de federerade domänerna med hjälp av Azure AD Connect. Under den här processen aktiverar du sömlös SSO och växlar dina domäner från federerade till hanterade.

1. Öppna guiden Azure AD Connect på Azure AD Connect-servern.
2. Välj **ändra användar inloggning** och välj sedan **Nästa**.
3. På sidan **Anslut till Azure AD** anger du användar namn och lösen ord för ett globalt administratörs konto.
4. På sidan **användar inloggning** väljer du knappen **lösen ords-hash-synkronisering** . Välj **aktivera enkel inloggning** och välj sedan **Nästa**.

   Innan du aktiverar Password hash Sync: ![ skärm bild som visar alternativet Konfigurera inte på användarens inloggnings sida](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   När du har aktiverat Password-hash-synkronisering: ![ skärm bild som visar nya alternativ på inloggnings sidan för användare](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Från och med Azure AD Connect version 1.1.880.0 är kryss rutan **sömlös enkel inloggning** markerad som standard.

5. På sidan **aktivera enkel inloggning** anger du autentiseringsuppgifterna för ett domän administratörs konto och väljer sedan **Nästa**.

   > [!NOTE]
   > Autentiseringsuppgifter för domän administratörs kontot krävs för att aktivera sömlös SSO. Processen utför följande åtgärder, som kräver dessa utökade behörigheter. Autentiseringsuppgifterna för domän administratörs kontot lagras inte i Azure AD Connect eller i Azure AD. Autentiseringsuppgifterna för domän administratörs kontot används bara för att aktivera funktionen. Autentiseringsuppgifterna tas bort när processen har slutförts.
   >
   > 1. Ett dator konto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i den lokala Active Directory-instansen.
   > 2. Dator kontots Kerberos-dekrypterings nyckel delas på ett säkert sätt med Azure AD.
   > 3. Två huvud namn för tjänsten Kerberos (SPN) skapas för att representera två URL: er som används vid inloggning i Azure AD.

6. På sidan **klar att konfigurera** kontrollerar du att kryss rutan **starta synkroniseringen När konfigurationen är klar** är markerad. Välj sedan **Konfigurera**.

   ![Skärm bild som visar knappen Konfigurera på sidan redo att konfigurera](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   När du väljer knappen **Konfigurera konfigureras** sömlös SSO som anges i föregående steg. Konfigurationen av Password hash-synkroniseringen har inte ändrats eftersom den var aktive rad tidigare.

   > [!IMPORTANT]
   > Inga ändringar görs på hur användarna loggar in just nu.

7. I Azure AD-portalen kontrollerar du följande inställningar:
   * **Federation** har angetts till **aktive rad**.
   * **Sömlös enkel inloggning** har angetts till **aktive rad**.
   * **Lösenordssynkronisering** är **aktiverat**.

   ![Skärm bild som visar inställningarna i användar inloggnings avsnittet](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Konvertera domäner från federerade till hanterade

I det här läget är federationen fortfarande aktive rad och fungerar för dina domäner. För att fortsätta med distributionen måste varje domän konverteras från federerade till hanterad för att tvinga användarautentisering via Lösenordssynkronisering.

> [!IMPORTANT]
> Du behöver inte konvertera alla domäner på samma tid. Du kan välja att starta med en test domän på din produktions klient eller börja med din domän som har det lägsta antalet användare.

Slutför konverteringen med hjälp av Azure AD PowerShell-modulen:

1. I PowerShell loggar du in på Azure AD med ett globalt administratörs konto.
2. Om du vill konvertera den första domänen kör du följande kommando:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. I Azure AD-portalen väljer du **Azure Active Directory**  >  **Azure AD Connect**.
4. Kontrol lera att domänen har konverterats till hanterad genom att köra följande kommando:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testning och nästa steg

Utför följande uppgifter för att kontrol lera Lösenordssynkronisering och slutföra konverterings processen.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Testa autentisering med hjälp av lösen ords-hash-synkronisering 

När din klient använder federerade identiteter omdirigerades användarna från inloggnings sidan för Azure AD till din AD FS-miljö. Nu när klienten har kon figurer ATS för att använda Lösenordssynkronisering i stället för federerad autentisering omdirigeras inte användarna till AD FS. I stället loggar användarna in direkt på inloggnings sidan för Azure AD.

Testa hash-synkronisering av lösen ord:

1. Öppna Internet Explorer i InPrivate-läge så att sömlös inloggning inte loggar in automatiskt.
2. Gå till Office 365-inloggnings sidan ( [https://portal.office.com](https://portal.office.com/) ).
3. Ange ett UPN för användare och välj sedan **Nästa**. Se till att du anger UPN för en hybrid användare som har synkroniserats från din lokala Active Directory-instans och som tidigare använde federerad autentisering. En sida där du anger användar namn och lösen ord visas:

   ![Skärm bild som visar inloggnings sidan där du anger ett användar namn](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Skärm bild som visar inloggnings sidan där du anger ett lösen ord](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. När du har angett lösen ordet och väljer **Logga** in omdirigeras du till Office 365-portalen.

   ![Skärm bild som visar Office 365-portalen](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Testa sömlös SSO

1. Logga in på en domänansluten dator som är ansluten till företags nätverket.
2. I Internet Explorer eller Chrome går du till någon av följande URL: er (Ersätt "contoso" med din domän):

   * https: \/ \/ myapps.Microsoft.com/contoso.com
   * https: \/ \/ myapps.Microsoft.com/contoso.onmicrosoft.com

   Användaren omdirigeras en kort stund till inloggnings sidan för Azure AD, som visar meddelandet "försöker logga in dig". Användaren behöver inte ange något användar namn eller lösen ord.<br />

   ![Skärm bild som visar inloggnings sidan för Azure AD och meddelandet](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
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

### <a name="troubleshooting"></a>Felsökning

Support teamet bör förstå hur man felsöker autentiseringsproblem som uppstår under eller efter ändringen från federationen till hanterad. Använd följande fel söknings dokumentation för att hjälpa Support teamet att bekanta sig med de vanliga fel söknings stegen och lämpliga åtgärder som kan hjälpa till att isolera och lösa problemet.

[Felsöka Azure Active Directory hash-synkronisering av lösen ord](./tshoot-connect-password-hash-synchronization.md)

[Felsöka Azure Active Directory sömlös enkel inloggning](./tshoot-connect-sso.md)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rulla över sömlös SSO Kerberos-dekrypterings nyckel

Det är viktigt att ofta rulla över Kerberos-dekrypteringsnyckeln för AZUREADSSOACC-dator kontot (som representerar Azure AD). Dator kontot AZUREADSSOACC skapas i din lokala Active Directory skog. Vi rekommenderar starkt att du går igenom Kerberos-dekrypteringsnyckeln minst var 30: e dag för att anpassas till det sätt som Active Directory domän medlemmar skickar lösen ords ändringar. Det finns ingen kopplad enhet kopplad till AZUREADSSOACC-datorns konto objekt, så du måste utföra förnyelsen manuellt.

Initiera förnyelsen av den sömlösa Kerberos-krypteringsnyckeln på den lokala server som kör Azure AD Connect.

Mer information finns i [Hur gör jag för att över Kerberos-dekrypteringsnyckeln för dator kontot för AZUREADSSOACC?](./how-to-connect-sso-faq.md).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att [Azure AD Connect design koncept](plan-connect-design-concepts.md).
* Välj [rätt autentisering](./choose-ad-authn.md).
* Lär dig mer om [topologier som stöds](plan-connect-design-concepts.md).
