---
title: Elastiska gränssnitt med externa processer som använder Azure AD B2C | Microsoft Docs
description: Metoder för att bygga elastiska gränssnitt med externa processer
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
ms.openlocfilehash: 58ef522f5b048db0ef120625d9e894c8e14c070e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724415"
---
# <a name="resilient-interfaces-with-external-processes"></a>Elastiska gränssnitt med externa processer

I den här artikeln får du vägledning om hur du planerar för och implementerar RESTFul-API: er i användar resan och gör programmet mer flexibelt för API-fel.

![Bild visar gränssnitt med externa process komponenter](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>Se till att API: erna har rätt placering

Med IEF-principer (Identity Experience Framework) kan du anropa ett externt system med en [RESTful-API-teknisk profil](../../active-directory-b2c/restful-technical-profile.md). Externa system kontrol leras inte av IEF Runtime Environment och är en möjlig felpunkt.

### <a name="how-to-manage-external-systems-using-apis"></a>Hantera externa system med hjälp av API: er

- När du anropar ett gränssnitt för att få åtkomst till vissa data kontrollerar du om data ska drivas av verifierings beslutet. Bedöm om informationen är nödvändig för programmets kärn funktioner. Till exempel en e-handel eller en sekundär funktion, till exempel en administration. Om informationen inte behövs för autentisering och bara krävs för sekundära scenarier, bör du överväga att flytta anropet till program logiken.

- Om data som krävs för autentisering är relativt statiska och små, och inte har någon annan affärs orsak som ska vara extern från katalogen, bör du överväga att använda den i katalogen.

- Ta bort API-anrop från förautentiserad sökväg närhelst det är möjligt. Om du inte gör det måste du placera strikta skydd för DOS-attacker (Denial of Service) och DDoS-attacker (distributed denial of Service) framför dina API: er. Angripare kan läsa in inloggnings sidan och försöka översvämma ditt API med DoS-attacker och Cripple ditt program. Genom att t. ex. använda CAPTCHA i din inloggning kan du få hjälp med att registrera flödet.

- Använd [API-kopplingar för inbyggda registrerings användar flöden](../../active-directory-b2c/api-connectors-overview.md) när det är möjligt att integrera med webb-API: er, antingen efter att du har loggat in med en identitetsprovider eller innan du skapar användaren. Eftersom användar flöden redan har testats grundligt är det troligt att du inte behöver utföra funktions-, prestanda-eller skalnings testning på användar flödes nivå. Du behöver fortfarande testa dina program för funktioner, prestanda och skalning.

- Azure AD RESTFul API- [tekniska profiler](../../active-directory-b2c/restful-technical-profile.md) ger inte några funktioner för cachelagring. I stället implementerar RESTFul API-profilen en logik för omprövning och en timeout som är inbyggd i principen.

- För API: er som behöver skriva data, köa upp en uppgift så att sådana uppgifter körs av en bakgrunds arbetare. Tjänster som [Azure-köer](../../storage/queues/storage-queues-introduction.md) kan användas. Detta gör att API: et returnerar en effektiv ökning av princip körningens prestanda.  

## <a name="api-error-handling"></a>API-fel hantering

Som API: er som är aktiva utanför Azure AD B2C systemet måste du ha rätt fel hantering i den tekniska profilen. Se till att slutanvändaren är korrekt informerad och att programmet kan hantera problem på ett smidigt sätt.

### <a name="how-to-gracefully-handle-api-errors"></a>Hantera API-fel på ett smidigt sätt

- Ett API kan Miss lyckas av olika orsaker, gör ditt program flexibelt för sådana misslyckanden. [Returnera ett HTTP-4XX fel meddelande](../../active-directory-b2c/restful-technical-profile.md#returning-validation-error-message) om API: et inte kan slutföra begäran. I Azure AD B2C-principen kan du försöka hantera otillgängligheten för API: et och kanske återge en minskad upplevelse.

- [Hantera tillfälliga fel](../../active-directory-b2c/restful-technical-profile.md#error-handling)på ett smidigt sätt. Med RESTFul API-profilen kan du konfigurera fel meddelanden för olika [krets brytare](/azure/architecture/patterns/circuit-breaker).

- Övervaka och Använd kontinuerlig integrering/kontinuerlig leverans (CICD) och rotera autentiseringsuppgifter för API-åtkomst, till exempel lösen ord och certifikat som används av den [tekniska profil motorn](../../active-directory-b2c/restful-technical-profile.md).

## <a name="api-management---best-practices"></a>API Management – bästa praxis

När du distribuerar REST-API: erna och konfigurerar den tekniska RESTful-profilen kan du följa de rekommenderade metod tipsen och hjälpa dig att inte göra vanliga misstag och saker som du överblickar.

### <a name="how-to-manage-apis"></a>Hantera API: er

- API Management (APIM) publicerar, hanterar och analyserar dina API: er. APIM hanterar även autentisering för att ge säker åtkomst till backend-tjänster och mikrotjänster. Använd en API-Gateway för att skala ut API-distributioner, cachelagring och belastnings utjämning.

- Rekommendation är att hämta rätt token i början av användar resan i stället för att anropa flera gånger för varje API och [säkra ett Azure APIM-API](../../active-directory-b2c/secure-api-management.md?tabs=app-reg-ga).

## <a name="next-steps"></a>Nästa steg

- [Återhämtnings resurser för Azure AD B2C utvecklare](resilience-b2c.md)
  - [Elastisk slut användar upplevelse](resilient-end-user-experience.md)
  - [Återhämtning genom bästa praxis för utvecklare](resilience-b2c-developer-best-practices.md)
  - [Återhämtning genom övervakning och analys](resilience-with-monitoring-alerting.md)
- [Bygg återhämtning i din infrastruktur för autentisering](resilience-in-infrastructure.md)
- [Öka återhämtningen av autentisering och auktorisering i dina program](resilience-app-development-overview.md)