---
title: Lokalisering-Azure Active Directory B2C
description: Ange lokaliserings element för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3a5afcd8c0ef0c31353cd2369ead332675c9877f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453129"
---
# <a name="localization-element"></a>Lokaliserings element

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Lokaliserings** elementet gör att du kan stödja flera språk eller språk i principen för användar resan. Lokaliserings stödet i principer gör att du kan:

- Konfigurera den explicita listan över de språk som stöds i en princip och välj ett standard språk.
- Ange språkspecifika strängar och samlingar.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

**Lokaliserings** elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Enabled | Inga | Möjliga värden: `true` eller `false` . |

**Lokaliserings** elementet innehåller följande XML-element

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Lista över språk som stöds. |
| LocalizedResources | 0: n | Lista över lokaliserade resurser. |

## <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| DefaultLanguage | Ja | Det språk som ska användas som standard för lokaliserade resurser. |
| MergeBehavior | Inga | Ett uppräknings värde för värden som slås samman med en ClaimType som finns i en överordnad princip med samma identifierare. Använd det här attributet när du skriver över ett anspråk som anges i bas principen. Möjliga värden: `Append` , `Prepend` , eller `ReplaceAll` . `Append`Värdet anger att data uppsättningen som finns ska läggas till i slutet av den samling som anges i den överordnade principen. `Prepend`Värdet anger att data uppsättningen som finns ska läggas till innan den samling som anges i den överordnade principen. `ReplaceAll`Värdet anger att data uppsättningen som definieras i den överordnade principen ska ignoreras, med i stället de data som definierats i den aktuella principen. |

### <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Visar innehåll som följer en språktagg enligt RFC 5646-taggar för att identifiera språk. |

## <a name="localizedresources"></a>LocalizedResources

**LocalizedResources** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare som används för att identifiera lokaliserade resurser unikt. |

**LocalizedResources** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definierar hela samlingar i olika kulturer. En samling kan ha olika antal objekt och olika strängar för olika kulturer. Exempel på samlingar är uppräkningar som visas i anspråks typer. Till exempel visas en lista över länder/regioner för användaren i en listruta. |
| LocalizedStrings | 0: n | Definierar alla strängar, förutom de strängar som visas i samlingar, i olika kulturer. |

### <a name="localizedcollections"></a>LocalizedCollections

**LocalizedCollections** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1: n | Lista över språk som stöds. |

#### <a name="localizedcollection"></a>LocalizedCollection

**LocalizedCollection** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ElementType | Ja | Refererar till ett ClaimType-element eller ett användar gränssnitts element i princip filen. |
| ElementId | Ja | En sträng som innehåller en referens till en anspråks typ som redan har definierats i ClaimsSchema-avsnittet som används om **ElementType** har angetts till en claimType. |
| TargetCollection | Ja | Mål samlingen. |

**LocalizedCollection** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Objekt | 0: n | Definierar ett tillgängligt alternativ som användaren kan välja för ett anspråk i användar gränssnittet, t. ex. ett värde i en listruta. |

Elementet **item** innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Text | Ja | Den användarvänliga visnings strängen som ska visas för användaren i användar gränssnittet för det här alternativet. |
| Värde | Ja | Det sträng anspråks värde som är associerat med att välja det här alternativet. |
| SelectByDefault | Inga | Anger om det här alternativet ska vara markerat som standard i användar gränssnittet. Möjliga värden: true eller false. |

I följande exempel visas användningen av **LocalizedCollections** -elementet. Den innehåller två **LocalizedCollection** -element, ett för engelska och ett för spanska. Båda anger **begränsnings** samlingen för anspråket `Gender` med en lista över objekt för engelska och spanska.

