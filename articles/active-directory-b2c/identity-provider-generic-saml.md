---
title: Konfigurera registrering och inloggning med SAML Identity Provider
titleSuffix: Azure Active Directory B2C
description: Konfigurera registrering och inloggning med valfri SAML Identity Provider (IdP) i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71d51c4303dbc4c0c2668dbfcf388b0d6c6bcffe
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107806"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Konfigurera registrering och inloggning med SAML Identity Provider med hjälp av Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) stöder Federation med SAML 2,0 Identity providers. Den här artikeln visar hur du aktiverar inloggning med ett användar konto för SAML-identitetsprovider, så att användarna kan logga in med sina befintliga sociala eller företags identiteter, till exempel [ADFS](identity-provider-adfs2016-custom.md) och [Salesforce](identity-provider-salesforce-saml.md).

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>Översikt över scenario

Du kan konfigurera Azure AD B2C så att användarna kan logga in till ditt program med autentiseringsuppgifter från externa sociala eller Enterprise SAML Identity providers (IdP). När Azure AD B2C federerar med en SAML-identitetsprovider fungerar det som en **tjänst leverantör** som initierar en SAML-begäran till SAML- **identitetsprovider** och väntar på ett SAML-svar. I följande diagram:

1. Programmet initierar en auktoriseringsbegäran till Azure AD B2C. Programmet kan vara ett [OAuth 2,0](protocols-overview.md) -eller [OpenID Connect](openid-connect.md) -program eller en [SAML-](saml-service-provider.md)tjänstleverantör. 
1. På Azure AD B2C inloggnings sida väljer användaren att logga in med ett SAML Identity Provider-konto (till exempel *contoso*). Azure AD B2C initierar en SAML-auktoriseringsbegäran och tar användaren till SAML-identitets leverantören för att slutföra inloggningen.
1. SAML Identity Provider returnerar ett SAML-svar.
1. Azure AD B2C validerar SAML-token, extraherar anspråk, utfärdar sin egen token och tar användaren tillbaka till programmet.  

