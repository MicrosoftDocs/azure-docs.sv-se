---
title: Konfigurera krav för lösen ords komplexitet
titleSuffix: Azure AD B2C
description: Hur du konfigurerar komplexitets kraven för lösen ord som tillhandahålls av användare i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0cd9f0d6cf9529439f7b5ce46b1a5807d0a68a7c
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111391"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurera komplexitets krav för lösen ord i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) stöder ändring av komplexitets kraven för lösen ord som tillhandahålls av en slutanvändare när ett konto skapas. Som standard använder Azure AD B2C **starka** lösen ord. Azure AD B2C också stöd för konfigurations alternativ för att kontrol lera komplexiteten för lösen ord som kunder kan använda.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>Tvingad lösen ords regel

Vid registrering eller återställning av lösen ord måste användaren ange ett lösen ord som uppfyller komplexitets reglerna. Reglerna för komplexitet av lösen ord upprätthålls per användar flöde. Det är möjligt att ett användar flöde kräver en fyrsiffrig PIN-kod under registreringen medan ett annat användar flöde kräver en sträng med åtta tecken under registreringen. Du kan till exempel använda ett användar flöde med annan lösen ords komplexitet för vuxna än för barn.

Lösen ords komplexitet tillämpas aldrig under inloggningen. Användare tillfrågas aldrig under inloggningen för att ändra sina lösen ord eftersom det inte uppfyller det aktuella komplexitets kravet.

Lösen ords komplexitet kan konfigureras i följande typer av användar flöden:

- Användar flöde för registrering eller inloggning
- Användar flöde för återställning av lösen ord

Om du använder anpassade principer kan du ([Konfigurera lösen ords komplexitet i en anpassad princip](password-complexity.md)).

## <a name="configure-password-complexity"></a>Konfigurera lösen ords komplexitet

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.
3. I Azure Portal söker du efter och väljer **Azure AD B2C**.
4. Välj **användar flöden**.
2. Välj ett användar flöde och klicka på **Egenskaper**.
3. Under **lösen ords komplexitet** ändrar du lösen ords komplexiteten för det här användar flödet till **enkel**, **stark** eller **anpassad**.

### <a name="comparison-chart"></a>Jämförelse diagram

| Komplexitet | Beskrivning |
| --- | --- |
| Enkelt | Ett lösen ord som består av minst 8 till 64 tecken. |
| Stark | Ett lösen ord som består av minst 8 till 64 tecken. Det kräver 3 av 4 av gemener, versaler, siffror eller symboler. |
| Anpassad | Det här alternativet ger störst kontroll över regler för lösen ords komplexitet.  Det gör det möjligt att konfigurera en anpassad längd.  Det tillåter också att du godkänner endast lösen ord (PIN-kod). |

## <a name="custom-options"></a>Anpassade alternativ

### <a name="character-set"></a>Teckenuppsättning

Du kan bara acceptera siffror (PIN-nummer) eller den fullständiga teckenuppsättningen.

- **Siffror endast** tillåter siffror (0-9) när du anger ett lösen ord.
- **Alla** tillåter bokstäver, siffror och symboler.

### <a name="length"></a>Längd

Gör att du kan kontrol lera lösen ordets längd krav.

- Den **minsta längden** måste vara minst 4.
- Den **maximala längden** måste vara större än eller lika med minimilängd och får innehålla högst 64 tecken.

### <a name="character-classes"></a>Character-klasser

Gör att du kan kontrol lera de olika teckenuppsättningarna som används i lösen ordet.

- **2 av 4: gemener, versaler, tal (0-9), symbol** ser till att lösen ordet innehåller minst två tecken typer. Till exempel ett tal och ett gemener.
- **3 av 4: gemener, versaler, tal (0-9), symbol** ser till att lösen ordet innehåller minst tre tecken typer. Till exempel ett tal, ett gement och ett versalt.
- **4 av 4: gemener, versaler, siffror (0-9), symbol** ser till att lösen ordet innehåller alla för tecken typer.

    > [!NOTE]
    > Genom att kräva **4 av 4** kan du få till följd av en slutanvändare. Vissa studier har visat att detta krav inte förbättrar entropi. Se [rikt linjer för NIST-lösenord](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>Verifiering av predikat

Om du vill konfigurera lösen ords komplexiteten åsidosätter du- `newPassword` och- `reenterPassword` [anspråks typerna](claimsschema.md) med en referens till [predikat-valideringar](predicates.md#predicatevalidations). PredicateValidations-elementet grupperar en uppsättning predikat som utgör en verifiering av användarindata som kan tillämpas på en anspråks typ. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Leta upp [ClaimsSchema](claimsschema.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till- `newPassword` och- `reenterPassword` anspråk i **ClaimsSchema** -elementet.

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predikat](predicates.md) definierar en grundläggande verifiering för att kontrol lera värdet för en anspråks typ och returnerar true eller false. Verifieringen görs med hjälp av ett angivet metod element och en uppsättning parametrar som är relevanta för metoden. Lägg till följande predikat i **BuildingBlocks** -elementet, direkt efter stängningen av `</ClaimsSchema>` elementet:

    ```xml
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Lägg till följande predikat verifieringar i **BuildingBlocks** -elementet, direkt efter stängningen av `</Predicates>` elementet:

    ```xml
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
            </PredicateReferences>
          </PredicateGroup>
          <PredicateGroup Id="CharacterClasses">
            <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
            <PredicateReferences MatchAtLeast="3">
              <PredicateReference Id="Lowercase" />
              <PredicateReference Id="Uppercase" />
              <PredicateReference Id="Number" />
              <PredicateReference Id="Symbol" />
            </PredicateReferences>
          </PredicateGroup>
        </PredicateGroups>
      </PredicateValidation>
    </PredicateValidations>
    ```

## <a name="disable-strong-password"></a>Inaktivera starkt lösen ord 

Följande tekniska profiler är [Active Directory tekniska profiler](active-directory-technical-profile.md)som läser och skriver data till Azure Active Directory. Åsidosätt dessa tekniska profiler i tilläggs filen. Använd `PersistedClaims` för att inaktivera principen för starkt lösen ord. Hitta **ClaimsProviders** -elementet.  Lägg till följande anspråks leverantörer enligt följande:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Spara princip filen.

## <a name="test-your-policy"></a>Testa principen

### <a name="upload-the-files"></a>Ladda upp filerna

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **ramverk för identitets upplevelse**.
5. På sidan anpassade principer klickar du på **Ladda upp princip**.
6. Välj **Skriv över principen om den finns** och Sök sedan efter och välj den *TrustFrameworkExtensions.xml* filen.
7. Klicka på **Överför**.

### <a name="run-the-policy"></a>Kör principen

1. Öppna registrerings-eller inloggnings principen. Till exempel *B2C_1A_signup_signin*.
2. För **program** väljer du ditt program som du har registrerat tidigare. Om du vill se token ska **svars-URL:** en visas `https://jwt.ms` .
3. Klicka på **Kör nu**.
4. Välj **Registrera dig nu**, ange en e-postadress och ange ett nytt lösen ord. Vägledning ges om lösen ords begränsningar. Ange användar informationen och klicka sedan på **skapa**. Du bör se innehållet i den token som returnerades.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurerar ändring av lösen ord med anpassade principer i Azure Active Directory B2C](custom-policy-password-change.md).
- Lär dig mer om [predikat](predicates.md) och [PredicateValidations](predicates.md#predicatevalidations) -elementen i IEF-referensen.


::: zone-end