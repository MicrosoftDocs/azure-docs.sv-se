---
title: Metod tips för Azure AD B2C
titleSuffix: Azure AD B2C
description: Rekommendationer och metod tips att tänka på när du arbetar med Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: d8c0a5ce6f3befd41c0e1399363fd73726693837
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96750228"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Rekommendationer och metod tips för Azure Active Directory B2C

Följande metod tips och rekommendationer beskriver några av de viktigaste aspekterna av att integrera Azure Active Directory (Azure AD) B2C i befintliga eller nya program miljöer.

## <a name="fundamentals"></a>Grunder

| Bästa praxis | Beskrivning |
|--|--|
| Välj användar flöden för de flesta scenarier | Azure AD B2C av identitets erfarenhets ramverket för är tjänstens kärn styrka. Principer beskriver fullständigt identitets upplevelser som registrering, inloggning eller profil redigering. För att hjälpa dig att skapa de vanligaste identitets uppgifterna innehåller Azure AD B2C portalen fördefinierade, konfigurerbara principer som kallas användar flöden. Med användar flöden kan du skapa fantastiska användar upplevelser på några minuter, med bara några få klick. [Lär dig när du ska använda användar flöden jämfört med anpassade principer](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Appregistreringar | Varje program (webb, ursprunglig) och API som skyddas måste registreras i Azure AD B2C. Om en app har både en webb-och en ursprunglig version av iOS och Android kan du registrera dem som ett program i Azure AD B2C med samma klient-ID. Lär dig hur du [registrerar OIDC-, SAML-, webb-och interna appar](./tutorial-register-applications.md?tabs=applications). Läs mer om [program typer som kan användas i Azure AD B2C](./application-types.md). |
| Flytta till månatliga aktiva användare fakturering | Azure AD B2C har flyttat från månatlig aktiv autentisering till månatliga aktiva användare (MAU) fakturering. De flesta kunder kommer att hitta den här modellen kostnads effektivt. [Lär dig mer om fakturering av månatliga aktiva användare](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>Planering och design

Definiera din program-och tjänst arkitektur, inventera aktuella system och Planera migreringen till Azure AD B2C.

| Bästa praxis | Beskrivning |
|--|--|
| Skapa en lösning från slut punkt till slut punkt | Ta med alla dina program beroenden när du planerar en Azure AD B2C-integrering. Överväg alla tjänster och produkter som för närvarande finns i din miljö eller som kan behöva läggas till i lösningen, till exempel Azure Functions, CRM-system (Customer Relations hip Management), Azure API Management Gateway och lagrings tjänster. Ta hänsyn till säkerhet och skalbarhet för alla tjänster. |
| Dokumentera användarnas upplevelser | Beskriv all den användare som dina kunder kan uppleva i ditt program. Ta med varje skärm och eventuella förgreningar som de kan stöta på när du interagerar med identitets-och profil aspekterna i ditt program. Inkludera användbarhet, tillgänglighet och lokalisering i planeringen. |
| Välj rätt autentiseringsprotokoll |  För en analys av de olika program scenarierna och deras rekommenderade autentiserings flöden, se [scenarier och stödda autentiserings flöden](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Piloterar en användar upplevelse från slut punkt till slut punkt för POC (proof-of-Concept) | Börja med våra [kod exempel från Microsoft](code-samples.md) och våra [Community-exempel](https://github.com/azure-ad-b2c/samples). |
| Skapa en migreringstabell |Planera framåt kan göra migreringen mer smidigt. Läs [mer om användarmigrering](user-migration.md).|
| Användbarhet kontra säkerhet | Din lösning måste träffa det högra saldot mellan program användbarhet och din organisations godtagbara risk nivå. |
| Flytta lokala beroenden till molnet | Överväg att flytta befintliga program beroenden till molnet för att säkerställa en elastisk lösning. |
| Migrera befintliga appar till b2clogin.com | Utfasningen av login.microsoftonline.com börjar gälla för alla Azure AD B2C klienter den 04 december 2020. [Läs mer](b2clogin.md). |
| Använda identitets skydd och villkorlig åtkomst | Använd de här funktionerna för att få betydligt bättre kontroll över riskfyllda autentiseringar och åtkomst principer. Azure AD B2C Premium P2 krävs. [Läs mer](conditional-access-identity-protection-overview.md). |

## <a name="implementation"></a>Implementering

Beakta följande rekommendationer under implementerings fasen.

| Bästa praxis | Beskrivning |
|--|--|
| Redigera anpassade principer med Azure AD B2C-tillägget för Visual Studio Code | Hämta Visual Studio Code och detta community-integrerade [tillägg från Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c). Även om det inte är en officiell Microsoft-produkt innehåller Azure AD B2C tillägget för Visual Studio Code flera funktioner som hjälper dig att arbeta med anpassade principer enklare. |
| Lär dig hur du felsöker Azure AD B2C | Lär dig hur du [felsöker anpassade principer](./troubleshoot-custom-policies.md?tabs=applications) under utveckling. Lär dig hur ett normalt autentiseringsschema ser ut och använder verktyg för att identifiera avvikelser och fel. Använd till exempel [Application Insights](troubleshoot-with-application-insights.md) för att granska utgående loggar för användar resor. |
| Utnyttja vårt bibliotek med beprövade anpassade princip mönster | Hitta [exempel](https://github.com/azure-ad-b2c/samples) för flera utökade Azure AD B2C användar resor för kund identitets-och åtkomst hantering (CIAM). |

## <a name="testing"></a>Testning

Testa och automatisera implementeringen av Azure AD B2C.

| Bästa praxis | Beskrivning |
|--|--|
| Konto för global trafik | Använd trafik källor från en annan global adress för att testa kraven på prestanda och lokalisering. Se till att alla HTML-, CSS-och-beroenden kan uppfylla dina prestanda behov. |
| Funktionella och UI-testning | Testa att användaren flödar från slut punkt till slut punkt. Lägg till syntetiska tester i några minuter med hjälp av selen, VS Web test osv. |
| Penn testning | Innan du fortsätter med din lösning kan du utföra inträngande test övningar för att kontrol lera att alla komponenter är säkra, inklusive eventuella beroenden från tredje part. Kontrol lera att du har skyddat dina API: er med åtkomsttoken och använt rätt autentiseringsprotokoll för program scenariot. Lär dig mer om [inträngande tester](../security/fundamentals/pen-testing.md) och [Microsoft Cloud enhetligt inträngande test regler för engagemang](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1). |
| A/B-testning | Flyg dina nya funktioner med en liten, slumpmässig uppsättning användare innan du lyfter ut till hela populationen. När java script är aktiverat i Azure AD B2C kan du integrera med ett/B-testverktyg som optimerat, klarhet och annat. |
| Belastningstestning | Azure AD B2C kan skala, men programmet kan skalas endast om alla dess beroenden kan skalas. Läs in – testa dina API: er och CDN. |
| Begränsning |  Azure AD B2C begränsar trafik om för många begär Anden skickas från samma källa under en kort tids period. Använd flera trafik källor vid belastnings testning och hantera `AADB2C90229` fel koden på ett smidigt sätt i dina program. |
| Automation | Använd en pipeline för kontinuerlig integrering och leverans (CI/CD) för att automatisera testning och distribution, till exempel [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operations

Hantera din Azure AD B2Cs miljö.

| Bästa praxis | Beskrivning |
|--|--|
| Skapa flera miljöer | För enklare drift och distribution kan du skapa separata miljöer för utveckling, testning, för produktion och produktion. Skapa Azure AD B2C klienter för var och en. |
| Använd versions kontroll för dina anpassade principer | Överväg att använda GitHub, Azure databaser eller något annat molnbaserad versions kontroll system för dina Azure AD B2C anpassade principer. |
| Använd Microsoft Graph API för att automatisera hanteringen av dina B2C-klienter | Microsoft Graph-API: er:<br/>Hantera [identitets miljö ramverk](/graph/api/resources/trustframeworkpolicy?preserve-view=true&view=graph-rest-beta) (anpassade principer)<br/>[Nycklar](/graph/api/resources/trustframeworkkeyset?preserve-view=true&view=graph-rest-beta)<br/>[Användarflöden](/graph/api/resources/identityuserflow?preserve-view=true&view=graph-rest-beta) |
| Integrera med Azure DevOps | En [CI/CD-pipeline](deploy-custom-policies-devops.md) gör det enkelt att flytta kod mellan olika miljöer och garanterar produktions beredskap hela tiden.   |
| Integrera med Azure Monitor | [Gransknings logg händelser](view-audit-logs.md) behålls endast i sju dagar. [Integrera med Azure Monitor](azure-monitor.md) för att spara loggar för långsiktig användning eller integrera med SIEM-verktyg från tredje part för att få insikter om din miljö. |
| Konfigurera aktiv avisering och övervakning | [Spåra användar beteende](./analytics-with-application-insights.md) i Azure AD B2C att använda Application Insights. |

## <a name="support-and-status-updates"></a>Support-och status uppdateringar

Håll dig uppdaterad med tjänstens tillstånd och hitta support alternativ.

| Bästa praxis | Beskrivning |
|--|--|
| [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Håll dig uppdaterad med Azure AD B2C produkt uppdateringar och meddelanden. |
| [Microsoft Support](support-options.md) | Skicka en supportbegäran till Azure AD B2C tekniska problem. Support för fakturering och prenumerations hantering ges utan kostnad. |
| [Azure-status](https://status.azure.com/status) | Visa aktuell hälso status för alla Azure-tjänster. |