---
title: Distributions överväganden för Azure AD Multi-Factor Authentication
description: Lär dig mer om distributions överväganden och strategin för lyckad implementering av Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ee81abd7cd0268a7cbd6b16aa6065ec7b54bef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861314"
---
# <a name="plan-an-azure-ad-multi-factor-authentication-deployment"></a>Planera en Azure AD Multi-Factor Authentication-distribution

Personer ansluter till organisations resurser i allt större komplicerade scenarier. Människor ansluter till och från organisationer som ägs, personliga och offentliga enheter på företags nätverket med hjälp av telefoner, surfplattor, datorer och bärbara datorer, ofta på flera plattformar. I den här enheten för alltid ansluten, flera enheter och flera plattformar är säkerheten för användar konton viktigare än någonsin. Lösen ord, oavsett om de är komplicerade, som används på enheter, nätverk och plattformar är inte längre tillräckliga för att säkerställa användar kontots säkerhet, särskilt när användare tenderar att återanvända lösen ord över konton. Sofistikerade nätfiske och andra sociala teknik attacker kan leda till att användar namn och lösen ord publiceras och säljs på den mörka webben.

[Azure AD Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) hjälper till att skydda åtkomsten till data och program. Det ger ett extra säkerhets lager med hjälp av en andra form av autentisering. Organisationer kan använda [villkorlig åtkomst](../conditional-access/overview.md) för att se till att lösningen passar deras speciella behov.

Den här distributions guiden visar hur du planerar och testar en distribution av Azure AD-Multi-Factor Authentication.

Om du snabbt vill se Azure AD Multi-Factor Authentication i praktiken och återgå till att förstå ytterligare distributions överväganden:

> [!div class="nextstepaction"]
> [Aktivera Azure AD-multifaktorautentisering](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar en distribution av Azure AD Multi-Factor Authentication finns det nödvändiga komponenter som bör övervägas.

| Scenario | Förutsättning |
| --- | --- |
| Identitets miljö med **enbart moln** med modern autentisering | **Inga ytterligare nödvändiga aktiviteter** |
| **Hybrid** identitets scenarier | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) distribueras och användar identiteter synkroniseras eller federerade med den lokala Active Directory Domain Services med Azure Active Directory. |
| Lokala äldre program som publicerats för moln åtkomst | Azure AD [Application Proxy](../manage-apps/application-proxy.md) har distribuerats. |
| Använda Azure AD MFA med RADIUS-autentisering | En [nätverks princip Server (NPS)](howto-mfa-nps-extension.md) har distribuerats. |
| Användare har Microsoft Office 2010 eller tidigare, eller Apple Mail för iOS 11 eller tidigare | Uppgradera till [Microsoft Office 2013 eller senare](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) och Apple Mail för iOS 12 eller senare. Villkorlig åtkomst stöds inte av bakåtkompatibla autentiseringsprotokoll. |

## <a name="plan-user-rollout"></a>Planera användar lansering

Din plan för MFA-distribution ska innehålla en pilot distribution som följs av distributions vågor som ligger inom support kapaciteten. Börja distributionen genom att tillämpa dina principer för villkorlig åtkomst till en liten grupp pilot användare. När du har utvärderat effekterna på funktionerna för pilot användare, process användning och registrering kan du antingen lägga till fler grupper i principen eller lägga till fler användare i de befintliga grupperna.

### <a name="user-communications"></a>Användar kommunikation

Det är viktigt att informera användarna, i planerad kommunikation, om kommande ändringar, Azure AD MFA-registrerings krav och eventuella nödvändiga användar åtgärder. Vi rekommenderar att kommunikationen utvecklas i samförstånd med representanter från din organisation, till exempel en kommunikation, en ändrings hanterings avdelning eller personalavdelningen.

