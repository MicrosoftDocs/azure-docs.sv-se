---
title: Metoder för användarmigrering
titleSuffix: Azure AD B2C
description: Migrera användar konton från en annan identitetsprovider till Azure AD B2C med hjälp av metoderna för migrering eller sömlös migrering.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2d4a61f653c5bedb31223d2eb3d37b92a076821
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010175"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Migrera användare till Azure AD B2C

Att migrera från en annan identitetsprovider till Azure Active Directory B2C (Azure AD B2C) kan också kräva att befintliga användar konton migreras. Två metoder för migrering beskrivs här, *för migrering* och *sömlös migrering*. Med båda metoderna måste du skriva ett program eller skript som använder [Microsoft Graph-API: et](microsoft-graph-operations.md) för att skapa användar konton i Azure AD B2C.

## <a name="pre-migration"></a>Före migrering

I flödet för migrering utför migreringen följande steg för varje användar konto:

1. Läs användar kontot från den gamla identitets leverantören, inklusive dess aktuella autentiseringsuppgifter (användar namn och lösen ord).
1. Skapa ett motsvarande konto i din Azure AD B2C katalog med de aktuella autentiseringsuppgifterna.

Använd för inmigrerings flödet i någon av dessa två situationer:

- Du har åtkomst till en användares autentiseringsuppgifter för oformaterad text (användar namn och lösen ord).
- Autentiseringsuppgifterna är krypterade, men du kan dekryptera dem.

Information om hur du skapar användar konton via programmering finns i [hantera Azure AD B2C användar konton med Microsoft Graph](microsoft-graph-operations.md).

## <a name="seamless-migration"></a>Sömlös migrering

Använd det sömlösa migreringsjobbet om lösen ord för klartext i den gamla identitets leverantören inte är tillgängliga. Här är några exempel:

- Lösen ordet lagras i ett enkelriktat krypterat format, t. ex. med en hash-funktion.
- Lösen ordet lagras av den äldre identitets leverantören på ett sätt som du inte kan komma åt. Till exempel när identitets leverantören verifierar autentiseringsuppgifter genom att anropa en webb tjänst.

Det sömlösa migreringsjobbet kräver fortfarande för migrering av användar konton, men använder sedan en [anpassad princip](custom-policy-get-started.md) för att fråga en [REST API](custom-policy-rest-api-intro.md) (som du skapar) för att ange varje användares lösen ord vid första inloggningen.

Det sömlösa migreringsjobbet har därför två faser: *för migrering* och *ange autentiseringsuppgifter*.

### <a name="phase-1-pre-migration"></a>Fas 1: för migrering

1. Ditt program för migrering läser användar kontona från den gamla identitets leverantören.
1. Programmet för migrering skapar motsvarande användar konton i din Azure AD B2C katalog, men *anger slumpmässiga lösen ord* som du skapar.

### <a name="phase-2-set-credentials"></a>Fas 2: ange autentiseringsuppgifter

När du har migrerat kontona är det en anpassad princip och REST API sedan utföra följande när en användare loggar in:

1. Läs Azure AD B2C användar kontot som motsvarar den angivna e-postadressen.
1. Kontrol lera om kontot är flaggat för migrering genom att utvärdera ett booleskt tilläggs attribut.
    - Om attributet för tillägg returnerar `True` , anropar du REST API för att verifiera lösen ordet mot den äldre identitets leverantören.
      - Om REST API anger att lösen ordet är felaktigt, returnerar du ett eget fel för användaren.
      - Om REST API anger att lösen ordet är korrekt skriver du lösen ordet till Azure AD B2C-kontot och ändrar attributet Boolean-tillägg till `False` .
    - Om attributet Boolean-tillägg returnerar `False` , Fortsätt inloggnings processen som vanligt.

Om du vill se ett exempel på en anpassad princip och REST API går du till [exemplet på sömlös](https://aka.ms/b2c-account-seamless-migration) användarmigrering på GitHub.

![Flödes schema diagram över sömlös migrering för att migrera användare](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagram: sömlöst migreringsjobb*

## <a name="best-practices"></a>Bästa praxis

### <a name="security"></a>Säkerhet

Den sömlösa migreringen använder egna anpassade REST API för att verifiera en användares autentiseringsuppgifter mot den äldre identitets leverantören.

**Du måste skydda din REST API mot brute-force-attacker.** En angripare kan skicka flera lösen ord i hoppas att du vill gissa användarens autentiseringsuppgifter. För att förhindra sådana angrepp kan du sluta betjäna begär anden till din REST API när antalet inloggnings försök passerar ett visst tröskelvärde. Skydda även kommunikationen mellan Azure AD B2C och REST API. Information om hur du skyddar dina RESTful-API: er för produktion finns i [Secure RESTful API](secure-rest-api.md).

### <a name="user-attributes"></a>Användarattribut

All information i den äldre identitets leverantören bör inte migreras till din Azure AD B2Cs katalog. Identifiera lämplig uppsättning användarattribut som ska lagras i Azure AD B2C innan du migrerar.

- **Lagra i** Azure AD B2C
  - Användar namn, lösen ord, e-postadresser, telefonnummer, medlemskaps nummer/identifierare.
  - Medgivande markörer för sekretess policy och slut användar licens avtal.
- Lagra **inte** i Azure AD B2C
  - Känsliga data som kreditkorts nummer, person nummer (SSN), medicinska uppgifter eller andra data som regleras av myndigheter eller organisationer som uppfyller bransch kraven.
  - Inställningar för marknadsföring eller kommunikation, användar beteenden och insikter.

### <a name="directory-clean-up"></a>Rensa katalog

Innan du påbörjar migreringsprocessen, kan du ta chansen att rensa katalogen.

- Identifiera den uppsättning användarattribut som ska lagras i Azure AD B2C och migrera bara vad du behöver. Om det behövs kan du skapa [anpassade attribut](user-flow-custom-attributes.md) för att lagra mer data om en användare.
- Om du migrerar från en miljö med flera källor för autentisering (till exempel varje program har en egen användar katalog), migrera till ett enhetligt konto i Azure AD B2C.
- Om flera program har olika användar namn kan du lagra dem i ett Azure AD B2C användar konto med hjälp av samlingen identiteter. Med avseende på lösen ordet kan användaren välja en och ange den i katalogen. Med den sömlösa migreringen ska du till exempel bara lagra det valda lösen ordet i Azure AD B2C-kontot.
- Ta bort oanvända användar konton före migrering eller migrera inte inaktuella konton.

### <a name="password-policy"></a>Lösen ords princip

Om de konton som du migrerar har svag lösen ords styrka än den [starka lösen ords styrkan](../active-directory/authentication/concept-sspr-policy.md) som tillämpas av Azure AD B2C, kan du inaktivera kravet på starkt lösen ord. Mer information finns i [egenskapen lösen ords princip](user-profile-attributes.md#password-policy-attribute).

## <a name="next-steps"></a>Nästa steg

[Azure-AD-B2C/User-migration](https://github.com/azure-ad-b2c/user-migration) -lagringsplatsen på GitHub innehåller ett sömlöst exempel på en anpassad princip för migrering och REST API kod exempel:

[Anpassad princip för migrering av en användare & REST API kod exempel](https://aka.ms/b2c-account-seamless-migration)
