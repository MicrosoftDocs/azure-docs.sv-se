---
title: Användning av autentiseringsmetoder & insikter – Azure Active Directory
description: Rapportering om användning av lösen ords återställning via självbetjäning i Azure AD och Multi-Factor Authentication
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfff8d450a6506cb37730838e4f70609080ca7b1
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670799"
---
# <a name="authentication-methods-usage--insights-preview"></a>Användning av autentiseringsmetoder & insikter (för hands version)

Med användning & insikter kan du förstå hur autentiseringsmetoder för funktioner som Azure AD Multi-Factor Authentication och lösen ords återställning via självbetjäning fungerar i din organisation. Den här rapporterings funktionen ger din organisation möjlighet att förstå vilka metoder som registreras och hur de används.

## <a name="permissions-and-licenses"></a>Behörigheter och licenser

Följande roller kan komma åt användning och insikter:

- Global administratör
- Säkerhetsläsare
- Säkerhetsadministratör
- Rapportläsare

 En Azure AD Premium P1-eller P2-licens krävs för att komma åt användning och insikter. Azure AD Multi-Factor Authentication och SSPR-licensierings information (Self-Service Password rereset) finns på [Azure Active Directory prissättnings webbplats](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Så här fungerar det

Åtkomst till användning av autentiseringsmetoder och insikter:

1. Bläddra till [Azure Portal](https://portal.azure.com).
1. Bläddra till **Azure Active Directory**  >  användning av **lösen ords återställning**  >  **& insikter**.
1. Från **registreringen** eller **användnings** översikterna kan du välja att öppna de för filtrerade rapporterna för att filtrera efter dina behov.

![Översikt över användning & Insights](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Om du vill komma åt användning & insikter direkt går du till [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) . Med den här länken får du översikt över registreringen.

Användare som är registrerade, användare aktiverade och användare som stöder paneler visar följande registrerings data för dina användare:

- Registrerad: en användare anses vara registrerad om de (eller en administratör) har registrerat tillräckligt autentiseringsmetoder för att uppfylla organisationens SSPR eller Multi-Factor Authentication princip.
- Aktive rad: en användare anses vara aktive rad om de befinner sig inom omfånget för SSPR-principen. Om SSPR har Aktiver ATS för en grupp anses användaren vara aktive rad om de är i gruppen. Om SSPR har Aktiver ATS för alla användare anses alla användare i klient organisationen (exklusive gäster) vara aktiverade.
- Kompatibel: en användare anses vara kapabel om de både är registrerade och aktiverade. Denna status innebär att de kan utföra SSPR när som helst, om det behövs.

Om du klickar på någon av dessa paneler eller de insikter som visas i dem visas en förfiltrerad lista över registrerings information.

I diagrammet **registreringar** på fliken **registrering** visas antalet lyckade och misslyckade registrerings metoder för autentiseringsmetoder efter autentiseringsmetod. Diagrammet **återställs** på fliken **användning** visar antalet lyckade och misslyckade autentiseringar under flödet för lösen ords återställnings flöde per autentiseringsmetod.

Om du klickar på någon av diagrammen visas en förfiltrerad lista över registrerings-eller återställnings händelser.

Med hjälp av kontrollen i det övre högra hörnet kan du ändra datum intervallet för gransknings data som visas i registreringarna och återställa diagram till 24 timmar, 7 dagar eller 30 dagar.

### <a name="registration-details"></a>Registrerings information

Om du klickar på de användare som är **registrerade**, **användare aktiverade** eller **användare som stöder** paneler eller insikter kommer du till registrerings informationen.

Rapporten registrerings information visar följande information för varje användare:

- Namn
- Användarnamn
- Registrerings status (alla, registrerad, ej registrerad)
- Aktive rad status (all, aktive rad, inte aktive rad)
- Kapacitets status (alla, kapabel, kan inte användas)
- Metoder (app notification, app Code, telefonsamtal, SMS, e-post, säkerhets frågor)

Med hjälp av kontrollerna överst i listan kan du söka efter en användare och filtrera listan över användare baserat på de kolumner som visas.

### <a name="reset-details"></a>Återställ information

Om du klickar på registreringar eller återställer diagram går du till återställnings informationen.

I rapporten Återställ detaljer visas registrerings-och återställnings händelser från de senaste 30 dagarna, inklusive:

- Namn
- Användarnamn
- Funktion (all, registrering, återställning)
- Autentiseringsmetod (app-meddelande, app-kod, telefonsamtal, Office-samtal, SMS, e-post, säkerhets frågor)
- Status (alla, lyckades, misslyckades)

Med hjälp av kontrollerna överst i listan kan du söka efter en användare och filtrera listan över användare baserat på de kolumner som visas.

## <a name="limitations"></a>Begränsningar

De data som visas i dessa rapporter kommer att fördröjas med upp till 60 minuter. Fältet "senast uppdaterat" finns i Azure Portal för att identifiera hur senaste dina data är.

Användnings-och insikts data är inte en ersättning för Azure AD Multi-Factor Authentication aktivitets rapporter eller information som finns i rapporten Azure AD-inloggningar.

Rapporten kan för närvarande inte filtreras för att undanta externa användare.

## <a name="next-steps"></a>Nästa steg

- [Arbeta med autentiserings metoder användning rapport-API](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Välja autentiseringsmetoder för din organisation](concept-authentication-methods.md)
- [Kombinerad registrerings upplevelse](concept-registration-mfa-sspr-combined.md)