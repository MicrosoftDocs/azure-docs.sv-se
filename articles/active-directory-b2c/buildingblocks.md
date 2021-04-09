---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Ange BuildingBlocks-elementet för en anpassad princip i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a42c1d06051c283f0e911c4cd166884ddd060f45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633282"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**BuildingBlocks** -elementet läggs till i [TrustFrameworkPolicy](trustframeworkpolicy.md) -elementet.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

**BuildingBlocks** -elementet innehåller följande element som måste anges i den definierade ordningen:

- [ClaimsSchema](claimsschema.md) – definierar de anspråks typer som kan refereras till som en del av principen. Anspråks schema är den plats där du deklarerar dina anspråks typer. En anspråks typ liknar en variabel i många programmerings språk. Du kan använda anspråks typen för att samla in data från användare av ditt program, ta emot anspråk från sociala identitets leverantörer, skicka och ta emot data från en anpassad REST API eller lagra interna data som används av den anpassade principen.

- [Predikat och PredicateValidationsInput](predicates.md) – gör att du kan utföra en verifierings process för att säkerställa att endast korrekt utformade data anges i ett anspråk.

- [ClaimsTransformations](claimstransformations.md) – innehåller en lista med anspråks omvandlingar som kan användas i principen.  En anspråks omvandling konverterar ett anspråk till ett annat. I omvandlings deklarationen anger du en Transformations metod, till exempel:
  - Ändra Skift läget för ett sträng anspråk till det som angetts. Du kan till exempel ändra en sträng från gemener till versaler.
  - Jämför två anspråk och returnerar ett anspråk med True och anger att anspråk matchar, annars falskt.
  - Skapar ett sträng anspråk från den angivna parametern i principen.
  - Skapar en slumpmässig sträng med slump tals generatorn.
  - Formatera ett anspråk enligt den angivna format strängen. Den här omvandlingen använder C#- `String.Format` metoden.

- InputValidation – det här elementet gör att du kan utföra booleska agg regeringar som liknar *och* och *eller*.

- [ContentDefinitions](contentdefinitions.md) – innehåller URL: er för HTML5-mallar som ska användas i användar resan. I en anpassad princip definierar en innehålls definition den HTML5-sid-URI som används för ett angivet steg i användar resan. Till exempel inloggnings-eller registrerings-, lösen ords återställning eller fel sidor. Du kan ändra utseendet och känslan genom att åsidosätta LoadUri för HTML5-filen. Eller så kan du skapa nya innehålls definitioner efter dina behov. Det här elementet kan innehålla en lokaliserad resurs referens med hjälp av ett lokaliserings-ID.

- [Lokalisering](localization.md) – gör att du kan stödja flera språk. Lokaliserings stödet i principer gör att du kan ställa in listan över språk som stöds i en princip och välja ett standard språk. Språkspecifika strängar och samlingar stöds också.

- [DisplayControls](display-controls.md) – definierar de kontroller som ska visas på en sida. Visa kontroller har särskilda funktioner och interagera med tekniska profiler för Server dels verifiering. 
