---
title: Användar flödes versioner i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om de versioner av användar flöden som är tillgängliga i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1c05ff1bf1956943230bf523584025787495d57f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517862"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Användar flödes versioner i Azure Active Directory B2C

Användar flöden i Azure Active Directory B2C (Azure AD B2C) hjälper dig att skapa gemensamma [principer](user-flow-overview.md) som fullständigt beskriver kund identitets upplevelser. Dessa upplevelser omfattar registrering, inloggning, lösen ords återställning eller profil redigering. Tabellerna nedan beskriver de användar flöden som är tillgängliga i Azure AD B2C.

> [!IMPORTANT]
> Vi har ändrat hur vi refererar till användarflödesversioner. Tidigare erbjöd vi V1-versioner (produktionsklara), V1.1 och V2 (förhandsversion). Nu har vi konsoliderat användar flöden i två versioner:
>
>- **Rekommenderade** användar flöden är de nya för hands versionerna av användar flöden. De har testats noggrant och kombinerar alla funktioner i äldre **v2** -och **v 1.1** -versioner. De nya rekommenderade användar flödena kommer att behållas och uppdateras. När du flyttar till dessa nya rekommenderade användar flöden har du till gång till nya funktioner när de släpps.
>- **Standard** användar flöden, som tidigare kallats **v1**, är allmänt tillgängliga, produktions färdiga användar flöden. Om dina användar flöden är verksamhets kritiska och är beroende av hög stabila versioner, kan du fortsätta att använda standard användar flöden, vilket innebär att dessa versioner inte upprätthålls och uppdateras.
>
>Alla äldre förhands gransknings användar flöden (V 1.1 och v2) finns på en sökväg till utfasningen den **1 augusti 2021**. När så är möjligt rekommenderar vi starkt att du [växlar till de nya **rekommenderade** versionerna](#how-to-switch-to-a-new-recommended-user-flow) så snart som möjligt, så att du alltid kan dra nytta av de senaste funktionerna och uppdateringarna. *Dessa ändringar gäller endast för det offentliga Azure-molnet. Andra miljöer fortsätter att använda [äldre användar flödes version](user-flow-versions-legacy.md).*

## <a name="recommended-user-flows"></a>Rekommenderade användar flöden

Rekommenderade användar flöden är för hands versioner som kombinerar nya funktioner med äldre v2-och V 1.1-funktioner. Vi går vidare och de rekommenderade användar flödena kommer att behållas och uppdateras.

| Användarflöde | Beskrivning |
| --------- | ----------- |
| Lösen ords återställning (förhands granskning) | Gör det möjligt för en användare att välja ett nytt lösen ord när de har verifierat sin e-post. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>Kompatibilitetsinställningar för token</li><li>[Åldershantering](age-gating.md)</li><li>[krav för lösen ords komplexitet](password-complexity.md)</li></ul> |
| Profil redigering (förhands granskning) | Gör det möjligt för en användare att konfigurera sina användarattribut. Med det här användar flödet kan du konfigurera: <ul><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li></ul> |
| Logga in (för hands version) | Gör det möjligt för en användare att logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li><li>[Åldershantering](age-gating.md)</li><li>Anpassning av inloggnings Sidan</li></ul> |
| Registrera dig (för hands version) | Gör det möjligt för en användare att skapa ett konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li><li>[Åldershantering](age-gating.md)</li><li>[Krav för lösen ords komplexitet](password-complexity.md)</li></ul> |
| Registrera dig och logga in (förhands granskning) | Gör det möjligt för en användare att skapa ett konto eller logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Åldershantering](age-gating.md)</li><li>[Krav för lösen ords komplexitet](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>Standard användar flöden

Standard användar flöden (tidigare kallat v1) är allmänt tillgängliga, produktions färdiga användar flöden. Standard användar flöden kommer inte att uppdateras framåt.

| Användarflöde | Beskrivning |
| --------- | ----------- | ----------- |
| Lösenordsåterställning | Gör det möjligt för en användare att välja ett nytt lösen ord när de har verifierat sin e-post. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>Kompatibilitetsinställningar för token</li><li>[Krav för lösen ords komplexitet](password-complexity.md)</li></ul> |
| Redigera profil | Gör det möjligt för en användare att konfigurera sina användarattribut. Med det här användar flödet kan du konfigurera: <ul><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li></ul> |
| Logga in | Gör det möjligt för en användare att logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li><li>Blockera inloggning</li><li>Framtvinga lösen ords återställning</li><li>Håll mig inloggad (KMSI avgör)</ul><br>Du kan inte anpassa användar gränssnittet med det här användar flödet. |
| Registrera dig | Gör det möjligt för en användare att skapa ett konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li><li>[Krav för lösen ords komplexitet](password-complexity.md)</li></ul> |
| Registrera dig och logga in | Gör det möjligt för en användare att skapa ett konto eller logga in på sitt konto. Med det här användar flödet kan du konfigurera: <ul><li>[Multifaktorautentisering](multi-factor-authentication.md)</li><li>[Livs längd för token](tokens-overview.md)</li><li>Kompatibilitetsinställningar för token</li><li>Sessionens beteende</li><li>[Krav för lösen ords komplexitet](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>Så här växlar du till ett nytt rekommenderat användar flöde

Följ dessa steg om du vill växla från en äldre version av ett användar flöde till den nya **rekommenderade** för hands versionen:

1. Skapa en ny princip för användar flöde genom att följa stegen i [Självstudier: skapa användar flöden i Azure Active Directory](tutorial-create-user-flows.md). När du skapar användar flödet väljer du den **rekommenderade** versionen.

3. Konfigurera det nya användar flödet med samma inställningar som har kon figurer ATS i den äldre principen.

4. Uppdatera din programmets inloggnings-URL till den nyligen skapade principen.

5. När du har testat användar flödet och bekräftat att det fungerar, tar du bort det äldre användar flödet genom att följa dessa steg:
   1. I menyn Azure AD B2C klient översikt väljer du **användar flöden**.
   2. Hitta det användar flöde som du vill ta bort.
   3. I den sista kolumnen väljer du snabb menyn (**...**) och väljer sedan **ta bort**.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>Kan jag fortfarande skapa gamla v2-och V 1.1-användar flöden?

Du kan inte skapa nya användar flöden baserat på de äldre v2-och V 1.1-versionerna, men du kan fortsätta att läsa, uppdatera och ta bort eventuella äldre v2-och V 1.1-användarkonton som du använder för närvarande.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>Finns det någon anledning till att fortsätta använda äldre v2-och V 1.1-användar flöden?

Inte riktigt. De nya **rekommenderade** för hands versionerna innehåller samma funktioner som de äldre versionerna v2 och v 1.1. Inget har tagits bort, och det finns i själva verket ytterligare funktioner.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>Hur påverkas mitt program om jag inte växlar från äldre v2-och V 1.1-principer?

Om du använder ett äldre v2-eller V 1.1-användar flöde påverkas inte ditt program av den här versions ändringen. Men för att få åtkomst till nya funktioner eller princip ändringar som går framåt måste du växla till de nya **rekommenderade** versionerna.

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Kommer Microsoft fortfarande att ha stöd för min äldre v2-eller V 1.1-användar flödes princip?

Äldre v2-och V 1.1-versioner av användar flöden kommer även fortsättnings vis att stödjas fullt ut.
