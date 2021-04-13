---
title: Direkt federation med en identitetsprovider för B2B – Azure AD
description: Federera direkt med en SAML- eller WS-Fed-identitetsprovider så att gäster kan logga in på dina Azure AD-appar
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 830119a5b3a7781e8b12e3d4df870f539a2cd63a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364914"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Direkt federation med AD FS och tredjepartsleverantörer för gästanvändare (förhandsversion)

> [!NOTE]
>  Direktfederation är en funktion i den offentliga förhandsversionen Azure Active Directory. Mer information om förhandsversioner finns i [Kompletterande användningsvillkor för Microsoft Azure förhandsversioner.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Den här artikeln beskriver hur du ställer in direkt federation med en annan organisation för B2B-samarbete. Du kan konfigurera direkt federation med alla organisationer vars identitetsprovider (IdP) stöder SAML 2.0 eller WS-Fed-protokollet.
När du ställer in direkt federation med en partners IdP kan nya gästanvändare från den domänen använda sitt eget IdP-hanterade organisationskonto för att logga in på din Azure AD-klient och börja samarbeta med dig. Gästanvändaren behöver inte skapa ett separat Azure AD-konto.

## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>När autentiseras en gästanvändare med direkt federation?
När du har ställt in direkt federation med en organisation autentiseras alla nya gästanvändare som du bjuder in med direkt federation. Observera att inställningen av direkt federation inte ändrar autentiseringsmetoden för gästanvändare som redan har löst in en inbjudan från dig. Här är några exempel:
 - Om gästanvändare redan har löst in inbjudningar från dig, och du senare har ställt in direkt federation med organisationen, fortsätter dessa gästanvändare att använda samma autentiseringsmetod som de använde innan du konfigurerade direkt federation.
 - Om du ställer in direkt federation med en partnerorganisation och bjuder in gästanvändare, och sedan partnerorganisationen senare flyttar till Azure AD, fortsätter gästanvändare som redan har löst in inbjudningar att använda direkt federation, så länge den direkta federationsprincipen i din klientorganisation finns.
 - Om du tar bort direkt federation med en partnerorganisation kan gästanvändare som för närvarande använder direkt federation inte logga in.

I något av dessa scenarier kan du uppdatera en gästanvändares autentiseringsmetod genom att återställa deras [inlösningsstatus](reset-redemption-status.md).

Direkt federation är kopplad till domännamnsrymder, till exempel contoso.com och fabrikam.com. När en direkt federationskonfiguration upprättas med AD FS eller en IdP från tredje part, associerar organisationer en eller flera domännamnsrymder med dessa IP:er. 

## <a name="end-user-experience"></a>Slutanvändarens upplevelse 
Med direkt federation loggar gästanvändare in på din Azure AD-klientorganisation med sitt eget organisationskonto. När de använder delade resurser och uppmanas att logga in omdirigeras direkt federationsanvändare till sin IdP. Efter en lyckad inloggning returneras de till Azure AD för att få åtkomst till resurser. Direkt federationsanvändares uppdateringstoken är giltiga i 12 timmar, [standardlängden för uppdateringstoken för genomströmning](../develop/active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties) i Azure AD. Om den federerade IdP:n har enkel inloggning aktiverat får användaren enkel inloggning och ser ingen inloggningsuppfråga efter den första autentiseringen.

## <a name="sign-in-endpoints"></a>Inloggningsslutpunkter

Direkt federationsgästanvändare kan nu logga in på dina appar för flera klienter eller Microsofts förstapartsappar med hjälp av en gemensam slutpunkt [(det](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) vill säga en allmän app-URL som inte innehåller din klientkontext). Under inloggningsprocessen väljer gästanvändaren Inloggningsalternativ **och** väljer sedan **Logga in på en organisation.** Användaren skriver sedan namnet på din organisation och fortsätter att logga in med sina egna autentiseringsuppgifter.

Direkt federation gästanvändare kan också använda programslutpunkter som innehåller din klientinformation, till exempel:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Du kan också ge direktfederation gästanvändare en direktlänk till ett program eller en resurs genom att inkludera din klientinformation, till exempel `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="limitations"></a>Begränsningar

### <a name="dns-verified-domains-in-azure-ad"></a>DNS-verifierade domäner i Azure AD
Den domän som du vill federera med får ***inte vara*** DNS-verifierad i Azure AD. Du kan konfigurera direkt federation med ohanterade (e-postverifierade eller "virala") Azure AD-klienter eftersom de inte är DNS-verifierade.

### <a name="authentication-url"></a>Autentiserings-URL
Direkt federation tillåts endast för principer där autentiserings-URL:ens domän matchar måldomänen, eller där autentiserings-URL:en är en av dessa tillåtna identitetsproviders (listan kan komma att ändras):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

När du till exempel ställer in direkt federation **för fabrikam.com**, kommer autentiserings-URL:en att `https://fabrikam.com/adfs` klara verifieringen. En värd i samma domän kommer också att skicka, till exempel `https://sts.fabrikam.com/adfs` . Autentiserings-URL:en `https://fabrikamconglomerate.com/adfs` eller för samma domän kommer dock inte att `https://fabrikam.com.uk/adfs` överföras.

### <a name="signing-certificate-renewal"></a>Förnyelse av signeringscertifikat
Om du anger metadata-URL:en i inställningarna för identitetsprovidern förnyar Azure AD automatiskt signeringscertifikatet när det upphör att gälla. Men om certifikatet roteras av någon anledning före förfallotiden, eller om du inte anger en metadata-URL, kan Azure AD inte förnya det. I det här fallet måste du uppdatera signeringscertifikatet manuellt.

### <a name="limit-on-federation-relationships"></a>Gräns för federationsrelationer
För närvarande stöds högst 1 000 federationsrelationer. Den här gränsen omfattar [både interna federationer](/powershell/module/msonline/set-msoldomainfederationsettings) och direkta federationer.

### <a name="limit-on-multiple-domains"></a>Gräns för flera domäner
Vi stöder för närvarande inte direkt federation med flera domäner från samma klientorganisation.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Kan jag konfigurera direkt federation med en domän där det finns en ohanterad (e-postverifierad) klient? 
Ja. Om domänen inte har verifierats och klientorganisationen inte har genomgått ett administratörs övertagande [kan](../enterprise-users/domains-admin-takeover.md)du konfigurera direkt federation med den domänen. Ohanterade eller e-postverifierade klienter skapas när en användare löser in en B2B-inbjudan eller utför en självbetjäningsregistrering för Azure AD med hjälp av en domän som för närvarande inte finns. Du kan konfigurera direkt federation med dessa domäner. Om du försöker konfigurera direkt federation med en DNS-verifierad domän, antingen i Azure Portal eller via PowerShell, visas ett fel.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Vilken metod har företräde om autentisering med direkt federation och e-post med ett lösenord är aktiverat?
När direkt federation upprättas med en partnerorganisation har den företräde framför autentisering med e-postkod vid en tidpunkt för nya gästanvändare från den organisationen. Om en gästanvändare löste in en inbjudan med hjälp av autentisering med ett lösenord innan du konfigurerade direkt federation fortsätter de att använda autentisering med ett lösenord. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Åtgärdar direkt federation inloggningsproblem på grund av en delvis synkroniserad innehavare?
Nej, funktionen [för e-post med ett lösenord](one-time-passcode.md) ska användas i det här scenariot. Ett "delvis synkroniserat innehav" avser en Azure AD-partnerklientorganisation där lokala användaridentiteter inte är helt synkroniserade till molnet. En gäst vars identitet ännu inte finns i molnet men som försöker lösa in din B2B-inbjudan kan inte logga in. Funktionen för ett lösenord gör att den här gästen kan logga in. Direktfederationsfunktionen åtgärdar scenarier där gästen har sitt eget IdP-hanterade organisationskonto, men organisationen inte har någon Azure AD-närvaro alls.
### <a name="once-direct-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>När direktfederation har konfigurerats med en organisation, måste varje gäst skickas och lösa in en enskild inbjudan?
Att konfigurera direkt federation ändrar inte autentiseringsmetoden för gästanvändare som redan har löst in en inbjudan från dig. Du kan uppdatera en gästanvändares autentiseringsmetod genom att [återställa inlösningsstatusen](reset-redemption-status.md).
## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Steg 1: Konfigurera partnerorganisationens identitetsprovider
Först måste din partnerorganisation konfigurera sin identitetsprovider med nödvändiga anspråk och förtroenden för förlitande part. 

> [!NOTE]
> För att illustrera hur du konfigurerar en identitetsprovider för direkt federation använder vi Active Directory Federation Services (AD FS) (AD FS) som exempel. Se artikeln [Konfigurera direkt federation med AD FS](direct-federation-adfs.md), som ger exempel på hur du konfigurerar AD FS som en SAML 2.0- eller WS-Fed-identitetsprovider som förberedelse för direkt federation.

### <a name="saml-20-configuration"></a>SAML 2.0-konfiguration

Azure AD B2B kan konfigureras för att federera med identitetsproviders som använder SAML-protokollet med specifika krav som anges nedan. Mer information om att konfigurera ett förtroende mellan din SAML-identitetsprovider och Azure AD finns i Använda  [en SAML 2.0-identitetsprovider (IdP)](../hybrid/how-to-connect-fed-saml-idp.md)för enkel inloggning.  

> [!NOTE]
> Måldomänen för direkt federation får inte vara DNS-verifierad i Azure AD. Autentiserings-URL-domänen måste matcha måldomänen eller vara domänen för en tillåten identitetsprovider. Mer information [finns](#limitations) i avsnittet Begränsningar. 

#### <a name="required-saml-20-attributes-and-claims"></a>Saml 2.0-attribut och -anspråk som krävs
Följande tabeller visar krav för specifika attribut och anspråk som måste konfigureras hos tredjeparts-identitetsprovidern. För att konfigurera direkt federation måste följande attribut tas emot i SAML 2.0-svaret från identitetsprovidern. Dessa attribut kan konfigureras genom att länka till XML-filen för onlinesäkerhetstokentjänsten eller genom att ange dem manuellt.

Obligatoriska attribut för SAML 2.0-svaret från IdP:n:

|Attribut  |Värde  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Utfärdarens URI för partner-IdP, till exempel `http://www.example.com/exk10l6w90DHM0yi...`         |


Obligatoriska anspråk för SAML 2.0-token som utfärdats av IdP:n:

|Attribut  |Värde  |
|---------|---------|
|NameID-format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed konfiguration 
Azure AD B2B kan konfigureras för att federera med identitetsproviders som använder WS-Fed-protokollet med vissa specifika krav som anges nedan. För närvarande har de WS-Fed leverantörerna testats för kompatibilitet med Azure AD, inklusive AD FS och Shibboleth. Mer information om att upprätta ett förtroende för förlitande part mellan en WS-Fed-kompatibel provider med Azure AD finns i "STS-integrationsdokument med WS-protokoll" som finns i Kompatibilitetsdokument för [Azure AD-identitetsprovider.](https://www.microsoft.com/download/details.aspx?id=56843)

> [!NOTE]
> Måldomänen för direkt federation får inte vara DNS-verifierad i Azure AD. Autentiserings-URL-domänen måste matcha antingen måldomänen eller domänen för en tillåten identitetsprovider. Mer information [finns](#limitations) i avsnittet Begränsningar. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Obligatoriska WS-Fed attribut och anspråk

Följande tabeller visar krav för specifika attribut och anspråk som måste konfigureras hos tredjepartsleverantörer WS-Fed identitetsprovidern. För att du ska kunna konfigurera direkt federation måste följande attribut tas emot WS-Fed meddelandet från identitetsprovidern. Dessa attribut kan konfigureras genom att länka till XML-filen för onlinesäkerhetstokentjänsten eller genom att ange dem manuellt.

Obligatoriska attribut i WS-Fed från IdP:n:
 
|Attribut  |Värde  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Utfärdar-URI för partner-IdP, till exempel `http://www.example.com/exk10l6w90DHM0yi...`         |

Obligatoriska anspråk för den WS-Fed token som utfärdats av IdP:n:

|Attribut  |Värde  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Steg 2: Konfigurera direkt federation i Azure AD 
Därefter konfigurerar du federation med identitetsprovidern som konfigurerades i steg 1 i Azure AD. Du kan använda antingen Azure AD-portalen eller PowerShell. Det kan ta 5–10 minuter innan direktfederationsprincipen börjar gälla. Under den här tiden ska du inte försöka lösa in en inbjudan till den direkta federationsdomänen. Följande attribut krävs:
- Utfärdar-URI för partner-IdP
- Passiv autentiseringsslutpunkt för partner-IdP (endast https stöds)
- Certifikat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Så här konfigurerar du direkt federation i Azure AD-portalen

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **External Identities Alla**  >  **identitetsproviders.**
3. Välj **Ny SAML/WS-Fed IdP.**

    ![Skärmbild som visar knappen för att lägga till en ny SAML eller WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. På sidan **New SAML/WS-Fed IdP (Ny SAML/WS-Fed IdP)** under Identity **Provider Protocol**(Protokoll för identitetsprovider) väljer du **SAML** **eller WS-FED**.

    ![Skärmbild som visar parsning-knappen på SIDAN SAML WS-Fed IdP](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Ange partnerorganisationens domännamn, som ska vara måldomännamnet för direkt federation
6. Du kan ladda upp en metadatafil för att fylla i metadatainformation. Om du väljer att mata in metadata manuellt anger du följande information:
   - Domännamn för partner-IdP
   - Entitets-ID för partner-IdP
   - Passiv begärandeslutpunkt för partner-IdP
   - Certifikat
   > [!NOTE]
   > Metadata-URL är valfritt, men vi rekommenderar det starkt. Om du anger metadata-URL:en kan Azure AD förnya signeringscertifikatet automatiskt när det upphör att gälla. Om certifikatet roteras av någon anledning före förfallotiden eller om du inte anger en metadata-URL kan Azure AD inte förnya det. I det här fallet måste du uppdatera signeringscertifikatet manuellt.

7. Välj **Spara**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Så här konfigurerar du direkt federation i Azure AD med PowerShell

1. Installera den senaste versionen av Azure AD PowerShell för Graph-modulen ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Om du behöver detaljerade steg innehåller snabbstarten för att lägga till en gästanvändare avsnittet Installera den [senaste AzureADPreview-modulen](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Kör följande kommando: 
   ```powershell
   Connect-AzureAD
   ```
1. Logga in med det hanterade globala administratörskontot i inloggningsuppmaning. 
2. Kör följande kommandon och ersätt värdena från federationsmetadatafilen. För AD FS Server och Okta är federationsfilen federationmetadata.xml, till exempel: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml` . 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Steg 3: Testa direkt federation i Azure AD
Testa nu konfigurationen av direktfederation genom att bjuda in en ny B2B-gästanvändare. Mer information finns i [Lägga till Azure AD B2B-samarbetsanvändare i Azure Portal](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Hur gör jag för att redigera en direkt federationsrelation?

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **External Identities**.
3. Välj **Alla identitetsproviders**
4. Under **SAML/WS-Fed-identitetsproviders** väljer du providern.
5. Uppdatera värdena i informationsfönstret för identitetsprovidern.
6. Välj **Spara**.


## <a name="how-do-i-remove-direct-federation"></a>Hur gör jag för att du ta bort direkt federation?
Du kan ta bort din direkt federationskonfiguration. Om du gör det kan inte direkt federationsgästanvändare som redan har löst in sina inbjudningar logga in. Men du kan ge dem åtkomst till dina resurser igen genom att [återställa deras inlösningsstatus](reset-redemption-status.md). Så här tar du bort direkt federation med en identitetsprovider i Azure AD-portalen:

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **External Identities**.
3. Välj **Alla identitetsproviders.**
4. Välj identitetsprovidern och välj sedan Ta **bort.** 
5. Bekräfta **borttagningen** genom att välja Ja. 

Så här tar du bort direkt federation med en identitetsprovider med hjälp av PowerShell:
1. Installera den senaste versionen av Azure AD PowerShell för Graph-modulen ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör följande kommando: 
   ```powershell
   Connect-AzureAD
   ```
3. Logga in med det hanterade globala administratörskontot i inloggningsuppmaning. 
4. Ange följande kommando:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Nästa steg

Läs mer om [inlösningsupplevelsen för inbjudningar](redemption-experience.md) när externa användare loggar in med olika identitetsproviders.
