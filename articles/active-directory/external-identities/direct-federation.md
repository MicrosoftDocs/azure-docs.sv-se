---
title: Direkt Federation med en identitets leverantör för B2B – Azure AD
description: Direkt federera med en SAML-eller WS-Fed identitetsprovider så att gästerna kan logga in till dina Azure AD-appar
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
ms.openlocfilehash: ff8ac540459ad79a8980542254cc15518959b5c0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552299"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Direkt Federation med AD FS och tredje parts leverantörer för gäst användare (för hands version)

> [!NOTE]
>  Direkt Federation är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Den här artikeln beskriver hur du konfigurerar direkt Federation med en annan organisation för B2B-samarbete. Du kan ställa in direkt Federation med en organisation vars identitetsprovider (IdP) stöder SAML 2,0 eller WS-Fed-protokollet.
När du konfigurerar direkt Federation med en partners IdP kan nya gäst användare från domänen använda sitt eget IdP-hanterade organisations konto för att logga in på din Azure AD-klient och börja samar beta med dig. Gäst användaren behöver inte skapa ett separat Azure AD-konto.

## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>När är en gäst användare autentiserad med direkt Federation?
När du har konfigurerat direkt Federation med en organisation kommer alla nya gäst användare som du Bjud in att autentiseras med hjälp av direkt Federation. Det är viktigt att Observera att konfiguration av direkt Federation inte ändrar autentiseringsmetoden för gäst användare som redan har löst en inbjudan från dig. Här är några exempel:
 - Om gäst användare redan har löst in inbjudningar från dig och du sedan konfigurerar direkt Federation med sin organisation, fortsätter gäst användarna att använda samma autentiseringsmetod som de använde innan du konfigurerade direkt federationen.
 - Om du ställer in direkt Federation med en partner organisation och bjuder in gäst användare, och partner organisationen senare flyttar till Azure AD, fortsätter gäst användare som redan har löst inbjudningarna att använda direkt Federation, så länge den direkta Federations principen i din klient organisation finns.
 - Om du tar bort direkt Federation med en partner organisation kan alla gäst användare som för närvarande använder direkt Federation inte logga in.

I något av dessa scenarier kan du uppdatera en gäst användares autentiseringsmetod genom att [återställa deras inlösnings status](reset-redemption-status.md).

Direkt federationen är knuten till domän namn rymder, till exempel contoso.com och fabrikam.com. När du etablerar en direkt Federations konfiguration med AD FS eller en IdP från tredje part, associerar organisationer en eller flera domän namn rymder till dessa IDP: er. 

