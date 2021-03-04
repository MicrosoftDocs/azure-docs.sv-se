---
title: Transformerings exempel för JSON-anspråk för anpassade principer
titleSuffix: Azure AD B2C
description: JSON-anspråk omvandlings exempel för IEF-schemat (Identity Experience Framework) för Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5c8e21f2ce3f6907547bf1b2fe4681eb937864b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119883"
---
# <a name="json-claims-transformations"></a>JSON-anspråk omvandlingar

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln innehåller exempel på hur du använder JSON-anspråks omvandlingar av Identity Experience Framework-schemat i Azure Active Directory B2C (Azure AD B2C). Mer information finns i [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GenerateJson

Använd antingen anspråks värden eller konstanter för att generera en JSON-sträng. Sök vägs strängen följande punkt notation används för att ange var data ska infogas i en JSON-sträng. Efter delning med punkter tolkas alla heltal som index för en JSON-matris och icke-heltal tolkas som index för ett JSON-objekt.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Valfri sträng efter punkt notation | sträng | JsonPath för JSON där anspråks värde ska infogas i. |
| InputParameter | Valfri sträng efter punkt notation | sträng | JsonPath för JSON där det konstanta strängvärdet ska infogas i. |
| OutputClaim | outputClaim | sträng | Den genererade JSON-strängen. |

### <a name="example-1"></a>Exempel 1

I följande exempel genereras en JSON-sträng baserat på anspråks värdet "email" och "eng ång slö sen" samt konstanta strängar.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

Följande anspråks omvandling matar ut ett JSON-sträng anspråk som kommer att bli bröd texten i begäran som skickas till SendGrid (en tredjeparts e-postprovider). JSON-objektets struktur definieras av ID: n i punkt notation för indataparametrarna och TransformationClaimTypes för InputClaims. Siffror i punkt notation innebär att matriser. Värdena hämtas från InputClaims-värdena och värdet för indataparametrar.

- Inmatade anspråk:
  - **e-post**, typ  **anpassningar av omvandlings anspråk. 0. till. 0. e-post**: " someone@example.com "
  - **eng ång slö sen ord**, typ av omvandlings anspråk **personalizations.0.dynamic_template_data. eng ång slö 346349 sen ord**
- Indataparameter:
  - **TEMPLATE_ID**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **från. e-post**: " service@contoso.com "
  - **anpassningar. 0. Subject** "e-postverifierings kod för Contoso-konto"
- Utgående anspråk:
  - **requestBody**: JSON-värde

```json
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

### <a name="example-2"></a>Exempel 2

I följande exempel genereras en JSON-sträng baserat på anspråks värden och konstanta strängar.

```xml
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="customerEntity.email" />
    <InputClaim ClaimTypeReferenceId="objectId" TransformationClaimType="customerEntity.userObjectId" />
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="customerEntity.firstName" />
    <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="customerEntity.lastName" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="customerEntity.role.name" DataType="string" Value="Administrator"/>
    <InputParameter Id="customerEntity.role.id" DataType="long" Value="1"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

Följande anspråks omvandling matar ut ett JSON-sträng anspråk som kommer att vara bröd texten i begäran som skickas till en REST API. JSON-objektets struktur definieras av ID: n i punkt notation för indataparametrarna och TransformationClaimTypes för InputClaims. Värdena hämtas från InputClaims-värdena och värdet för indataparametrar.

- Inmatade anspråk:
  - **e-post**, omvandlings anspråks typ  **customerEntity. e-post**: " john.s@contoso.com "
  - **ObjectID**, omvandlings anspråks typ **customerEntity. userObjectId** "01234567-89ab-cdef-0123-456789abcdef"
  - **givenName**, omvandlings anspråks typ **customerEntity. FirstName** "John"
  - Efter **namn**, typ av omvandlings anspråk **customerEntity. LastName** "Svensson"
- Indataparameter:
  - **customerEntity.Role.name**: "administratör"
  - **customerEntity.Role.ID** 1
- Utgående anspråk:
  - **requestBody**: JSON-värde

```json
{
   "customerEntity":{
      "email":"john.s@contoso.com",
      "userObjectId":"01234567-89ab-cdef-0123-456789abcdef",
      "firstName":"John",
      "lastName":"Smith",
      "role":{
         "name":"Administrator",
         "id": 1
      }
   }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Hämta ett angivet element från en JSON-data.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | sträng | ClaimTypes som används av anspråks omvandlingen för att hämta objektet. |
| InputParameter | claimToExtract | sträng | namnet på det JSON-element som ska extraheras. |
| OutputClaim | extractedClaim | sträng | Den ClaimType som skapas efter att den här anspråks omvandlingen har anropats, det element värde som anges i indataparametern _claimToExtract_ . |

I följande exempel extraherade anspråks omvandlingen `emailAddress` elementet från JSON-data: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```xml
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **inputJson**: {"EmailAddress": " someone@example.com ", "DisplayName": "någon"}
- Indataparameter:
    - **claimToExtract**: EmailAddress
- Utgående anspråk:
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Hämta en lista med angivna element från JSON-data.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | sträng | ClaimTypes som används av anspråks omvandlingen för att hämta anspråk. |
| InputParameter | errorOnMissingClaims | boolean | Anger om ett fel ska genereras om en av anspråken saknas. |
| InputParameter | includeEmptyClaims | sträng | Ange om tomma anspråk ska tas med. |
| InputParameter | jsonSourceKeyName | sträng | Element nyckel namn |
| InputParameter | jsonSourceValueName | sträng | Element värde namn |
| OutputClaim | Samling | sträng, int, Boolean och datetime |Lista över anspråk att extrahera. Namnet på anspråket ska vara lika med det som anges i _jsonSourceClaim_ -indatamängden. |

I följande exempel extraherar anspråks omvandlingen följande anspråk: email (sträng), displayName (sträng), membershipNum (int), Active (Boolean) och födelse datum (datetime) från JSON-data.

```json
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```xml
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- Inmatade anspråk:
  - **jsonSourceClaim**: [{"Key": "e-post", "value": " someone@example.com "}, {"Key": "DisplayName", "värde": "någon"}, {"Key": "membershipNum", "värde": 6353399}, {"Key": "Active", "värde": true}, {"Key": "födelse datum", "värde": "1980-09-23T00:00:00Z"}]
- Indataparametrar:
    - **errorOnMissingClaims**: falskt
    - **includeEmptyClaims**: falskt
    - **jsonSourceKeyName**: nyckel
    - **jsonSourceValueName**: värde
- Utgående anspråk:
  - **e-post**: " someone@example.com "
  - **DisplayName**: "någon"
  - **membershipNum**: 6353399
  - **aktiv**: true
  - **födelse datum**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Hämtar ett angivet numeriskt (långt) element från JSON-data.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | sträng | ClaimTypes som används av anspråks omvandlingen för att hämta anspråket. |
| InputParameter | claimToExtract | sträng | Namnet på det JSON-element som ska extraheras. |
| OutputClaim | extractedClaim | long | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats, elementets värde som anges i indataparametrarna för _claimToExtract_ . |

I följande exempel extraherar anspråks omvandlingen `id` elementet från JSON-data.

```json
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```xml
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **inputJson**: {"EmailAddress": " someone@example.com ", "DisplayName": "någon", "id": 6353399}
- Indataparametrar
    - **claimToExtract**: ID
- Utgående anspråk:
    - **extractedClaim**: 6353399

## <a name="getsingleitemfromjson"></a>GetSingleItemFromJson

Hämtar det första elementet från en JSON-data.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | sträng | ClaimTypes som används av anspråks omvandlingen för att hämta objektet från JSON-data. |
| OutputClaim | key | sträng | Den första element nyckeln i JSON. |
| OutputClaim | värde | sträng | Det första elementets värde i JSON. |

I följande exempel extraherar anspråks omvandlingen det första elementet (tilldelat namn) från JSON-data.

```xml
<ClaimsTransformation Id="GetGivenNameFromResponse" TransformationMethod="GetSingleItemFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="json" TransformationClaimType="inputJson" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="givenNameKey" TransformationClaimType="key" />
    <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **inputJson**: {"givenName": "Emilty", "Lastname": "Svensson"}
- Utgående anspråk:
  - **nyckel**: givenName
  - **värde**: Emilty


## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Hämtar det första elementet från en JSON-datamatris.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | sträng | ClaimTypes som används av anspråks omvandlingen för att hämta objektet från JSON-matrisen. |
| OutputClaim | extractedClaim | sträng | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats, det första elementet i JSON-matrisen. |

I följande exempel extraherar anspråks omvandlingen det första elementet (e-postadress) från JSON-matrisen  `["someone@example.com", "Someone", 6353399]` .

```xml
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exempel

- Inmatade anspråk:
  - **inputJsonClaim**: [" someone@example.com ", "någon", 6353399]
- Utgående anspråk:
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Konverterar XML-data till JSON-format.

| Objekt | TransformationClaimType | Datatyp | Kommentarer |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | sträng | ClaimTypes som används av anspråks omvandlingen för att konvertera data från XML till JSON-format. |
| OutputClaim | json | sträng | Den ClaimType som skapas efter att denna ClaimsTransformation har anropats, data i JSON-format. |

```xml
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

I följande exempel konverterar anspråks omvandlingen följande XML-data till JSON-format.

#### <a name="example"></a>Exempel
Indatamängds anspråk:

```xml
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Utgående anspråk:

```json
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```


