---
title: ClaimsTransformations – Azure Active Directory B2C | Microsoft Docs
description: Definition av ClaimsTransformations-elementet i Azure Active Directory B2C för identitets upplevelsens Framework-schema.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d9bd78a80ac52d2a790537bf47e33807720349
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85202967"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsTransformations** -elementet innehåller en lista över anspråks omvandlings funktioner som kan användas i användar resor som en del av en [anpassad princip](custom-policy-overview.md). En anspråkstransformering konverterar ett specifikt anspråk till ett annat. I omvandlings deklarationen anger du metoden Transform, till exempel lägger till ett objekt i en sträng samling eller ändrar Skift läge för en sträng.

Om du vill lägga till en lista över anspråksmedvetna omvandlings funktioner som kan användas i användar resan, måste ett ClaimsTransformations XML-element deklareras under BuildingBlocks-avsnittet i principen.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

**ClaimsTransformation** -elementet innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- |-------- | ----------- |
| Id |Ja | En identifierare som används för att unikt identifiera anspråks omvandlingen. Det finns referenser till identifieraren från andra XML-element i principen. |
| TransformationMethod | Ja | Den Transformations metod som ska användas i anspråks omvandlingen. Varje anspråks omvandling har sina egna värden. I [omvandlings referensen för anspråk](#claims-transformations-reference) finns en fullständig lista över tillgängliga värden. |

## <a name="claimstransformation"></a>ClaimsTransformation

**ClaimsTransformation** -elementet innehåller följande element:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Element | Förekomster | Beskrivning |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | En lista med **InputClaim** -element som anger anspråks typer som ska tas in i anspråks omvandlingen. Vart och ett av dessa element innehåller en referens till en ClaimType som redan har definierats i ClaimsSchema-avsnittet i principen. |
| Indataparametrar | 0:1 | En lista med **InputParameter** -element som tillhandahålls som indata till anspråks omvandlingen.
| OutputClaims | 0:1 | En lista med **OutputClaim** -element som anger anspråks typer som skapas efter att ClaimsTransformation har anropats. Vart och ett av dessa element innehåller en referens till en ClaimType som redan har definierats i ClaimsSchema-avsnittet. |

### <a name="inputclaims"></a>InputClaims

**InputClaims** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| InputClaim | 1: n | En förväntad typ av Indatatyp. |

#### <a name="inputclaim"></a>InputClaim

**InputClaim** -elementet innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Ja | En referens till en ClaimType som redan har definierats i ClaimsSchema-avsnittet i principen. |
| TransformationClaimType |Ja | En identifierare som refererar till en omvandlings anspråks typ. Varje anspråks omvandling har sina egna värden. I [omvandlings referensen för anspråk](#claims-transformations-reference) finns en fullständig lista över tillgängliga värden. |

### <a name="inputparameters"></a>Indataparametrar

Elementet **indataparametrar** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| InputParameter | 1: n | En förväntad indataparameter. |

#### <a name="inputparameter"></a>InputParameter

| Attribut | Krävs |Beskrivning |
| --------- | ----------- |----------- |
| Id | Ja | En identifierare som är en referens till en parameter i omvandlings metoden för anspråk. Varje anspråks omvandlings metod har sina egna värden. I omvandlings tabellen för anspråk finns en fullständig lista över tillgängliga värden. |
| DataType | Ja | Data typen för parametern, till exempel String, Boolean, int eller DateTime enligt uppräkningen av data typen i XML-schemat för anpassade principer. Den här typen används för att utföra aritmetiska åtgärder på rätt sätt. Varje anspråks omvandling har sina egna värden. I [omvandlings referensen för anspråk](#claims-transformations-reference) finns en fullständig lista över tillgängliga värden. |
| Värde | Ja | Ett värde som överfördes orda Grant till omvandlingen. Några av värdena är godtyckliga och vissa av dem du väljer från omvandlings metoden för anspråk. |

### <a name="outputclaims"></a>OutputClaims

**OutputClaims** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| OutputClaim | 0: n | En förväntad typ av utgående anspråk. |

#### <a name="outputclaim"></a>OutputClaim

**OutputClaim** -elementet innehåller följande attribut:

| Attribut |Krävs | Beskrivning |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Ja | En referens till en ClaimType som redan har definierats i ClaimsSchema-avsnittet i principen.
| TransformationClaimType | Ja | En identifierare som refererar till en omvandlings anspråks typ. Varje anspråks omvandling har sina egna värden. I [omvandlings referensen för anspråk](#claims-transformations-reference) finns en fullständig lista över tillgängliga värden. |

Om indata-anspråk och utgående anspråk är av samma typ (sträng eller boolesk) kan du använda samma indata-anspråk som utgående anspråk. I det här fallet ändrar anspråks omvandlingen indata-anspråk med värdet output.

## <a name="example"></a>Exempel

Du kan till exempel lagra den senaste versionen av dina tjänst villkor som användaren har accepterat. När du uppdaterar villkoren för tjänsterna kan du be användaren att godkänna den nya versionen. I följande exempel jämför **HasTOSVersionChanged** -anspråks omvandlingen värdet för **TOSVersion** -anspråket med värdet för **LastTOSAcceptedVersion** -anspråk och returnerar sedan det booleska **TOSVersionChanged** -anspråket.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Referens för anspråk omvandlingar

Exempel på anspråks omvandlingar finns i följande referens sidor:

- [Boolesk](boolean-transformations.md)
- [Datum](date-transformations.md)
- [Integer](integer-transformations.md)
- [JSON](json-transformations.md)
- [Telefonnummer](phone-number-claims-transformations.md)
- [Allmänt](general-transformations.md)
- [Socialt konto](social-transformations.md)
- [Sträng](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