```xml
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

**LocalizedStrings** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | En lokaliserad sträng. |

**LocalizedString** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| ElementType | Ja | Möjliga värden: [ClaimsProvider](#claimsprovider), [claimType](#claimtype), [errormessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [FormatLocalizedStringTransformationClaimType](#formatlocalizedstringtransformationclaimtype), [predikat](#predicate), [InputValidation](#inputvalidation)eller [UxElement](#uxelement).   | 
| ElementId | Ja | Om **ElementType** är inställt på `ClaimType` , `Predicate` eller `InputValidation` , innehåller det här elementet en referens till en anspråks typ som redan har definierats i avsnittet ClaimsSchema. |
| StringId | Ja | Om **ElementType** är inställt på `ClaimType` , innehåller det här elementet en referens till ett attribut för en anspråks typ. Möjliga värden: `DisplayName` , `AdminHelpText` , eller `PatternHelpText` . `DisplayName`Värdet används för att ange visnings namn för anspråket. `AdminHelpText`Värdet används för att ange hjälp text namnet för anspråks användaren. `PatternHelpText`Värdet används för att ange hjälp texten för anspråks mönstret. Om **ElementType** är inställt på `UxElement` , innehåller det här elementet en referens till ett attribut i ett användar gränssnitts element. Om **ElementType** är inställt på `ErrorMessage` , anger det här elementet identifieraren för ett fel meddelande. Se [lokaliserings Strängs-ID: n](localization-string-ids.md) för en fullständig lista över `UxElement` identifierare.|

## <a name="elementtype"></a>ElementType

ElementType-referensen till en anspråks typ, en anspråks omvandling eller ett användar gränssnitts element i principen som ska lokaliseras.

| Element att lokalisera | ElementType | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| Namn på identitetsprovider |`ClaimsProvider`| | ID för ClaimsExchange-elementet|
| Attribut för anspråks typ|`ClaimType`|Namn på anspråks typen| Attributet för det anspråk som ska lokaliseras. Möjliga värden: `AdminHelpText` , `DisplayName` , `PatternHelpText` och `UserHelpText` .|
|Felmeddelande|`ErrorMessage`||ID för fel meddelandet |
|Kopierar lokaliserade strängar till anspråk|`GetLocalizedStringsTra nsformationClaimType`||Namnet på det utgående anspråket|
|Predikat användar meddelande|`Predicate`|Namnet på predikatet| Attributet för predikatet som ska lokaliseras. Möjliga värden: `HelpText` .|
|Användar meddelande för predika grupp|`InputValidation`|ID för PredicateValidation-elementet.|ID för PredicateGroup-elementet. Predikatet-gruppen måste vara underordnat predikatet predikat som definieras i ElementId.|
|Element för användargränssnitt |`UxElement` | | ID för det användar gränssnitts element som ska lokaliseras.|
|[Visa kontroll](display-controls.md) |`DisplayControl` |ID för visnings kontrollen. | ID för det användar gränssnitts element som ska lokaliseras.|

## <a name="examples"></a>Exempel

### <a name="claimsprovider"></a>ClaimsProvider

ClaimsProvider-värdet används för att lokalisera ett av visnings namnen för anspråks leverantörer. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

I följande exempel visas hur du lokaliserar visnings namn för anspråks leverantörer.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

Värdet för ClaimType används för att lokalisera ett av attributen för anspråk. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

I följande exempel visas hur du lokaliserar DisplayName-, UserHelpText-och PatternHelpText-attributen för e-postanspråks typen.

```xml
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

ErrorMessage-värdet används för att lokalisera ett av system fel meddelandena. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

I följande exempel visas hur du kan lokalisera UserMessageIfClaimsPrincipalAlreadyExists-fel meddelandet.


```xml
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="formatlocalizedstringtransformationclaimtype"></a>FormatLocalizedStringTransformationClaimType

FormatLocalizedStringTransformationClaimType-värdet används för att formatera anspråk till en lokaliserad sträng. Mer information finns i [FormatLocalizedString Claims-transformering](string-transformations.md#formatlocalizedstring)


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

I följande exempel visas hur du lokaliserar sträng formatet för FormatLocalizedStringTransformationClaimType-anspråks omvandlingen.

```xml
<LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

GetLocalizedStringsTransformationClaimType-värdet används för att kopiera lokaliserade strängar till anspråk. Mer information finns i [GetLocalizedStringsTransformation Claims-transformering](string-transformations.md#getlocalizedstringstransformation)


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

I följande exempel visas hur du kan lokalisera utdata-anspråk för GetLocalizedStringsTransformation-anspråks omvandlingen.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predikatet

Predikatet-värdet används för att lokalisera ett av de [predikat](predicates.md) fel meddelandena. 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

I följande exempel visas hur du lokaliserar hjälp text för predikat.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

InputValidation-värdet används för att lokalisera ett av [PredicateValidation](predicates.md) -gruppens fel meddelanden. 

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

I följande exempel visas hur du lokaliserar en predika validerings grupp hjälp text.

```xml
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

UxElement-värdet används för att lokalisera ett av användar gränssnitts elementen. I följande exempel visas hur du kan lokalisera knapparna Fortsätt och Avbryt.

```xml
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

### <a name="displaycontrol"></a>Visa

Värdet visnings värde används för att lokalisera ett av användar gränssnitts elementen för [visnings kontroll](display-controls.md) . När den här inställningen är aktive rad tar localizedStrings ***prioritet** _ över några av _ *UxElement**-StringIDs **som ver_but_send**, **ver_but_edit**, **ver_but_resend** och **ver_but_verify**. I följande exempel visas hur du kan lokalisera knapparna skicka och verifiera. 

```xml
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
```

I avsnittet metadata i en självkontrollerad teknisk profil måste det refererade ContentDefinition ha DataUri inställt på [Page Layout version](page-layout.md) 2.1.0 eller högre. Exempel:

```xml
<ContentDefinition Id="api.selfasserted">
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.1.0</DataUri>
  ...
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om lokaliserings exempel:

- [Språk anpassning med anpassad princip i Azure Active Directory B2C](language-customization.md)
- [Språk anpassning med användar flöden i Azure Active Directory B2C](language-customization.md)
