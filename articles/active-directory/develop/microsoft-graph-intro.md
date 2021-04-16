---
title: Microsoft Graph API
description: API:Microsoft Graph är ett RESTful-webb-API som gör att du kan komma åt Microsoft Cloud-tjänstresurser.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: 2e689e620a5aeb7c5028f1a1b30dd6def8e447ab
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529986"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

API:Microsoft Graph är ett RESTful-webb-API som gör att du kan komma åt Microsoft Cloud-tjänstresurser. När du har registrerat din app och fått autentiseringstoken för en användare eller tjänst kan du göra begäranden till Microsoft Graph API. Mer information finns i [Översikt över Microsoft Graph](/graph/overview).

Microsoft Graph exponerar REST-API:er och klientbibliotek för att komma åt data på följande Microsoft 365 tjänster:
- Microsoft 365 tjänster: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner och SharePoint
- Enterprise Mobility and Security Services: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager och Intune
- Windows 10 tjänster: aktiviteter, enheter, meddelanden
- Dynamics 365 Business Central

## <a name="versions"></a>Versioner

Microsoft Graph stöder för närvarande två versioner: v1.0 och beta. Versionen v1.0 innehåller allmänt tillgängliga API:er. Använd v1.0-versionen för alla produktionsappar. Betaversionen innehåller API:er som för närvarande är i förhandsversion. Eftersom vi kan införa större ändringar i våra beta-API:er rekommenderar vi att du endast använder betaversionen för att testa appar som är under utveckling. använd inte beta-API:er i dina produktionsappar. Mer information finns i [Versionshantering, support och policyer för större ändringar för Microsoft Graph](/graph/versioning-and-support).

Information om hur du börjar använda beta-API:Microsoft Graph finns [i Microsoft Graph betaslutpunktsreferens](/graph/api/overview?view=graph-rest-beta&preserve-view=true)

Information om hur du börjar använda v1.0-API:er [finns Microsoft Graph REST API v1.0-referens](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)

## <a name="get-started"></a>Kom igång

Om du vill läsa från eller skriva till en resurs, till exempel en användare eller ett e-postmeddelande, skapar du en begäran som ser ut så här:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Mer information om elementen i den konstruerade begäran finns i [Använda Microsoft Graph API](/graph/use-the-api)

Det finns snabbstartsexempel som visar hur du får åtkomst till kraften i Microsoft Graph API. De exempel som är tillgängliga har åtkomst till två tjänster med en autentisering: Microsoft-konto och Outlook. Varje snabbstart kommer åt information Microsoft-konto användarnas profiler och visar händelser från kalendern.
Snabbstarterna omfattar fyra steg:
- Välj din plattform
- Hämta ditt app-ID (klient-ID)
- Skapa exemplet
- Logga in och visa händelser i kalendern

När du har slutfört snabbstarten har du en app som är redo att köras. Mer information finns i Microsoft Graph [vanliga frågor och svar.](/graph/quick-start-faq) Information om hur du kommer igång med exemplen finns [i Microsoft Graph Snabbstart.](https://developer.microsoft.com/graph/quick-start)

## <a name="tools"></a>Verktyg

Microsoft Graph Explorer är ett webbaserat verktyg som du kan använda för att skapa och testa begäranden med hjälp av Microsoft Graph-API:er. Du kan komma Microsoft Graph Explorer på: `https://developer.microsoft.com/graph/graph-explorer` .

Postman är ett verktyg som du också kan använda för att skapa och testa begäranden med hjälp Microsoft Graph API:er. Du kan ladda ned Postman på: `https://www.getpostman.com/` . Om du vill Microsoft Graph i Postman använder du Microsoft Graph i Postman. Mer information finns i [Använda Postman med Microsoft Graph API.](/graph/use-postman)
