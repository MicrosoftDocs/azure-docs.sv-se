---
title: Support-och hjälp alternativ för Azure AD Apps-utvecklare
description: Lär dig mer om att få hjälp och support för utvecklings-relaterade frågor och problem när du skapar program som integreras med Microsoft-identiteter (Azure Active Directory och Microsoft-konto)
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 1d50e0ed6ac67ed1595b289d27bd08a089840308
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051914"
---
# <a name="support-and-help-options-for-developers"></a>Support- och hjälpalternativ för utvecklare

Om du bara börjar integrera med Azure Active Directory (Azure AD), Microsoft-identiteter eller Microsoft Graph API eller när du implementerar en ny funktion för ditt program, finns det tillfällen när du behöver få hjälp från communityn eller förstå de support alternativ som du har som utvecklare. Den här artikeln hjälper dig att förstå dessa alternativ, inklusive:

> [!div class="checklist"]
> * Så här söker du efter om din fråga inte har besvarats av communityn eller om en befintlig dokumentation för funktionen som du försöker implementera redan finns
> * I vissa fall vill du bara använda våra support verktyg för att felsöka ett visst problem
> * Om du inte hittar det svar du behöver kan du ställa en fråga på *Microsoft Q&a*
> * Om du hittar ett problem med ett av våra autentiseringspaket, Utlös ett *GitHub* -problem
> * Slutligen, om du behöver prata med någon, kanske du vill öppna en support förfrågan

## <a name="search"></a>Sök

Om du har en utvecklings fråga kanske du kan hitta svaret i dokumentationen, [GitHub-exempel](https://github.com/azure-samples)eller svar på [Microsoft Q-&](https://docs.microsoft.com/answers/products/) med frågor.

### <a name="scoped-search"></a>Omfattnings sökning

För snabbare resultat kan du begränsa sökningen till Microsoft Q&A, dokumentationen och kod exemplen med hjälp av följande fråga i din favorit sökmotor:

```
{Your Search Terms} (site:http://www.docs.microsoft.com/answers/products/ OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Där *{dina Sök villkor}* motsvarar dina Sök nyckelord.

## <a name="use-the-development-support-tools"></a>Använd verktyg för utvecklings support

| Verktyg  | Beskrivning  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Klistra in ett ID eller en åtkomsttoken för att avkoda anspråks namnen och värdena. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Verktyg som du kan använda för att göra förfrågningar och se svar mot Microsoft Graph API. |

## <a name="post-a-question-to-microsoft-qa"></a>Skicka en fråga till Microsoft Q&A

Microsoft Q&A är den föredragna kanalen för utvecklings frågor. Här är medlemmar i utvecklings gruppen och Microsoft-team medlemmar direkt inblandade i att hjälpa dig att lösa dina problem.

Om du inte hittar ett svar på din fråga via Sök skickar du en ny fråga till Microsoft Q&A. Använd någon av följande Taggar när du ställer frågor som hjälper communityn att identifiera och besvara din fråga snabbare:

|Komponent/Area  | Taggar |
|---------|---------|
| ADAL-bibliotek | [ADAL](https://docs.microsoft.com/answers/topics/azure-ad-adal-deprecation.html) |
| MSAL-bibliotek     | [msal](https://docs.microsoft.com/answers/topics/azure-ad-msal.html) |
| OWIN mellanprogram  | [[Azure-Active-Directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |
| [Azure B2B](../external-identities/what-is-b2b.md)  | [[Azure-AD-B2B]](https://docs.microsoft.com/answers/topics/azure-ad-b2b.html) |
| [Azure-B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[Azure-AD-B2C]](https://docs.microsoft.com/answers/topics/azure-ad-b2c.html) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[Azure-AD-Graph]](https://docs.microsoft.com/answers/topics/azure-ad-graph.html) |
| Andra områden som rör autentiserings-eller auktoriserings ämnen | [[Azure-Active-Directory]](https://docs.microsoft.com/answers/topics/azure-ad-graph.html) |

Följande inlägg från Microsoft Q&ett innehåller tips om hur du ställer frågor och hur du lägger till käll koden. Följ dessa rikt linjer för att öka sannolikheten för att community-medlemmar ska kunna utvärdera och svara på din fråga snabbt:

* [Hur gör jag för att fråga en lämplig fråga](https://docs.microsoft.com/answers/articles/24951/how-to-write-a-quality-question.html)
* [Så här skapar du ett minimalt, komplett och verifierbart exempel](https://docs.microsoft.com/answers/articles/24907/how-to-write-a-quality-answer.html)

## <a name="create-a-github-issue"></a>Skapa ett GitHub-problem

Om du hittar ett fel eller problem som rör våra bibliotek kan du generera ett problem i våra GitHub-databaser. Eftersom våra bibliotek är öppna källor kan du också skicka in en pull-begäran.

En lista över bibliotek och deras GitHub-databaser finns i följande avsnitt:

* [ADAL-bibliotek (Azure Active Directory Authentication Library)](../azuread-dev/active-directory-authentication-libraries.md) och GitHub-databaser
* Bibliotek för [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) och GitHub-databaser

## <a name="open-a-support-request"></a>Öppna en supportbegäran

Om du behöver prata med någon kan du öppna en support förfrågan. Om du är en Azure-kund finns det flera tillgängliga support alternativ. Information om hur du jämför planer finns på [den här sidan](https://azure.microsoft.com/support/plans/). Support för utvecklare är också tillgängligt för Azure-kunder. Information om hur du köper support avtal för utvecklare finns på [den här sidan](https://azure.microsoft.com/support/plans/developer/).

* Om du redan har ett support avtal för Azure [öppnar du en support förfrågan här](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Om du inte är en Azure-kund kan du också öppna en supportbegäran med Microsoft via [vår kommersiella support](https://support.serviceshub.microsoft.com/supportforbusiness).

Du kan också prova en [virtuell agent](https://support.microsoft.com/contactus/?ws=support) för att få support eller ställa frågor.