---
title: Problem med att logga in på ett Microsoft-| Microsoft Docs
description: Felsök vanliga problem när du loggar in på Microsoft-program från första part med Azure AD (till exempel Microsoft 365).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56e356a9bacc6479d3a3a33be905457c26e732e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378184"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problem med att logga in i ett Microsoft-program

Microsoft-program (som Exchange, SharePoint, Yammer osv.) tilldelas och hanteras lite annorlunda än SaaS-program från tredje part eller andra program som du integrerar med Azure AD för enkel inloggning.

Det finns tre huvudsakliga sätt som en användare kan få åtkomst till ett Program som publicerats av Microsoft.

-   För program i Microsoft 365 eller andra betalda paket beviljas  användare åtkomst via licenstilldelning antingen direkt till sitt användarkonto eller via en grupp som använder vår gruppbaserade licenstilldelningsfunktion.

-   För program som Microsoft eller tredje part publicerar kostnadsfritt för alla kan användare beviljas åtkomst via **användarmedgivande.** Det innebär att de loggar in på programmet med sitt Azure AD-arbets- eller skolkonto och ger det åtkomst till en begränsad uppsättning data på sitt konto.

-   För program som Microsoft eller en tredje part publicerar fritt för vem som helst kan använda kan användare också beviljas åtkomst via **administratörsmedgivande.** Det innebär att en administratör har fastställt att programmet kan användas av alla i organisationen, så att de loggar in på programmet med ett globalt administratörskonto och beviljar åtkomst till alla i organisationen.

