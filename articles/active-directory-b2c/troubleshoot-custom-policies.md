---
title: Felsöka anpassade principer i Azure Active Directory B2C
description: Lär dig mer om metoder för att lösa fel när du arbetar med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87ea77540e5fcaac0c4231403473d25ebae46aac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94840261"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Felsöka Azure AD B2C anpassade principer och identitets miljö ramverk

Om du använder Azure Active Directory B2C (Azure AD B2C) anpassade principer kan du uppleva utmaningarna med att skapa ett ramverk för identitets upplevelse i XML-formatet för princip språket. Inlärning för att skriva anpassade principer kan vara till exempel att lära sig ett nytt språk. I den här artikeln beskrivs några verktyg och tips som kan hjälpa dig att identifiera och lösa problem.

Den här artikeln fokuserar på fel sökning av din Azure AD B2C anpassade princip konfiguration. Den tar inte itu med det förlitande parts programmet eller dess identitets bibliotek.

## <a name="xml-editing"></a>XML-redigering

Det vanligaste felet vid inställning av anpassade principer är felaktigt formaterat XML. En bra XML-redigerare är nästan viktig. Den visar XML-kod i ursprungligt format, färg kods innehåll, för att fylla i vanliga termer, håller XML-elementen indexerade och kan val IDE ras mot ett XML-schema.

Två av våra favorit redigerare är [Visual Studio Code](https://code.visualstudio.com/) och [anteckningar + +](https://notepad-plus-plus.org/).

Verifiering av XML-schema identifierar fel innan du laddar upp XML-filen. I rotmappen för [Start paketet](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)hämtar du XML-schemats definitions fil *TrustFrameworkPolicy_0.3.0.0. xsd*. Om du vill ta reda på hur du använder XSD schema-filen för verifiering i redigeraren, letar du efter *XML-verktyg* och *XML-verifiering* eller liknande i redigerarens dokumentation.

Du kanske vill ha en granskning av XML-regler som är användbara. Azure AD B2C avvisar eventuella XML-formateringsfel som identifieras. Ibland kan felaktigt formaterade XML orsaka fel meddelanden som är vilseledande.

## <a name="upload-policies-and-policy-validation"></a>Ladda upp principer och princip validering

Valideringen av XML-principagenten utförs automatiskt vid uppladdning. De flesta fel gör att överföringen inte fungerar. Verifiering innehåller princip filen som du överför. Den innehåller också fil kedjan som uppladdnings filen refererar till (den förlitande partens princip fil, tilläggs filen och bas filen).

Vanliga verifierings fel inkluderar följande:

> Fel kodfragment: `...makes a reference to ClaimType with id "displayName" but neither the policy nor any of its base policies contain such an element`

* Värdet för ClaimType kan vara felstavat eller så finns det inte i schemat.
* ClaimType-värden måste definieras i minst en av filerna i principen.
    Exempelvis: `<ClaimType Id="issuerUserId">`
* Om ClaimType definieras i tilläggs filen, men den också används i ett TechnicalProfile-värde i bas filen, resulterar det i ett fel när bas filen laddas upp.

> Fel kodfragment: `...makes a reference to a ClaimsTransformation with id...`

* Orsakerna till det här felet kan vara samma som för ClaimType-felet.

> Fel kodfragment: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`

* Kontrol lera att TenantId-värdet i `<TrustFrameworkPolicy\>` `<BasePolicy\>` elementen och matchar mål Azure AD B2C klient.

## <a name="troubleshoot-the-runtime"></a>Felsöka körningen

* Använd **Kör nu** och `https://jwt.ms` för att testa dina principer oberoende av ditt webb-eller mobil program. Den här webbplatsen fungerar som ett förlitande parts program. Den visar innehållet i JSON-webbtoken (JWT) som genereras av din Azure AD B2Cs princip.

    Så här skapar du ett testprogram som kan omdirigeras till `https://jwt.ms` för kontroll av token:

    [!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

* Använd [Fiddler](https://www.telerik.com/fiddler)om du vill spåra utbytet av meddelanden mellan klientens webbläsare och Azure AD B2C. Det kan hjälpa dig att få en indikation på var din användar resa inte fungerar i ditt Dirigerings steg.

* I **utvecklings läge** använder du [Application Insights](troubleshoot-with-application-insights.md) för att spåra aktiviteten i användar resan för ditt användar gränssnitt i identiteter. I **utvecklings läget** kan du observera utbytet av anspråk mellan identitets miljö ramverket och de olika anspråks leverantörer som definieras av tekniska profiler, till exempel identitets leverantörer, API-baserade tjänster, Azure AD B2C användar katalog och andra tjänster, t. ex. Azure AD Multi-Factor Authentication.

## <a name="recommended-practices"></a>Rekommenderade metoder

**Behåll flera versioner av dina scenarier. Gruppera dem i ett projekt med ditt program.** Filerna Base, Extensions och förlitande part är direkt beroende av varandra. Spara dem som en grupp. När nya funktioner läggs till i dina principer bör du hålla separata arbets versioner. Stegvisa arbets versioner i ditt eget fil system med program koden som de interagerar med. Dina program kan anropa många olika principer för förlitande part i en klient organisation. De kan bli beroende av de anspråk som de förväntar sig från dina Azure AD B2C-principer.

**Utveckla och testa tekniska profiler med kända användar resor.** Använd principer för testade start paket för att konfigurera dina tekniska profiler. Testa dem separat innan du infogar dem i dina egna användar resor.

**Utveckla och testa användar resor med testade tekniska profiler.** Ändra Dirigerings stegen för en användar resa stegvis. Bygg dina avsedda scenarier progressivt.

## <a name="next-steps"></a>Nästa steg

På GitHub, laddar du ned [Active-Directory-B2C-Custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) . zip-arkivet. Du kan också klona lagrings platsen:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
```
