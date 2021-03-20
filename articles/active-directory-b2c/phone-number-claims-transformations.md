---
title: Antal anspråk för telefonnummer i anpassade principer
titleSuffix: Azure AD B2C
description: Anpassad princip referens för inloggnings uppgifter för telefonnummer i Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e175a81efc1ab0950c1fda314efb206ff97a2b7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85385390"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definiera anspråks omvandlingar för telefonnummer i Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller en referens och exempel på hur du kan använda anspråks omvandlingarna för ett identitets Miljös schema i Azure Active Directory B2C (Azure AD B2C). Mer information om anspråks omvandlingar i allmänhet finns i [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

Konverterar en datatyp `phoneNumber` till en `string` datatyp.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  ClaimType som ska konverteras till en sträng. |
| OutputClaim | phoneNumberString | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

I det här exemplet konverteras cellPhoneNumber-anspråket med värde typen `phoneNumber` till ett mobilt anspråk med värde typen `string` .

```xml
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **telefonnummer**: + 11234567890 (telefonnummer)
- Utgående anspråk:
  - **phoneNumberString**: + 11234567890 (sträng)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Den här anspråks omvandlingen verifierar formatet för telefonnumret. Om det är i ett giltigt format ändrar du det till ett standardformat som används av Azure AD B2C. Om det angivna telefonnumret inte har ett giltigt format returneras ett fel meddelande.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | sträng |  Sträng anspråket för telefonnumret. Telefonnumret måste vara i internationellt format, slutföras med en inledande "+"-och lands-/regions kod. Om indata `country` -anspråk anges, är telefonnumret i lokalt format (utan lands-/region kod). |
| InputClaim | land | sträng | Valfritt Sträng anspråket för lands-/regionkoden för telefonnumret i ISO3166-format (ISO-3166 land/region-kod). |
| OutputClaim | outputClaim | phoneNumber | Resultatet av denna omvandling av anspråk. |

Omvandlingen av **ConvertStringToPhoneNumberClaim** -anspråk körs alltid från en [teknisk verifierings profil](validation-technical-profile.md) som anropas av en [självkontrollerad teknisk profil](self-asserted-technical-profile.md) eller [visnings kontroll](display-controls.md). **UserMessageIfClaimsTransformationInvalidPhoneNumber** -metadata för självkontrollerad teknisk profil styr det fel meddelande som visas för användaren.

![Diagram över sökväg för körning av fel meddelande](./media/phone-authentication/assert-execution.png)

Du kan använda den här anspråks omvandlingen för att se till att det angivna sträng anspråket är ett giltigt telefonnummer. Annars genereras ett fel meddelande. I följande exempel kontrol leras att **phoneString** -claimType faktiskt är ett giltigt telefonnummer och returnerar sedan telefonnumret i standardformatet för Azure AD B2C. Annars genereras ett fel meddelande.

```xml
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Den självkontrollerade tekniska profilen som anropar den tekniska verifierings profilen som innehåller den här anspråks omvandlingen kan definiera fel meddelandet.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Exempel 1

- Inmatade anspråk:
  - **phoneNumberString**: 033 456-7890
  - **land**: dk
- Utgående anspråk:
  - **outputClaim**: + 450334567890

### <a name="example-2"></a>Exempel 2

- Inmatade anspråk:
  - **phoneNumberString**: + 1 (123) 456-7890
- Utgående anspråk:
  - **outputClaim**: + 11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Detta extraherar lands-/regionkoden och det nationella numret från det inskickade anspråket och returnerar ett undantag om det angivna telefonnumret inte är giltigt.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | sträng | Telefonnumrets sträng anspråk. Telefonnumret måste vara i internationellt format, slutföras med en inledande "+"-och lands-/regions kod. |
| InputParameter | throwExceptionOnFailure | boolean | Valfritt En parameter som anger om ett undantag genereras när telefonnumret är ogiltigt. Standardvärdet är false. |
| InputParameter | countryCodeType | sträng | Valfritt En parameter som anger typen av lands-/regionkod i utgående anspråk. Tillgängliga värden är **CallingCode** (den internationella anrops koden för ett land/en region, t. ex. + 1) eller **ISO3166** (ISO-3166 land/region-kod). |
| OutputClaim | nationalNumber | sträng | Sträng anspråket för det nationella numret för telefonnumret. |
| OutputClaim | countryCode | sträng | Sträng anspråket för telefonnumret för land/region. |


Om omvandlingen av **GetNationalNumberAndCountryCodeFromPhoneNumberString** -anspråk körs från en [teknisk verifierings profil](validation-technical-profile.md) som anropas av en [självkontrollerad teknisk profil](self-asserted-technical-profile.md) eller en [åtgärd för att Visa kontroll](display-controls.md#display-control-actions), styr **UserMessageIfPhoneNumberParseFailure** för självkontrollerad teknisk profil det fel meddelande som visas för användaren.

![Diagram över sökväg för körning av fel meddelande](./media/phone-authentication/assert-execution.png)

Du kan använda den här anspråks omvandlingen för att dela upp ett fullständigt telefonnummer i lands-/region koden och det nationella numret. Om det angivna telefonnumret inte är giltigt kan du välja att utlösa ett fel meddelande.

I följande exempel försöker du dela telefonnumret i det nationella numret och land/region-koden. Om telefonnumret är giltigt kommer telefonnumret att åsidosättas av det nationella numret. Om telefonnumret inte är giltigt, genereras inget undantag och telefonnumret har fortfarande sitt ursprungliga värde.

```xml
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Den självkontrollerade tekniska profilen som anropar den tekniska verifierings profilen som innehåller den här anspråks omvandlingen kan definiera fel meddelandet.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Exempel 1

- Inmatade anspråk:
  - **telefonnummer**: + 49 (123) 456-7890
- Indataparametrar:
  - **throwExceptionOnFailure**: falskt
  - **countryCodeType**: ISO3166
- Utgående anspråk:
  - **nationalNumber**: 1234567890
  - **countryCode**: de

### <a name="example-2"></a>Exempel 2

- Inmatade anspråk:
  - **telefonnummer**: + 49 (123) 456-7890
- Indataparametrar
  - **throwExceptionOnFailure**: falskt
  - **countryCodeType**: CallingCode
- Utgående anspråk:
  - **nationalNumber**: 1234567890
  - **countryCode**: + 49
