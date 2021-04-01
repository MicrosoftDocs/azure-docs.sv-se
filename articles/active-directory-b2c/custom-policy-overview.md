---
title: Azure Active Directory B2C anpassade principer | Microsoft Docs
description: Lär dig mer om att Azure Active Directory B2C anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 043cf83d804803e1b5b47d3ac51bbccaa06e4e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87116425"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Anpassade principer är konfigurationsfiler som definierar beteendet för din Azure Active Directory B2C-klientorganisation (Azure AD B2C). Användarflöden för de vanligaste identitetsuppgifterna är fördefinierade i Azure AD B2C-portalen. Identitetsutvecklare kan redigera anpassade principer för att utföra många olika uppgifter.

## <a name="comparing-user-flows-and-custom-policies"></a>Jämföra användar flöden och anpassade principer

| Kontext | Användarflöden | Anpassade principer |
|-|-------------------|-----------------|
| Mål användare | Alla program utvecklare med eller utan identitets kunskaper. | Identitets tekniker, system integrerare, konsulter och interna identitets team. De är bekvämt med OpenID Connect-flöden och förstå identitets leverantörer och anspråksbaserad autentisering. |
| Konfigurations metod | Azure Portal med ett användarvänligt användar gränssnitt (UI). | Redigera XML-filer direkt och ladda upp till Azure Portal. |
| UI-anpassning | Fullständig anpassning av gränssnittet, inklusive HTML, CSS och Java Script.<br><br>Stöd för flera språk med anpassade strängar. | Samma |
| Attribut anpassning | Standard-och anpassade attribut. | Samma |
| Hantering av token och sessioner | Alternativ för anpassad token och flera sessioner. | Samma |
| Identitetsprovidrar | Fördefinierad lokal eller social Provider och de flesta OIDC-identitets leverantörer, till exempel Federation med Azure Active Directory klienter. | Standardbaserad OIDC, OAUTH och SAML.  Autentisering är också möjligt med hjälp av integrering med REST API: er. |
| Identitets uppgifter | Registrera dig eller logga in med lokala eller många sociala konton.<br><br>Lösen ords återställning via självbetjäning.<br><br>Redigera profil.<br><br>Multi-Factor Authentication.<br><br>Anpassa token och sessioner.<br><br>Flöden för åtkomsttoken. | Slutför samma uppgifter som användar flöden med anpassade identitets leverantörer eller Använd anpassade omfattningar.<br><br>Etablera ett användar konto i ett annat system vid tidpunkten för registreringen.<br><br>Skicka ett välkomst meddelande med din egen e-posttjänstprovider.<br><br>Använd ett användar Arkiv utanför Azure AD B2C.<br><br>Verifiera användarens angivna information med ett betrott system med hjälp av ett API. |

## <a name="policy-files"></a>Principfiler

Dessa tre typer av principfiler används:

- **Bas fil** – innehåller de flesta av definitionerna. Vi rekommenderar att du gör ett minsta antal ändringar i den här filen för att felsöka och långsiktigt underhåll av dina principer.
- **Fil tillägg** – innehåller de unika konfigurations ändringarna för din klient.
- **Förlitande part (RP)-fil** – den enda Task-fokuserade filen som anropas direkt av programmet eller tjänsten (kallas även förlitande part). Varje unik aktivitet kräver sin egen RP och, beroende på varumärkes krav, antalet kan vara "totalt antal program x totalt antal användnings fall".

Användar flöden i Azure AD B2C följer fil mönstret som illustreras ovan, men utvecklaren ser bara RP-filen, medan Azure Portal gör ändringar i bakgrunden till tilläggs filen.

Även om det finns tre typer av principfiler är du inte begränsad till tre filer. Du kan ha flera filer av varje filtyp. Om du t. ex. inte vill göra ändringar i tilläggs filen kan du skapa en Extensions2-fil för att ytterligare utöka tilläggs filen.

## <a name="custom-policy-core-concepts"></a>Grundläggande begrepp för anpassad princip

Tjänsten för kund identitets-och åtkomst hantering (CIAM) i Azure innehåller:

- En användar katalog som är tillgänglig med hjälp av Microsoft Graph och som innehåller användar data för både lokala konton och federerade konton.
- Åtkomst till det **identitets miljö ramverk** som dirigerar förtroende mellan användare och entiteter och skickar anspråk mellan dem för att slutföra en identitets-eller åtkomst hanterings uppgift.
- En säkerhetstokentjänst (STS) som utfärdar ID-token, uppdaterar tokens och åtkomsttoken (och motsvarande SAML-kontroller) och validerar dem för att skydda resurser.

Azure AD B2C interagerar med identitets leverantörer, användare, andra system och med den lokala användar katalogen i turordning för att få en identitets uppgift. Du kan till exempel Logga in en användare, registrera en ny användare eller återställa ett lösen ord. I ramverket med identitets upplevelsen och en princip (kallas även för en användar resa eller en princip för förtroende ramverk) fastställs flera parter och det uttryckligen definierar aktörerna, åtgärderna, protokollen och hur många steg som ska utföras.

Identitets Miljös ramverket är en helt konfigurerbar, molnbaserad, molnbaserad, molnbaserad Azure-plattform som dirigerar förtroende mellan entiteter i standard protokoll format, till exempel OpenID Connect, OAuth, SAML och några icke-standardvärden, till exempel för REST API-baserade system-till-system-anspråk. Ramverket skapar användarvänliga, vita och märkta upplevelser som stöder HTML och CSS.

En anpassad princip representeras som en eller flera XML-formaterade filer som refererar till varandra i en hierarkisk kedja. XML-elementen definierar anspråks schema, anspråks omvandlingar, innehålls definitioner, anspråks leverantörer, tekniska profiler och användnings steg för användar resan bland andra element. En anpassad princip är tillgänglig som en eller flera XML-filer som körs av identitets miljö ramverket när den anropas av en förlitande part. Utvecklare som konfigurerar anpassade principer måste definiera de betrodda relationerna i noggrann detalj för att inkludera slut punkter för metadata, exakta definitioner för anspråk och konfigurera hemligheter, nycklar och certifikat efter behov av varje identitets leverantör.

### <a name="inheritance-model"></a>Arvs modell

När ett program anropar RP-principagenten, lägger identitets miljö ramverket i Azure AD B2C till alla element från bas filen, från tilläggs filen och sedan från RP-princip filen för att sammanställa den aktuella principen.  Element av samma typ och namn i RP-filen åsidosätter de i tilläggen och tilläggen åsidosätter bas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med anpassade principer](custom-policy-get-started.md)