Om du vill felsöka problemet börjar du med de allmänna [problemområden](#general-problem-areas-with-application-access-to-consider) med programåtkomst att överväga och läser sedan Genomgång: Steg för att felsöka åtkomst till Microsoft-program för att få mer information.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Allmänna problemområden med programåtkomst att överväga

Följande är en lista över de allmänna problemområden som du kan gå in på detalj i om du har en uppfattning om var du ska börja, men vi rekommenderar att du läser genomgången för att komma igång snabbt: Genomgång: Steg för att felsöka åtkomst till Microsoft-program.

-   [Problem med användarkontot](#problems-with-the-users-account)

-   [Problem med grupper](#problems-with-groups)

-   [Problem med principer för villkorsstyrd åtkomst](#problems-with-conditional-access-policies)

-   [Problem med programmedgivande](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Steg för att felsöka åtkomst till Microsoft-program

Här följer några vanliga problem som användarna får problem med när de inte kan logga in på ett Microsoft-program.

- Allmänna problem att kontrollera först

  * Kontrollera att användaren loggar in på rätt **URL och inte** en lokal program-URL.

  * Kontrollera att användarkontot inte är **låst.**

  * Kontrollera att **användarkontot finns i** Azure Active Directory. [Kontrollera om det finns ett användarkonto i Azure Active Directory](#problems-with-the-users-account)

  * Kontrollera att användarkontot är **aktiverat** för inloggningar. [Kontrollera en användares kontostatus](#problems-with-the-users-account)

  * Kontrollera att användarens lösenord inte **har upphört att gälla eller har glömts.** [Återställa en användares lösenord eller Aktivera](#reset-a-users-password) [lösenordsåterställning via självbetjäning](../authentication/tutorial-enable-sspr.md)

  * Kontrollera att **Multi-Factor Authentication** inte blockerar användaråtkomst. [Kontrollera en användares status för multifaktorautentisering](#check-a-users-multi-factor-authentication-status) [eller Kontrollera kontaktinformation för en användares autentisering](#check-a-users-authentication-contact-info)

  * Kontrollera att en **princip för villkorlig åtkomst** eller Identity **Protection** inte blockerar användaråtkomst. [Kontrollera en specifik princip för villkorlig åtkomst](#problems-with-conditional-access-policies) eller Kontrollera ett specifikt [programs princip för villkorlig åtkomst](#check-a-specific-applications-conditional-access-policy) eller Inaktivera en specifik princip för villkorlig [åtkomst](#disable-a-specific-conditional-access-policy)

  * Kontrollera att en användares **kontaktinformation** för autentisering är uppdaterad så att principer för multifaktorautentisering eller villkorsstyrd åtkomst kan tillämpas. [Kontrollera en användares status för multifaktorautentisering](#check-a-users-multi-factor-authentication-status) [eller Kontrollera en användares autentiseringskontaktinformation](#check-a-users-authentication-contact-info)

- För  **Microsoft-program som** kräver en licens (t.ex. Office365) finns här några specifika problem att kontrollera när du har uteslutit de allmänna problemen ovan:

  * Se till att användaren eller har en **tilldelad licens.** [Kontrollera en användares tilldelade licenser eller](#check-a-users-assigned-licenses) [Kontrollera en grupps tilldelade licenser](#check-a-groups-assigned-licenses)

  * Om licensen **tilldelas till en** **statisk grupp** ska du se till att användaren är **medlem** i den gruppen. [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

  * Om licensen är **tilldelad till en** **dynamisk grupp** ser du till att regeln för dynamisk grupp är korrekt **inställd.** [Kontrollera medlemskapskriterierna för en dynamisk grupp](#check-a-dynamic-groups-membership-criteria)

  * Om licensen **tilldelas till** en dynamisk grupp ska  du se till att den dynamiska gruppen har slutfört bearbetningen av dess medlemskap och att användaren är medlem **(detta** kan ta lite tid). [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

  *  När du har försäkrat dig om att licensen har tilldelats kontrollerar du att licensen **inte har upphört att gälla.**

  *  Kontrollera att licensen gäller **för det program** som de använder.

- För  **Microsoft-program som inte kräver någon licens** kan du kontrollera följande:

  * Om programmet begär behörigheter på användarnivå **(till** exempel "Åtkomst till den här användarens postlåda") kontrollerar  du att användaren har loggat in i programmet och har utfört en medgivandeåtgärd på användarnivå för att ge programmet åtkomst till deras data.

  * Om programmet begär behörigheter på administratörsnivå **(till** exempel "Åtkomst till alla användares postlådor") kontrollerar du att en global administratör har utfört en medgivandeåtgärd på administratörsnivå åt alla användare i organisationen. 

## <a name="problems-with-the-users-account"></a>Problem med användarens konto

Programåtkomst kan blockeras på grund av ett problem med en användare som har tilldelats till programmet. Följande är några sätt att felsöka och lösa problem med användare och deras kontoinställningar:

-   [Kontrollera om det finns ett användarkonto i Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Kontrollera en användares kontostatus](#check-a-users-account-status)

-   [Återställa en användares lösenord](#reset-a-users-password)

-   [Aktivera lösenordsåterställning via självbetjäning](#enable-self-service-password-reset)

-   [Kontrollera en användares status för multifaktorautentisering](#check-a-users-multi-factor-authentication-status)

-   [Kontrollera en användares kontaktuppgifter för autentisering](#check-a-users-authentication-contact-info)

-   [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

-   [Kontrollera en användares tilldelade licenser](#check-a-users-assigned-licenses)

-   [Tilldela en användare en licens](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Kontrollera om det finns ett användarkonto i Azure Active Directory

Följ dessa steg om du vill kontrollera om det finns ett användarkonto:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna tillägget **Azure Active Directory genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objektet.

4.  klicka **på Användare och grupper** på navigeringsmenyn.

5.  klicka **på Alla användare.**

6.  **Sök** efter den användare som du är intresserad av och **klicka på den rad som** ska väljas.

7.  Kontrollera egenskaperna för användarobjektet för att se till att de ser ut som förväntat och att inga data saknas.

### <a name="check-a-users-account-status"></a>Kontrollera en användares kontostatus

Följ dessa steg om du vill kontrollera en användares kontostatus:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna Azure Active Directory **genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objekt.

4.  klicka **på Användare och grupper** på navigeringsmenyn.

5.  klicka **på Alla användare.**

6.  **Sök** efter den användare som du är intresserad av och **klicka på raden att** välja.

7.  klicka **på Profil**.

8.  Under **Inställningar ser** du till att Blockera **inloggning** är inställt **på Nej.**

### <a name="reset-a-users-password"></a>Återställa en användares lösenord

Följ dessa steg om du vill återställa en användares lösenord:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna Azure Active Directory **genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objekt.

4.  klicka **på Användare och grupper** på navigeringsmenyn.

5.  klicka **på Alla användare.**

6.  **Sök** efter den användare som du är intresserad av och **klicka på raden att** välja.

7.  klicka på **knappen Återställ** lösenord längst upp i användarfönstret.

8.  klicka på **knappen Återställ** lösenord i **fönstret Återställ** lösenord som visas.

9.  Kopiera det **tillfälliga lösenordet** eller ange **ett nytt** lösenord för användaren.

10. Meddela användaren det nya lösenordet. De måste ändra det här lösenordet vid nästa inloggning till Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Följ distributionsstegen nedan om du vill aktivera lösenordsåterställning via självbetjäning:

-   [Gör det möjligt för användare att Azure Active Directory sina lösenord](../authentication/tutorial-enable-sspr.md)

-   [Gör det möjligt för användare att återställa eller ändra sina lokala Active Directory-lösenord](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kontrollera en användares status för multifaktorautentisering

Följ dessa steg om du vill kontrollera en användares status för multifaktorautentisering:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna tillägget **Azure Active Directory genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.

3. Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objektet.

4. klicka **på Användare och grupper** på navigeringsmenyn.

5. klicka **på Alla användare.**

6. klicka på **knappen Multi-Factor Authentication** längst upp i fönstret.

7. När **multifaktorautentiseringsportalen har** läses in ser du till att du är **på fliken** Användare.

8. Hitta användaren i listan över användare genom att söka, filtrera eller sortera.

9. Välj användaren i listan över användare och **Aktivera,** **Inaktivera** eller **Framtvinga** multifaktorautentisering efter behov.

   * **Obs!** Om en användare är i ett **framtvingat** tillstånd kan du ställa in användaren på **Inaktiverad** tillfälligt för att låta användaren komma tillbaka till sitt konto. När de är tillbaka kan du ändra deras tillstånd till **Aktiverad** igen för att kräva att de registrerar sin kontaktinformation på nytt vid nästa inloggning. Du kan också följa stegen i Kontrollera en användares kontaktinformation för autentisering för [att](#check-a-users-authentication-contact-info) verifiera eller ange dessa data för dem.

### <a name="check-a-users-authentication-contact-info"></a>Kontrollera en användares kontaktinformation för autentisering

Följ dessa steg om du vill kontrollera en användares kontaktinformation för autentisering som används för multifaktorautentisering, villkorsstyrd åtkomst, identitetsskydd och lösenordsåterställning:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna tillägget **Azure Active Directory genom** att klicka **på Alla** tjänster längst upp i den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objekt.

4.  klicka **på Användare och grupper** på navigeringsmenyn.

5.  klicka **på Alla användare.**

6.  **Sök** efter den användare som du är intresserad av och **klicka på raden att** välja.

7.  klicka **på Profil**.

8.  Rulla ned till **Autentiseringskontaktinformation**.

9.  **Granska** de data som har registrerats för användaren och uppdatera efter behov.

### <a name="check-a-users-group-memberships"></a>Kontrollera en användares gruppmedlemskap

Följ dessa steg om du vill kontrollera en användares gruppmedlemskap:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna Azure Active Directory **genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objekt.

4.  klicka **på Användare och grupper** på navigeringsmenyn.

5.  klicka **på Alla användare.**

6.  **Sök** efter den användare som du är intresserad av och **klicka på raden att** välja.

7.  klicka **på Grupper** för att se vilka grupper som användaren är medlem i.

### <a name="check-a-users-assigned-licenses"></a>Kontrollera en användares tilldelade licenser

Följ dessa steg om du vill kontrollera en användares tilldelade licenser:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna Azure Active Directory **genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objektet.

4.  klicka **på Användare och grupper** på navigeringsmenyn.

5.  klicka **på Alla användare.**

6.  **Sök** efter den användare som du är intresserad av och **klicka på raden som** ska väljas.

7.  klicka **på Licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

### <a name="assign-a-user-a-license"></a>Tilldela en användare en licens 

Följ dessa steg om du vill tilldela en licens till en användare:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna tillägget **Azure Active Directory genom** att klicka **på Alla** tjänster längst upp i den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objektet.

4.  klicka **på Användare och grupper** i navigeringsmenyn.

5.  klicka **på Alla användare.**

6.  **Sök** efter den användare som du är intresserad av och **klicka på raden som** ska väljas.

7.  klicka **på Licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

8.  klicka på **knappen** Tilldela.

9.  Välj **en eller flera produkter** i listan över tillgängliga produkter.

10. **Om du** vill kan **du klicka på** objektet tilldelningsalternativ om du vill tilldela produkter i detalj. Klicka **på OK** när det här är klart.

11. Klicka på **knappen Tilldela** för att tilldela dessa licenser till den här användaren.

## <a name="problems-with-groups"></a>Problem med grupper

Programåtkomst kan blockeras på grund av ett problem med en grupp som har tilldelats till programmet. Här följer några sätt att felsöka och lösa problem med grupper och gruppmedlemskap:

-   [Kontrollera en grupps medlemskap](#check-a-groups-membership)

-   [Kontrollera medlemskapskriterierna för en dynamisk grupp](#check-a-dynamic-groups-membership-criteria)

-   [Kontrollera en grupps tilldelade licenser](#check-a-groups-assigned-licenses)

-   [Ombearbeta en grupps licenser](#reprocess-a-groups-licenses)

-   [Tilldela en grupp en licens](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Kontrollera en grupps medlemskap

Följ dessa steg om du vill kontrollera medlemskapet för en grupp:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna Azure Active Directory **genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objekt.

4.  klicka **på Användare och grupper** på navigeringsmenyn.

5.  klicka **på Alla grupper**.

6.  **Sök** efter den grupp som du är intresserad av och **klicka på raden att** välja.

7.  klicka **på Medlemmar** för att granska listan över användare som är tilldelade till den här gruppen.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Kontrollera medlemskapskriterierna för en dynamisk grupp 

Följ dessa steg om du vill kontrollera medlemskapskriterierna för en dynamisk grupp:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna Azure Active Directory **genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objektet.

4.  klicka **på Användare och grupper** i navigeringsmenyn.

5.  klicka **på Alla grupper.**

6.  **Sök** efter den grupp som du är intresserad av och **klicka på den rad som** du vill välja.

7.  klicka **på Regler för dynamiskt medlemskap.**

8.  Granska den **enkla** eller **avancerade regeln** som definierats för den här gruppen och se till att den användare som du vill ska vara medlem i den här gruppen uppfyller dessa kriterier.

### <a name="check-a-groups-assigned-licenses"></a>Kontrollera en grupps tilldelade licenser

Följ dessa steg om du vill kontrollera en grupps tilldelade licenser:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna tillägget **Azure Active Directory genom** att klicka **på Alla** tjänster längst upp i den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objektet.

4.  klicka **på Användare och grupper** i navigeringsmenyn.

5.  klicka **på Alla grupper.**

6.  **Sök** efter den grupp som du är intresserad av och **klicka på den rad som** du vill välja.

7.  klicka **på Licenser** för att se vilka licenser som gruppen för närvarande har tilldelat.

### <a name="reprocess-a-groups-licenses"></a>Ombearbeta en grupps licenser

Följ dessa steg om du vill bearbeta om en grupps tilldelade licenser:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna tillägget **Azure Active Directory genom** att klicka **på Alla** tjänster längst upp i den vänstra navigeringsmenyn.

3. Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objektet.

4. klicka **på Användare och grupper** på navigeringsmenyn.

5. klicka **på Alla grupper**.

6. **Sök** efter den grupp som du är intresserad av och **klicka på raden att** välja.

7. Klicka **på Licenser** för att se vilka licenser som gruppen för närvarande har tilldelats.

8. Klicka på **knappen Ombearbeta** för att säkerställa att licenserna som tilldelats till den här gruppens medlemmar är uppdaterade. Det kan ta lång tid, beroende på gruppens storlek och komplexitet.

   >[!NOTE]
   >Om du vill göra detta snabbare kan du tillfälligt tilldela en licens till användaren direkt. [Tilldela en användare en licens](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Tilldela en grupp en licens

Följ dessa steg om du vill tilldela en licens till en grupp:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna Azure Active Directory **genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.

3. Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objekt.

4. klicka **på Användare och grupper** på navigeringsmenyn.

5. klicka **på Alla grupper**.

6. **Sök** efter den grupp som du är intresserad av och **klicka på raden att** välja.

7. Klicka **på Licenser** för att se vilka licenser som gruppen för närvarande har tilldelats.

8. klicka på **knappen** Tilldela.

9. Välj **en eller flera produkter** i listan över tillgängliga produkter.

10. **Om du** vill kan **du klicka på** tilldelningsalternativobjektet för att tilldela produkter på ett detaljerad sätt. Klicka **på OK** när det här är klart.

11. Klicka på **knappen Tilldela** för att tilldela dessa licenser till den här gruppen. Detta kan ta lång tid, beroende på gruppens storlek och komplexitet.

    >[!NOTE]
    >Om du vill göra det snabbare kan du tillfälligt tilldela en licens till användaren direkt. [Tilldela en användare en licens](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problem med principer för villkorsstyrd åtkomst

### <a name="check-a-specific-conditional-access-policy"></a>Kontrollera en specifik princip för villkorlig åtkomst

Så här kontrollerar eller validerar du en princip för villkorlig åtkomst:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna tillägget **Azure Active Directory genom** att klicka **på Alla** tjänster längst upp i den vänstra navigeringsmenyn.

3. Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objektet.

4. klicka **på Företagsprogram** på navigeringsmenyn.

5. klicka på **navigeringsobjektet Villkorlig** åtkomst.

6. klicka på den princip som du är intresserad av att inspektera.

7. Kontrollera att det inte finns några specifika villkor, tilldelningar eller andra inställningar som kan blockera användaråtkomst.

   >[!NOTE]
   >Du kanske tillfälligt vill inaktivera den här principen för att säkerställa att den inte påverkar inloggningar. Det gör du genom att ställa **in Aktivera** princip på **Nej** och klicka på **knappen** Spara.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Kontrollera ett specifikt programs princip för villkorsstyrd åtkomst

Så här kontrollerar du ett enskilt programs konfigurerade princip för villkorlig åtkomst:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna tillägget **Azure Active Directory genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objekt.

4.  klicka **på Företagsprogram** på navigeringsmenyn.

5.  klicka **på Alla program**.

6.  Sök efter det program som du är intresserad av, eller så försöker användaren logga in på med programmets visningsnamn eller program-ID.

     >[!NOTE]
     >Om du inte ser det program som du letar efter klickar du på **knappen Filter** och expanderar listans omfång till **Alla program.** Om du vill se fler kolumner klickar du på knappen **Kolumner för** att lägga till ytterligare information för dina program.
     >
     >

7.  klicka på **navigeringsobjektet för** villkorsstyrd åtkomst.

8.  klicka på den princip som du är intresserad av att inspektera.

9.  Kontrollera att det inte finns några specifika villkor, tilldelningar eller andra inställningar som kan blockera användaråtkomst.

     >[!NOTE]
     >Du kanske vill inaktivera den här principen tillfälligt för att säkerställa att den inte påverkar inloggningar. Det gör du genom att ställa **in Aktivera** princip på **Nej** och klicka på **knappen** Spara.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Inaktivera en specifik princip för villkorlig åtkomst

Så här kontrollerar du en princip för villkorlig åtkomst:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**

2.  Öppna Azure Active Directory **genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objekt.

4.  klicka **på Företagsprogram** på navigeringsmenyn.

5.  klicka på **navigeringsobjektet för** villkorsstyrd åtkomst.

6.  klicka på den princip som du är intresserad av att inspektera.

7.  Inaktivera principen genom att ställa **in Aktivera princip** på Nej **och** klicka på **knappen** Spara.

## <a name="problems-with-application-consent"></a>Problem med programmedgivande

Programåtkomst kan blockeras eftersom åtgärden för behörighetsmedgivande inte har skett. Här följer några exempel på hur du kan felsöka och lösa problem med programmedgivande:

-   [Utföra en medgivandeåtgärd på användarnivå](#perform-a-user-level-consent-operation)

-   [Utföra en medgivandeåtgärd på administratörsnivå för alla program](#perform-administrator-level-consent-operation-for-any-application)

-   [Utföra medgivande på administratörsnivå för ett program för en enskild klientorganisation](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Utföra medgivande på administratörsnivå för ett program för flera innehavare](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Utföra en åtgärd för medgivande på användarnivå

-   För alla Open ID Connect-aktiverade program som begär behörigheter, utför navigering till programmets inloggningsskärm ett medgivande på användarnivå till programmet för den inloggade användaren.

-   Om du vill göra detta programmatiskt kan du se [Begär individuellt användarmedgivande.](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Utföra en medgivandeåtgärd på administratörsnivå för alla program

-   Endast för program som utvecklats med **V1-programmodellen** kan du tvinga det här medgivande på administratörsnivå att ske genom att lägga till "**?prompt=admin \_ consent**" i slutet av ett programs inloggnings-URL.

-   För program som utvecklats med **V2-programmodellen** kan du framtvinga detta  medgivande på administratörsnivå genom att följa anvisningarna i avsnittet Begär behörigheter från en katalogadministratör i Använda slutpunkten för administratörsmedgivande. [](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Utföra medgivande på administratörsnivå för ett program för en enskild klientorganisation

-   För  program med en enda klientorganisation som begär behörigheter (t.ex.  de som du utvecklar eller äger i din organisation) kan  du utföra en medgivandeåtgärd på administrativ nivå åt alla användare genom att logga in som global administratör och klicka på knappen Bevilja behörigheter längst upp i programregistret – Alla program – Välj en **&gt; &gt; app – &gt;** fönstret Nödvändiga behörigheter.

-   För alla program som har utvecklats med **V1- eller V2-programmodellen** kan du  framtvinga det här medgivandet på administratörsnivå genom att följa anvisningarna i avsnittet Begär behörigheter från en katalogadministratör i Använda slutpunkten för administratörsmedgivande. [](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Utföra medgivande på administratörsnivå för ett program för flera innehavare

-   För **program med flera innehavare** som begär behörigheter (t.ex. ett program som en tredje part eller Microsoft utvecklar) kan du utföra en **medgivandeåtgärd på administrativ** nivå. Logga in som global administratör  och klicka på knappen Bevilja behörigheter under fönstret Företagsprogram – Alla program – Välj **en app &gt; – &gt; &gt; Behörigheter** (snart tillgängligt).

-   Du kan också framtvinga detta medgivande på  administratörsnivå genom att följa anvisningarna i avsnittet Begär behörigheter från en katalogadministratör i Använda slutpunkten för [administratörsmedgivande.](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)

## <a name="next-steps"></a>Nästa steg
[Använda slutpunkten för administratörsmedgivande](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)