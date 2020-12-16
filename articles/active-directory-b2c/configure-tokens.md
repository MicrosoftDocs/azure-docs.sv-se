---
title: Konfigurera tokens – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du konfigurerar livs längd och kompatibilitetsinställningar för token i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a4299eff296d9795f8d256ff1236a8e8b4ad3e42
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585215"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurera tokens i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

I den här artikeln får du lära dig hur du konfigurerar [livs längd och kompatibilitet för en token](tokens-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Krav

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>Livs längd för token

Du kan konfigurera livs längden för token, inklusive:

- **Livs längd för åtkomst och ID-token (minuter)** – livstiden för OAuth 2,0 Bearer-token och ID-token. Standardvärdet är 60 minuter (1 timme). Minimivärdet (inklusive) är 5 minuter. Det största (inklusive) är 1 440 minuter (24 timmar).
- **Giltighets tid för token (dagar)** – den längsta tids perioden innan en uppdateringstoken kan användas för att hämta en ny åtkomsttoken, om ditt program har beviljats som `offline_access` omfång. Standardvärdet är 14 dagar. Minimivärdet (inklusive) är en dag. Max (inklusive) 90 dagar.
- **Uppdatera token glidande fönster livs längd** -typ av glidande token. `Bounded` anger att uppdateringstoken kan utökas på det sätt som anges i **livs längdens längd (dagar)**. `No expiry` anger att den glidande tidsintervallen för uppdateringstoken aldrig upphör att gälla.
- **Livs längd för livs längd (dagar)** – efter att den här tids perioden har gått ut tvingas användaren att autentiseras på nytt, oberoende av giltighets perioden för den senaste uppdateringstoken som hämtats av programmet. Värdet måste vara större än eller lika med värdet för **uppdateringstoken** .

I följande diagram visas livs längden för skjutreglaget för att uppdatera token.

![Livstid för uppdateringstoken](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> Program med en enda sida som använder auktoriseringskod med PKCE har alltid en livs längd på 24 timmar. [Lär dig mer om säkerhets konsekvenserna av uppdateringstoken i webbläsaren](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="configure-token-lifetime"></a>Konfigurera livs längd för token

::: zone pivot="b2c-user-flow"

Så här konfigurerar du livs längden för token för användar flöde:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C-klient. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller Azure AD B2C klienten.
1. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
1. Välj **användar flöden (principer)**.
1. Öppna det användar flöde som du skapade tidigare.
1. Välj **Egenskaper**.
1. Under **token livs längd** justerar du egenskaperna så att de passar dina programs behov.
1. Klicka på **Spara**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Om du vill ändra inställningarna för din token-kompatibilitet ställer du in metadata [utfärdares](jwt-issuer-technical-profile.md) tekniska profil metadata i tillägget eller den förlitande part filen för den princip som du vill påverka. Den tekniska profilen för token Issuer ser ut som i följande exempel:

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
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

Följande värden anges i föregående exempel:

- livstid för token för **token_lifetime_secs** -åtkomsttoken (sekunder). Standardvärdet är 3 600 (1 timme). Minimivärdet är 300 (5 minuter). Det maximala värdet är 86 400 (24 timmar). 
- livstid för **id_token_lifetime_secs** -ID-token (sekunder). Standardvärdet är 3 600 (1 timme). Minimivärdet är 300 (5 minuter). Det maximala värdet är 86 400 (24 timmar). 
- **refresh_token_lifetime_secs** Livstid för uppdateringstoken (sekunder). Standardvärdet är 120, 9600 (14 dagar). Minimivärdet är 86 400 (24 timmar). Det maximala värdet är 7 776 000 (90 dagar). 
- tidsintervallet för glidning av **rolling_refresh_token_lifetime_secs** -uppdateringstoken (sekunder). Standardvärdet är 7 776 000 (90 dagar). Minimivärdet är 86 400 (24 timmar). Det maximala värdet är 31 536 000 (365 dagar). Om du inte vill framtvinga en glidande fönster livs längd ställer du in värdet `allow_infinite_rolling_refresh_token` till `true` . 
- tids perioden för glidning av **allow_infinite_rolling_refresh_token** -uppdateringstoken upphör aldrig att gälla. 

::: zone-end


## <a name="token-compatibility-settings"></a>Kompatibilitetsinställningar för token

Du kan konfigurera token-kompatibiliteten, inklusive:

- **Issuer-anspråk (ISS)-anspråk** – åtkomst-och ID-token-format.
- **Subject (sub)-anspråk** – det huvud konto som används för att ställa in information om token, till exempel användaren av ett program. Värdet är oföränderligt och kan inte tilldelas om eller återanvändas. Den kan användas för att utföra verifierings kontroller på ett säkert sätt, till exempel när token används för att få åtkomst till en resurs. Som standard fylls ämnes anspråket med objekt-ID: t för användaren i katalogen.
- **Anspråk som representerar användar flöde** – det här anspråket identifierar det användar flöde som kördes. Möjliga värden: `tfp` (standard) eller `acr` .

::: zone pivot="b2c-user-flow"

Så här konfigurerar du inställningarna för användar flödets kompatibilitet:

1. Välj **användar flöden (principer)**.
1. Öppna det användar flöde som du skapade tidigare.
1. Välj **Egenskaper**.
1. Under **Inställningar för token-kompatibilitet** justerar du egenskaperna så att de passar dina program behov.
1. Klicka på **Spara**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Om du vill ändra inställningarna för din token-kompatibilitet ställer du in metadata [utfärdares](jwt-issuer-technical-profile.md) tekniska profil metadata i tillägget eller den förlitande part filen för den princip som du vill påverka. Den tekniska profilen för token Issuer ser ut som i följande exempel:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

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

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>Tillhandahålla valfria anspråk till din app

Program anspråk är värden som returneras till programmet. Uppdatera ditt användar flöde så att det innehåller önskade anspråk.

::: zone pivot="b2c-user-flow"

1. Välj **användar flöden (principer)**.
1. Öppna det användar flöde som du skapade tidigare.
1. Välj **Programanspråk**.
1. Välj de anspråk och attribut som du vill skicka tillbaka till ditt program.
1. Klicka på **Spara**.

::: zone-end

::: zone pivot="b2c-custom-policy"

De utgångna anspråken för [teknisk profil för den förlitande parten](relyingparty.md#technicalprofile) är värden som returneras till ett program. Att lägga till utgående anspråk utfärdar anspråk till token efter en lyckad användar resa och skickas till programmet. Ändra det tekniska profil elementet i avsnittet förlitande part för att lägga till önskade anspråk som ett utgående anspråk.

1. Öppna din anpassade princip fil. Till exempel SignUpOrSignin.xml.
1. Hitta OutputClaims-elementet. Lägg till OutputClaim som du vill ska ingå i token. 
1. Ange attributen för utdata-anspråk. 

I följande exempel läggs `accountBalance` anspråket till. AccountBalance-anspråket skickas till programmet som ett saldo. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

OutputClaim-elementet innehåller följande attribut:

- **ClaimTypeReferenceId** – identifieraren för en anspråks typ som redan har definierats i [ClaimsSchema](claimsschema.md) -avsnittet i princip filen eller den överordnade princip filen.
- **PartnerClaimType** – låter dig ändra namnet på anspråket i token. 
- **Standardvärde** -ett standardvärde. Du kan också ställa in standardvärdet för en [anspråks lösare](claim-resolver-overview.md), t. ex. klient-ID.
- **AlwaysUseDefaultValue** – Framtvinga användning av standardvärdet.

::: zone-end

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du [begär åtkomsttoken](access-tokens.md).