---
title: Transformerings exempel för StringCollection-anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Transformerings exempel för StringCollection-anspråk för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9104fb4598eb62ed96d0b21734053fa118b5237
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102120290"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection-anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du kan använda anspråks omvandlingar för identitets samling i ett Ramverks schema för identitets miljö i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Lägger till ett sträng anspråk till ett nytt unikt värde stringCollection-anspråk.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | objekt | sträng | Den ClaimType som ska läggas till i utgående anspråk. |
| InputClaim | samling | stringCollection | Den sträng samling som ska läggas till i utgående anspråk. Om samlingen innehåller objekt, kopierar anspråks omvandlingen objekten och lägger till objektet i slutet av anspråket för utgående data insamling. |
| OutputClaim | samling | stringCollection | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats, med det värde som anges i indatamängden. |

Använd den här anspråks omvandlingen för att lägga till en sträng till en ny eller befintlig stringCollection. Den används ofta i en **AAD-UserWriteUsingAlternativeSecurityId** teknisk profil. Innan ett nytt socialt konto skapas läser **CreateOtherMailsFromEmail** Claims-omvandlingen claimType och lägger till värdet i **otherMails** -claimType.

Följande påstående-omvandling lägger till **e-** **otherMails** för claimType.

```xml
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **samling**: [" someone@outlook.com "]
  - **objekt**: " admin@contoso.com "
- Utgående anspråk:
  - **samling**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Lägger till en sträng parameter till ett nytt unikt värde stringCollection-anspråk.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | samling | stringCollection | Den sträng samling som ska läggas till i utgående anspråk. Om samlingen innehåller objekt, kopierar anspråks omvandlingen objekten och lägger till objektet i slutet av anspråket för utgående data insamling. |
| InputParameter | objekt | sträng | Det värde som ska läggas till i utgående anspråk. |
| OutputClaim | samling | stringCollection | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats, med det angivna värdet i indataparametern. |

Använd den här anspråks omvandlingen för att lägga till ett sträng värde till en ny eller befintlig stringCollection. I följande exempel läggs en konstant e-postadress ( admin@contoso.com ) till i **otherMails** -anspråket.

```xml
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **samling**: [" someone@outlook.com "]
- Indataparametrar
  - **objekt**: " admin@contoso.com "
- Utgående anspråk:
  - **samling**: [" someone@outlook.com ", " admin@contoso.com "]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Hämtar det första objektet från den angivna sträng samlingen.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | samling | stringCollection | ClaimTypes som används av anspråks omvandlingen för att hämta objektet. |
| OutputClaim | extractedItem | sträng | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats. Det första objektet i samlingen. |

I följande exempel läses **otherMails** -anspråket och det första objektet returneras till **e-** postanspråket.

```xml
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **samling**: [" someone@outlook.com ", " someone@contoso.com "]
- Utgående anspråk:
  - **extractedItem**: " someone@outlook.com "


## <a name="stringcollectioncontains"></a>StringCollectionContains

Kontrollerar om en StringCollection-anspråks typ innehåller ett-element.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | Anspråks typen som ska genomsökas. |
|InputParameter|objekt|sträng|Det värde som ska genomsökas.|
|InputParameter|ignoreCase|sträng|Anger om jämförelsen ska ignorera Skift läget för strängarna som jämförs.|
| OutputClaim | outputClaim | boolean | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. En boolesk indikator om samlingen innehåller en sådan sträng |

Följande exempel kontrollerar om `roles` stringCollection-anspråks typen innehåller värdet **administratör**.

```xml
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- Inmatade anspråk:
    - **inputClaim**: ["Reader", "author", "admin"]
- Indataparametrar:
    - **objekt**: "admin"
    - **ignoreCase**: "true"
- Utgående anspråk:
    - **outputClaim**: "true"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

Kontrollerar om en StringCollection-anspråks typ innehåller ett anspråks värde.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | samling | stringCollection | Anspråks typen som ska genomsökas. |
| InputClaim | objekt|sträng| Anspråks typen som innehåller värdet som ska genomsökas.|
|InputParameter|ignoreCase|sträng|Anger om jämförelsen ska ignorera Skift läget för strängarna som jämförs.|
| OutputClaim | outputClaim | boolean | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. En boolesk indikator om samlingen innehåller en sådan sträng |

Följande exempel kontrollerar om `roles` stringCollection-anspråks typen innehåller värdet för `role` anspråks typen.

```xml
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- Inmatade anspråk:
    - **samling**: ["läsare", "författare", "admin"]
    - **objekt**: "admin"
- Indataparametrar:
    - **ignoreCase**: "true"
- Utgående anspråk:
    - **outputClaim**: "true"
