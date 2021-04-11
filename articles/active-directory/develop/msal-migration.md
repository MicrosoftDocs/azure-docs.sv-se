---
title: Migrera till Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Lär dig mer om skillnaderna mellan Microsoft Authentication Library (MSAL) och Azure AD Authentication Library (ADAL) och hur du migrerar till MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/07/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 6517cdd7aafa7ae2fe351b349e62a66104469dcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653787"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>Migrera program till Microsoft Authentication Library (MSAL)

Många utvecklare har skapat och distribuerat program med hjälp av Azure Active Directory Authentication Library (ADAL). Vi rekommenderar nu att du använder Microsoft Authentication Library (MSAL) för autentisering och auktorisering av Azure AD-entiteter.

Genom att använda MSAL i stället för ADAL:

- Du kan autentisera en bredare uppsättning identiteter:
  - Azure AD-identiteter
  - Microsoft-konton
  - Sociala och lokala konton med hjälp av Azure AD B2C
- Användarna får bästa möjliga upplevelse med enkel inloggning.
- Ditt program kan möjliggöra stegvist tillstånd.
- Det är enklare att stödja villkorlig åtkomst.
- Du får nytta av innovation. Eftersom alla Microsoft Development-ansträngningar nu fokuserar på MSAL, kommer inga nya funktioner att implementeras i ADAL.

**MSAL är nu det rekommenderade autentiseringscertifikatet för användning med Microsoft Identity Platform**.

## <a name="migration-guidance"></a>Vägledning för migrering

Följande artiklar kan hjälpa dig att migrera till MSAL:

- [Migrera till MSAL.Android](migrate-android-adal-msal.md)
- [Migrera till MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [Migrera till MSAL Java](migrate-adal-msal-java.md)
- [Migrera till MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrera till MSAL.NET](msal-net-migration.md)
- [Migrera till MSAL Python](migrate-python-adal-msal.md)
- [Migrera Xamarin-appar med hjälp av asynkrona meddelandeköer till MSAL.NET](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

__F: är ADAL föråldrad?__  
S: Ja. Från och med den 30 juni 2020 kommer vi inte längre att lägga till nya funktioner i ADAL. Vi fortsätter att lägga till viktiga säkerhets korrigeringar till ADAL fram till den 30 juni 2022. Efter det här datumet kommer dina appar som använder ADAL att fortsätta att fungera, men vi rekommenderar att du uppgraderar till MSAL för att dra nytta av de senaste funktionerna och för att hålla dig säker.

__F: upphör mina befintliga ADAL-appar att fungera?__  
S: Nej. Dina befintliga appar fortsätter att fungera utan att du behöver göra några ändringar. Om du planerar att behålla dem efter den 30 juni 2022 bör du överväga att uppdatera dina appar till MSAL för att skydda dem, men migrera till MSAL krävs inte för att upprätthålla befintliga funktioner.

__F: Hur gör jag för att vet vilka av mina appar som använder ADAL?__  
A: om du har käll koden för programmet kan du referera till ovanstående stöd linjer för att avgöra vilket bibliotek appen använder och hur du migrerar den till MSAL. Om du samarbetar med en ISV rekommenderar vi att du når dem direkt för att förstå migreringen till MSAL.

__F: Varför bör jag investera i MSAL?__  
A: MSAL innehåller nya funktioner som inte finns i ADAL, inklusive stegvist godkännande, enkel inloggning och hantering av token cache. Till skillnad från ADAL kommer MSAL även fortsättnings vis att få säkerhets uppdateringar utöver den 30 juni 2022. [Läs mer](msal-overview.md).

__F: kommer Microsoft att uppdatera sina egna appar till MSAL?__  
Ja. Microsoft håller på att migrera sina program till MSAL vid slut för ande av supporten, vilket garanterar att de kommer att ha nytta av säkerhets-och funktions förbättringar i MSAL.

__F: kommer du att lansera ett verktyg som hjälper mig att flytta mina appar från ADAL till MSAL?__  
S: Nej. Skillnader mellan biblioteken kräver att dedikerade resurser för utveckling och underhåll av verktyget som annars skulle ägnas åt att förbättra MSAL. Vi ger dock den föregående uppsättningen med stöd guider för att hjälpa dig att göra nödvändiga ändringar i ditt program.

__F: Hur fungerar MSAL med AD FS?__  
S: MSAL.NET stöder vissa scenarier för autentisering mot AD FS 2019. Om din app behöver hämta tokens direkt från en tidigare version av AD FS bör du vara kvar på ADAL. [Läs mer](msal-net-adfs-support.md).

__F: Hur gör jag för att få hjälp med att migrera mitt program?__  
A: se avsnittet om [migrations vägledning](#migration-guidance) i den här artikeln. Om du efter att ha läst guiden för appens plattform har fler frågor kan du publicera på [Microsoft Q&A](/answers/topics/azure-ad-adal-deprecation.html) med taggen `[azure-ad-adal-deprecation]` eller öppna ett ärende i bibliotekets GitHub-lagringsplats. I avsnittet [språk och ramverk](msal-overview.md#languages-and-frameworks) i översikts artikeln om MSAL finns länkar till varje biblioteks lagrings platsen.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera dina program så att de använder Microsoft Authentication Library och Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Översikt över Microsoft Identity Platform](v2-overview.md)
- [Granska våra MSAL-kod exempel](sample-v2-code.md)