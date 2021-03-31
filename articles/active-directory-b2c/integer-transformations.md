---
title: Exempel på heltals anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Transformerings exempel för heltals anspråk för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 066a6489e6244369453ec5d9f21d5e1e83fcd6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85201758"
---
# <a name="integer-claims-transformations"></a>Transformeringar av heltals anspråk

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder heltals anspråks omvandlingar av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Konverterar en lång data typ till en sträng data typ.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | ClaimType som ska konverteras till en sträng. |
| OutputClaim | outputClaim | sträng | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats. |

I det här exemplet `numericUserId` konverteras anspråket med värde typen Long till ett `UserId` anspråk med ett värde av sträng typ.

```xml
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **inputClaim**: 12334 (långt)
- Utgående anspråk:
    - **outputClaim**: "12334" (sträng)

