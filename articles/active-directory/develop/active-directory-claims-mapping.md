---
title: Anpassa azure AD-klientorganisationsappanspråk (PowerShell)
titleSuffix: Microsoft identity platform
description: Den här sidan Azure Active Directory mappning av anspråk.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: e77155f8a6efd3916ae90fcb562d688bb5b5126f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598898"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Gör så här: Anpassa anspråk som genereras i token för en specifik app i en klientorganisation (förhandsversion)

> [!NOTE]
> Den här funktionen ersätter och ersätter [anspråksanpassningen som erbjuds](active-directory-saml-claims-customization.md) via portalen idag. Om du anpassar anspråk i samma program med hjälp av portalen utöver Graph/PowerShell-metoden som beskrivs i det här dokumentet ignorerar token som utfärdas för programmet konfigurationen i portalen. Konfigurationer som görs med metoderna som beskrivs i det här dokumentet återspeglas inte i portalen.

> [!NOTE]
> Den här funktionen är för närvarande i offentlig förhandsversion. Observera att du kan behöva återställa eller ta bort eventuella ändringar. Funktionen är tillgänglig i alla prenumerationer Azure Active Directory (Azure AD) under den offentliga förhandsversionen. Men när funktionen blir allmänt tillgänglig kan vissa aspekter av funktionen kräva en Azure AD Premium-prenumeration. Den här funktionen stöder konfiguration av anspråksmappningsprinciper för WS-Fed-, SAML-, OAuth- och OpenID Connect protokoll.

Den här funktionen används av klientorganisationsadministratörer för att anpassa anspråk som genereras i token för ett specifikt program i klientorganisationen. Du kan använda anspråksmappningsprinciper för att:

- Välj vilka anspråk som ingår i token.
- Skapa anspråkstyper som inte redan finns.
- Välj eller ändra datakällan som genereras i specifika anspråk.

I den här artikeln går vi igenom några vanliga scenarier som kan hjälpa dig att förstå hur du använder [principtypen för anspråksmappning.](reference-claims-mapping-policy-type.md)

När du skapar en princip för anspråksmappning kan du även generera ett anspråk från ett attribut för katalogschematillägg i token. Använd *ExtensionID* för tilläggsattributet i stället för *ID* i `ClaimsSchema` elementet .  Mer information om tilläggsattribut finns i Använda [attribut för katalogschematillägg.](active-directory-schema-extensions.md)

## <a name="prerequisites"></a>Förutsättningar

I följande exempel skapar, uppdaterar, länkar och tar du bort principer för tjänstens huvudnamn. Principer för anspråksmappning kan bara tilldelas till tjänstens huvudnamnsobjekt. Om azure AD är nytt för dig rekommenderar vi att du lär dig hur du hämtar en [Azure AD-klient](quickstart-create-new-tenant.md) innan du fortsätter med de här exemplen.

Kom igång genom att göra följande:

1. Ladda ned den senaste [offentliga förhandsversionen av Azure AD PowerShell-modulen.](https://www.powershellgallery.com/packages/AzureADPreview)
1. Kör kommandot Anslut för att logga in på ditt Azure AD-administratörskonto. Kör det här kommandot varje gång du startar en ny session.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Om du vill se alla principer som har skapats i din organisation kör du följande kommando. Vi rekommenderar att du kör det här kommandot efter de flesta åtgärder i följande scenarier för att kontrollera att dina principer skapas som förväntat.

   ``` powershell
   Get-AzureADPolicy
   ```

## <a name="omit-the-basic-claims-from-tokens"></a>Utelämna grundläggande anspråk från token

I det här exemplet skapar du en princip som tar bort den grundläggande [anspråksuppsättningen](reference-claims-mapping-policy-type.md#claim-sets) från token som utfärdats till länkade tjänsthuvudnamn.

1. Skapa en princip för anspråksmappning. Den här principen, som är länkad till specifika tjänsthuvudnamn, tar bort den grundläggande anspråksuppsättningen från token.
   1. Kör det här kommandot för att skapa principen:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Om du vill se den nya principen och hämta principen ObjectId kör du följande kommando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvudnamn. Du måste också hämta ObjectId för tjänstens huvudnamn.
   1. Om du vill se alla organisationens tjänsthuvudnamn kan du [köra frågor mot Microsoft Graph API.](/graph/traverse-the-graph) Eller logga in [Microsoft Graph Ditt](https://developer.microsoft.com/graph/graph-explorer)Azure AD-konto i Microsoft Graph Explorer.
   2. När du har ObjectId för tjänstens huvudnamn kör du följande kommando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>Inkludera EmployeeID och TenantCountry som anspråk i token

I det här exemplet skapar du en princip som lägger till EmployeeID och TenantCountry till token som utfärdas till länkade tjänsthuvudnamn. EmployeeID genereras som namnanspråkstyp i både SAML-token och JWT. TenantCountry genereras som anspråkstyp för land/region i både SAML-token och JWT. I det här exemplet fortsätter vi att inkludera de grundläggande anspråk som angetts i token.

1. Skapa en princip för anspråksmappning. Den här principen, som är länkad till specifika tjänsthuvudnamn, lägger till EmployeeID- och TenantCountry-anspråken till tokens.
   1. Kör följande kommando för att skapa principen:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Om du vill se den nya principen och hämta principen ObjectId kör du följande kommando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvudnamn. Du måste också hämta ObjectId för tjänstens huvudnamn.
   1. Om du vill se alla tjänstens huvudnamn för din organisation kan du [fråga Microsoft Graph API.](/graph/traverse-the-graph) Eller logga in [Microsoft Graph Ditt](https://developer.microsoft.com/graph/graph-explorer)Azure AD-konto i Microsoft Graph Explorer.
   2. När du har ObjectId för tjänstens huvudnamn kör du följande kommando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>Använda en anspråkstransformering i token

I det här exemplet skapar du en princip som skickar ett anpassat anspråk "JoinedData" till JWT som utfärdats till länkade tjänsthuvudnamn. Det här anspråket innehåller ett värde som skapats genom att koppla data som lagras i attributet extensionattribute1 i användarobjektet med ".sandbox". I det här exemplet utesluter vi de grundläggande anspråk som angetts i token.

1. Skapa en princip för anspråksmappning. Den här principen, som är länkad till specifika tjänsthuvudnamn, lägger till EmployeeID- och TenantCountry-anspråken till tokens.
   1. Kör följande kommando för att skapa principen:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Om du vill se den nya principen och hämta principen ObjectId kör du följande kommando:

      ``` powershell
      Get-AzureADPolicy
      ```
1. Tilldela principen till tjänstens huvudnamn. Du måste också hämta ObjectId för tjänstens huvudnamn.
   1. Om du vill se alla organisationens tjänsthuvudnamn kan du köra frågor [mot Microsoft Graph API.](/graph/traverse-the-graph) Eller logga in [Microsoft Graph Ditt](https://developer.microsoft.com/graph/graph-explorer)Azure AD-konto i Microsoft Graph Explorer.
   2. När du har ObjectId för tjänstens huvudnamn kör du följande kommando:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Säkerhetsöverväganden

Program som tar emot token förlitar sig på det faktum att anspråksvärdena utfärdas auktoritativt av Azure AD och inte kan manipuleras. Men när du ändrar tokeninnehållet via principer för anspråksmappning kanske dessa antaganden inte längre är korrekta. Program måste uttryckligen bekräfta att token har ändrats av skaparen av anspråksmappningsprincipen för att skydda sig mot principer för anspråksmappning som skapats av illvilliga aktörer. Detta kan göras på följande sätt:

- Konfigurera en anpassad signeringsnyckel
- Uppdatera programmanifestet för att acceptera mappade anspråk.
 
Utan detta returnerar Azure AD [ `AADSTS50146` felkoden](reference-aadsts-error-codes.md#aadsts-error-codes).

### <a name="custom-signing-key"></a>Anpassad signeringsnyckel

Om du vill lägga till en anpassad signeringsnyckel till tjänstens huvudnamnsobjekt kan du använda cmdleten Azure PowerShell för att skapa en certifikatnyckel för [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) ditt Application-objekt.

Appar som har aktiverad anspråksmappning måste verifiera sina tokensigneringsnycklar genom att lägga till sina `appid={client_id}` [OpenID Connect metadatabegäranden](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document). Nedan visas formatet för det OpenID Connect metadatadokument som du bör använda:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Uppdatera programmanifestet

Du kan också ange egenskapen till `acceptMappedClaims` i `true` [programmanifestet](reference-app-manifest.md). Enligt beskrivningen i [resurstypen apiApplication](/graph/api/resources/apiapplication#properties)kan ett program använda anspråksmappning utan att ange en anpassad signeringsnyckel.

Detta kräver att den begärda tokenmålgruppen använder ett verifierat domännamn för din Azure AD-klientorganisation, vilket innebär att du bör se till att ange (som representeras av i programmanifestet) till exempel till eller (använd bara `Application ID URI` standardklientnamnet). `identifierUris` `https://contoso.com/my-api` `https://contoso.onmicrosoft.com/my-api`

Om du inte använder en verifierad domän returnerar Azure AD en felkod med meddelandet `AADSTS501461` "AcceptMappedClaims stöds endast för en tokenmålgrupp som matchar programmets GUID eller en målgrupp inom klientorganisationens verifierade *domäner. Ändra antingen resursidentifieraren eller använd en programspecifik signeringsnyckel."*

## <a name="next-steps"></a>Nästa steg

- Läs [referensartikeln om principtyp för anspråksmappning](reference-claims-mapping-policy-type.md) om du vill veta mer.
- Information om hur du anpassar anspråk som utfärdats i SAML-token via Azure Portal finns i Så här anpassar du anspråk som utfärdats i [SAML-token för företagsprogram](active-directory-saml-claims-customization.md)
- Mer information om tilläggsattribut finns i [Använda attribut för katalogschematillägg i anspråk.](active-directory-schema-extensions.md)