## <a name="end-user-experience"></a>Slutanvändarens upplevelse 
Med direkt Federation loggar gäst användare in på din Azure AD-klient med hjälp av sitt eget organisations konto. När de har åtkomst till delade resurser och uppmanas att logga in, omdirigeras direkt Federations användare till deras IdP. Efter lyckad inloggning returneras de till Azure AD för att få åtkomst till resurser. Direkta Federations användares uppdaterings-token är giltiga i 12 timmar, [Standard längden för passthrough-uppdateringstoken](../develop/active-directory-configurable-token-lifetimes.md#exceptions) i Azure AD. Om den federerade IdP har SSO aktive rad, kommer användaren att uppleva SSO och kommer inte att se några inloggnings meddelanden efter den första autentiseringen.

## <a name="sign-in-endpoints"></a>Inloggnings slut punkter

Direkt Federations gäst användare kan nu logga in till din klient-eller Microsoft-appar från första part med hjälp av en [gemensam slut punkt](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (med andra ord en allmän App-URL som inte innehåller din klient kontext). Under inloggnings processen väljer gäst användaren **inloggnings alternativ** och väljer sedan logga in i **en organisation**. Användaren skriver sedan namnet på din organisation och fortsätter att logga in med sina egna autentiseringsuppgifter.

Gäst användare i direkt federationen kan också använda program slut punkter som innehåller din klient information, till exempel:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Du kan också ge direkt Federations gäst användare en direkt länk till ett program eller en resurs genom att inkludera din klient information, till exempel `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="limitations"></a>Begränsningar

### <a name="dns-verified-domains-in-azure-ad"></a>DNS-verifierade domäner i Azure AD
Den domän som du vill federera med får ***inte*** vara DNS-verifierad i Azure AD. Du kan konfigurera direkt Federation med ohanterad (e-postverifierad eller "viral") Azure AD-klienter eftersom de inte är DNS-verifierade.

### <a name="authentication-url"></a>URL för autentisering
Direkt Federation tillåts bara för principer där autentiserings-URL: en domän matchar mål domänen, eller där autentiserings-URL: en är en av dessa tillåtna identitets leverantörer (den här listan kan ändras):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

När du till exempel ställer in direkt Federation för **fabrikam.com**, kommer URL: en för autentisering att `https://fabrikam.com/adfs` klara verifieringen. En värd i samma domän kommer också att skickas till exempel `https://sts.fabrikam.com/adfs` . Men autentiserings-URL: en `https://fabrikamconglomerate.com/adfs` eller `https://fabrikam.com.uk/adfs` för samma domän godkänns inte.

### <a name="signing-certificate-renewal"></a>Förnyelse av signerings certifikat
Om du anger URL: en för metadata i inställningarna för identitetsprovider förnyar Azure AD automatiskt signerings certifikatet när det upphör att gälla. Men om certifikatet roteras av någon anledning innan förfallo tiden, eller om du inte anger en URL för metadata, kan inte Azure AD förnya det. I så fall måste du uppdatera signerings certifikatet manuellt.

### <a name="limit-on-federation-relationships"></a>Gräns för Federations relationer
För närvarande stöds högst 1 000 Federations relationer. Den här gränsen omfattar både [interna federationar](/powershell/module/msonline/set-msoldomainfederationsettings) och direkta federationer.

### <a name="limit-on-multiple-domains"></a>Begränsa för flera domäner
Vi stöder för närvarande inte direkt Federation med flera domäner från samma klient organisation.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Kan jag konfigurera direkt Federation med en domän för vilken en ohanterad (e-autentiserad) klient organisation finns? 
Ja. Om domänen inte har verifierats och klienten inte har genomgått någon [Administratörs övertag](../enterprise-users/domains-admin-takeover.md)Ande kan du konfigurera direkt Federation med domänen. Ohanterad eller e-postverifierad skapas klienter när en användare löser in en B2B-inbjudan eller utför en självbetjänings registrering för Azure AD med en domän som inte finns för närvarande. Du kan konfigurera direkt Federation med dessa domäner. Om du försöker konfigurera direkt Federation med en DNS-verifierad domän, antingen i Azure Portal eller via PowerShell, visas ett fel meddelande.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Om direkt Federation och e-postautentisering med eng ång slö sen ord är båda aktiverade, vilken metod har företräde?
När direkt Federation upprättas med en partner organisation har den företräde framför e-postautentisering med eng ång slö sen ord för nya gäst användare från organisationen. Om en gäst användare har löst en inbjudan med autentisering med eng ång slö sen ord innan du konfigurerar direkt Federation, fortsätter de att använda autentisering med eng ång slö sen ord. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Är direkta Federations adress inloggnings problem på grund av ett delvis synkroniserat innehav?
Nej, funktionen för [eng ång slö sen ord](one-time-passcode.md) ska användas i det här scenariot. Ett "delvis synkroniserat innehav" syftar på en partner Azure AD-klient där lokala användar identiteter inte är fullständigt synkroniserade med molnet. En gäst vars identitet ännu inte finns i molnet men som försöker lösa in B2B-inbjudan kommer inte att kunna logga in. Funktionen eng ång slö sen ord gör det möjligt för den här gästen att logga in. Direkt Federations funktionen löser scenarier där gästen har sitt eget IdP-hanterat organisations konto, men organisationen inte har någon Azure AD-närvaro alls.
### <a name="once-direct-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>När direkt Federation har kon figurer ATS med en organisation behöver varje gäst skickas och lösa in en enskild inbjudan?
Konfiguration av direkt Federation ändrar inte autentiseringsmetoden för gäst användare som redan har löst en inbjudan från dig. Du kan uppdatera en gäst användares autentiseringsmetod genom att [återställa deras inlösnings status](reset-redemption-status.md).
## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Steg 1: Konfigurera partner organisationens identitetsprovider
Först måste partner organisationen konfigurera sin identitets leverantör med nödvändiga anspråk och förtroenden för förlitande part. 

> [!NOTE]
> För att illustrera hur du konfigurerar en identitets leverantör för direkt Federation använder vi Active Directory Federation Services (AD FS) (AD FS) som exempel. Se artikeln [Konfigurera direkt Federation med AD FS](direct-federation-adfs.md), vilket ger exempel på hur du konfigurerar AD FS som en SAML 2,0-eller WS-Fed-identitetsprovider som förberedelse för direkt Federation.

### <a name="saml-20-configuration"></a>SAML 2,0-konfiguration

Azure AD B2B kan konfigureras för att federera med identitets leverantörer som använder SAML-protokollet med särskilda krav som anges nedan. Mer information om hur du konfigurerar ett förtroende mellan SAML Identity Provider och Azure AD finns i  [använda en SAML 2,0 Identity Provider (IdP) för enkel inloggning](../hybrid/how-to-connect-fed-saml-idp.md).  

> [!NOTE]
> Mål domänen för direkt Federation får inte vara DNS-verifierad i Azure AD. Autentiserings-URL: en måste matcha mål domänen eller så måste den vara en tillåten identitets leverantörs domän. Mer information finns i avsnittet [begränsningar](#limitations) . 

#### <a name="required-saml-20-attributes-and-claims"></a>Obligatoriska SAML 2,0-attribut och anspråk
Följande tabeller visar krav för särskilda attribut och anspråk som måste konfigureras hos tredjeparts identitets leverantör. Om du vill konfigurera direkt Federation måste följande attribut tas emot i SAML 2,0-svaret från identitets leverantören. Dessa attribut kan konfigureras genom att länka till XML-filen för Online Security token eller genom att ange dem manuellt.

Attribut som krävs för SAML 2,0-svaret från IdP:

|Attribut  |Värde  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Utfärdar-URI för partner IdP, till exempel `http://www.example.com/exk10l6w90DHM0yi...`         |


Obligatoriska anspråk för SAML 2,0-token som utfärdats av IdP:

|Attribut  |Värde  |
|---------|---------|
|NameID-format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed konfiguration 
Azure AD B2B kan konfigureras för att federera med identitets leverantörer som använder WS-Fed protokoll med vissa särskilda krav som anges nedan. För närvarande har de två WS-Fed-leverantörerna testats för kompatibilitet med Azure AD, inklusive AD FS och Shibboleth. Mer information om hur du etablerar ett förtroende för en förlitande part mellan en WS-Fed-kompatibel Provider med Azure AD finns i "STS integration Paper using WS Protocols" i [Azure AD Identity Provider Compatibility-dokumentation](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Mål domänen för direkt Federation får inte vara DNS-verifierad i Azure AD. URL: en för autentisering måste matcha antingen mål domänen eller domänen för en tillåten identitets leverantör. Mer information finns i avsnittet [begränsningar](#limitations) . 

#### <a name="required-ws-fed-attributes-and-claims"></a>Obligatoriska WS-Fed attribut och anspråk

Följande tabeller visar krav för särskilda attribut och anspråk som måste konfigureras hos tredje part WS-Fed identitetsprovider. Om du vill konfigurera direkt Federation måste följande attribut tas emot i WS-Fed-meddelandet från identitets leverantören. Dessa attribut kan konfigureras genom att länka till XML-filen för Online Security token eller genom att ange dem manuellt.

Obligatoriska attribut i WS-Fed-meddelandet från IdP:
 
|Attribut  |Värde  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Målgrupp     |`urn:federation:MicrosoftOnline`         |
|Utfärdare     |Utfärdar-URI för partner IdP, till exempel `http://www.example.com/exk10l6w90DHM0yi...`         |

Obligatoriska anspråk för den WS-Fed token som utfärdats av IdP:

|Attribut  |Värde  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Steg 2: Konfigurera direkt Federation i Azure AD 
Sedan konfigurerar du federationen med den identitetsprovider som du konfigurerade i steg 1 i Azure AD. Du kan använda antingen Azure AD-portalen eller PowerShell. Det kan ta 5-10 minuter innan den direkta Federations principen börjar gälla. Under den här tiden försöker du inte lösa in en inbjudan för direkt Federations domänen. Följande attribut krävs:
- Utfärdar-URI för partner IdP
- Slut punkt för passiv autentisering av partner IdP (endast https stöds)
- Certifikat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Konfigurera direkt Federation i Azure AD-portalen

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **externa identiteter**  >  **alla identitets leverantörer**.
3. Välj **ny SAML/WS-utfodras IDP**.

    ![Skärm bild som visar knapp för att lägga till en ny SAML-eller WS-Fed-IdP](media/direct-federation/new-saml-wsfed-idp.png)

4. På sidan **ny SAML/WS-utfodras IDP** under **Identity Provider Protocol** väljer du **SAML** eller **WS-utfodras**.

    ![Skärm bild som visar knappen parsa på SAML-eller WS-Fed IdP-Sidan](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Ange din partner organisations domän namn, som är mål domän namnet för direkt Federation
6. Du kan ladda upp en metadatafil om du vill fylla i metadatainformation. Ange följande information om du väljer att ange metadata manuellt:
   - Domän namnet för partner IdP
   - Enhets-ID för partner IdP
   - Passive begär ande slut punkt för partner IdP
   - Certifikat
   > [!NOTE]
   > URL för metadata är valfritt, men vi rekommenderar starkt det. Om du anger URL: en för metadata kan Azure AD automatiskt förnya signerings certifikatet när det upphör att gälla. Om certifikatet roteras av någon anledning innan förfallo tiden eller om du inte anger en URL för metadata, kan inte Azure AD förnya det. I så fall måste du uppdatera signerings certifikatet manuellt.

7. Välj **Spara**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Konfigurera direkt Federation i Azure AD med hjälp av PowerShell

1. Installera den senaste versionen av Azure AD PowerShell för Graph module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Om du behöver detaljerade anvisningar inkluderar snabb starten för att lägga till en gäst användare avsnittet [installera den senaste AzureADPreview-modulen](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Kör följande kommando: 
   ```powershell
   Connect-AzureAD
   ```
1. Logga in med det hanterade globala administratörs kontot vid inloggnings meddelandet. 
2. Kör följande kommandon och ersätt värdena från federationsmetadata. För AD FS server-och okta är Federations filen federationmetadata.xml, till exempel: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml` . 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Steg 3: testa Direct Federation i Azure AD
Testa nu din direkt Federations installation genom att bjuda in en ny B2B-gäst användare. Mer information finns i [lägga till Azure AD B2B-samarbets användare i Azure Portal](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Hur gör jag för att redigera en direkt Federations relation?

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **externa identiteter**.
3. Välj **alla identitets leverantörer**
4. Under **SAML/WS-utfodras identitets leverantörer** väljer du providern.
5. Uppdatera värdena i fönstret information om identitetsprovider.
6. Välj **Spara**.


## <a name="how-do-i-remove-direct-federation"></a>Hur gör jag för att ta bort direkt federationen?
Du kan ta bort den direkt Federations konfigurationen. Om du gör det kommer direkt Federations gäst användare som redan har löst deras inbjudningar inte att kunna logga in. Men du kan ge dem åtkomst till dina resurser igen genom att [återställa deras inlösnings status](reset-redemption-status.md). Ta bort direkt Federation med en identitets leverantör i Azure AD-portalen:

1. Gå till [Azure-portalen](https://portal.azure.com/). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **externa identiteter**.
3. Välj **alla identitets leverantörer**.
4. Välj identitets leverantören och välj sedan **ta bort**. 
5. Bekräfta borttagningen genom att välja **Ja** . 

Ta bort direkt Federation med en identitetsprovider med hjälp av PowerShell:
1. Installera den senaste versionen av Azure AD PowerShell för Graph module ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör följande kommando: 
   ```powershell
   Connect-AzureAD
   ```
3. Logga in med det hanterade globala administratörs kontot vid inloggnings meddelandet. 
4. Ange följande kommando:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [inlösnings funktionen för inbjudan](redemption-experience.md) när externa användare loggar in med olika identitets leverantörer.