![Logga in med SAML Identity Provider-flöde](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>Komponenter i lösningen

Följande komponenter krävs för det här scenariot:

* En SAML **Identity-Provider** med möjlighet att ta emot, avkoda och svara på SAML-begäranden från Azure AD B2C.
* En offentligt tillgänglig **slut punkt** för SAML-metadata för din identitetsprovider.
* En [Azure AD B2C klient](tutorial-create-tenant.md).
 

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Om du vill upprätta förtroende mellan Azure AD B2C och SAML Identity Provider måste du ange ett giltigt X509-certifikat med den privata nyckeln. Azure AD B2C signerar SAML-begärandena med hjälp av certifikatets privata nyckel. Identitets leverantören verifierar begäran med hjälp av certifikatets offentliga nyckel. Den offentliga nyckeln är tillgänglig via metadata för teknisk profil. Alternativt kan du ladda upp CER-filen manuellt till SAML-identitetsprovider.

Ett självsignerat certifikat är acceptabelt i de flesta fall. För produktions miljöer rekommenderar vi att du använder ett X509-certifikat som utfärdats av en certifikat utfärdare. Som beskrivs senare i det här dokumentet för en miljö som inte är en produktions miljö kan du inaktivera SAML-signering på båda sidorna.

### <a name="obtain-a-certificate"></a>Skaffa ett certifikat

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Ladda upp certifikatet

Du måste lagra ditt certifikat i Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. På sidan Översikt väljer du **ID för identitets miljö**.
1. Välj **princip nycklar** och välj sedan **Lägg till**.
1. För **alternativ** väljer du `Upload` .
1. Ange ett **namn** för princip nyckeln. Till exempel `SAMLSigningCert`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
1. Bläddra till och välj din Certificate. pfx-fil med den privata nyckeln.
1. Klicka på **Skapa**.

## <a name="configure-the-saml-technical-profile"></a>Konfigurera den tekniska SAML-profilen

Definiera SAML Identity Provider genom att lägga till den i **ClaimsProviders** -elementet i principens tilläggs fil. Anspråks leverantörerna innehåller en teknisk profil för SAML som avgör vilka slut punkter och protokoll som behövs för att kommunicera med SAML Identity Provider. Så här lägger du till en anspråks leverantör med en teknisk SAML-profil:

1. Öppna *TrustFrameworkExtensions.xml*.
1. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

Uppdatera följande XML-element med det relevanta värdet:

|XML-element  |Värde  |
|---------|---------|
|ClaimsProvider\Domain  | Domän namnet som används för [direkt inloggning](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Ange det domän namn som du vill använda i direkt inloggningen. Till exempel *contoso.com*. |
|TechnicalProfile\DisplayName|Det här värdet kommer att visas på inloggnings knappen på inloggnings skärmen. Till exempel *contoso*. |
|Metadata\PartnerEntity|URL för metadata för SAML Identity Provider. Du kan också kopiera metadata för identitets leverantören och lägga till dem i CDATA-elementet `<![CDATA[Your IDP metadata]]>` .|

## <a name="map-the-claims"></a>Mappa anspråken

**OutputClaims** -elementet innehåller en lista över anspråk som returnerats av SAML Identity Provider. Mappa namnet på det anspråk som definierats i principen till det kontroll namn som definierats i identitets leverantören. Kontrol lera identitets leverantören för listan över anspråk (intyg). Mer information finns i [anspråk-mappning](identity-provider-generic-saml-options.md#claims-mapping).

I exemplet ovan inkluderar *contoso-SAML2* de anspråk som returneras av en SAML-identitetsprovider:

* **IssuerUserId** -anspråket är mappat till **assertionSubjectName** -anspråket.
* **First_name** -anspråk är mappat till **givenName** -anspråket.
* **Last_Name** -anspråk är mappat till anspråket efter **namn** .
* **Visnings** kravet är mappat till `http://schemas.microsoft.com/identity/claims/displayname` anspråket.
* **E-** postanspråk utan namn mappning.

Den tekniska profilen returnerar även anspråk som inte returneras av identitets leverantören:

* **IdentityProvider** -anspråket som innehåller namnet på identitets leverantören.
* **AuthenticationSource** -anspråket med standardvärdet **socialIdpAuthentication**.
 
### <a name="add-the-saml-session-technical-profile"></a>Lägg till den tekniska profilen för SAML-sessionen

Om du inte redan har en `SM-Saml-idp` teknisk profil för SAML-sessionen lägger du till en i tilläggs principen. Leta upp `<ClaimsProviders>` avsnittet och Lägg till följande XML-kodfragment. Om principen redan innehåller den `SM-Saml-idp` tekniska profilen går du vidare till nästa steg. Mer information finns i [hantering av enkel inloggnings session](custom-policy-reference-sso.md).

```xml
<ClaimsProvider>
  <DisplayName>Session Management</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SM-Saml-idp">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IncludeSessionIndex">false</Item>
        <Item Key="RegisterServiceProviders">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>Konfigurera din SAML Identity Provider

När principen har kon figurer ATS måste du konfigurera SAML Identity Provider med Azure AD B2C SAML-metadata. SAML-metadata är information som används i SAML-protokollet för att exponera konfigurationen för principen, **tjänst leverantören**. Den definierar platsen för tjänsterna, till exempel inloggning och utloggning, certifikat, inloggnings metod med mera.

Varje SAML Identity-Provider har olika steg för att ställa in en tjänst leverantör. Vissa SAML Identity providers begär Azure AD B2C metadata, medan andra kräver att du går igenom metadatafilen manuellt och anger informationen. Mer information finns i identitets leverantörens dokumentation.

I följande exempel visas en URL-adress för SAML-metadata för en Azure AD B2C teknisk profil:

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Ersätt följande värden:

- **din klient** organisation med ditt klient namn, till exempel Your-Tenant.onmicrosoft.com.
- **din princip** med ditt princip namn. Till exempel B2C_1A_signup_signin_adfs.
- **din tekniska profil** med namnet på din SAML Identity Provider-tekniska profil. Till exempel contoso-SAML2.

Öppna en webbläsare och gå till URL: en. Se till att du anger rätt URL och att du har åtkomst till XML-metadatafilen.

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**
1. Välj en princip för förlitande part, till exempel `B2C_1A_signup_signin` .
1. För **program** väljer du ett webb program som du [har registrerat tidigare](troubleshoot-custom-policies.md#troubleshoot-the-runtime). **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera alternativ för SAML-identitetsprovider med Azure Active Directory B2C](identity-provider-generic-saml-options.md)

::: zone-end
