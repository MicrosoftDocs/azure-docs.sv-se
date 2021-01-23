---
title: Återhämtning genom övervakning och analys med Azure AD B2C | Microsoft Docs
description: Återhämtning genom övervakning och analys med Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90b2cd4521613a7b449598f0d097a7ec1c2958c6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724550"
---
# <a name="resilience-through-monitoring-and-analytics"></a>Återhämtning genom övervakning och analys

Övervakning maximerar tillgängligheten och prestandan för dina program och tjänster. Den innehåller en omfattande lösning för att samla in, analysera och agera på telemetri från din infrastruktur och dina program. Aviseringar proaktivt meddela dig när problem påträffas med din tjänst eller dina program. De gör att du kan identifiera och åtgärda problem innan användarna av tjänsten kan se dem. Med [Azure AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) kan du analysera, söka i gransknings loggar och inloggnings loggar och bygga anpassade vyer.

## <a name="monitor-and-get-notified-through-alerts"></a>Övervaka och få meddelanden via aviseringar

Övervakning av systemet och infrastrukturen är avgörande för att säkerställa den övergripande hälsan hos dina tjänster. Den börjar med definitionen av affärs mått, till exempel ny användares ankomst, slutanvändarens autentiseringsnivåer och konvertering. Konfigurera sådana indikatorer för övervakning. Om du planerar för en kommande överspänning på grund av befordran eller semester trafik kan du ändra beräkningarna specifikt för händelsen och motsvarande benchmark för affärs måtten. Efter händelsen återgår du till föregående benchmark.

På samma sätt kan du upptäcka fel eller prestanda avbrott genom att ställa in en bra bas linje och sedan definiera aviseringar är en metod som är oundgänglig för att svara på nya problem efter frågan.

![Bild som visar övervaknings-och analys komponenter](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>Så här implementerar du övervakning och aviseringar

- **Övervakning**: Använd [Azure Monitor](../../active-directory-b2c/azure-monitor.md) för att kontinuerligt övervaka hälsan mot viktiga service nivå mål (service nivå mål) och få ett meddelande när en kritisk ändring sker. Börja med att identifiera Azure AD B2C policy eller ett program som en viktig komponent i ditt företag vars hälsa måste övervakas för att upprätthålla service nivå mål. Identifiera nyckel indikatorer som passar dina SLO: erna.
Spåra till exempel följande mått, eftersom ett plötslig släpp i antingen leder till förlust av verksamhet.

  - **Totalt antal förfrågningar**: totalt antal-n-begäranden som skickats till Azure AD B2C principen.

  - **Lyckad frekvens (%)**: lyckade förfrågningar/totalt antal begär Anden.

  Få åtkomst till de [viktigaste indikatorerna](../../active-directory-b2c/view-audit-logs.md) i [application Insights](../../active-directory-b2c/analytics-with-application-insights.md) där Azure AD B2C principbaserade loggar, [gransknings loggar](../../active-directory-b2c/analytics-with-application-insights.md)och inloggnings loggar lagras.  

   - **Visualiseringar**: använda Log Analytics skapar instrument paneler för att visuellt övervaka nyckel indikatorerna.

   - **Aktuell period**: skapa temporala diagram för att visa ändringar i totalt antal förfrågningar och lyckade kostnader (%) i den aktuella perioden, till exempel den aktuella veckan.

   - **Föregående period**: skapa temporala diagram för att visa ändringar i totalt antal förfrågningar och lyckade kostnader (%) under en tidigare period, till exempel förra veckan.

- **Varning**: använda Log Analytics definiera [aviseringar](../../azure-monitor/platform/alerts-log.md) som utlöses när det finns plötsliga ändringar i nyckel indikatorerna. Dessa ändringar kan påverka SLO: erna negativt. Aviseringar använder olika typer av meddelande metoder, t. ex. e-post, SMS och Webhooks. Börja med att definiera ett kriterium som fungerar som ett tröskelvärde mot vilket en avisering ska utlösas. Ett exempel:
  - Varning mot en felsänkning i totalt antal begär Anden: utlöser en avisering när antalet totala förfrågningar som plötsligt släpps. Om det till exempel finns 25% Drop i det totala antalet begär Anden jämfört med föregående period, Utlös en avisering.  
  - Varning mot betydande minskning i lyckade frekvenser (%): utlöser en avisering när lyckade frekvenser av den valda principen sjunker.
  - När du får en avisering kan du felsöka problemet med hjälp av [Log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md), [Application Insights](../../active-directory-b2c/troubleshoot-with-application-insights.md)och [VS Code-tillägg](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) för Azure AD B2C. När du har löst problemet och distribuerat ett uppdaterat program eller en uppdaterad princip fortsätter det att övervaka nyckel indikatorerna tills de återgår till normalt intervall.

- **Tjänst aviseringar**: Använd [Azure AD B2C service nivå aviseringar](../../service-health/service-health-overview.md) för att få meddelanden om service problem, planerat underhåll, hälso rådgivning och säkerhets rådgivning.

- **Rapportering**: med [hjälp av Log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)kan du bygga rapporter som hjälper dig att få förståelse för användar insikter, tekniska utmaningar och tillväxt möjligheter.
  - **Hälso instrument panel**: skapa [anpassade instrument paneler med hjälp av Azure Dashboard](../../azure-monitor/learn/tutorial-app-dashboards.md) -funktionen, som har stöd för att lägga till diagram med Log Analytics frågor. Identifiera exempelvis mönster för lyckade och misslyckade inloggningar, fel orsaker och telemetri om enheter som används för att göra förfrågningarna.
  - **Överge Azure AD B2C-transporter**: Använd [arbets boken](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) för att spåra listan över avbrutna Azure AD B2C körningar där användaren startade inloggnings-eller registrerings resan men inte avslutade den. Den innehåller information om princip-ID och nedbrytning av steg som fattas av användaren innan resan överges.
  - **Azure AD B2C övervakning av arbets böcker**: Använd [arbets böckerna övervakning](https://github.com/azure-ad-b2c/siem), som innehåller Azure AD B2C instrument panel, Multi-Factor Authentication (MFA) åtgärder, villkorlig åtkomst rapport och Sök loggar per correlationId för att få bättre insikter om hälsan hos din Azure AD B2C miljö.
  
## <a name="next-steps"></a>Nästa steg

- [Återhämtnings resurser för Azure AD B2C utvecklare](resilience-b2c.md)
  - [Elastisk slut användar upplevelse](resilient-end-user-experience.md)
  - [Elastiska gränssnitt med externa processer](resilient-external-processes.md)
  - [Återhämtning genom bästa praxis för utvecklare](resilience-b2c-developer-best-practices.md)
- [Bygg återhämtning i din infrastruktur för autentisering](resilience-in-infrastructure.md)
- [Öka återhämtningen av autentisering och auktorisering i dina program](resilience-app-development-overview.md)
