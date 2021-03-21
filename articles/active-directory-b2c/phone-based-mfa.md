---
title: Skydda telefonbaserad MFA i Azure AD B2C
titleSuffix: Azure AD B2C
description: Lär dig tips för att skydda telefonbaserad Multi-Factor Authentication (MFA) i Azure AD B2C-klienten genom att använda Azure Monitor Log Analytics rapporter och aviseringar. Använd vår arbets bok för att identifiera falska telefonsamtal och minimera bedrägliga inloggningar. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc9e0be90c138ba33e1b4dfe11ea6f9c8b7da297
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033562"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>Skydda telefonbaserad Multi-Factor Authentication (MFA)

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Med Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) kan användarna välja att få ett automatiskt röst samtal till ett telefonnummer som de registrerar för verifiering. Skadliga användare kan dra nytta av den här metoden genom att skapa flera konton och ringa telefonsamtal utan att slutföra MFA-registrerings processen. De här många misslyckade inloggnings försöken kan belasta de tillåtna inloggnings försöken, vilket hindrar andra användare från att registrera sig för nya konton i Azure AD B2C-klienten. För att skydda dig mot dessa attacker kan du använda Azure Monitor för att övervaka fel i telefonen och minimera bedräglig inloggning.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar skapar du en [arbets yta för Log Analytics](azure-monitor.md).

## <a name="create-a-phone-based-mfa-events-workbook"></a>Skapa en arbets bok för telefonbaserade MFA-händelser

[Azure AD B2C rapporter & aviserings](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) lager i GitHub innehåller artefakter som du kan använda för att skapa och publicera rapporter, aviseringar och instrument paneler baserat på Azure AD B2C loggar. I utkastet av utkastet nedan markeras de diskrelaterade felen.

### <a name="overview-tab"></a>Fliken Översikt

Följande information visas på fliken **Översikt** :

- Misslyckade orsaker (det totala antalet misslyckade telefonsamtal för varje orsak)
- Blockeras på grund av dåligt rykte
- IP-adress med misslyckade telefonsamtal (totalt antal misslyckade telefonsamtal för varje specifik IP-adress)
- Telefonnummer med IP-adress – misslyckade telefonsamtal
- Webbläsare (autentiseringsfel per klient webbläsare)
- Operativ system (autentiseringsfel per klient operativ system)

![Fliken Översikt](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>Fliken information

Följande information rapporteras på fliken **information** :

- Azure AD B2C princip – misslyckade telefonsamtal
- Autentiseringsfel efter telefonnummer – tids diagram (justerbar tids linje)
- Autentiseringsfel i Azure AD B2C princip – tids diagram (justerbar tids linje)
- Autentiseringsfel av telefonen efter IP-adress – tids diagram (justerbar tids linje)
- Välj telefonnummer för att visa information om felen (Välj ett telefonnummer för en detaljerad lista över felen)

![Fliken information 1 av 3](media/phone-based-mfa/details-tab-1.png)

![Fliken information 2 av 3](media/phone-based-mfa/details-tab-2.png)

![Fliken information 3 av 3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>Använd arbets boken för att identifiera bedrägliga inloggningar

Du kan använda arbets boken för att förstå telefonbaserade MFA-händelser och identifiera potentiellt skadlig användning av telefoni tjänsten.

1. Förstå vad som är normalt för din klient genom att besvara dessa frågor:

   - Var finns de regioner från vilka du förväntar dig av telefonbaserade MFA?
   - Undersök orsaken till misslyckade, telefonbaserad MFA-försök; anses de vara normala eller förväntade?

2. Identifiera egenskaperna för falsk registrering:

   - **Plats-baserad**: Undersök **Telefonsamtals problem med IP-adresser** för alla konton som är associerade med platser som du inte förväntar dig att användarna ska registrera sig för.

   > [!NOTE]
   > Den angivna IP-adressen är en ungefärlig region.

   - **Velocity-based**: titta på **misslyckade telefonsamtal övertid (per dag)**, vilket innebär att telefonnummer som utgör ett onormalt antal misslyckade autentiseringsförsök per dag sorteras från högsta (till vänster) till lägsta (till höger).

3. Minimera bedrägliga inloggningar genom att följa stegen i nästa avsnitt.
 

## <a name="mitigate-fraudulent-sign-ups"></a>Minimera bedrägliga inloggningar

Vidta följande åtgärder för att minimera bedrägliga inloggningar.

- Använd de **rekommenderade** versionerna av användar flöden för att göra följande:
     
   - [Aktivera e-post med eng ång slö sen ord (eng ång slö sen ord)](phone-authentication-user-flows.md) för MFA (gäller både inloggnings-och inloggnings flöden).
   - [Konfigurera en princip för villkorlig åtkomst](conditional-access-user-flow.md) för att blockera inloggningar baserat på plats (gäller enbart inloggnings flöden, inte registrerings flöden).
   - Använd API-kopplingar för att [integrera med en anti-bot-lösning som recaptcha](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (gäller för registrerings flöden).

- Ta bort lands koder som inte är relevanta för din organisation från den nedrullningsbara menyn där användaren verifierar sitt telefonnummer (den här ändringen gäller för framtida registrering):
    
   1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör för Azure AD B2C-klientorganisationen.

   2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din klient.

   3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.

   4. Välj användar flödet och välj sedan **språk**. Välj språket för din organisations geografiska plats för att öppna panelen språk information. (I det här exemplet väljer vi **engelska en** för USA). Välj **sidan multifaktorautentisering** och välj sedan **Hämta standardinställningar (en)**.
 
      ![Ladda upp nya åsidosättningar för att ladda ned standardvärden](media/phone-based-mfa/download-defaults.png)

   5. Öppna JSON-filen som hämtades i föregående steg. Sök efter i filen `DEFAULT` och ersätt raden med `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"` . Var noga med att ställa in `Overrides` på `true` .

   > [!NOTE]
   > Du kan anpassa listan över tillåtna lands koder i `countryList` elementet (se [exemplet på sidan med telefon faktorns autentisering](localization-string-ids.md#phone-factor-authentication-page-example)).

   7. Spara JSON-filen. I panelen språk information, under **överför nya åsidosättningar**, väljer du den modifierade JSON-filen för att ladda upp den.

   8. Stäng panelen och välj **Kör användar flöde**. I det här exemplet bekräftar du att **USA** är den enda lands koden som är tillgänglig i list rutan:
 
      ![Listruta för landskod](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [identitets skydd och villkorlig åtkomst för Azure AD B2C](conditional-access-identity-protection-overview.md) 

- Tillämpa [villkorlig åtkomst till användar flöden i Azure Active Directory B2C](conditional-access-user-flow.md)