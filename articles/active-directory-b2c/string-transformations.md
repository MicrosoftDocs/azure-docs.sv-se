---
title: Transformerings exempel för sträng anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Exempel på sträng anspråk för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85574b7d33af6d9abfe25f5af4d811255f08ce4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452245"
---
# <a name="string-claims-transformations"></a>Transformeringar av sträng anspråk

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder sträng anspråks omvandlingar i ett Ramverks schema för identitets miljö i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Jämför två anspråk och Utlös ett undantag om de inte är lika enligt de angivna jämförelse inputClaim1, inputClaim2 och stringComparison.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | Första anspråkets typ, som ska jämföras. |
| InputClaim | inputClaim2 | sträng | Andra anspråkets typ, som ska jämföras. |
| InputParameter | stringComparison | sträng | sträng jämförelse, ett av värdena: ordinal, OrdinalIgnoreCase. |

Omvandlingen av **AssertStringClaimsAreEqual** -anspråk körs alltid från en [teknisk verifierings profil](validation-technical-profile.md) som anropas av en [självkontrollerad teknisk profil](self-asserted-technical-profile.md)eller en [DisplayConrtol](display-controls.md). `UserMessageIfClaimsTransformationStringsAreNotEqual`Metadata för en självkontrollerad teknisk profil styr det fel meddelande som visas för användaren. Fel meddelandena kan [lokaliseras](localization-string-ids.md#claims-transformations-error-messages).


![AssertStringClaimsAreEqual-körning](./media/string-transformations/assert-execution.png)

Du kan använda den här anspråks omvandlingen för att se till att två ClaimTypes har samma värde. Annars genereras ett fel meddelande. I följande exempel kontrol leras att **strongAuthenticationEmailAddress** claimType är lika med **e-** postclaimtype. Annars genereras ett fel meddelande.

```xml
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


Den tekniska profilen för inloggning som inte är **interaktiv** anropar **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** Claims-transformeringen.
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Den självkontrollerade tekniska profilen anropar verifierings **inloggningen-inaktiv** teknisk profil.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Indataparametrar:
  - **stringComparison**: ordinalIgnoreCase
- Resultat: fel utlöst

## <a name="changecase"></a>ChangeCase

Ändrar Skift läget för det angivna anspråket till lägre eller versaler beroende på operatören.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | Den ClaimType som ska ändras. |
| InputParameter | toCase | sträng | Ett av följande värden: `LOWER` eller `UPPER` . |
| OutputClaim | outputClaim | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

Använd den här anspråks omvandlingen för att ändra sträng-ClaimType till lägre eller versaler.

```xml
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **e-post**: SomeOne@contoso.com
- Indataparametrar:
    - **toCase**: lägre
- Utgående anspråk:
  - **e-post**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Skapar ett sträng anspråk från den angivna Indataparametern i omvandlingen.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
|----- | ----------------------- | --------- | ----- |
| InputParameter | värde | sträng | Strängen som ska anges. Den här Indataparametern stöder [omvandlings uttryck för sträng anspråk](string-transformations.md#string-claim-transformations-expressions). |
| OutputClaim | createdClaim | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats, med det angivna värdet i indataparametern. |

Använd den här anspråks omvandlingen för att ange ett sträng värde för ClaimType.

```xml
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Indataparameter:
    - **värde**: contoso-villkor för tjänsten...
- Utgående anspråk:
    - **createdClaim**: TOS-claimType innehåller "användar villkoren för contoso..." värde.

## <a name="copyclaimifpredicatematch"></a>CopyClaimIfPredicateMatch

Kopiera värdet för ett anspråk till ett annat om värdet för indata-anspråket matchar predikatet utgående anspråk. 

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | Anspråks typen, som ska kopieras. |
| OutputClaim | outputClaim | sträng | Anspråks typen som skapas efter att den här anspråks omvandlingen har anropats. Värdet för det inmatade anspråket kontrol leras mot detta Claim-predikat. |

I följande exempel kopieras värdet för signInName-anspråk till anspråket telefonnummer, endast om signInName är ett telefonnummer. För det fullständiga exemplet, se [telefonnummer eller inloggnings](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/Phone_Email_Base.xml) start paket princip för e-post.

```xml
<ClaimsTransformation Id="SetPhoneNumberIfPredicateMatch" TransformationMethod="CopyClaimIfPredicateMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="signInName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>Exempel 1

- Inmatade anspråk:
    - **inputClaim**: bob@contoso.com
- Utgående anspråk:
    - **outputClaim**: utgående anspråk ändras inte från det ursprungliga värdet.

### <a name="example-2"></a>Exempel 2

- Inmatade anspråk:
    - **inputClaim**: + 11234567890
- Utgående anspråk:
    - **outputClaim**: + 11234567890

## <a name="compareclaims"></a>CompareClaims

Avgör om ett sträng anspråk är lika med ett annat. Resultatet är en ny boolesk ClaimType med värdet `true` eller `false` .

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | Första anspråks typen, som ska jämföras. |
| InputClaim | inputClaim2 | sträng | Andra anspråks typen, som ska jämföras. |
| InputParameter | operator | sträng | Möjliga värden: `EQUAL` eller `NOT EQUAL` . |
| InputParameter | ignoreCase | boolean | Anger om jämförelsen ska ignorera Skift läget för strängarna som jämförs. |
| OutputClaim | outputClaim | boolean | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

Använd den här anspråks omvandlingen för att kontrol lera om ett anspråk är lika med ett annat anspråk. Till exempel kontrollerar följande anspråksbaserad omvandling om värdet för **e-** postanspråket är lika med **Verified.email** -anspråket.

```xml
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Indataparametrar:
    - **operator**: inte lika med
    - **ignoreCase**: sant
- Utgående anspråk:
    - **outputClaim**: sant

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Anger om ett anspråks värde är lika med värdet för indataparametern.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | sträng | Anspråkets typ, som ska jämföras. |
| InputParameter | operator | sträng | Möjliga värden: `EQUAL` eller `NOT EQUAL` . |
| InputParameter | compareTo | sträng | sträng jämförelse, ett av värdena: ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Anger om jämförelsen ska ignorera Skift läget för strängarna som jämförs. |
| OutputClaim | outputClaim | boolean | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

Du kan använda den här anspråks omvandlingen för att kontrol lera om ett anspråk motsvarar ett värde som du har angett. Till exempel kontrollerar följande anspråksbaserad omvandling om värdet för **termsOfUseConsentVersion** -anspråket är lika med `v1` .

```xml
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel
- Inmatade anspråk:
    - **inputClaim1**: v1
- Indataparametrar:
    - **compareTo**: v1
    - **operator**: lika med
    - **ignoreCase**: sant
- Utgående anspråk:
    - **outputClaim**: sant

## <a name="createrandomstring"></a>CreateRandomString

Skapar en slumpmässig sträng med slump tals generatorn. Om slump tals generatorn är av typen `integer` kan du ange en start parameter och ett maximalt antal. Med en valfri sträng format parameter kan utdata formateras med den, och en valfri base64-parameter anger om utdata är Base64-kodad randomGeneratorType [GUID, Integer] outputClaim (sträng).

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | sträng | Anger det slumpmässiga värdet som ska genereras, `GUID` (globalt unikt ID) eller `INTEGER` (ett tal). |
| InputParameter | stringFormat | sträng | Valfritt Formatera det slumpmässiga värdet. |
| InputParameter | base64 | boolean | Valfritt Omvandla det slumpmässiga värdet till base64. Om sträng format används kodas värdet efter sträng format till base64. |
| InputParameter | maximumNumber | int | Valfritt `INTEGER` Endast för randomGeneratorType. Ange det maximala antalet. |
| InputParameter | dirigeringsrouter  | int | Valfritt `INTEGER` Endast för randomGeneratorType. Ange start värde för det slumpmässiga värdet. Obs! samma utsäde ger samma sekvens med slumpmässiga tal. |
| OutputClaim | outputClaim | sträng | Den ClaimTypes som ska skapas efter att den här anspråks omvandlingen har anropats. Det slumpmässiga värdet. |

I följande exempel skapas ett globalt unikt ID. Den här anspråks omvandlingen används för att skapa det slumpmässiga UPN-namnet (användar Principens namn).

```xml
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exempel

- Indataparametrar:
    - **randomGeneratorType**: GUID
- Utgående anspråk:
    - **outputClaim**: bc8bedd2-AAA3-411E-bdee-2f1810b73dfc

I följande exempel genereras ett heltals slump värde mellan 0 och 1000. Värdet är formaterat till OTP_ {slump värde}.

```xml
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Indataparametrar:
    - **randomGeneratorType**: heltal
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **base64**: falskt
- Utgående anspråk:
    - **outputClaim**: OTP_853


## <a name="formatlocalizedstring"></a>FormatLocalizedString

Formatera flera anspråk enligt en angiven lokaliserad format sträng. Den här omvandlingen använder C#- `String.Format` metoden.


| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaims |  |sträng | Insamlingen av ingångs anspråk som fungerar som sträng format {0} , {1} {2} parametrar. |
| InputParameter | stringFormatId | sträng |  `StringId`För en [lokaliserad sträng](localization.md).   |
| OutputClaim | outputClaim | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

> [!NOTE]
> Den största tillåtna storleken för sträng format är 4000.

Så här använder du omvandling av FormatLocalizedString-anspråk:

1. Definiera en [lokaliserings sträng](localization.md)och koppla den till en [självkontrollerad teknisk profil](self-asserted-technical-profile.md).
1. `ElementType`För `LocalizedString` elementet måste anges till `FormatLocalizedStringTransformationClaimType` .
1. `StringId`Är en unik identifierare som du definierar och använder den senare i din anspråks omvandling `stringFormatId` .
1. I omvandling för anspråk anger du listan över anspråk som ska ställas in med den lokaliserade strängen. Ange sedan `stringFormatId` till `StringId` för det lokaliserade sträng elementet. 
1. I en [självkontrollerad teknisk profil](self-asserted-technical-profile.md), eller en transformering av [visnings kontroll](display-controls.md) indata eller utgående anspråk, gör du en referens till din anspråks omvandling.


I följande exempel genereras ett fel meddelande när ett konto redan finns i katalogen. Exemplet definierar lokaliserade strängar för engelska (standard) och spanska.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">Este correo electrónico "{0}" ya es una cuenta de esta organización. Haga clic en Siguiente para iniciar sesión con esa cuenta.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

Transformationen Claims skapar ett svars meddelande baserat på den lokaliserade strängen. Meddelandet innehåller användarens e-postadress som är inbäddad i den lokaliserade STING- *ResponseMessge_EmailExists*.

```xml
<ClaimsTransformation Id="SetResponseMessageForEmailAlreadyExists" TransformationMethod="FormatLocalizedString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormatId" DataType="string" Value="ResponseMessge_EmailExists" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: sarah@contoso.com
- Indataparametrar:
    - **stringFormat**: ResponseMessge_EmailExists
- Utgående anspråk:
  - **outputClaim**: e-postmeddelandet sarah@contoso.com är redan ett konto i den här organisationen. Klicka på Nästa för att logga in med det kontot.


## <a name="formatstringclaim"></a>FormatStringClaim

Formatera ett anspråk enligt den angivna format strängen. Den här omvandlingen använder C#- `String.Format` metoden.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |sträng |Den ClaimType som fungerar som sträng format {0} parameter. |
| InputParameter | stringFormat | sträng | Sträng formatet, inklusive {0}  parametern. Den här Indataparametern stöder [omvandlings uttryck för sträng anspråk](string-transformations.md#string-claim-transformations-expressions).  |
| OutputClaim | outputClaim | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

> [!NOTE]
> Den största tillåtna storleken för sträng format är 4000.

Använd den här anspråks omvandlingen för att formatera en sträng med en parameter {0} . I följande exempel skapas ett **userPrincipalName**. Alla tekniska profiler för sociala identitets leverantörer, till exempel `Facebook-OAUTH` anropar **CreateUserPrincipalName** för att generera ett **userPrincipalName**.

```xml
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Indataparametrar:
    - **stringFormat**: cpim_ {0} @ {RelyingPartyTenantId}
- Utgående anspråk:
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formatera två anspråk enligt den angivna format strängen. Den här omvandlingen använder C#- `String.Format` metoden.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |sträng | Den ClaimType som fungerar som sträng format {0} parameter. |
| InputClaim | inputClaim | sträng | Den ClaimType som fungerar som sträng format {1} parameter. |
| InputParameter | stringFormat | sträng | Sträng formatet, inklusive {0} {1} parametrarna och. Den här Indataparametern stöder [omvandlings uttryck för sträng anspråk](string-transformations.md#string-claim-transformations-expressions).   |
| OutputClaim | outputClaim | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats. |

> [!NOTE]
> Den största tillåtna storleken för sträng format är 4000.

Använd den här anspråks omvandlingen för att formatera en sträng med två parametrar {0} och {1} . I följande exempel skapas ett **DisplayName** med det angivna formatet:

```xml
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim1**: Johan
    - **inputClaim2**: Fernando
- Indataparametrar:
    - **stringFormat**: {0}{1}
- Utgående anspråk:
    - **outputClaim**: Johan Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Kopierar lokaliserade strängar till anspråk.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | Namnet på den lokaliserade strängen | sträng | Lista med anspråks typer som skapas efter att den här anspråks omvandlingen har anropats. |

Så här använder du omvandling av GetLocalizedStringsTransformation-anspråk:

1. Definiera en [lokaliserings sträng](localization.md) och koppla den till en [självkontrollerad teknisk profil](self-asserted-technical-profile.md).
1. `ElementType`För `LocalizedString` elementet måste anges till `GetLocalizedStringsTransformationClaimType` .
1. `StringId`Är en unik identifierare som du definierar och använder den senare i din anspråks omvandling.
1. I omvandling för anspråk anger du listan över anspråk som ska ställas in med den lokaliserade strängen. `ClaimTypeReferenceId`Är en referens till en claimType som redan har definierats i ClaimsSchema-avsnittet i principen. `TransformationClaimType`Är namnet på den lokaliserade strängen som definieras i `StringId` `LocalizedString` elementet.
1. I en [självkontrollerad teknisk profil](self-asserted-technical-profile.md), eller en transformering av [visnings kontroll](display-controls.md) indata eller utgående anspråk, gör du en referens till din anspråks omvandling.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

I följande exempel visas e-postmeddelandets ämne, brödtext, ditt kod meddelande och signaturen för e-postmeddelandet, från lokaliserade strängar. Dessa anspråk används senare av mallen för anpassad e-postverifiering.

Definiera lokaliserade strängar för engelska (standard) och spanska.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

Transformationen Claims anger värdet för anspråks typen som *omfattas* av värdet för `StringId` *email_subject*.

```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Utgående anspråk:
  - **ämne**: e-postverifierings kod för Contoso-konto
  - **meddelande**: Tack för att du verifierar ditt konto!
  - **codeIntro**: din kod är
  - **signatur**: vänliga hälsningar


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Söker efter ett objekt från en **begränsnings** samling för anspråk.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | sträng | Det anspråk som innehåller den text som ska slås upp i **restrictionValueClaim** -anspråk med **begränsnings** samlingen.  |
| OutputClaim | restrictionValueClaim | sträng | Det anspråk som innehåller **begränsnings** samlingen. När anspråks omvandlingen har anropats innehåller värdet för det här anspråket värdet för det valda objektet. |

Följande exempel söker efter beskrivningen av fel meddelandet baserat på fel nyckeln. **ResponseMsg** -anspråket innehåller en samling fel meddelanden som ska visas för slutanvändaren eller som skickas till den förlitande parten.

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
Transformationen Claims söker efter texten i objektet och returnerar dess värde. Om begränsningen lokaliseras med hjälp av `<LocalizedCollection>` anspråks omvandlingen returnerar det lokaliserade värdet.

```xml
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **mapFromClaim**: B2C_V1_90001
- Utgående anspråk:
    - **restrictionValueClaim**: det går inte att logga in eftersom du är en mindre.

## <a name="lookupvalue"></a>LookupValue

Leta upp ett anspråks värde från en lista med värden baserat på värdet för ett annat anspråk.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | sträng | Det anspråk som innehåller uppslags värdet |
| InputParameter | |sträng | Insamling av indataparametrar. |
| InputParameter | errorOnFailedLookup | boolean | Kontrollerar om ett fel returneras när det inte finns någon matchande sökning. |
| OutputClaim | inputParameterId | sträng | Den ClaimTypes som ska skapas efter att den här anspråks omvandlingen har anropats. Värdet för matchningen `Id` . |

I följande exempel söker du efter domän namnet i en av indataparametrar-samlingarna. Omvandlingen av anspråk söker upp domän namnet i identifieraren och returnerar dess värde (ett program-ID).

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputParameterId**: test.com
- Indataparametrar:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: falskt
- Utgående anspråk:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

När `errorOnFailedLookup` Indataparametern är inställt `true` på,  utförs omvandlingen av sökanspråk alltid från en [teknisk verifierings profil](validation-technical-profile.md) som anropas av en [självkontrollerad teknisk profil](self-asserted-technical-profile.md)eller en [DisplayConrtol](display-controls.md). `LookupNotFound`Metadata för en självkontrollerad teknisk profil styr det fel meddelande som visas för användaren.

![AssertStringClaimsAreEqual-körning](./media/string-transformations/assert-execution.png)

I följande exempel söker du efter domän namnet i en av indataparametrar-samlingarna. Transformationen Claims letar upp domän namnet i identifieraren och returnerar dess värde (ett program-ID) eller genererar ett fel meddelande.

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputParameterId**: Live.com
- Indataparametrar:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: sant
- Fel:
    - Ingen matchning hittades för det angivna anspråks värdet i listan med ID: n för indataparametrar och errorOnFailedLookup är true.


## <a name="nullclaim"></a>NullClaim

Rensa värdet för ett angivet anspråk.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | sträng | Anspråkets värde är inställt på NULL. |

Använd den här anspråks omvandlingen för att ta bort onödiga data från egenskaps påsen för anspråk så att sessionens cookie blir mindre. I följande exempel tas värdet för `TermsOfService` anspråks typen bort.

```xml
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Inmatade anspråk:
    - **outputClaim**: Välkommen till contoso-appen. Om du fortsätter att söka efter och använda den här webbplatsen, samtycker du till att följa och vara bindande för följande villkor...
- Utgående anspråk:
    - **outputClaim**: null

## <a name="parsedomain"></a>ParseDomain

Hämtar domän delen av en e-postadress.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | sträng | Den ClaimType som innehåller e-postadressen. |
| OutputClaim | domän | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats – domänen. |

Använd den här anspråks omvandlingen för att parsa domän namnet efter användarens @-symbol. Följande anspråks omvandling visar hur du kan parsa domän namnet från ett **e-** postanspråk.

```xml
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **EmailAddress**: joe@outlook.com
- Utgående anspråk:
    - **domän**: Outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Kontrollerar att en sträng anspråks `claimToMatch` `matchTo` -och indataparameter är lika, och anger de utgående anspråken med värdet som finns i `outputClaimIfMatched` Indataparametern, tillsammans med jämförelse resultat för utgående anspråk, som ska anges som `true` eller `false` baserat på resultatet av jämförelsen.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | sträng | Anspråks typen, som ska jämföras. |
| InputParameter | matchTo | sträng | Det reguljära uttrycket som ska matchas. |
| InputParameter | outputClaimIfMatched | sträng | Värdet som ska anges om strängarna är lika. |
| InputParameter | extractGroups | boolean | Valfritt Anger om regex matchningen ska extrahera grupp värden. Möjliga värden: `true` , eller `false` (standard). | 
| OutputClaim | outputClaim | sträng | Om reguljärt uttryck matchar, innehåller det här utgående anspråket värdet för `outputClaimIfMatched` indataparametern. Eller null, om ingen matchning. |
| OutputClaim | regexCompareResultClaim | boolean | Den reguljära uttrycks typen för resultat av utgående anspråk, som ska anges som `true` eller `false` baserat på resultatet av matchningen. |
| OutputClaim| Anspråkets namn| sträng | Om extractGroups-Indataparametern har angetts till true, är listan över anspråks typer som skapas efter att den här anspråks omvandlingen har anropats. Namnet på claimType måste matcha regex-gruppens namn. | 

### <a name="example-1"></a>Exempel 1

Kontrollerar om det angivna telefonnumret är giltigt, baserat på vanligt uttrycks mönster för telefonnummer.

```xml
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Inmatade anspråk:
    - **claimToMatch**: "64854114520"
- Indataparametrar:
    - **matchTo**: "^ [0-9] {4,16} $"
    - **outputClaimIfMatched**: "isPhone"
- Utgående anspråk:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: sant

### <a name="example-2"></a>Exempel 2

Kontrollerar om den angivna e-postadressen är giltig och returnerar e-postaliaset.

```xml
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- Inmatade anspråk:
    - **claimToMatch**: " emily@contoso.com "
- Indataparametrar:
    - **matchTo**: `(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**: "isEmail"
    - **extractGroups**: sant
- Utgående anspråk:
    - **outputClaim**: "isEmail"
    - **regexCompareResultClaim**: sant
    - **köalias**: Emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Kontrollerar att en sträng anspråks `matchTo` -och indataparameter är lika, och anger de utgående anspråken med värdet som finns i `stringMatchMsg` `stringMatchMsgCode` parametrarna och indataparametrarna, tillsammans med jämförelse resultat för utgående anspråk, som ska anges som `true` eller `false` baserat på resultatet av jämförelsen.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | Anspråks typen, som ska jämföras. |
| InputParameter | matchTo | sträng | Strängen som ska jämföras med `inputClaim` . |
| InputParameter | stringComparison | sträng | Möjliga värden: `Ordinal` eller `OrdinalIgnoreCase` . |
| InputParameter | stringMatchMsg | sträng | Det första värdet som ska anges om strängarna är lika. |
| InputParameter | stringMatchMsgCode | sträng | Det andra värdet som ska anges om strängarna är lika. |
| OutputClaim | outputClaim1 | sträng | Om strängarna är lika med, innehåller det här utgående anspråket värdet för `stringMatchMsg` indataparametern. |
| OutputClaim | outputClaim2 | sträng | Om strängarna är lika med, innehåller det här utgående anspråket värdet för `stringMatchMsgCode` indataparametern. |
| OutputClaim | stringCompareResultClaim | boolean | Anspråks typen jämför resultat, som ska anges som `true` eller `false` baserat på jämförelse resultatet. |

Du kan använda den här anspråks omvandlingen för att kontrol lera om ett anspråk motsvarar det värde som du har angett. Till exempel kontrollerar följande anspråksbaserad omvandling om värdet för **termsOfUseConsentVersion** -anspråket är lika med `v1` . Om ja, ändra värdet till `v2` .

```xml
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: v1
- Indataparametrar:
    - **matchTo**: v1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: TOS uppgraderas till v2
- Utgående anspråk:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: TOS uppgraderas till v2
    - **stringCompareResultClaim**: sant

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Kontrollerar att en sträng anspråks `matchTo` -och indataparameter är lika, och anger de utgående anspråken med värdet som finns i `outputClaimIfMatched` Indataparametern, tillsammans med jämförelse resultat för utgående anspråk, som ska anges som `true` eller `false` baserat på resultatet av jämförelsen.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | sträng | Anspråks typen, som ska jämföras. |
| InputParameter | matchTo | sträng | Strängen som ska jämföras med inputClaim. |
| InputParameter | stringComparison | sträng | Möjliga värden: `Ordinal` eller `OrdinalIgnoreCase` . |
| InputParameter | outputClaimIfMatched | sträng | Värdet som ska anges om strängarna är lika. |
| OutputClaim | outputClaim | sträng | Om strängarna är lika med, innehåller det här utgående anspråket värdet för `outputClaimIfMatched` indataparametern. Eller null, om strängarna inte matchar. |
| OutputClaim | stringCompareResultClaim | boolean | Anspråks typen jämför resultat, som ska anges som `true` eller `false` baserat på jämförelse resultatet. |

Till exempel kontrollerar följande anspråksbaserad omvandling om värdet för **ageGroup** -anspråk är lika med `Minor` . Om ja, returnerar du värdet till `B2C_V1_90001` .

```xml
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **claimToMatch**: mindre
- Indataparametrar:
    - **matchTo**: mindre
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Utgående anspråk:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: sant


## <a name="stringcontains"></a>StringContains

Avgör om en angiven under sträng inträffar inom det inmatade anspråket. Resultatet är en ny boolesk ClaimType med värdet `true` eller `false` . `true` om värde parametern förekommer i den här strängen, annars, `false` .

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | Anspråks typen, som ska genomsökas. |
|InputParameter|innehåller|sträng|Det värde som ska genomsökas.|
|InputParameter|ignoreCase|sträng|Anger om jämförelsen ska ignorera Skift läge för strängen som jämförs.|
| OutputClaim | outputClaim | sträng | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. En boolesk indikator om under strängen inträffar i det inmatade anspråket. |

Använd den här anspråks omvandlingen för att kontrol lera om en sträng anspråks typ innehåller en under sträng. Följande exempel kontrollerar om `roles` sträng anspråks typen innehåller värdet **administratör**.

```xml
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: "admin, god kännare, redigerare"
- Indataparametrar:
    - **innehåller**: "admin"
    - **ignoreCase**: sant
- Utgående anspråk:
    - **outputClaim**: sant

## <a name="stringsubstring"></a>StringSubstring

Extraherar delar av en sträng anspråks typ, med början vid tecknet vid den angivna positionen och returnerar det angivna antalet tecken.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | Anspråks typen, som innehåller strängen. |
| InputParameter | Start | int | Den nollbaserade start tecken positionen för en under sträng i den här instansen. |
| InputParameter | length | int | Antalet tecken i under strängen. |
| OutputClaim | outputClaim | boolean | En sträng som motsvarar den del sträng som börjar vid start index i denna instans, eller tom om start index är lika med längden på den här instansen och längden är noll. |

Hämta till exempel telefonnumret land/region för telefonnummer.


```xml
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: "+ 1644114520"
- Indataparametrar:
    - **Start index**: 0
    - **längd**: 2
- Utgående anspråk:
    - **outputClaim**: "+ 1"

## <a name="stringreplace"></a>StringReplace

Söker efter en anspråks typ sträng för ett angivet värde och returnerar en ny anspråks typ sträng där alla förekomster av en angiven sträng i den aktuella strängen ersätts med en annan angiven sträng.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | Anspråks typen, som innehåller strängen. |
| InputParameter | Gammalt | sträng | Strängen som ska genomsökas. |
| InputParameter | newValue | sträng | Strängen som ska ersätta alla förekomster av `oldValue` |
| OutputClaim | outputClaim | boolean | En sträng som motsvarar den aktuella strängen, förutom att alla instanser av oldValue ersätts med newValue. Om oldValue inte hittas i den aktuella instansen returnerar metoden den aktuella instansen oförändrad. |

Du kan till exempel normalisera ett telefonnummer genom att ta bort `-` tecknen


```xml
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: "+ 164-411-452-054"
- Indataparametrar:
    - **OldValue**: "-"
    - **newValue**: ""
- Utgående anspråk:
    - **outputClaim**: "+ 164411452054"

## <a name="stringjoin"></a>StringJoin

Sammanfogar elementen i en angiven typ av sträng samlings anspråk med hjälp av den angivna avgränsaren mellan varje element eller medlem.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | En samling som innehåller de strängar som ska sammanfogas. |
| InputParameter | avgränsare | sträng | Strängen som ska användas som avgränsare, till exempel kommatecken `,` . |
| OutputClaim | outputClaim | sträng | En sträng som består av medlemmarna i `inputClaim` sträng samlingen, avgränsade med `delimiter` indataparametern. |

I följande exempel används en sträng uppsättning med användar roller och konverterar den till en kommaavgränsad sträng. Du kan använda den här metoden för att lagra en sträng samling i Azure AD-användarkontot. Senare, när du läser kontot från katalogen, använder `StringSplit` du för att konvertera kommatecken sträng tillbaka till sträng samling.

```xml
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **inputClaim**: ["admin", "författare", "läsare"]
- Indataparametrar:
  - **avgränsare**: ","
- Utgående anspråk:
  - **outputClaim**: "admin, Author, Reader"


## <a name="stringsplit"></a>StringSplit

Returnerar en sträng mat ris som innehåller under strängarna i den här instansen som är avgränsade med element i en angiven sträng.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | sträng | En sträng anspråks typ som innehåller de under strängar som ska delas. |
| InputParameter | avgränsare | sträng | Strängen som ska användas som avgränsare, till exempel kommatecken `,` . |
| OutputClaim | outputClaim | stringCollection | En sträng samling vars element innehåller del strängarna i den här strängen som avgränsas med `delimiter` indataparametern. |

I följande exempel används en kommaavgränsad sträng för användar roller och konverterar den till en sträng samling.

```xml
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **inputClaim**: "admin, Author, Reader"
- Indataparametrar:
  - **avgränsare**: ","
- Utgående anspråk:
  - **outputClaim**: ["admin", "författare", "läsare"]

## <a name="string-claim-transformations-expressions"></a>Uttryck för omvandling av sträng anspråk
Anspråk omvandlings uttryck i Azure AD B2C anpassade principer ger Sammanhangs information om klient-ID och ID för teknisk profil.

  | Uttryck | Beskrivning | Exempel |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | Det tekniska profil-namnet. | Facebook – OAUTH |
 | `{RelyingPartyTenantId}` | Klient-ID för den förlitande part principen. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | Klient-ID för förtroende ramverket. | your-tenant.onmicrosoft.com |
