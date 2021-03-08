---
title: Lägg till AD FS som en SAML Identity-Provider genom att använda anpassade principer
titleSuffix: Azure AD B2C
description: Konfigurera AD FS 2016 med hjälp av SAML-protokollet och anpassade principer i Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3082c249b04b5efc71187dd03515bc8c875b7c2f
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448599"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Lägg till AD FS som en SAML Identity-Provider med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för ett AD FS användar konto med hjälp av [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C). Du aktiverar inloggning genom att lägga till en [SAML-identitetsprovider](identity-provider-generic-saml.md) i en anpassad princip.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Skapa en princip nyckel

Du måste lagra ditt certifikat i Azure AD B2C-klienten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. På sidan Översikt väljer du **ID för identitets miljö**.
5. Välj **princip nycklar** och välj sedan **Lägg till**.
6. För **alternativ** väljer du `Upload` .
7. Ange ett **namn** för princip nyckeln. Till exempel `SAMLSigningCert`. Prefixet `B2C_1A_` läggs till automatiskt till namnet på din nyckel.
8. Bläddra till och välj din Certificate. pfx-fil med den privata nyckeln.
9. Klicka på **Skapa**.

## <a name="add-a-claims-provider"></a>Lägg till en anspråks leverantör

Om du vill att användarna ska logga in med ett AD FS konto måste du definiera kontot som en anspråks leverantör som Azure AD B2C kan kommunicera med via en slut punkt. Slut punkten innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en speciell användare har autentiserats.

Du kan definiera ett AD FS-konto som en anspråks leverantör genom att lägga till det i **ClaimsProviders** -elementet i principens tilläggs fil. Mer information finns i [definiera en SAML Identity Provider](identity-provider-generic-saml.md).

1. Öppna *TrustFrameworkExtensions.xml*.
1. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet.
1. Lägg till en ny **ClaimsProvider** enligt följande:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

1. Ersätt `your-AD-FS-domain` med namnet på din AD FS domän och Ersätt värdet för **identityProvider** utgående anspråk med DNS (valfritt värde som anger din domän).

1. Leta upp `<ClaimsProviders>` avsnittet och Lägg till följande XML-kodfragment. Om principen redan innehåller den `SM-Saml-idp` tekniska profilen går du vidare till nästa steg. Mer information finns i [hantering av enkel inloggnings session](custom-policy-reference-sso.md).

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

1. Spara filen.

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

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-an-ad-fs-relying-party-trust"></a>Konfigurera ett AD FS förtroende för förlitande part

Om du vill använda AD FS som identitets leverantör i Azure AD B2C måste du skapa ett AD FS förlitande part förtroende med Azure AD B2C SAML-metadata. I följande exempel visas en URL-adress till SAML-metadata för en Azure AD B2C teknisk profil:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ersätt följande värden:

- **din klient** organisation med ditt klient namn, till exempel Your-Tenant.onmicrosoft.com.
- **din princip** med ditt princip namn. Till exempel B2C_1A_signup_signin_adfs.
- **din tekniska profil** med namnet på din SAML Identity Provider-tekniska profil. Till exempel contoso-SAML2.

Öppna en webbläsare och gå till URL: en. Se till att du anger rätt URL och att du har åtkomst till XML-metadatafilen. Om du vill lägga till ett nytt förtroende för förlitande part med hjälp av snapin-modulen AD FS hantering och konfigurera inställningarna manuellt, utför följande procedur på en Federations Server. Det krävs minst medlemskap i **Administratörer** eller motsvarande på den lokala datorn för att kunna slutföra den här proceduren.

1. I Serverhanteraren väljer du **verktyg** och väljer sedan **AD FS hantering**.
2. Välj **Lägg till förtroende för förlitande part**.
3. På sidan **Välkommen** väljer du **anspråks medveten** och klickar sedan på **Starta**.
4. På sidan **Välj data källa** väljer du **Importera data om den förlitande parten publicera online eller i ett lokalt nätverk**, anger din Azure AD B2C metadata-URL och klickar sedan på **Nästa**.
5. På sidan **Ange visnings namn** anger du ett **visnings namn** under **anteckningar**, anger en beskrivning för det här förlitande part förtroendet och klickar sedan på **Nästa**.
6. På sidan **välj Access Control princip** väljer du en princip och klickar sedan på **Nästa**.
7. På sidan **redo att lägga till förtroende** granskar du inställningarna och klickar sedan på **Nästa** för att spara information om förtroende för förlitande part.
8. På sidan **Slutför** klickar du på **Stäng**. den här åtgärden visar automatiskt dialog rutan **Redigera anspråks regler** .
9. Välj **Lägg till regel**.
10. I **anspråks regel mal len** väljer du **Skicka LDAP-attribut som anspråk**.
11. Ange ett **namn på anspråks regeln**. För **attributarkivet** väljer du **Välj Active Directory**, lägger till följande anspråk och klickar sedan på **Slutför** och **OK**.

    | LDAP-attribut | Utgående anspråks typ |
    | -------------- | ------------------- |
    | Användarens huvud namn | userPrincipalName |
    | Efternamn | family_name |
    | Given-Name | given_name |
    | E-post adress | e-post |
    | Display-Name | name |

    Observera att dessa namn inte visas i list rutan Utgående anspråks typer. Du måste skriva in dem manuellt i. (List rutan kan redige ras i själva verket).

