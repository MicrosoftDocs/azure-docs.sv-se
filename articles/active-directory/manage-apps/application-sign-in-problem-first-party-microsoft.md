---
title: Problem med att logga in till ett Microsoft-program | Microsoft Docs
description: Felsök vanliga problem som har uppstått vid inloggning till Microsoft-program från första part med hjälp av Azure AD (t. ex. Microsoft 365).
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79c8d6d072379853d6eca561d372f61dbb8acc8a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99260019"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problem med att logga in till ett Microsoft-program

Microsoft-program (t. ex. Exchange, SharePoint, Yammer osv.) tilldelas och hanteras lite annorlunda än SaaS-program från tredje part eller andra program som du integrerar med Azure AD för enkel inloggning.

En användare kan få till gång till ett Microsoft-publicerat program på tre sätt.

-   För program i Microsoft 365 eller andra betalda sviter beviljas användare åtkomst via **licens tilldelning** antingen direkt till sitt användar konto eller via en grupp som använder vår gruppbaserade licens tilldelnings funktion.

-   För program som Microsoft eller en tredje part publicerar fritt för alla som ska användas kan användare beviljas åtkomst genom **användar medgivande**. Det innebär att de loggar in på programmet med sitt Azure AD-arbets-eller skol konto och ger åtkomst till viss begränsad uppsättning data på sitt konto.

-   För program som Microsoft eller en tredje part publicerar fritt för alla som ska användas kan användare även beviljas åtkomst genom **Administratörs medgivande**. Det innebär att en administratör har fastställt att programmet kan användas av alla i organisationen, så att de loggar in på programmet med ett globalt administratörs konto och ger åtkomst till alla i organisationen.

