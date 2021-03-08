---
title: ClaimsProviders – Azure Active Directory B2C | Microsoft Docs
description: Ange ClaimsProvider-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 846394266b981c14788148be465912b14bc1fb3e
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447919"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ett anspråk ger ett gränssnitt för att kommunicera med olika typer av parter via dess [tekniska profiler](technicalprofiles.md). Varje påståendeprovider måste ha en eller flera tekniska profiler som styr slutpunkterna och de protokoll som behövs för att kunna kommunicera med påståendeprovidern. En anspråks leverantör kan ha flera tekniska profiler. Till exempel kan flera tekniska profiler definieras eftersom anspråks leverantören stöder flera protokoll, olika slut punkter med olika funktioner eller släpper olika anspråk på olika garanti nivåer. Det kan vara acceptabelt att släppa känsliga anspråk i en användar resa, men inte i en annan.

En användar resa kombinerar anrop av tekniska profiler via Orchestration-steg för att definiera din affärs logik. 

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

**ClaimsProviders** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1: n | En ackrediterad anspråks leverantör som kan utnyttjas i olika användar resor. |

## <a name="claimsprovider"></a>ClaimsProvider

**ClaimsProvider** -elementet innehåller följande underordnade element:

| Element | Förekomster | Beskrivning |
| ------- | ---------- | ----------- |
| Domain | 0:1 | En sträng som innehåller domän namnet för anspråks leverantören. Om din anspråks leverantör till exempel innehåller den tekniska Facebook-profilen för Facebook, är domän namnet Facebook.com. Det här domän namnet används för alla tekniska profiler som definierats i anspråks leverantören, såvida de inte åsidosätts av den tekniska profilen. Domän namnet kan också refereras till i en **domain_hint**. Mer information finns i avsnittet **omdirigera inloggning till en socialt leverantör** i [Konfigurera direkt inloggning med Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | En sträng som innehåller namnet på anspråks leverantören. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | En uppsättning tekniska profiler som stöds av anspråks leverantören |

**ClaimsProvider** ordnar hur dina tekniska profiler är relaterade till anspråks leverantören. I följande exempel visas providern Azure Active Directory anspråk med Azure Active Directory tekniska profiler:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

I följande exempel visas Facebook-anspråks leverantören med den tekniska profilen för **Facebook-OAuth** .

```xml
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