12.  Utifrån din certifikat typ kan du behöva ange HASH-algoritmen. I fönstret Egenskaper för förlitande part förtroende (B2C demo) väljer du fliken **Avancerat** och ändrar sedan den **säkra hash-algoritmen** till `SHA-256` och klickar på **OK**.
13. I Serverhanteraren väljer du **verktyg** och väljer sedan **AD FS hantering**.
14. Välj det förtroende för förlitande part som du har skapat, Välj **Uppdatera från federationsmetadata** och klicka sedan på **Uppdatera**.

## <a name="test-your-custom-policy"></a>Testa din anpassade princip

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **principer** väljer du **Identity Experience Framework**
1. Välj en princip för förlitande part, till exempel `B2C_1A_signup_signin` .
1. För **program** väljer du ett webb program som du [har registrerat tidigare](tutorial-register-applications.md). **Svars-URL: en** ska visas `https://jwt.ms` .
1. Välj knappen **Kör nu** .
1. På sidan registrering eller inloggning väljer du **contoso-AD FS** för att logga in med contoso AD FS Identity Provider.

Om inloggnings processen lyckas omdirigeras webbläsaren till `https://jwt.ms` , som visar innehållet i den token som returnerades av Azure AD B2C.

## <a name="troubleshooting-ad-fs-service"></a>Felsöka AD FS tjänsten  

AD FS har kon figurer ATS för att använda Windows program logg. Om du upplever utmaningar med att konfigurera AD FS som en SAML-identitetsprovider som använder anpassade principer i Azure AD B2C, vill du kanske kontrol lera AD FS händelse loggen:

1. I Windows **search-fältet** skriver du **Loggboken** och väljer sedan **Loggboken** Desktop-appen.
1. Om du vill visa loggen för en annan dator högerklickar du på **Loggboken (lokal)**. Välj **Anslut till en annan dator** och fyll i fälten för att slutföra dialog rutan **Välj dator** .
1. I **Loggboken** öppnar du **program-och tjänst loggarna**.
1. Välj **AD FS** och välj sedan **admin**. 
1. Om du vill visa mer information om en händelse dubbelklickar du på händelsen.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>SAML-begäran är inte signerad med den förväntade signaturen för Signeringsalgoritm

Det här felet indikerar att SAML-begäran som skickats av Azure AD B2C inte är signerad med den förväntade signeringsalgoritmen som kon figurer ATS i AD FS. SAML-begäran är till exempel signerad med signeringsalgoritmen `rsa-sha256` , men den förväntade signeringsalgoritmen är `rsa-sha1` . Åtgärda problemet genom att kontrol lera att både Azure AD B2C och AD FS har kon figurer ATS med samma Signeringsalgoritm.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Alternativ 1: Ange signeringsalgoritmen i Azure AD B2C  

Du kan konfigurera hur SAML-begäran ska signeras i Azure AD B2C. [XmlSignatureAlgorithm](identity-provider-generic-saml.md) metadata styr värdet för `SigAlg` parametern (frågesträng eller post parameter) i SAML-begäran. I följande exempel konfigureras Azure AD B2C att använda `rsa-sha256` signeringsalgoritmen.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Alternativ 2: Ange signeringsalgoritmen i AD FS 

Du kan också konfigurera den förväntade SAML-begärans signaturalgoritm i AD FS.

1. I Serverhanteraren väljer du **verktyg** och väljer sedan **AD FS hantering**.
1. Välj det **förtroende för förlitande part** som du skapade tidigare.
1. Välj **Egenskaper** och välj sedan **gå vidare**
1. Konfigurera den **säkra hash-algoritmen** och spara ändringarna genom att klicka på **OK** .

::: zone-end