Om du vill felsöka problemet börjar du med de [allmänna problem områdena med program åtkomst](#general-problem-areas-with-application-access-to-consider) och läser sedan genom gången: steg för att felsöka Microsoft Application Access för att få till gång till informationen.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Allmänna problem områden med program åtkomst att överväga

Nedan följer en lista över allmänna problemområden som du kan detaljgranska i om du har en uppfattning om var du ska starta, men vi rekommenderar att du läser genom gången för att komma igång snabbt: genom gång: steg för att felsöka Microsoft Application Access.

-   [Problem med användarens konto](#problems-with-the-users-account)

-   [Problem med grupper](#problems-with-groups)

-   [Problem med principer för villkorlig åtkomst](#problems-with-conditional-access-policies)

-   [Problem med program medgivande](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Steg för att felsöka åtkomst till Microsoft-program

Här följer några vanliga problem som folk kan köras i när deras användare inte kan logga in på ett Microsoft-program.

- Allmänna problem att kontrol lera först

  * Se till att användaren loggar in på **rätt URL** och inte till en lokal program-URL.

  * Kontrol lera att användarens konto **inte är utelåst.**

  * Kontrol lera att **användarens konto finns** i Azure Active Directory. [Kontrol lera om ett användar konto finns i Azure Active Directory](#problems-with-the-users-account)

  * Kontrol lera att användar kontot är **aktiverat** för inloggningar. [Kontrol lera en användares konto status](#problems-with-the-users-account)

  * Kontrol lera att användarens **lösen ord inte har upphört att gälla eller är bortglömt.** [Återställa en användares lösen ord](#reset-a-users-password) eller [Aktivera återställning av lösen ord för självbetjäning](../authentication/tutorial-enable-sspr.md)

  * Kontrol lera att **Multi-Factor Authentication** inte blockerar användar åtkomsten. [Kontrol lera en användares Multi-Factor Authentication-status](#check-a-users-multi-factor-authentication-status) eller [kontrol lera användarens kontakt information för autentisering](#check-a-users-authentication-contact-info)

  * Se till att en **princip för villkorlig åtkomst** eller **identitets skydds** princip inte blockerar användar åtkomst. [Kontrol lera en specifik princip för villkorlig åtkomst](#problems-with-conditional-access-policies) eller [kontrol lera ett specifik programs villkorliga åtkomst policy](#check-a-specific-applications-conditional-access-policy) eller [inaktivera en specifik princip för villkorlig åtkomst](#disable-a-specific-conditional-access-policy)

  * Se till att en användares **kontakt information för autentisering** är uppdaterad för att tillåta att Multi-Factor Authentication eller villkorliga åtkomst principer tillämpas. [Kontrol lera en användares Multi-Factor Authentication-status](#check-a-users-multi-factor-authentication-status) eller [kontrol lera användarens kontakt information för autentisering](#check-a-users-authentication-contact-info)

- För **Microsoft** - **program som kräver en licens** (t. ex. Office365) finns det några specifika problem att kontrol lera när du har reglerat de allmänna problemen ovan:

  * Se till att användaren eller har en **tilldelad licens.** [Kontrol lera en användares tilldelade licenser](#check-a-users-assigned-licenses) eller [kontrol lera en grupps tilldelade licenser](#check-a-groups-assigned-licenses)

  * Om licensen är **tilldelad en** **statisk grupp** kontrollerar du att **användaren är medlem** i den gruppen. [Kontrol lera en användares grupp medlemskap](#check-a-users-group-memberships)

  * Om licensen är **tilldelad en** **dynamisk grupp** kontrollerar du att **regeln för dynamisk grupp är korrekt inställd**. [Kontrol lera medlemskaps villkoren för en dynamisk grupp](#check-a-dynamic-groups-membership-criteria)

  * Om licensen är **tilldelad en** **dynamisk grupp** kontrollerar du att den dynamiska gruppen har **avslutat bearbetningen** av sitt medlemskap och att **användaren är medlem** (detta kan ta lite tid). [Kontrol lera en användares grupp medlemskap](#check-a-users-group-memberships)

  *  När du ser till att licensen har tilldelats kontrollerar du att licensen inte har **upphört att gälla**.

  *  Kontrol lera att licensen är **för programmet** som de har åtkomst till.

- För **Microsoft** - **program som inte kräver en licens** finns här några saker att kontrol lera:

  * Om programmet begär behörigheter på **användar nivå** (till exempel "åtkomst till den här användarens post låda"), se till att användaren har loggat in i programmet och har utfört en **åtgärd på användar nivå** för att ge programmet åtkomst till sina data.

  * Om programmet begär behörigheter på **Administratörs nivå** (till exempel "åtkomst till alla användares post lådor") ser du till att en global administratör har utfört en **medgivande åtgärd på administratörs nivå för alla användare** i organisationen.

## <a name="problems-with-the-users-account"></a>Problem med användarens konto

Program åtkomst kan blockeras på grund av ett problem med en användare som är tilldelad till programmet. Här följer några exempel på hur du kan felsöka och lösa problem med användare och deras konto inställningar:

-   [Kontrol lera om ett användar konto finns i Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Kontrol lera en användares konto status](#check-a-users-account-status)

-   [Återställa en användares lösen ord](#reset-a-users-password)

-   [Aktivera lösenordsåterställning via självbetjäning](#enable-self-service-password-reset)

-   [Kontrol lera en användares Multi-Factor Authentication-status](#check-a-users-multi-factor-authentication-status)

-   [Kontrol lera en användares kontakt information för autentisering](#check-a-users-authentication-contact-info)

-   [Kontrol lera en användares grupp medlemskap](#check-a-users-group-memberships)

-   [Kontrol lera en användares tilldelade licenser](#check-a-users-assigned-licenses)

-   [Tilldela en licens för en användare](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Kontrol lera om ett användar konto finns i Azure Active Directory

Följ dessa steg om du vill kontrol lera om det finns ett användar konto:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Kontrol lera egenskaperna för användarobjektet och se till att de ser ut som du förväntar dig och att inga data saknas.

### <a name="check-a-users-account-status"></a>Kontrol lera en användares konto status

Följ dessa steg om du vill kontrol lera en användares konto status:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **profil**.

8.  Under **Inställningar** kontrollerar du att **blockera inloggning** är inställt på **Nej**.

### <a name="reset-a-users-password"></a>Återställa en användares lösen ord

Följ dessa steg om du vill återställa en användares lösen ord:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på knappen **Återställ lösen ord** överst i fönstret användare.

8.  Klicka på knappen **Återställ lösen ord** i rutan **Återställ lösen ord** som visas.

9.  Kopiera det **tillfälliga lösen ordet** eller **Ange ett nytt lösen ord** för användaren.

10. Meddela användaren det nya lösen ordet, de måste ändra lösen ordet vid nästa inloggning till Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning

Aktivera självbetjäning för återställning av lösen ord genom att följa distributions stegen nedan:

-   [Gör det möjligt för användare att återställa sina Azure Active Directory lösen ord](../authentication/tutorial-enable-sspr.md)

-   [Gör det möjligt för användare att återställa eller ändra sina Active Directory lokala lösen ord](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kontrol lera en användares Multi-Factor Authentication-status

Följ dessa steg om du vill kontrol lera en användares Multi-Factor Authentication-status:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **användare och grupper** på navigerings menyn.

5. Klicka på **alla användare**.

6. Klicka på knappen **Multi-Factor Authentication** överst i fönstret.

7. Se till att du är på fliken **användare** när **Multi-Factor Authentication administrations Portal** har lästs in.

8. Hitta användaren i listan med användare genom att söka, filtrera eller sortera.

9. Välj användaren i listan med användare och **Aktivera**, **inaktivera** eller **tillämpa** Multi-Factor Authentication som önskade.

   * **Obs!** om en användare är i **tvingande** tillstånd kan du ange att de tillfälligt **inaktive ras** för att låta dem gå tillbaka till sitt konto. När du har loggat in igen kan du ändra deras status till **aktive rad** för att kräva att de registrerar sin kontakt information igen under nästa inloggning. Du kan också följa stegen i [kontakt informationen för en användares autentisering](#check-a-users-authentication-contact-info) för att verifiera eller ange data för dem.

### <a name="check-a-users-authentication-contact-info"></a>Kontrol lera en användares kontakt information för autentisering

Följ dessa steg om du vill kontrol lera en användares kontaktinfo kontakt information som används för Multi-Factor Authentication, villkorlig åtkomst, identitets skydd och lösen ords återställning:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **profil**.

8.  Rulla ned till **autentisering kontakt information**.

9.  **Granska** de data som har registrerats för användaren och uppdatera dem efter behov.

### <a name="check-a-users-group-memberships"></a>Kontrol lera en användares grupp medlemskap

Följ dessa steg om du vill kontrol lera en användares grupp medlemskap:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **grupper** för att se vilka grupper användaren är medlem i.

### <a name="check-a-users-assigned-licenses"></a>Kontrol lera en användares tilldelade licenser

Följ dessa steg om du vill kontrol lera en användares tilldelade licenser:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

### <a name="assign-a-user-a-license"></a>Tilldela en licens för en användare 

Följ dessa steg om du vill tilldela en licens till en användare:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla användare**.

6.  **Sök** efter den användare som du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

8.  Klicka på knappen **tilldela** .

9.  Välj **en eller flera produkter** i listan med tillgängliga produkter.

10. **Valfritt** Klicka på objektet **tilldelnings alternativ** om du vill tilldela produkter i detalj. Klicka på **OK** när det är klart.

11. Klicka på knappen **tilldela** för att tilldela dessa licenser till den här användaren.

## <a name="problems-with-groups"></a>Problem med grupper

Program åtkomst kan blockeras på grund av ett problem med en grupp som är tilldelad till programmet. Här följer några exempel på hur du kan felsöka och lösa problem med grupper och grupp medlemskap:

-   [Kontrol lera en grupps medlemskap](#check-a-groups-membership)

-   [Kontrol lera medlemskaps villkoren för en dynamisk grupp](#check-a-dynamic-groups-membership-criteria)

-   [Kontrol lera en grupps tilldelade licenser](#check-a-groups-assigned-licenses)

-   [Ombearbeta en grupps licenser](#reprocess-a-groups-licenses)

-   [Tilldela en licens för en grupp](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Kontrol lera en grupps medlemskap

Följ dessa steg om du vill kontrol lera en grupps medlemskap:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla grupper**.

6.  **Sök** efter den grupp du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **medlemmar** för att granska listan över användare som har tilldelats den här gruppen.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Kontrol lera medlemskaps villkoren för en dynamisk grupp 

Följ dessa steg om du vill kontrol lera medlemskaps villkoren för en dynamisk grupp:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla grupper**.

6.  **Sök** efter den grupp du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **regler för dynamiskt medlemskap.**

8.  Granska den **enkla** eller **avancerade** regeln som definierats för den här gruppen och se till att den användare som du vill ska vara medlem i den här gruppen uppfyller dessa villkor.

### <a name="check-a-groups-assigned-licenses"></a>Kontrol lera en grupps tilldelade licenser

Följ dessa steg om du vill kontrol lera en grupps tilldelade licenser:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **användare och grupper** på navigerings menyn.

5.  Klicka på **alla grupper**.

6.  **Sök** efter den grupp du är intresse rad av och **Klicka på den rad** som du vill välja.

7.  Klicka på **licenser** för att se vilka licenser som gruppen för närvarande har tilldelats.

### <a name="reprocess-a-groups-licenses"></a>Ombearbeta en grupps licenser

Följ dessa steg om du vill bearbeta om en grupps tilldelade licenser:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **användare och grupper** på navigerings menyn.

5. Klicka på **alla grupper**.

6. **Sök** efter den grupp du är intresse rad av och **Klicka på den rad** som du vill välja.

7. Klicka på **licenser** för att se vilka licenser som gruppen för närvarande har tilldelats.

8. Klicka på knappen **bearbeta** för att se till att de licenser som har tilldelats den här gruppens medlemmar är uppdaterade. Detta kan ta lång tid, beroende på gruppens storlek och komplexitet.

   >[!NOTE]
   >För att göra detta snabbare bör du tillfälligt tilldela en licens till användaren direkt. [Tilldela en licens för en användare](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Tilldela en licens för en grupp

Följ dessa steg om du vill tilldela en licens till en grupp:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **användare och grupper** på navigerings menyn.

5. Klicka på **alla grupper**.

6. **Sök** efter den grupp du är intresse rad av och **Klicka på den rad** som du vill välja.

7. Klicka på **licenser** för att se vilka licenser som gruppen för närvarande har tilldelats.

8. Klicka på knappen **tilldela** .

9. Välj **en eller flera produkter** i listan med tillgängliga produkter.

10. **Valfritt** Klicka på objektet **tilldelnings alternativ** om du vill tilldela produkter i detalj. Klicka på **OK** när det är klart.

11. Klicka på knappen **tilldela** för att tilldela dessa licenser till den här gruppen. Detta kan ta lång tid, beroende på gruppens storlek och komplexitet.

    >[!NOTE]
    >För att göra detta snabbare bör du tillfälligt tilldela en licens till användaren direkt. [Tilldela en licens för en användare](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problem med principer för villkorlig åtkomst

### <a name="check-a-specific-conditional-access-policy"></a>Kontrol lera en speciell princip för villkorlig åtkomst

Så här kontrollerar eller validerar du en enskild princip för villkorlig åtkomst:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** på navigerings menyn.

5. Klicka på navigerings objekt för **villkorlig åtkomst** .

6. Klicka på den princip som du är intresse rad av att inspektera.

7. Se till att det inte finns några speciella villkor, tilldelningar eller andra inställningar som kan blockera användar åtkomst.

   >[!NOTE]
   >Du kanske vill inaktivera den här principen tillfälligt för att säkerställa att den inte påverkar inloggningar. Det gör du genom att ställa in **Aktivera princip** växla till **Nej** och klicka på knappen **Spara** .
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Kontrol lera ett specifik programs princip för villkorlig åtkomst

Så här kontrollerar eller validerar du en enskild programs aktuella konfigurerade princip för villkorlig åtkomst:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** på navigerings menyn.

5.  Klicka på **alla program**.

6.  Sök efter det program som du är intresse rad av, eller så försöker användaren logga in till med programmets visnings namn eller program-ID.

     >[!NOTE]
     >Om du inte ser programmet du letar efter klickar du på knappen **filter** och expanderar omfånget för listan till **alla program**. Om du vill visa fler kolumner klickar du på knappen **kolumner** för att lägga till ytterligare information för dina program.
     >
     >

7.  Klicka på navigerings objekt för **villkorlig åtkomst** .

8.  Klicka på den princip som du är intresse rad av att inspektera.

9.  Se till att det inte finns några speciella villkor, tilldelningar eller andra inställningar som kan blockera användar åtkomst.

     >[!NOTE]
     >Du kanske vill inaktivera den här principen tillfälligt för att säkerställa att den inte påverkar inloggningar. Det gör du genom att ställa in **Aktivera princip** växla till **Nej** och klicka på knappen **Spara** .
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Inaktivera en speciell princip för villkorlig åtkomst

Så här kontrollerar eller validerar du en enskild princip för villkorlig åtkomst:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** på navigerings menyn.

5.  Klicka på navigerings objekt för **villkorlig åtkomst** .

6.  Klicka på den princip som du är intresse rad av att inspektera.

7.  Inaktivera principen genom att ställa in **Aktivera princip** växla till **Nej** och klicka på knappen **Spara** .

## <a name="problems-with-application-consent"></a>Problem med program medgivande

Åtkomst till programmet kan blockeras eftersom det inte har inträffat rätt behörighet för medgivande. Här följer några exempel på hur du kan felsöka och lösa problem med program medgivande:

-   [Utföra en medgivande åtgärd på användar nivå](#perform-a-user-level-consent-operation)

-   [Utför en åtgärd på administratörs nivå för alla program](#perform-administrator-level-consent-operation-for-any-application)

-   [Utföra ett godkännande på administratörs nivå för ett program med en klient](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Utföra ett godkännande på administratörs nivå för ett program med flera klienter](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Utföra en medgivande åtgärd på användar nivå

-   För alla öppna ID Connect-aktiverade program som begär behörigheter kan du gå till programmets inloggnings skärm för att göra en användar nivå godkänd för programmet för den inloggade användaren.

-   Om du vill göra detta program mässigt kan du läsa mer i [begära individuell användar medgivande](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Utför en åtgärd på administratörs nivå för alla program

-   För att endast få program som har **utvecklats med v1-programmodellen** kan du tvinga den här administratörs behörigheten att ske genom att lägga till "**? prompt = admin \_ medgivande**" i slutet av programmets inloggnings-URL.

-   För **alla program som har utvecklats med v2-programmodellen** kan du tvinga detta medgivande på administratörs nivå genom att följa anvisningarna i avsnittet **be behörigheterna från en katalog administratör** i att [använda den administrativa medgivande slut punkten](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Utföra ett godkännande på administratörs nivå för ett program med en klient

-   För **program med en enda klient** som begär behörigheter (t. ex. de som du utvecklar eller äger i din organisation) kan du utföra en godkännande åtgärd på **Administratörs nivå** för alla användare genom att logga in som global administratör och klicka på knappen **bevilja behörigheter** överst i **program registret – &gt; alla program – &gt; Välj en app- &gt; nödvändig behörighets** ruta.

-   För **alla program som har utvecklats med v1-eller v2-programmodellen** kan du tvinga detta medgivande på administratörs nivå genom att följa anvisningarna i avsnittet **be behörigheterna från en katalog administratör** i att [använda den administrativa medgivande slut punkten](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Utföra ett godkännande på administratörs nivå för ett program med flera klienter

-   För **program med flera klienter** som begär behörigheter (t. ex. ett program från tredje part eller Microsoft utvecklar) kan du utföra en **medgivande åtgärd på administrativ nivå** . Logga in som global administratör och klicka på knappen **bevilja behörigheter** under **företags program – &gt; alla program – &gt; Välj en app- &gt; Permissions** -fönster (tillgänglig snart).

-   Du kan också tvinga detta medgivande på administratörs nivå genom att följa anvisningarna i avsnittet **be behörigheterna från en katalog administratör** i [att använda den administrativa medgivande slut punkten](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Nästa steg
[Använda slut punkten för administratörs medgivande](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)