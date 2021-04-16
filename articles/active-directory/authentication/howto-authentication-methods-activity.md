---
title: Aktivitet för autentiseringsmetoder – Azure Active Directory
description: Översikt över de autentiseringsmetoder som användarna registrerar för att logga in och återställa lösenord.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/16/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 699ff88e4181dada5eacaa3f13469722cdf7ceaa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530455"
---
# <a name="authentication-methods-activity"></a>Aktiviteter i autentiseringsmetoder 

Med den nya instrumentpanelen för autentiseringsmetoder kan administratörer övervaka registrering och användning av autentiseringsmetoder i hela organisationen. Den här rapporteringsfunktionerna ger din organisation möjlighet att förstå vilka metoder som registreras och hur de används.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Behörigheter och licenser

Inbyggda och anpassade roller med följande behörigheter kan komma åt bladet Aktivitet för autentiseringsmetoder och API:er:

- Microsoft.directory/auditLogs/allProperties/read
- Microsoft.directory/signInReports/allProperties/read

Följande roller har de behörigheter som krävs:

- Rapportläsare
- Säkerhetsläsare
- Global läsare
- Säkerhetsoperatör
- Säkerhetsadministratör
- Global administratör

 En Azure AD Premium P1- eller P2-licens krävs för att få åtkomst till användning och insikter. Licensieringsinformation för Azure AD Multi-Factor Authentication och självbetjäning av lösenordsåterställning (SSPR) [finns på Azure Active Directory prissättningswebbplatsen](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Så här fungerar det

Så här får du åtkomst till autentiseringsmetodanvändning och insikter:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka **Azure Active Directory aktivitet** för  >    >    >  **autentiseringsmetoder för säkerhet.**
1. Det finns två flikar i rapporten: **Registrering** och **användning.**

   ![Översikt över aktivitet för autentiseringsmetoder](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Registreringsinformation

Du kan öppna [**fliken Registrering för**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) att visa antalet användare som kan använda multifaktorautentisering, lösenordslös autentisering och lösenordsåterställning via självbetjäning. 

Klicka på något av följande alternativ för att förfiltrera en lista med användarregistreringsinformation:

- **Användare som kan använda Azure Multi-Factor Authentication** visar uppdelningen av användare som båda är:
  - Registrerad för en stark autentiseringsmetod 
  - Har aktiverats av principen för att använda den metoden för MFA 
  
  Det här numret återspeglar inte användare som har registrerats för MFA utanför Azure AD. 
- **Användare som kan** använda lösenordslös autentisering visar uppdelningen av användare som är registrerade för att logga in utan lösenord med hjälp av FIDO2, Windows Hello för företag eller lösenordsfri telefon-inloggning med Microsoft Authenticator-appen. 
- **Användare som kan återställa lösenord via självbetjäning** visar en analys av användare som kan återställa sina lösenord. Användare kan återställa sina lösenord om båda är:
  - Registrerad för tillräckligt med metoder för att uppfylla organisationens princip för lösenordsåterställning via självbetjäning 
  - Aktiverat för att återställa sitt lösenord 

  ![Skärmbild av användare som kan registrera sig](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Användare som registrerats med autentiseringsmetod** visar hur många användare som är registrerade för varje autentiseringsmetod. Klicka på en autentiseringsmetod för att se vem som är registrerad för den metoden.

![Skärmbild av registrerade användare](media/how-to-authentication-methods-usage-insights/users-registered.png)

**Den senaste registreringen efter autentiseringsmetod** visar hur många registreringar som lyckades och misslyckades, sorterade efter autentiseringsmetod. Klicka på en autentiseringsmetod för att se de senaste registreringshändelserna för den metoden.

![Skärmbild av nyligen registrerade](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Användningsinformation

**Användningsrapporten** visar vilka autentiseringsmetoder som används för att logga in och återställa lösenord.

![Skärmbild av sidan Användning](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Inloggningar efter autentiseringskrav** visar antalet lyckade interaktiva användarbaserade inloggningar som krävdes för enkelfaktors- och multifaktorautentisering i Azure AD. Inloggningar där MFA har verkställts av en tredjeparts-MFA-provider ingår inte.

![Skärmbild av inloggningar efter autentiseringskrav](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Inloggningar efter autentiseringsmetod visar** antalet användar interaktiva inloggningar (lyckade och misslyckade) efter autentiseringsmetod som används. Den innehåller inte inloggningar där autentiseringskravet uppfyllts av ett anspråk i token.

![Skärmbild av inloggningar efter metod](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**Antalet lösenordsåterställningar och kontoupplåsningar** visar antalet lyckade lösenordsändringar och lösenordsåterställningar (självbetjäning och av administratör) över tid.

![Skärmbild av återställningar och upplåsningar](media/how-to-authentication-methods-usage-insights/password-changes.png)

**Lösenordsåterställningar med autentiseringsmetod** visar antalet lyckade och misslyckade autentiseringar under flödet för lösenordsåterställning med hjälp av autentiseringsmetod.

![Skärmbild av återställningar efter metod](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Användarregistreringsinformation 

Med hjälp av kontrollerna överst i listan kan du söka efter en användare och filtrera listan över användare baserat på de kolumner som visas.

Rapporten med registreringsinformation visar följande information för varje användare:

- Användarens huvudnamn
- Name
- MFA-kompatibel (kompatibel, inte kompatibel)
- Lösenordsfri kapacitet (kompatibel, inte kompatibel)
- SSPR-registrerad (registrerad, inte registrerad)
- SSPR aktiverat (aktiverat, inte aktiverat)
- SSPR-kompatibel (kompatibel, inte kompatibel) 
- Registrerade metoder (e-post, mobiltelefon, alternativ mobiltelefon, kontorstelefon, Microsoft Authenticator Push, software one time-lösenord, FIDO2, säkerhetsnyckel, säkerhetsfrågor)

  ![Skärmbild av användarregistreringsinformation](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Registrerings- och återställningshändelser 

**Registrerings- och återställningshändelser** visar registrerings- och återställningshändelser från de senaste 24 timmarna, de senaste sju dagarna eller de senaste 30 dagarna, inklusive:

- Datum
- Användarnamn
- Användare 
- Funktion (registrering, återställning)
- Metod som används (appmeddelande, appkod, telefonsamtal, Office-samtal, alternativt mobilsamtal, SMS, e-post, säkerhetsfrågor)
- Status (lyckades, misslyckades)
- Orsak till fel (förklaring)

  ![Skärmbild av registrerings- och återställningshändelser](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Begränsningar

- Data i rapporten uppdateras inte i realtid och kan visa en svarstid på upp till några timmar.
- Tillfällig åtkomstkod registreringar visas inte på registreringsfliken i rapporten eftersom de bara är giltiga under en kort tidsperiod.
- De **PhoneAppNotification-** **eller PhoneAppOTP-metoder** som en användare har konfigurerat visas inte på instrumentpanelen. 

## <a name="next-steps"></a>Nästa steg

- [Arbeta med API:et för autentiseringsmetoder för användningsrapport](/graph/api/resources/authenticationmethods-usage-insights-overview)
- [Välja autentiseringsmetoder för din organisation](concept-authentication-methods.md)
- [Kombinerad registreringsupplevelse](concept-registration-mfa-sspr-combined.md)
