---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: Ange ContentDefinitions-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62bae22b6a4bb06b1e97c18e52ad614fd2439902
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489329"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan anpassa utseendet på en [egen, kontrollerad teknisk profil](self-asserted-technical-profile.md). Azure Active Directory B2C (Azure AD B2C) kör kod i kundens webbläsare och använder en modern metod som kallas CORS (Cross-Origin Resource Sharing).

Om du vill anpassa användar gränssnittet anger du en URL i **ContentDefinition** -elementet med anpassat HTML-innehåll. I den självkontrollerade tekniska profilen eller **OrchestrationStep** pekar du på den innehålls definitions identifieraren. Innehålls definitionen kan innehålla ett **LocalizedResourcesReferences** -element som anger en lista över lokaliserade resurser som ska läsas in. Azure AD B2C sammanfogar användargränssnittets element med HTML-innehåll som läses in från din URL och visar sedan sidan för användaren.

**ContentDefinitions** -elementet innehåller URL: er till HTML5-mallar som kan användas i en användar resa. HTML5-sidans URI används för ett angivet användar gränssnitts steg. Till exempel inloggnings-eller registrerings-, lösen ords återställning eller fel sidor. Du kan ändra utseendet och känslan genom att åsidosätta LoadUri för HTML5-filen. Du kan skapa nya innehålls definitioner efter dina behov. Det här elementet kan innehålla en lokaliserad resurs referens till lokaliserings identifieraren som anges i [lokaliserings](localization.md) elementet.

I följande exempel visas innehålls definitions identifieraren och definitionen av lokaliserade resurser:

```xml
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

Metadata för den **LocalAccountSignUpWithLogonEmail** självkontrollerade tekniska profilen innehåller den innehålls Definitions-ID **ContentDefinitionReferenceId** som är inställt på `api.localaccountsignup`

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinition

**ContentDefinition** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Id | Ja | En identifierare för en innehålls definition. Värdet är ett angivet i avsnittet **innehålls Definitions-ID** senare på den här sidan. |

**ContentDefinition** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | En sträng som innehåller webb adressen till HTML5-sidan för innehålls definitionen. |
| RecoveryUri | 1:1 | En sträng som innehåller URL: en för HTML-sidan för att visa ett fel som rör innehålls definitionen. Används inte för närvarande, värdet måste vara `~/common/default_page_error.html` . |
| DataUri | 1:1 | En sträng som innehåller den relativa URL: en för en HTML-fil som ger användar upplevelsen för att anropa steget. |
| Metadata | 0:1 | En samling nyckel/värde-par som innehåller de metadata som används av innehålls definitionen. |
| LocalizedResourcesReferences | 0:1 | En samling med lokaliserade resurs referenser. Använd det här elementet för att anpassa lokaliseringen av ett användar gränssnitt och ett anspråks attribut. |

### <a name="datauri"></a>DataUri

**DataUri** -elementet används för att ange sid-ID. Azure AD B2C använder sid identifieraren för att läsa in och initiera GRÄNSSNITTs element och Java Script på klient sidan. Formatet för värdet är `urn:com:microsoft:aad:b2c:elements:page-name:version` . I följande tabell visas de sid identifierare som du kan använda.

| Sid identifierare | Beskrivning |
| ----- | ----------- |
| `globalexception` | Visar en felsida när ett undantag eller ett fel påträffas. |
| `providerselection`, `idpselection` | Visar en lista över identitets leverantörer som användarna kan välja bland under inloggningen.  |
| `unifiedssp` | Visar ett formulär för att logga in med ett lokalt konto baserat på en e-postadress eller ett användar namn. Det här värdet ger även "Behåll mig inloggnings funktioner" och "glömt ditt lösen ord?" . |
| `unifiedssd` | Visar ett formulär för att logga in med ett lokalt konto baserat på en e-postadress eller ett användar namn. |
| `multifactor` | Verifierar telefonnummer med hjälp av text eller röst under registrering eller inloggning. |
| `selfasserted` | Visar ett formulär för insamling av data från en användare. Till exempel kan användarna skapa eller uppdatera sin profil. |

### <a name="select-a-page-layout"></a>Välj en sidlayout

Du kan aktivera [JavaScript-kod på klient sidan](javascript-and-page-layout.md) genom att infoga `contract` mellan `elements` och sid typen. Till exempel `urn:com:microsoft:aad:b2c:elements:contract:page-name:version`.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

I [versions](page-layout.md) delen av `DataUri` anges det innehålls paket som innehåller HTML, CSS och Java Script för användar gränssnitts elementen i principen. Om du tänker aktivera Java Script på klient sidan måste de element som du baserar Java Script på vara oföränderliga. Om de inte är oföränderliga kan eventuella ändringar orsaka oväntade beteenden på dina användar sidor. Du kan förhindra dessa problem genom att framtvinga användningen av en sidlayout och ange en version för sidlayouten. På så sätt ser du till att alla innehålls definitioner som du har baserat på Java Script är oföränderliga. Även om du inte tänker aktivera Java Script måste du fortfarande ange sidlayouten för sidorna.

I följande exempel visas **DataUri** för `selfasserted` version `1.2.0` :

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>Migrera till sidlayouten

Formatet för värdet måste innehålla ordet `contract` : _urn: com: Microsoft: AAD: B2C: Elements:**kontrakt**:p ålder-Name: version_. Om du vill ange en sidlayout i dina anpassade principer som använder ett gammalt **DataUri** -värde, använder du följande tabell för att migrera till det nya formatet.

| Gammalt DataUri-värde | Nytt DataUri-värde |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |

I följande exempel visas innehålls definitions identifierarna och motsvarande **DataUri** med den senaste versionen av sidan: 

```xml
<!-- 
<BuildingBlocks> -->
  <ContentDefinitions>
    <ContentDefinition Id="api.error">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.idpselections.signup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.1</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.selfasserted.profileupdate">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.phonefactor">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!-- 
