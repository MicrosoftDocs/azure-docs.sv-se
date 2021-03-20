---
title: Transformerings exempel för sociala konto anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: Transformerings exempel för sociala konto anspråk för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c60cecb9d6bfeeefc0569a1a57185d13f0c6442f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953125"
---
# <a name="social-accounts-claims-transformations"></a>Anspråk omvandlingar för sociala konton

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I Azure Active Directory B2C (Azure AD B2C) lagras identiteter för sociala konton i ett `userIdentities` attribut av en **alternativeSecurityIdCollection** -anspråks typ. Varje objekt i **alternativeSecurityIdCollection** anger utfärdaren (identitets leverantörens namn, till exempel Facebook.com) och `issuerUserId` , vilket är ett unikt användar-ID för utfärdaren.

```json
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Den här artikeln innehåller exempel på hur du kan använda anspråk för social konto-omvandlingar av identitets Experience Framework-schemat i Azure AD B2C. Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Skapar en JSON-representation av användarens alternativeSecurityId-egenskap som kan användas i anrop till Azure Active Directory. Mer information finns i [AlternativeSecurityId](/graph/api/resources/alternativesecurityid) -schemat.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | sträng | ClaimType som anger den unika användar identifierare som används av den sociala identitets leverantören. |
| InputClaim | identityProvider | sträng | ClaimType som anger namnet på den sociala konto identitets leverantören, till exempel facebook.com. |
| OutputClaim | alternativeSecurityId | sträng | Den ClaimType som skapas efter att ClaimsTransformation har anropats. Innehåller information om identiteten för en användare av sociala konton. **Utfärdaren** är värdet för `identityProvider` anspråket. **IssuerUserId** är värdet för `key` anspråket i base64-format. |

Använd den här anspråks omvandlingen för att generera en `alternativeSecurityId` claimType. Den används av alla tekniska profiler för sociala identitets leverantörer, till exempel `Facebook-OAUTH` . Följande anspråks omvandling får ID för användarens sociala konto och namnet på identitets leverantören. Utdata från den här tekniska profilen är ett JSON-sträng format som kan användas i Azure AD-katalogtjänster.

```xml
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **nyckel**: 12334
    - **identityProvider**: Facebook.com
- Utgående anspråk:
    - **alternativeSecurityId**: {"Issuer": "Facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Lägger till ett `AlternativeSecurityId` till ett `alternativeSecurityIdCollection` anspråk.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | objekt | sträng | Den ClaimType som ska läggas till i utgående anspråk. |
| InputClaim | samling | alternativeSecurityIdCollection | Den ClaimTypes som används av anspråks omvandlingen om den är tillgänglig i principen. Om det här alternativet anges lägger anspråks omvandlingen i `item` slutet av samlingen. |
| OutputClaim | samling | alternativeSecurityIdCollection | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats. Den nya samlingen som innehåller både objekten från indatamängden `collection` och `item` . |

I följande exempel länkas en ny social identitet med ett befintligt konto. Så här länkar du en ny social identitet:
1. I de tekniska profilerna **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserReadUsingObjectId** skriver du användarens **alternativeSecurityIds** -anspråk.
1. Be användaren att logga in med en av identitets leverantörerna som inte är associerade med den här användaren.
1. Använd **CreateAlternativeSecurityId** Claims-omvandlingen och skapa en ny **alternativeSecurityId** -anspråks typ med namnet `AlternativeSecurityId2`
1. Anropa **AddItemToAlternativeSecurityIdCollection** Claims-omvandlingen för att lägga till **AlternativeSecurityId2** -anspråket i det befintliga **AlternativeSecurityIds** -anspråket.
1. Behåll **alternativeSecurityIds** -anspråk för användar kontot

```xml
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **objekt**: {"Issuer": "Facebook.com", "issuerUserId": "MTIzNDU ="}
    - **samling**: [{"Issuer": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]
- Utgående anspråk:
    - **samling**: [{"Issuer": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"utfärdare": "Facebook.com", "issuerUserId": "MTIzNDU ="}]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Returnerar lista över utfärdare från **alternativeSecurityIdCollection** -anspråket till ett nytt **stringCollection** -anspråk.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Den ClaimType som ska användas för att hämta listan över identitets leverantörer (utfärdare). |
| OutputClaim | identityProvidersCollection | stringCollection | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats. Lista över identitets leverantörer som är kopplade till alternativeSecurityIdCollection-indatamängden |

Följande Claims-omvandling läser användar **alternativeSecurityIds** -anspråket och extraherar listan över identitets leverantörs namn som är associerade med det kontot. Använd output **identityProvidersCollection** för att Visa användaren listan över identitets leverantörer som är associerade med kontot. Du kan också filtrera listan över identitets leverantörer baserat på utgående **identityProvidersCollection** -anspråk på sidan för val av identitetsprovider. Användaren kan alltså välja att länka en ny social identitet som inte redan är kopplad till kontot.

```xml
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Inmatade anspråk:
    - **alternativeSecurityIdCollection**: [{"Issuer": "Google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"utfärdare": "Facebook.com", "issuerUserId": "MTIzNDU ="}]
- Utgående anspråk:
    - **identityProvidersCollection**: ["Facebook.com", "Google.com"]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Tar bort en **AlternativeSecurityId** från ett **alternativeSecurityIdCollection** -anspråk.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | sträng | Den ClaimType som innehåller identitets leverantörens namn som ska tas bort från samlingen. |
| InputClaim | samling | alternativeSecurityIdCollection | ClaimTypes som används av anspråks omvandlingen. Transformationen Claims tar bort identityProvider från samlingen. |
| OutputClaim | samling | alternativeSecurityIdCollection | ClaimTypes som skapas efter att denna ClaimsTransformation har anropats. Den nya samlingen efter att identityProvider har tagits bort från samlingen. |

I följande exempel avlänkas en av den sociala identiteten med ett befintligt konto. Ta bort länken till en social identitet:
1. I de tekniska profilerna **AAD-UserReadUsingAlternativeSecurityId** och **AAD-UserReadUsingObjectId** skriver du användarens **alternativeSecurityIds** -anspråk.
2. Be användaren att välja vilket socialt konto som ska tas bort från listan med identitets leverantörer som är associerade med den här användaren.
3. Anropa en teknisk profil för anspråks omvandling som anropar omvandlingen av **RemoveAlternativeSecurityIdByIdentityProvider** -anspråk, som tog bort den valda sociala identiteten med hjälp av namnet på identitets leverantören.
4. Behåll **alternativeSecurityIds** -anspråket för användar kontot.

```xml
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
    - **identityProvider**: Facebook.com
    - **samling**: [{"Issuer": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"utfärdare": "Facebook.com", "issuerUserId": "MTIzNDU ="}]
- Utgående anspråk:
    - **samling**: [{"Issuer": "Live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]