Microsoft tillhandahåller [kommunikations mallar](https://aka.ms/mfatemplates) och [dokumentation för slutanvändare](../user-help/security-info-setup-signin.md) för att hjälpa till att formulera kommunikationen. Du kan skicka användare till [https://myprofile.microsoft.com](https://myprofile.microsoft.com) att registrera direkt genom att välja länkarna för **säkerhets information** på sidan.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Azure AD Multi-Factor Authentication distribueras genom att tvinga principer med villkorlig åtkomst. En princip för villkorlig åtkomst kan kräva att användare utför Multi-Factor Authentication när vissa villkor uppfylls, t. ex.:

* Alla användare, en speciell användare, medlem i en grupp eller tilldelad roll
* Specifika moln program som ska användas
* Enhetsplattform
* Enhetens tillstånd
* Nätverks plats eller geo-lokaliserad IP-adress
* Klientprogram
* Inloggnings risk (kräver identitets skydd)
* Kompatibel enhet
* Hybrid Azure AD-ansluten enhet
* Godkänt klient program

Använd anpassningsbara affischer och e-postmallar i distributions [material för Multi-Factor Authentication](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) för att distribuera Multi-Factor Authentication till din organisation.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Aktivera Multi-Factor Authentication med villkorlig åtkomst

Principer för villkorlig åtkomst framtvingar registrering, vilket kräver att användare som är oregistrerade att slutföra registreringen vid första inloggning, en viktig säkerhets åtgärd.

[Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) bidrar både till en registrerings princip för och automatiserade identifierings-och reparations principer till Azure AD Multi-Factor Authentication-artikeln. Principer kan skapas för att tvinga lösen ords ändringar när det finns ett hot mot komprometterad identitet eller kräver MFA när en inloggning bedöms vara riskfylld av följande [händelser](../identity-protection/overview-identity-protection.md):

* Läckta autentiseringsuppgifter
* Inloggningar från anonyma IP-adresser
* Omöjliga resor till ovanliga platser
* Inloggningar från okända platser
* Inloggningar från angripna enheter
* Inloggningar från IP-adresser med misstänkta aktiviteter

Några av de risker som identifieras av Azure Active Directory Identity Protection uppstår i real tid och vissa kräver offline-bearbetning. Administratörer kan välja att blockera användare som har uppvisat riskfyllda beteenden och reparera manuellt, kräva en lösen ords ändring eller kräva Multi-Factor Authentication som en del av principerna för villkorlig åtkomst.

## <a name="define-network-locations"></a>Definiera nätverks platser

Vi rekommenderar att organisationer använder villkorlig åtkomst för att definiera deras nätverk med [namngivna platser](../conditional-access/location-condition.md#named-locations). Om din organisation använder identitets skydd bör du överväga att använda riskfyllda principer i stället för namngivna platser.

### <a name="configuring-a-named-location"></a>Konfigurera en namngiven plats

1. Öppna **Azure Active Directory** i Azure Portal
2. Välj **säkerhet**
3. Under **Hantera** väljer du **namngivna platser**
4. Välj **ny plats**
5. I fältet **namn** anger du ett beskrivande namn
6. Välj om du definierar platsen med hjälp av *IP-intervall* eller *länder/regioner*
   1. Om du använder *IP-intervall*
      1. Bestäm om du vill *Markera som betrodd plats*. Inloggning från en betrodd plats minskar en användares inloggningsrisk. Markera bara den här platsen som betrodd om du vet att de angivna IP-intervallen är etablerade och trovärdiga i din organisation.
      2. Ange IP-intervall
   2. Om du använder *länder/regioner*
      1. Expandera den nedrullningsbara menyn och välj de länder eller regioner som du vill definiera för den här namngivna platsen.
      2. Bestäm om du vill *Inkludera okända områden*. Okända områden är IP-adresser som inte kan mappas till ett land/en region.
7. Välj **Skapa**

## <a name="plan-authentication-methods"></a>Planera autentiseringsmetoder

Administratörer kan välja de [autentiseringsmetoder](../authentication/concept-authentication-methods.md) som de vill göra tillgängliga för användare. Det är viktigt att tillåta mer än en enda autentiseringsmetod så att användare har en säkerhets kopierings metod som är tillgänglig om deras primära metod inte är tillgänglig. Följande metoder är tillgängliga för administratörer att aktivera:

> [!TIP]
> Microsoft rekommenderar att du använder Microsoft Authenticator (mobilappen) som primär metod för Azure AD Multi-Factor Authentication för en säkrare och bättre användar upplevelse. Microsoft Authenticator-appen [uppfyller](https://azure.microsoft.com/resources/microsoft-nist/) även National Institute of Standards och Technology Authentication Assurance-nivåerna. 

### <a name="notification-through-mobile-app"></a>Meddelande via mobilapp

Ett push-meddelande skickas till Microsoft Authenticator-appen på din mobila enhet. Användaren visar meddelandet och väljer **Godkänn** för att slutföra verifieringen. Push-meddelanden via en mobilapp ger användarna minst påträngande-alternativ. De är också det mest pålitliga och säkra alternativet eftersom de använder en data anslutning i stället för telefoni.

> [!NOTE]
> Om din organisation har personal som arbetar i eller reser till Kina, fungerar inte **meddelandet via mobilappen** på **Android-enheter** i landet/regionen. Alternativa metoder bör göras tillgängliga för dessa användare.

### <a name="verification-code-from-mobile-app"></a>Verifierings kod från mobilapp

En mobilapp som Microsoft Authenticator-appen genererar en ny OATH-verifierings kod var 30: e sekund. Användaren anger verifierings koden i inloggnings gränssnittet. Alternativet mobil app kan användas om telefonen har en data-eller mobilen signal.

### <a name="call-to-phone"></a>Ring till telefon

Ett automatiskt röst samtal placeras till användaren. Användaren svarar på anropet och trycker **#** på telefon tangent bordet för att godkänna deras autentisering. Samtal till telefon är en bra säkerhets kopierings metod för meddelande-eller verifierings kod från en mobilapp.

### <a name="text-message-to-phone"></a>Textmeddelande till telefon

Ett textmeddelande som innehåller en verifierings kod skickas till användaren, användaren uppmanas att ange verifierings koden i inloggnings gränssnittet.

### <a name="choose-verification-options"></a>Välj verifierings alternativ

1. Bläddra till **Azure Active Directory**, **användare** **Multi-Factor Authentication**.

   ![Öppna Multi-Factor Authentication portal från Azure AD-bladet i Azure Portal](media/howto-mfa-getstarted/users-mfa.png)

1. På den nya fliken som öppnas Bläddra till **tjänst inställningar**.
1. Under **verifierings alternativ** markerar du alla rutor för metoder som är tillgängliga för användare.

   ![Konfigurera verifierings metoder på fliken Multi-Factor Authentication tjänst inställningar](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Klicka på **Spara**.
1. Stäng fliken **tjänst inställningar** .

## <a name="plan-registration-policy"></a>Planera registrerings princip

Administratörer måste bestämma hur användarna ska registrera sina metoder. Organisationer bör [Aktivera den nya kombinerade registrerings upplevelsen](howto-registration-mfa-sspr-combined.md) för Azure AD MFA och lösen ords återställning via självbetjäning (SSPR). SSPR tillåter användare att återställa sina lösen ord på ett säkert sätt med samma metoder som de använder för Multi-Factor Authentication. Vi rekommenderar den här kombinerade registreringen eftersom det är en bra upplevelse för användarna, med möjlighet att registrera sig en gång för båda tjänsterna. Om du aktiverar samma metoder för SSPR och Azure AD MFA kan användarna registreras för att använda båda funktionerna.

### <a name="registration-with-identity-protection"></a>Registrering med identitets skydd

Om din organisation använder Azure Active Directory Identity Protection [konfigurerar du principen för MFA-registrering](../identity-protection/howto-identity-protection-configure-mfa-policy.md) så att användarna kan registrera sig nästa gången de loggar in interaktivt.

### <a name="registration-without-identity-protection"></a>Registrering utan identitets skydd

Om din organisation inte har licenser som aktiverar identitets skydd uppmanas användarna att registrera sig nästa gången MFA krävs vid inloggningen. Användare får inte registreras för MFA om de inte använder program som skyddas med MFA. Det är viktigt att du får alla användare registrerade så att felaktiga aktörer inte kan gissa lösen ordet för en användare och registrera sig för MFA för deras räkning, vilket effektivt tar kontroll över kontot.

#### <a name="enforcing-registration"></a>Verkställa registrering

Med hjälp av följande steg kan en princip för villkorlig åtkomst tvinga användare att registrera sig för Multi-Factor Authentication

1. Skapa en grupp, Lägg till alla användare som inte är registrerade för tillfället.
2. Använd villkorlig åtkomst för att genomdriva Multi-Factor Authentication för den här gruppen för åtkomst till alla resurser.
3. Utvärdera grupp medlemskapet med jämna mellanrum och ta bort användare som har registrerat sig från gruppen.

Du kan identifiera registrerade och icke-registrerade Azure AD MFA-användare med PowerShell-kommandon som är beroende av [MSOnline PowerShell-modulen](/powershell/azure/active-directory/install-msonlinev1).

#### <a name="identify-registered-users"></a>Identifiera registrerade användare

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identifiera icke-registrerade användare

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konvertera användare från per användare MFA till villkorlig åtkomst baserat MFA

Om dina användare har Aktiver ATS med alternativet per användare aktiverat och tillämpade Azure AD Multi-Factor Authentication följande PowerShell kan hjälpa dig att göra konverteringen till den villkorliga åtkomstbaserade Azure AD-Multi-Factor Authentication.

Kör PowerShell i ett ISE-fönster eller Spara som en `.PS1` fil för att köra lokalt.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Vi ändrade nyligen beteendet och PowerShell-skriptet ovan enligt detta. Tidigare har skriptet sparat av MFA-metoderna, inaktiverat MFA och återställt metoderna. Detta behövs inte längre nu när standard beteendet för inaktivera inte tar bort metoderna.

## <a name="plan-conditional-access-policies"></a>Planera principer för villkorlig åtkomst

För att planera strategin för principer för villkorlig åtkomst som avgör när MFA och andra kontroller krävs, se [vanliga principer för villkorlig åtkomst](../conditional-access/concept-conditional-access-policy-common.md).

Det är viktigt att du förhindrar oavsiktligt låst av din Azure AD-klient. Du kan minska effekten av denna oavsiktliga brist på administrativ åtkomst genom att [skapa två eller fler konton för nöd åtkomst i din klient organisation](../roles/security-emergency-access.md) och utesluta dem från din princip för villkorlig åtkomst.

### <a name="create-conditional-access-policy"></a>Skapa princip för villkorlig åtkomst

1. Logga in på [Azure Portal](https://portal.azure.com) med ett globalt administratörs konto.
1. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
1. Välj **ny princip**.
   ![Skapa en princip för villkorlig åtkomst för att aktivera MFA för Azure Portal användare i pilot gruppen](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Ange ett beskrivande namn för principen.
1. Under **användare och grupper**:
   * På fliken **Inkludera** väljer du alternativ knappen **alla användare**
   * Markera kryss rutan för **användare och grupper** på fliken **exkludera** och välj sedan återställnings konton.
   * Klicka på **Klar**.
1. Under **molnappar** väljer du alternativ knappen **alla molnappar** .
   * Alternativt: på fliken **exkludera** väljer du de molnappar som din organisation inte behöver MFA för.
   * Klicka på **Klar**.
1. Avsnittet **villkor** :
   * Du kan också välja att utvärdera inloggnings risker som en del av principen om du har aktiverat Azure Identity Protection.
   * Om du har konfigurerat betrodda platser eller namngivna platser kan du ange att de platserna ska tas med eller undantas från principen.
1. Under **bevilja**, se till att alternativ knappen **bevilja åtkomst** är markerad.
    * Markera kryss rutan för att **kräva Multi-Factor Authentication**.
    * Klicka på **Välj**.
1. Hoppa över avsnittet **session** .
1. Ange **aktiverings principen** växla till **på**.
1. Klicka på **Skapa**.

## <a name="plan-integration-with-on-premises-systems"></a>Planera integrering med lokala system

Vissa äldre program och lokala program som inte autentiserar direkt mot Azure AD kräver ytterligare åtgärder för att använda MFA, inklusive:

* Äldre lokala program som behöver använda programproxyn.
* Lokala RADIUS-program som kommer att behöva använda MFA adapter med NPS-servern.
* Lokala AD FS program som behöver använda MFA adapter med AD FS 2016 eller senare.

Program som autentiserar direkt med Azure AD och har modern autentisering (WS-utfodras, SAML, OAuth, OpenID Connect) kan använda principer för villkorlig åtkomst direkt.

### <a name="use-azure-ad-mfa-with-azure-ad-application-proxy"></a>Använda Azure AD MFA med Azure AD-programproxy

Program som finns lokalt kan publiceras till din Azure AD-klient via [azure AD-programproxy](../manage-apps/application-proxy.md) och kan dra nytta av azure AD Multi-Factor Authentication om de är konfigurerade för att använda förautentisering i Azure AD.

De här programmen omfattas av principer för villkorlig åtkomst som tvingar Azure AD Multi-Factor Authentication, precis som andra Azure AD-integrerade program.

På samma sätt, om Azure AD Multi-Factor Authentication tillämpas för alla användar inloggningar, kommer lokala program som publiceras med Azure AD-programproxy att skyddas.

### <a name="integrating-azure-ad-multi-factor-authentication-with-network-policy-server"></a>Integrera Azure AD Multi-Factor Authentication med nätverks princip Server

Nätverks princip Server (NPS)-tillägget för Azure AD MFA lägger till molnbaserade MFA-funktioner i din infrastruktur för autentisering med hjälp av befintliga servrar. Med NPS-tillägget kan du lägga till telefonsamtal, textmeddelande eller standardapp för ditt befintliga autentiseringspaket. Denna integrering har följande begränsningar:

* Med ett CHAPv2-protokoll stöds endast push-meddelanden för Authenticator-appar och röst samtal.
* Det går inte att tillämpa principer för villkorlig åtkomst.

NPS-tillägget fungerar som ett kort mellan RADIUS och molnbaserad Azure AD MFA för att tillhandahålla en andra faktor för autentisering för att skydda [VPN](howto-mfa-nps-extension-vpn.md), [anslutning till fjärr skrivbords-Gateway](howto-mfa-nps-extension-rdg.md)eller andra RADIUS-kompatibla program. Användare som registrerar sig för Azure AD MFA i den här miljön kommer att anropas för alla autentiseringsförsök, bristen på principer för villkorlig åtkomst innebär att MFA alltid krävs.

#### <a name="implementing-your-nps-server"></a>Implementera NPS-servern

Om du har en distribuerad NPS-instans och redan använder, [integrerar du din befintliga NPS-infrastruktur med Azure AD Multi-Factor Authentication](howto-mfa-nps-extension.md). Om du konfigurerar NPS för första gången, se [nätverks Policy Server (NPS)](/windows-server/networking/technologies/nps/nps-top) för instruktioner. Fel söknings vägledning hittar du i artikeln [lösa fel meddelanden från NPS-tillägget för Azure AD Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Förbered NPS för användare som inte har registrerats för MFA

Välj vad som ska hända när användare som inte är registrerade med MFA försöker autentisera sig. Använd register inställningen `REQUIRE_USER_MATCH` i register Sök vägen `HKLM\Software\Microsoft\AzureMFA` för att styra funktions sättet. Den här inställningen har ett enda konfigurations alternativ.

| Tangent | Värde | Standardvärde |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE/FALSE | Inte angivet (motsvarar sant) |

Syftet med den här inställningen är att fastställa vad som ska göras när en användare inte är registrerad för MFA. Effekterna av att ändra den här inställningen visas i tabellen nedan.

| Inställningar | MFA-status för användare | Animeringseffekt |
| --- | --- | --- |
| Nyckeln finns inte | Inte registrerad | MFA-utmaningen lyckades inte |
| Värdet är inställt på Sant/ej angivet | Inte registrerad | MFA-utmaningen lyckades inte |
| Nyckeln har angetts till false | Inte registrerad | Autentisering utan MFA |
| Nyckeln har angetts till false eller True | Registrerad | Måste autentisera med MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integrera med Active Directory Federation Services (AD FS)

Om din organisation är federerad med Azure AD kan du använda [Azure ad Multi-Factor Authentication för att skydda AD FS resurser](multi-factor-authentication-get-started-adfs.md), både lokalt och i molnet. Med Azure AD MFA kan du minska lösen ord och tillhandahålla ett säkrare sätt att autentisera. Från och med Windows Server 2016 kan du nu konfigurera Azure AD MFA för primär autentisering.

Till skillnad från AD FS i Windows Server 2012 R2 integrerar AD FS 2016 Azure AD MFA adapter direkt med Azure AD och kräver inte en lokal Azure MFA-Server. Azure AD MFA-adaptern är inbyggd i Windows Server 2016 och det finns inget behov av en ytterligare installation.

När du använder Azure AD MFA med AD FS 2016 och mål programmet omfattas av principen för villkorlig åtkomst finns det ytterligare överväganden:

* Villkorlig åtkomst är tillgängligt när programmet är en förlitande part till Azure AD, federerad med AD FS 2016 eller senare.
* Villkorlig åtkomst är inte tillgängligt när programmet är en förlitande part till AD FS 2016 eller AD FS 2019 och hanteras eller sammanslags med AD FS 2016 eller AD FS 2019.
* Villkorlig åtkomst är inte heller tillgängligt när AD FS 2016 eller AD FS 2019 har kon figurer ATS för att använda Azure AD MFA som primär autentiseringsmetod.

#### <a name="ad-fs-logging"></a>AD FS loggning

Standard AD FS 2016-och 2019-loggning i både Windows-säkerhetsloggen och AD FS admin-loggen innehåller information om autentiseringsbegäranden och deras framgångar eller haverier. Händelse logg data i dessa händelser anger om Azure AD MFA användes. Till exempel kan en AD FS granskning av händelse-ID 1200 innehålla:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Förnya och hantera certifikat

På varje AD FS server finns det ett självsignerat Azure AD MFA-certifikat med titeln OU = Microsoft AD FS Azure MFA som innehåller certifikatets förfallo datum i den lokala datorns mitt arkiv. Kontrol lera giltighets perioden för det här certifikatet på varje AD FS server för att fastställa förfallo datumet.

Om giltighets tiden för dina certifikat snart upphör att gälla, [genererar och verifierar du ett nytt MFA-certifikat på varje AD FS server](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Följande anvisningar beskriver hur du hanterar Azure AD MFA-certifikat på dina AD FS-servrar. När du konfigurerar AD FS med Azure AD MFA, är de certifikat som genereras via `New-AdfsAzureMfaTenantCertificate` PowerShell-cmdleten giltiga i två år. Förnya och installera de förnyade certifikaten innan de upphör att gälla för ovoid-avbrott i MFA-tjänsten.

## <a name="implement-your-plan"></a>Implementera din plan

Nu när du har planerat din lösning kan du implementera genom att följa stegen nedan:

1. Uppfylla alla nödvändiga komponenter
   1. Distribuera [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) för alla hybrid scenarier
   1. Distribuera [Azure AD-programproxy](../manage-apps/application-proxy.md) för i alla lokala appar som publicerats för moln åtkomst
   1. Distribuera [NPS](/windows-server/networking/technologies/nps/nps-top) för alla RADIUS-autentiseringar
   1. Se till att användarna har uppgraderat till stödda versioner av Microsoft Office med modern autentisering aktive rad
1. Konfigurera valda [autentiseringsmetoder](#choose-verification-options)
1. Definiera dina [namngivna nätverks platser](../conditional-access/location-condition.md#named-locations)
1. Välj grupper för att börja distribuera MFA.
1. Konfigurera [principer för villkorlig åtkomst](#create-conditional-access-policy)
1. Konfigurera din princip för MFA-registrering
   1. [Kombinera MFA och SSPR](howto-registration-mfa-sspr-combined.md)
   1. Med [identitets skydd](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
1. Skicka användar kommunikation och få användare att registrera sig [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Håll koll på vem som har registrerats](#identify-non-registered-users)

> [!TIP]
> Statliga moln användare kan registrera sig på [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Hantera din lösning

Rapporter för Azure AD MFA

Azure AD Multi-Factor Authentication ger rapporter via Azure Portal:

| Rapport | Location | Beskrivning |
| --- | --- | --- |
| Användnings-och bedrägeri varningar | Inloggnings program för Azure AD > | Innehåller information om allmän användning, användar Sammanfattning och användar information. samt en historik över bedrägeri aviseringar som skickats under det angivna datum intervallet. |

## <a name="troubleshoot-mfa-issues"></a>Felsök MFA-problem

Hitta lösningar på vanliga problem med Azure AD MFA i [artikeln om fel sökning av Azure ad Multi-Factor Authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) i Microsoft Support Center.

## <a name="next-steps"></a>Nästa steg

Om du vill se hur Azure AD Multi-Factor Authentication i praktiken, slutför du följande självstudie:

> [!div class="nextstepaction"]
> [Aktivera Azure AD-multifaktorautentisering](tutorial-enable-azure-mfa.md)