</BuildingBlocks> -->
```

### <a name="metadata"></a>Metadata

Ett **metadataelement** innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| Objekt | 0: n | Metadata som relaterar till innehålls definitionen. |

**Objekt** elementet i **metadata** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Nyckel | Ja | Nyckeln metadata.  |

#### <a name="metadata-keys"></a>Nycklar för metadata

Innehålls definitionen stöder följande metadata:

| Nyckel | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| DisplayName | Inga | En sträng som innehåller namnet på innehålls definitionen. |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

**LocalizedResourcesReferences** -elementet innehåller följande element:

| Element | Förekomster | Beskrivning |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1: n | En lista med lokaliserade resurs referenser för innehålls definitionen. |

**LocalizedResourcesReference** -elementet innehåller följande attribut:

| Attribut | Krävs | Beskrivning |
| --------- | -------- | ----------- |
| Språk | Ja | En sträng som innehåller ett språk som stöds för principen per RFC 5646-taggar för att identifiera språk. |
| LocalizedResourcesReferenceId | Ja | Identifieraren för **LocalizedResources** -elementet. |

I följande exempel visas en registrerings-eller inloggnings innehålls definition med en referens till lokalisering av engelska, franska och spanska:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

Information om hur du lägger till lokaliserings stöd i dina innehålls definitioner finns i [lokalisering](localization.md).

## <a name="content-definition-ids"></a>ID för innehålls definition

ID-attributet för **ContentDefinition** -elementet anger vilken typ av sida som relaterar till innehålls definitionen. Elementet definierar den kontext som en anpassad HTML5/CSS-mall ska använda. I följande tabell beskrivs de olika innehålls Definitions-ID: n som identifieras av identitets miljö ramverket och de sidtyper som är relaterade till dem. Du kan skapa egna innehålls definitioner med ett godtyckligt ID.

| ID | Standardmall | Beskrivning |
| -- | ---------------- | ----------- |
| **API. error** | [exception. cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **Felsida** – visar en felsida när ett undantag eller ett fel påträffas. |
| **API. idpselections** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Sidan Val av identitetsprovider** – visar en lista över identitets leverantörer som användarna kan välja bland under inloggningen. Alternativen är vanligt vis företags identitets leverantörer, sociala identitets leverantörer som Facebook och Google + eller lokala konton. |
| **API. idpselections. signup** | [idpSelector. cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **Val av identitetsprovider för registrering** – visar en lista över identitets leverantörer som användarna kan välja bland vid registreringen. Alternativen är vanligt vis företags identitets leverantörer, sociala identitets leverantörer som Facebook och Google + eller lokala konton. |
| **API. localaccountpasswordreset** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Sidan glömt lösen ord** – visar ett formulär som användarna måste utföra för att initiera en lösen ords återställning. |
| **API. localaccountsignin** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Inloggnings sida för lokalt konto** – visar ett formulär för att logga in med ett lokalt konto som baseras på en e-postadress eller ett användar namn. Formuläret kan innehålla text rutorna text rutor och lösen ord. |
| **API. localaccountsignup** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registrerings sida för lokalt konto** – visar ett formulär för att registrera ett lokalt konto baserat på en e-postadress eller ett användar namn. Formuläret kan innehålla olika inmatnings kontroller, t. ex. en text inmatnings ruta, en ruta för lösen ords inmatning, en alternativ knapp, en listruta med flera val och kryss rutor med flera val. |
| **API. phonefactor** | [multifaktaor-1.0.0. cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Sidan Multi-Factor Authentication** – verifierar telefonnummer med hjälp av text eller röst under registrering eller inloggning. |
| **API. selfasserted** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **Registrerings sida för socialt konto** – visar ett formulär som användarna måste slutföra när de registrerar sig genom att använda ett befintligt konto från en social identitetsprovider. Den här sidan liknar inloggnings sidan för det föregående sociala kontot, förutom fälten för lösen ords inmatning. |
| **API. selfasserted. profileupdate** | [updateprofile. cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **Sidan profil uppdatering** – visar ett formulär som användarna kan använda för att uppdatera sin profil. Den här sidan liknar registrerings sidan för sociala konton, förutom fälten för lösen ords inmatning. |
| **API. signuporsignin** | [Unified. cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **Enhetlig registrering eller inloggnings sida** – hanterar användarens registrerings-och inloggnings process. Användare kan använda företags identitets leverantörer, sociala identitets leverantörer som Facebook, Google + eller lokala konton. |

## <a name="next-steps"></a>Nästa steg

Ett exempel på hur du anpassar användar gränssnittet med hjälp av innehålls definitioner finns i:

[Anpassa ditt programs användar gränssnitt med hjälp av en anpassad princip](customize-ui-with-html.md)
