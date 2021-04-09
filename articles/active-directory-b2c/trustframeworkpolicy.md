---
title: TrustFrameworkPolicy – Azure Active Directory B2C | Microsoft Docs
description: Ange TrustFrameworkPolicy-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bf1cc197a7d6977ccb6ef69e157d9f8a76a58d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470721"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

En anpassad princip visas som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. XML-elementen definierar element i principen, till exempel anspråks schema, anspråk omvandlingar, innehålls definitioner, anspråks leverantörer, tekniska profiler, användar resa och Orchestration-steg. Varje princip fil definieras i **TrustFrameworkPolicy** -elementet på den översta nivån i en princip fil.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**TrustFrameworkPolicy** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ja | Den schema version som ska användas för att köra principen. Värdet måste vara `0.3.0.0` |
| TenantObjectId | Inga | Den unika objekt identifieraren för Azure Active Directory B2C-klienten (Azure AD B2C). |
| TenantId | Ja | Den unika identifieraren för den klient som principen tillhör. |
| PolicyId | Ja | Den unika identifieraren för principen. Den här identifieraren måste föregås av *B2C_1A_* |
| PublicPolicyUri | Ja | En URI för principen, som är en kombination av klient-ID och princip-ID. |
| DeploymentMode | Inga | Möjliga värden: `Production` , eller `Development` . `Production` används som standard. Använd den här egenskapen för att felsöka principen. Mer information finns i [samla in loggar](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | Inga | Den slut punkt som används för loggning. Värdet måste anges till `urn:journeyrecorder:applicationinsights` om attributet finns. Mer information finns i [samla in loggar](troubleshoot-with-application-insights.md). |


I följande exempel visas hur du anger **TrustFrameworkPolicy** -elementet:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

**TrustFrameworkPolicy** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| Identifieraren för en bas princip. |
| [BuildingBlocks](buildingblocks.md) | 0:1 | Bygg stenarna i principen. |
| [ClaimsProviders](claimsproviders.md) | 0:1 | En samling av anspråks leverantörer. |
| [UserJourneys](userjourneys.md) | 0:1 | En samling användar resor. |
| [RelyingParty](relyingparty.md) | 0:1 | En definition av en princip för förlitande part. |

Om du vill ärva en princip från en annan princip måste ett **BasePolicy** -element deklareras under **TrustFrameworkPolicy** -elementet i princip filen. **BasePolicy** -elementet är en referens till den grundläggande princip som den här principen härleds från.

**BasePolicy** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | --------|
| TenantId | 1:1 | ID för din Azure AD B2C klient. |
| PolicyId | 1:1 | Identifierare för den överordnade principen. |


I följande exempel visas hur du anger en bas princip. Den här **B2C_1A_TrustFrameworkExtensions** principen härleds från **B2C_1A_TrustFrameworkBases** principen.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

