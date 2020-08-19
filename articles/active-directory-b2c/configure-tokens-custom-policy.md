---
title: Hantera SSO-och token-anpassning med anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig mer om att hantera SSO-och token-anpassning med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9f3cd5c3280308f6da15a52361857fa02567d595
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505469"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Hantera SSO-och token-anpassning med anpassade principer i Azure Active Directory B2C

Den här artikeln innehåller information om hur du kan hantera din token, session och konfigurationer med enkel inloggning (SSO) med [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="jwt-token-lifetimes-and-claims-configuration"></a>Livs längd för JWT-token och anspråks konfiguration

Om du vill ändra inställningarna för din token för din token lägger du till ett [ClaimsProviders](claimsproviders.md) -element i den förlitande part filen i den princip som du vill påverka.  **ClaimsProviders** -elementet är underordnat [TrustFrameworkPolicy](trustframeworkpolicy.md) -elementet.

Infoga elementet ClaimsProviders mellan elementet BasePolicy och RelyingParty-elementet i den förlitande part filen.

I måste du ange den information som påverkar dina livstider för token. XML-filen ser ut så här:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Följande värden anges i föregående exempel:

- **Livstid** för åtkomsttoken – åtkomsttoken för åtkomsttoken anges med **token_lifetime_secs** metadata-objekt. Standardvärdet är 3600 sekunder (60 minuter).
- **ID-token livs längd** -värdet för ID-token anges med **id_token_lifetime_secs** metadataobjektet. Standardvärdet är 3600 sekunder (60 minuter).
- **Giltighets tid** för uppdateringstoken – värdet för uppdateringstoken anges med **refresh_token_lifetime_secs** metadataobjektet. Standardvärdet är 1209600 sekunder (14 dagar).
- **Uppdatera token glidande fönster livs längd** – om du vill ange en varaktighet för en glidande period till din uppdateringstoken anger du värdet för **rolling_refresh_token_lifetime_secs** metadataelement. Standardvärdet är 7776000 (90 dagar). Om du inte vill framtvinga en glidande fönster livs längd ersätter du objektet med `<Item Key="allow_infinite_rolling_refresh_token">True</Item>` .
- **Issuer-anspråk (ISS)** -anspråket Issuer (ISS) anges med **IssuanceClaimPattern** metadata-objekt. Tillämpliga värden är `AuthorityAndTenantGuid` och `AuthorityWithTfp` .
- **Inställning av anspråk som representerar princip-ID** – alternativen för att ange det här värdet är `TFP` (förtroende Ramverks princip) och `ACR` (referens för autentisering). `TFP` är det rekommenderade värdet. Ange **AuthenticationContextReferenceClaimPattern** med värdet för `None` .

    Lägg till följande element i **ClaimsSchema** -elementet:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Lägg till följande element i **OutputClaims** -elementet:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Ta bort **AuthenticationContextReferenceClaimPattern** -objektet för ACR.

- **Subject (sub)-anspråk** – det här alternativet är standardvärdet ObjectID, om du vill växla den här inställningen till `Not Supported` ersätter du den här raden:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    med den här raden:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure AD B2C-sessionen](session-overview.md).
- Lär dig hur du [konfigurerar sessionens beteende i anpassade principer](session-behavior-custom-policy.md).
- Referens: [JwtIssuer](jwt-issuer-technical-profile.md).
