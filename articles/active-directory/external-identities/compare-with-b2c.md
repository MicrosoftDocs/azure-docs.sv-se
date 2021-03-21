---
title: Jämför externa identiteter – Azure Active Directory | Microsoft Docs
description: Externa Azure AD-identiteter gör att personer utanför organisationen kan komma åt dina appar och resurser med sin egen identitet. Jämför lösningar för externa identiteter, inklusive Azure Active Directory B2B-samarbete och Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 03/02/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78fd91e2ba7badb1936b8603c8ed089130b29ffb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644073"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Vad är externa identiteter i Azure Active Directory?

Med externa identiteter i Azure AD kan du ge personer utanför organisationen åtkomst till dina appar och resurser, samtidigt som de kan logga in med vilken identitet de föredrar. Dina partner, distributörer, leverantörer, leverantörer och andra gäst användare kan "ta med sig sina egna identiteter". Oavsett om de har fått en digital identitet eller en icke hanterad person som Google eller Facebook kan de använda sina egna autentiseringsuppgifter för att logga in. Den externa användarens identitetsprovider hanterar sin identitet och du kan hantera åtkomsten till dina appar med Azure AD för att skydda dina resurser.

## <a name="external-identities-scenarios"></a>Scenarier för externa identiteter

Externa Azure AD-identiteter fokuserar mindre på användarens relation till din organisation och mer om hur användaren vill logga in på dina appar och resurser. I det här ramverket har Azure AD stöd för en mängd olika scenarier från Business-to-Business (B2B)-samarbete för att komma åt hantering för kunder/kund-eller program som riktas mot allmänheten (företags-till-kund eller B2C).

