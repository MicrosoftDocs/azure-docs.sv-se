---
title: Autentiseringsmetod-aktivitet – Azure Active Directory
description: Översikt över de autentiseringsmetoder som användarna registrerar för att logga in och återställa lösen ord.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/25/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe69967d84777091aec0bbbf1626b98f5018d0e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693300"
---
# <a name="authentication-methods-activity"></a>Aktiviteter i autentiseringsmetoder 

Med den nya autentiseringsmetoden aktivitets instrument panel kan administratörer övervaka registreringen och användningen av autentiseringsmetoder i organisationen. Den här rapporterings funktionen ger din organisation möjlighet att förstå vilka metoder som registreras och hur de används.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Behörigheter och licenser

Följande roller kan komma åt användning och insikter:

- Rapportläsare
- Säkerhetsläsare
- Säkerhetsadministratör
- Global administratör

 En Azure AD Premium P1-eller P2-licens krävs för att komma åt användning och insikter. Azure AD Multi-Factor Authentication och SSPR-licensierings information (Self-Service Password rereset) finns på [Azure Active Directory prissättnings webbplats](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Så här fungerar det

Åtkomst till användning av autentiseringsmetoder och insikter:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Azure Active Directory**  >  **säkerhets**  >  **autentiseringsmetoder**  >  .
1. Det finns två flikar i rapporten: **registrering** och **användning**.

   ![Översikt över autentiseringsmetoder](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Registrerings information

Du kan komma åt [**fliken registrering**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) för att visa antalet användare som stöder Multi-Factor Authentication, passowordless-autentisering och lösen ords återställning via självbetjäning. 

Klicka på något av följande alternativ för att filtrera en lista över användar registrerings information i förväg:

- **Användare som kan använda Azure Multi-Factor Authentication** visar en nedbrytning av de användare som är båda:
  - Registrerad för en stark autentiseringsmetod 
  - Aktive rad av princip för att använda den metoden för MFA 
  
  Det här talet återspeglar inte användare som registrerats för MFA utanför Azure AD. 
- **Användare som stöder lösen ords lös autentisering** visar en uppdelning av användare som är registrerade för att logga in utan lösen ord med hjälp av FIDO2, Windows Hello för företag eller lösen ords lös telefonin loggning med Microsoft Authenticator-appen. 
- **Användare som stöder lösen ords återställning via självbetjäning** visar en uppdelning av användare som kan återställa sina lösen ord. Användare kan återställa sina lösen ord om båda är:
  - Registrerad för tillräckligt många metoder för att uppfylla organisationens principer för lösen ords återställning via självbetjäning 
  - Aktiverat för att återställa lösen ordet 

  ![Skärm bild av användare som kan registrera sig](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Användare som registrerats med autentiseringsmetoden** visar hur många användare som har registrerats för varje autentiseringsmetod. Klicka på en autentiseringsmetod för att se vem som har registrerats för den metoden.

![Skärm bild av registrerade användare](media/how-to-authentication-methods-usage-insights/users-registered.png)

Den **senaste registreringen per autentiseringsmetod** visar hur många registreringar som har lyckats och misslyckats, sorterade efter autentiseringsmetod. Klicka på en autentiseringsmetod för att se de senaste registrerings händelserna för den metoden.

![Skärm bild av nyligen registrerad](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Användningsinformation

**Användnings** rapporten visar vilka autentiseringsmetoder som används för att logga in och återställa lösen ord.

![Skärm bild av sidan användning](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Inloggningar efter autentiseringskrav** visar antalet lyckade användare interaktiva inloggningar som krävdes för en enkel faktor jämfört med Multi-Factor Authentication i Azure AD. Inloggningar där MFA tillämpades av en tredjepartsleverantör för MFA-leverantörer ingår inte.

![Skärm bild av inloggnings krav för autentisering](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**Inloggningar per autentiseringsmetod** visar antalet interaktiva inloggningar för användare (lyckade och misslyckade) med autentiseringsmetoden som används. Den innehåller inte inloggningar där autentiseringskrav uppfylldes av ett anspråk i token.

![Skärm bild av Sign-ins enligt metod](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

Antal återställningar **av lösen ord och konto upplåsningar** visar antalet lyckade lösen ords ändringar och återställning av lösen ord (självbetjäning och administratör) över tid.

![Skärm bild av Återställ och låsa upp](media/how-to-authentication-methods-usage-insights/password-changes.png)

Vid återställning **av lösen ord per autentiseringsmetod** visas antalet lyckade och misslyckade autentiseringar under flödet för lösen ords återställnings flöde per autentiseringsmetod.

![Skärm bild av metoden RESETS efter](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Information om användar registrering 

Med hjälp av kontrollerna överst i listan kan du söka efter en användare och filtrera listan över användare baserat på de kolumner som visas.

Rapporten registrerings information visar följande information för varje användare:

- Användarens huvud namn
- Namn
- MFA-kompatibel (kapabel, kan inte användas)
- Stöd för lösen ord (möjligt, stöder inte)
- SSPR registrerad (registrerad, ej registrerad)
- SSPR aktiverat (aktiverat, inte aktiverat)
- SSPR-kapabel (kapabel, stöder inte) 
- Registrerade metoder (e-post, mobil telefon, alternativt mobil telefon, arbets telefon, Microsoft Authenticator push, program vara för en tid, FIDO2, säkerhets nyckel, säkerhets frågor)

  ![Skärm bild av användar registrerings information](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Registrerings-och återställnings händelser 

**Registrerings-och återställnings händelser** visar registrerings-och återställnings händelser från de senaste 24 timmarna, de senaste sju dagarna eller de senaste 30 dagarna, inklusive:

- Datum
- Användarnamn
- User 
- Funktion (registrering, återställning)
- Använd metod (app-meddelande, app-kod, telefonsamtal, Office-samtal, alternativt mobil samtal, SMS, e-post, säkerhets frågor)
- Status (lyckades, misslyckades)
- Orsak till startfel (förklaring)

  ![Skärm bild av sidan användning](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Begränsningar

Registreringar av tillfälliga åtkomst pass (KNACKNING) visas inte på fliken registrering i rapporten eftersom de bara är giltiga under en kort tids period.

## <a name="next-steps"></a>Nästa steg

- [Arbeta med autentiserings metoder användning rapport-API](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Välja autentiseringsmetoder för din organisation](concept-authentication-methods.md)
- [Kombinerad registrerings upplevelse](concept-registration-mfa-sspr-combined.md)
