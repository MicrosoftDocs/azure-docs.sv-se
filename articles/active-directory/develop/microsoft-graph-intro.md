---
title: Microsoft Graph API
description: Microsoft Graph API är ett RESTful webb-API som gör att du kan komma åt Microsoft Cloud tjänst resurser.
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
ms.openlocfilehash: a799d7c32ad632303931adf838ca8e2e715b2adc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860192"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API är ett RESTful webb-API som gör att du kan komma åt Microsoft Cloud tjänst resurser. När du har registrerat din app och get Authentication-token för en användare eller tjänst kan du göra förfrågningar till Microsoft Graph API. Mer information finns i [Översikt över Microsoft Graph](/graph/overview).

Microsoft Graph exponerar REST-API: er och klient bibliotek för att komma åt data på följande Microsoft 365-tjänster:
- Microsoft 365 tjänster: Delve, Excel, Microsoft-bokningar, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner och SharePoint
- Enterprise Mobility and Security Services: Advanced Threat Analytics, Avancerat skydd, Azure Active Directory, Identity Manager och Intune
- Windows 10-tjänster: aktiviteter, enheter, meddelanden
- Dynamics 365 Business Central

## <a name="versions"></a>Versioner

Microsoft Graph stöder för närvarande två versioner: v 1.0 och beta. V 1.0-versionen innehåller allmänt tillgängliga API: er. Använd v 1.0-versionen för alla produktions program. Beta versionen innehåller API: er som för närvarande finns i för hands version. Eftersom vi kan införa ändringar i våra beta-API: er, rekommenderar vi att du bara använder Beta versionen för att testa appar som utvecklas. Använd inte beta-API: er i dina produktions program. Mer information finns i [Versioning, support och brytande ändrings principer för Microsoft Graph](/graph/versioning-and-support).

För att börja använda beta-API: erna, se [Microsoft Graph beta slut punkts referens](/graph/api/overview?view=graph-rest-beta)

Om du vill börja använda v 1.0-API: erna, se [Microsoft Graph REST API v 1.0-referens](/graph/api/overview)

## <a name="get-started"></a>Kom igång

Om du vill läsa från eller skriva till en resurs, till exempel en användare eller ett e-postmeddelande, skapar du en begäran som ser ut ungefär så här:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Mer information om elementen i den konstruerade begäran finns i [använda Microsoft Graph API](/graph/use-the-api)

Snabb starts exempel finns tillgängliga för att visa hur du får åtkomst till kraften i Microsoft Graph-API: et. Exempel som är tillgängliga för åtkomst till två tjänster med en autentisering: Microsoft-konto och Outlook. Varje snabb start kommer åt information från Microsoft-konto användarnas profiler och visar händelser från deras kalender.
Snabb starterna omfattar fyra steg:
- Välj din plattform
- Hämta ditt app-ID (klient-ID)
- Bygg exemplet
- Logga in och Visa händelser i din kalender

När du har slutfört snabb starten har du en app som är redo att köras. Mer information finns i [vanliga frågor och svar om Microsoft Graph snabb start](/graph/quick-start-faq). Information om hur du kommer igång med exemplen finns i [Microsoft Graph snabb start](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Verktyg

Microsoft Graph Explorer är ett webbaserat verktyg som du kan använda för att bygga och testa förfrågningar med hjälp av Microsoft Graph-API: er. Du kan komma åt Microsoft Graph Explorer på: `https://developer.microsoft.com/graph/graph-explorer` .

Postman är ett verktyg som du kan använda för att bygga och testa förfrågningar med hjälp av Microsoft Graph API: er. Du kan hämta Postman på: `https://www.getpostman.com/` . Om du vill interagera med Microsoft Graph i Postman använder du samlingen Microsoft Graph i Postman. Mer information finns i [använda Postman med Microsoft Graph-API: et](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta).
