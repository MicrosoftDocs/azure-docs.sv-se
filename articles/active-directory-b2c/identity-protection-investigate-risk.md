---
title: Undersök risker med Azure Active Directory B2C identitets skydd
description: Lär dig hur du undersöker riskfyllda användare och identifieringar i Azure AD B2C Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8919285f31e04a51ce10afe3313b28cf86b64ee0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055719"
---
# <a name="investigate-risk-with-identity-protection-in-azure-ad-b2c"></a>Undersök risker med identitets skydd i Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Identitets skydd ger kontinuerlig identifiering av risker för din Azure AD B2C klient. Det gör det möjligt för organisationer att upptäcka, undersöka och åtgärda identitetsbaserade risker. Identitets skydd innehåller risk rapporter som kan användas för att undersöka identitets risker i Azure AD B2C klienter. I den här artikeln får du lära dig hur du undersöker och minimerar risker.

## <a name="overview"></a>Översikt

Azure AD B2C Identity Protection innehåller två rapporter. Rapporten *riskfyllda användare* är den plats där administratörer kan hitta vilka användare som är utsatta för risk och information om identifieringar. Rapporten *risk identifieringar* innehåller information om varje risk identifiering, inklusive typ, andra risker som utlöses vid samma tidpunkt, plats för inloggnings försök och mer.

Varje rapport öppnas med en lista över alla identifieringar för den period som visas överst i rapporten. Rapporter kan filtreras med filtren högst upp i rapporten. Administratörer kan välja att hämta data eller använda [MS Graph API och Microsoft Graph POWERSHELL SDK](../active-directory/identity-protection/howto-identity-protection-graph-api.md) för att kontinuerligt exportera data.

## <a name="service-limitations-and-considerations"></a>Begränsningar och överväganden för tjänsten

När du använder identitets skydd bör du tänka på följande:

- Identity Protection är aktiverat som standard.
- Identitets skydd är tillgängligt för både lokala och sociala identiteter, till exempel Google eller Facebook. För sociala identiteter måste villkorlig åtkomst aktive ras. Identifieringen är begränsad eftersom autentiseringsuppgifterna för sociala konton hanteras av den externa identitets leverantören.
- I Azure AD B2C klienter är endast en del av [Azure AD Identity Protection risk identifieringar](../active-directory/identity-protection/overview-identity-protection.md) tillgängliga. Följande identifieringar av risker stöds av Azure AD B2C:  

|Typ av risk identifiering  |Beskrivning  |
|---------|---------|
| Ovanlig resa     | Logga in från en ovanlig-plats baserat på användarens senaste inloggningar.        |
|Anonym IP-adress     | Logga in från en anonym IP-adress (t. ex. Tor webbläsare, Anonymizer VPN).        |
|Länkad IP-adress för skadlig kod     | Logga in från en länkad IP-adress med skadlig kod.         |
|Obekanta inloggningsegenskaper     | Logga in med egenskaper som vi inte har sett nyligen för den angivna användaren.        |
|Administratören bekräftade att användaren har komprometterats    | En administratör har angett att en användare har komprometterats.             |
|Lösen ords sprayning     | Logga in via ett angrepp vid lösen ords spridning.      |
|Azure AD Threat Intelligence     | Microsofts interna och externa hot informations källor har identifierat ett känt angrepps mönster.        |

## <a name="pricing-tier"></a>Prisnivå

Azure AD B2C Premium P2 krävs för vissa identitets skydds funktioner. Om det behövs [ändrar du Azure AD B2C pris nivå till Premium P2](./billing.md). I följande tabell sammanfattas identitets skydds funktionerna och den pris nivå som krävs.  

|Funktion   |P1   |P2|
|----------|:-----------:|:------------:|
|Rapport över riskfyllda användare     |&#x2713; |&#x2713; |
|Rapport information om riskfyllda användare  | |&#x2713; |
|Uppdatering av riskfyllda användare    | &#x2713; |&#x2713; |
|Rapport över risk identifieringar   |&#x2713;|&#x2713;|
|Rapportinformation om risk identifiering  ||&#x2713;|
|Hämta rapport |  &#x2713;| &#x2713;|
|MS Graph API-åtkomst |  &#x2713;| &#x2713;|

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="investigate-risky-users"></a>Undersöka riskfyllda användare

Med den information som rapporteras av riskfyllda användare kan administratörerna hitta:

- Vilka användare är utsatta för risker har de haft risk åtgärdade eller har haft risk har avslagits?
- Information om identifieringar
- Historik över alla riskfyllda inloggningar
- Risk historik
 
Administratörer kan sedan välja att vidta åtgärder för dessa händelser. Administratörer kan välja att:

- Återställ användar lösen ordet
- Bekräfta komprometterade användare
- Ignorera användar risk
- Blockera användare från att logga in
- Undersök ytterligare med Azure ATP

### <a name="navigating-the-risky-users-report"></a>Navigera i rapporten riskfyllda användare

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj ikonen **katalog + prenumeration** i portalens verktygsfält och välj sedan den katalog som innehåller Azure AD B2C klienten.

1. Under **Azure-tjänster** väljer du **Azure AD B2C**. Eller Använd sökrutan för att söka efter och välja **Azure AD B2C**.

1. Under **säkerhet** väljer du **riskfyllda användare (förhands granskning)**.

   ![Riskfyllda användare](media/identity-protection-investigate-risk/risky-users.png)

    Om du väljer enskilda poster expanderas ett informations fönster under identifieringarna. I vyn information kan administratörer undersöka och utföra åtgärder för varje identifiering.

    ![Riskfyllda användar åtgärder](media/identity-protection-investigate-risk/risky-users-report-actions.png)


## <a name="risk-detections-report"></a>Rapport över risk identifieringar

Rapporten risk identifieringar innehåller filtrerings bara data för upp till de senaste 90 dagarna (tre månader).

Med den information som tillhandahålls av rapporten risk identifiering kan administratörerna hitta:

- Information om varje risk identifiering, inklusive typ.
- Andra risker utlöses samtidigt.
- Plats för inloggnings försök.

Administratörer kan sedan välja att återgå till användarens risk-eller inloggnings rapport för att vidta åtgärder baserat på insamlad information.

### <a name="navigating-the-risk-detections-report"></a>Navigera i rapporten risk identifieringar

1. I Azure Portal söker du efter och väljer **Azure AD B2C**.
1. Under **säkerhet** väljer du **risk identifieringar (för hands version)**.

   ![Riskidentifieringar](media/identity-protection-investigate-risk/risk-detections.png)


## <a name="next-steps"></a>Nästa steg

- [Lägg till villkorlig åtkomst till ett användar flöde](conditional-access-user-flow.md).