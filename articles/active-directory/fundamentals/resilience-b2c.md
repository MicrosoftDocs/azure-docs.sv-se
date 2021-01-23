---
title: Bygg återhämtning i kund identitets-och åtkomst hantering med Azure AD B2C | Microsoft Docs
description: Metoder för att bygga återhämtning i kund identitets-och åtkomst hantering med hjälp av Azure AD B2C
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
ms.openlocfilehash: b40918db03c260f899c36d306c892b787cc6371c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724899"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Bygg återhämtning i din kund identitets-och åtkomst hantering med Azure Active Directory B2C

[Azure Active Directory (AD) B2C](../../active-directory-b2c/overview.md) är en CIAM-plattform (Customer Identity and Access Management) som är utformad för att hjälpa dig att starta din kritiska kund mot program. Vi har många inbyggda funktioner för [återhämtning](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) som är utformade för att hjälpa vår tjänst att anpassa sig efter dina behov och förbättra flexibiliteten i situationen för potentiella avbrott. När du startar ett verksamhets kritiskt program är det viktigt att överväga olika design-och konfigurations element i ditt program, samt hur programmet konfigureras i Azure AD B2C för att säkerställa att du får ett flexibelt beteende som svar på avbrott eller fel scenarier. I den här artikeln diskuterar vi några av de metod tips som hjälper dig att öka återhämtningen.

En elastisk tjänst är en funktion som fortsätter att fungera trots störningar. Du kan hjälpa till att förbättra återhämtningen i din tjänst genom att:

- förstå alla komponenter

- eliminera enskilda felpunkter

- isolera felaktiga komponenter för att begränsa deras påverkan

- tillhandahålla redundans med metoder för snabb redundans och återställnings Sök vägar

När du utvecklar ditt program rekommenderar vi att du överväger att [öka återhämtningen av autentisering och auktorisering i dina program](resilience-app-development-overview.md) med identitets komponenterna i din lösning. Den här artikeln försöker åtgärda förbättringar för återhämtnings information till Azure AD B2C program. Våra rekommendationer grupperas efter CIAM-funktioner.

![Bilden visar CIAM ](media/resilience-b2c/high-level-components.png) -komponenter i de följande avsnitten. Vi hjälper dig att bygga återhämtning i följande områden:

- [Slutanvändarens upplevelse](resilient-end-user-experience.md): Aktivera en återställnings plan för ditt autentiseringsschema och minska den potentiella påverkan från ett avbrott i Azure AD B2C-Autentiseringstjänsten.

- [Gränssnitt med externa processer](resilient-external-processes.md): skapa återhämtning i dina program och gränssnitt genom att återställa från fel.  

- [Bästa metoder för utvecklare](resilience-b2c-developer-best-practices.md): Undvik fragility på grund av vanliga anpassade princip problem och förbättra fel hanteringen i områden som interaktioner med anspråk verifierare, tredjepartsprogram och REST-API: er.

- [Övervakning och analys](resilience-with-monitoring-alerting.md): utvärdera hälso tillståndet för din tjänst genom att övervaka nyckel indikatorer och identifiera fel och prestanda avbrott genom aviseringar.

- [Bygg återhämtning i din infrastruktur för autentisering](resilience-in-infrastructure.md)

- [Öka återhämtningen av autentisering och auktorisering i dina program](resilience-app-development-overview.md)

Titta på den här videon och lär dig hur du skapar elastiska och skalbara flöden med Azure AD B2C.
>[!Video https://www.youtube.com/embed/8f_Ozpw9yTs]