- **Dela dina appar och resurser med externa användare (B2B-samarbete)**. Bjud in externa användare till din egen klient organisation som "gäst"-användare som du kan tilldela behörigheter till (för auktorisering) samtidigt som de låter dem använda sina befintliga autentiseringsuppgifter (för autentisering). Användarna loggar in på de delade resurserna med hjälp av en enkel Inbjudnings-och inlösnings process med sitt arbete, skola eller annat e-postkonto. Du kan också använda [hantering av Azure AD](../governance/entitlement-management-overview.md) -behörighet för att konfigurera principer som [hanterar åtkomst för externa användare](../governance/entitlement-management-external-users.md#how-access-works-for-external-users). Och nu med tillgängligheten av [användar flöden för självbetjänings registrering](self-service-sign-up-overview.md)kan du tillåta externa användare att registrera sig för själva program. Upplevelsen kan anpassas för att möjliggöra registrering med en arbets-, skol-eller social identitet (som Google eller Facebook). Du kan också samla in information om användaren under registrerings processen. Mer information finns i [Azure AD B2B-dokumentationen](index.yml).

- **Bygg användar resan med en lösning för identitets hantering med vit etikett för konsument-och kundriktade appar (Azure AD B2C)**. Om du är ett företag eller en utvecklare som skapar kundrelaterade appar kan du skala till miljon tals konsumenter, kunder eller medborgare med hjälp av Azure AD B2C. Utvecklare kan använda Azure AD som det fullständiga CIAM-systemet (Customer Identity and Access Management) för sina program. Kunder kan logga in med en identitet som de redan har upprättat (t. ex. Facebook eller Gmail). Med Azure AD B2C kan du helt anpassa och styra hur kunderna registrerar sig, loggar in och hanterar sina profiler när de använder dina program. Mer information finns i Azure AD B2C- [dokumentationen](../../active-directory-b2c/index.yml).

## <a name="compare-external-identities-solutions"></a>Jämför lösningar för externa identiteter

Följande tabell innehåller en detaljerad jämförelse av de scenarier som du kan aktivera med externa Azure AD-identiteter.

|   | Samarbete mellan externa användare (B2B) | Åtkomst till konsumenter/kundappar (B2C)  |
| ---- | --- | --- |
| **Primärt scenario** | Samarbete med Microsoft-program (Microsoft 365, team osv.) eller dina egna program (SaaS-appar, anpassade appar osv.).  | Identitets-och åtkomst hantering för moderna SaaS eller anpassade program (inte första parts Microsoft-appar).   |
| **Avsett för**    | Samar beta med affärs partner från externa organisationer som leverantörer, partner och leverantörer. Användare visas som gäst användare i din katalog. De här användarna kan eventuellt inte hantera dem.  | Kunder av produkten. Dessa användare hanteras i en separat Azure AD-katalog.  |
| **Identitets leverantörer som stöds** | Externa användare kan samar beta med arbets konton, skol konton, e-postadresser, SAML och WS-Fed baserade identitets leverantörer, Gmail och Facebook.  | Konsument användare med lokala program konton (valfri e-postadress eller användar namn), olika sociala identiteter som stöds och användare med företags-och myndighets utfärdade identiteter via direkt Federation.       |
| **Hantering av externa användare**   | Externa användare hanteras i samma katalog som anställda, men är vanligt vis kommenterade som gäst användare. Gäst användare kan hanteras på samma sätt som anställda, som läggs till i samma grupper och så vidare.    | Externa användare hanteras i Azure AD B2C-katalogen. De hanteras separat från organisationens anställde och partner katalog (om det finns några).  |
| **Enkel inloggning (SSO)**      | SSO till alla Azure AD-anslutna appar stöds. Du kan till exempel ge åtkomst till Microsoft 365 eller lokala appar och till andra SaaS-appar som Salesforce eller Workday.    | Stöd för enkel inloggning till kundägda appar i Azure AD B2C-klientorganisationer. SSO till Microsoft 365 eller till andra Microsoft SaaS-appar stöds inte.    |
| **Säkerhets principer och efterlevnad**        | Hanteras av den värd/bjudande organisationen (till exempel med [principer för villkorlig åtkomst](conditional-access.md)). | Hanteras av organisationen via villkorlig åtkomst och identitets skydd.        |
| **Anpassning**  | Värd/Bjud in organisationens varumärke används.    | Helt anpassningsbar anpassning per program eller organisation.   |
| **Faktureringsmodell** | [Prissättning för externa identiteter](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) baserat på månatliga aktiva användare (MAU). <br>(Se även: [B2B-installations information](external-identities-pricing.md)) | [Prissättning för externa identiteter](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) baserat på månatliga aktiva användare (MAU). <br>(Se även: [B2C setup details](../../active-directory-b2c/billing.md)) |
| **Mer information** | [Blogginlägg](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Dokumentation](what-is-b2b.md)                   | [Produktsida](https://azure.microsoft.com/services/active-directory-b2c/), [Dokumentation](../../active-directory-b2c/index.yml)       |

Skydda och hantera kunder och partners bortom organisationens gränser med externa Azure AD-identiteter.

## <a name="about-multitenant-applications"></a>Om program med flera klienter

Om du tillhandahåller en app som en tjänst och inte vill hantera dina kunders användar konton, är en app för flera klient organisationer det rätta valet för dig. När du utvecklar program som är avsedda för andra Azure AD-klienter kan du rikta användare från en enda organisation (en enda klient) eller användare från vilken organisation som helst som redan har en Azure AD-klient (flera klient program). Appregistreringar i Azure AD är en enda klient som standard, men du kan göra registreringen av flera klienter. Detta program för flera innehavare registreras en gång av dig själv i din egen Azure AD. Men alla Azure AD-användare från en organisation kan använda programmet utan ytterligare arbete på din sida. Mer information finns i [Hantera identitet i program](/azure/architecture/multitenant-identity/)med flera klienter, [instruktions guide](../develop/howto-convert-app-to-be-multi-tenant.md).

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Om Azure AD B2C](../../active-directory-b2c/overview.md)