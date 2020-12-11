---
title: Felsöka problem med att logga in till ett program från Azure AD Mina appar
description: Felsöka problem med att logga in till ett program från Azure AD Mina appar
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: 59d733bfe5580e64d531eeac1db443982a308517
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033637"
---
# <a name="troubleshoot-problems-signing-in-to-an-application-from-azure-ad-my-apps"></a>Felsöka problem med att logga in till ett program från Azure AD Mina appar

Mina appar är en webbaserad portal som gör det möjligt för en användare med ett arbets-eller skol konto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har beviljat åtkomst till. Mina appar öppnas med hjälp av en webbläsare på [https://myapps.microsoft.com](https://myapps.microsoft.com) .

Mer information om hur du använder Azure AD som identitets leverantör för en app finns i [Vad är program hantering i Azure AD](what-is-application-management.md). Kom igång snabbt genom att titta på [snabb starts serien för program hantering](view-applications-portal.md).

Dessa program är konfigurerade för användarens räkning i Azure AD-portalen. Programmet måste vara korrekt konfigurerat och tilldelat användaren eller en grupp som användaren är medlem i för att kunna se programmet i Mina appar. 

Den typ av appar som en användare kan se hamnar i följande kategorier:
-   Microsoft 365 program
-   Program från Microsoft och tredje part som kon figurer ATS med Federation-baserad SSO
-   Lösenordsbaserade SSO-program
-   Program med befintliga SSO-lösningar

Här är några saker att kontrol lera om en app visas eller inte visas.
- Kontrol lera att appen har lagts till i Azure AD och se till att användaren är tilldelad. Mer information finns i [snabb starts serien för program hantering](add-application-portal.md).
- Om du nyligen har lagt till en app kan du logga ut och sedan in igen. 
- Om appen kräver en licens, till exempel Office, kontrollerar du att användaren har tilldelats rätt licens.
- Hur lång tid det tar för licensierings ändringar kan variera beroende på gruppens storlek och komplexitet.

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrol lera först

-   Kontrol lera att webbläsaren uppfyller kraven, se webbläsare som [stöds i Mina appar](../user-help/my-apps-portal-end-user-access.md).
-   Kontrol lera att användarens webbläsare har lagt till URL: en för programmet på **Betrodda platser**.
-   Kontrol lera att programmet har **kon figurer ATS** korrekt.
-   Kontrol lera att användar kontot är **aktiverat** för inloggningar.
-   Kontrol lera att användarens konto **inte är utelåst.**
-   Kontrol lera att användarens **lösen ord inte har upphört att gälla eller är bortglömt.**
-   Kontrol lera att **Multi-Factor Authentication** inte blockerar användar åtkomsten.
-   Se till att en **princip för villkorlig åtkomst** eller **identitets skydds** princip inte blockerar användar åtkomst.
-   Se till att en användares **kontakt information för autentisering** är uppdaterad för att tillåta att Multi-Factor Authentication eller villkorliga åtkomst principer tillämpas.
-   Se till att du också försöker rensa webbläsarens cookies och försöka logga in igen.

## <a name="problems-with-the-users-account"></a>Problem med användarens konto
Åtkomst till Mina appar kan blockeras på grund av ett problem med användarens konto. Här följer några exempel på hur du kan felsöka och lösa problem med användare och deras konto inställningar:
-   [Kontrol lera om ett användar konto finns i Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)
-   [Kontrol lera en användares konto status](#check-a-users-account-status)
-   [Återställa en användares lösen ord](#reset-a-users-password)
-   [Aktivera lösenordsåterställning via självbetjäning](#enable-self-service-password-reset)
-   [Kontrol lera en användares Multi-Factor Authentication-status](#check-a-users-multi-factor-authentication-status)
-   [Kontrol lera en användares kontakt information för autentisering](#check-a-users-authentication-contact-info)
-   [Kontrol lera en användares grupp medlemskap](#check-a-users-group-memberships)
-   [Kontrol lera om en användare har fler än 999 roll tilldelningar för appar](#check-if-a-user-has-more-than-999-app-role-assignments)
-   [Kontrol lera en användares tilldelade licenser](#check-a-users-assigned-licenses)
-   [Tilldela en licens för en användare](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Kontrol lera om ett användar konto finns i Azure Active Directory
Följ dessa steg om du vill kontrol lera om det finns ett användar konto:
1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**
2.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.
4.  Välj **användare och grupper** på navigerings menyn.
5.  Välj **alla användare**.
6.  **Sök** efter den användare som du är intresse rad av och **Välj den rad** som du vill välja.
7.  Kontrol lera egenskaperna för användarobjektet och se till att de ser ut som du förväntar dig och att inga data saknas.

### <a name="check-a-users-account-status"></a>Kontrol lera en användares konto status
Följ dessa steg om du vill kontrol lera en användares konto status:
1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**
2.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.
4.  Välj **användare och grupper** på navigerings menyn.
5.  Välj **alla användare**.
6.  **Sök** efter den användare som du är intresse rad av och **Välj den rad** som du vill välja.
7.  Välj **profil**.
8.  Under **Inställningar** kontrollerar du att **blockera inloggning** är inställt på **Nej**.

### <a name="reset-a-users-password"></a>Återställa en användares lösen ord
Följ dessa steg om du vill återställa en användares lösen ord:
1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**
2.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.
4.  Välj **användare och grupper** på navigerings menyn.
5.  Välj **alla användare**.
6.  **Sök** efter den användare som du är intresse rad av och **Välj den rad** som du vill välja.
7.  Välj knappen **Återställ lösen ord** överst i fönstret användare.
8.  Välj knappen **Återställ lösen ord** i fönstret **Återställ lösen ord** som visas.
9.  Kopiera det **tillfälliga lösen ordet** eller **Ange ett nytt lösen ord** för användaren.
10. Skicka det nya lösen ordet till användaren, de måste ändra lösen ordet vid nästa inloggning till Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Aktivera lösenordsåterställning via självbetjäning
Följ dessa distributions steg för att aktivera självbetjäning för återställning av lösen ord:
-   [Gör det möjligt för användare att återställa sina Azure Active Directory lösen ord](../authentication/tutorial-enable-sspr.md)
-   [Gör det möjligt för användare att återställa eller ändra sina Active Directory lokala lösen ord](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Kontrol lera en användares Multi-Factor Authentication-status
Följ dessa steg om du vill kontrol lera en användares Multi-Factor Authentication-status:
1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**
2. Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.
4. Välj **användare och grupper** på navigerings menyn.
5. Välj **alla användare**.
6. Välj knappen **Multi-Factor Authentication** överst i fönstret.
7. Se till att du är på fliken **användare** när **Multi-Factor Authentication administrations Portal** har lästs in.
8. Hitta användaren i listan med användare genom att söka, filtrera eller sortera.
9. Välj användaren i listan med användare och **Aktivera**, **inaktivera** eller **tillämpa** Multi-Factor Authentication som önskade.
   >[!NOTE]
   >Om en användare är i **tvingande** tillstånd kan du ange att de tillfälligt **inaktive ras** för att låta dem gå tillbaka till sitt konto. När du har loggat in igen kan du ändra deras status till **aktive rad** för att kräva att de registrerar sin kontakt information igen under nästa inloggning. Du kan också följa stegen i [kontakt informationen för en användares autentisering](#check-a-users-authentication-contact-info) för att verifiera eller ange data för dem.

### <a name="check-a-users-authentication-contact-info"></a>Kontrol lera en användares kontakt information för autentisering
Följ dessa steg om du vill kontrol lera en användares kontaktinfo kontakt information som används för Multi-Factor Authentication, villkorlig åtkomst, identitets skydd och lösen ords återställning:
1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**
2.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.
4.  Välj **användare och grupper** på navigerings menyn.
5.  Välj **alla användare**.
6.  **Sök** efter den användare som du är intresse rad av och **Välj den rad** som du vill välja.
7.  Välj **profil**.
8.  Rulla ned till **autentisering kontakt information**.
9.  **Granska** de data som har registrerats för användaren och uppdatera dem efter behov.

### <a name="check-a-users-group-memberships"></a>Kontrol lera en användares grupp medlemskap
Följ dessa steg om du vill kontrol lera en användares grupp medlemskap:
1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**
2.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.
4.  Välj **användare och grupper** på navigerings menyn.
5.  Välj **alla användare**.
6.  **Sök** efter den användare som du är intresse rad av och **Välj den rad** som du vill välja.
7.  Välj **grupper** för att se vilka grupper användaren är medlem i.

### <a name="check-if-a-user-has-more-than-999-app-role-assignments"></a>Kontrol lera om en användare har fler än 999 roll tilldelningar för appar
Om en användare har fler än 999 roll tilldelningar för en användare kanske de inte ser alla sina appar i Mina appar.

Detta beror på att mina appar för närvarande läser upp till 999 program roll tilldelningar för att fastställa vilka appar som användarna tilldelas till. Om en användare är tilldelad fler än 999 appar går det inte att styra vilka appar som ska visas i portalen för Mina appar.

Följ dessa steg om du vill kontrol lera antalet program roll tilldelningar som har beviljats för en användare:
1. Installera [**Microsoft. Graph**](https://github.com/microsoftgraph/msgraph-sdk-powershell) PowerShell-modulen.
2. Kör `Connect-MgGraph -Scopes "Directory.Read.All"` och autentisera som **Global administratör.**
3. Kör `$m = Get-MgUserAppRoleAssignment -UserId "<userId>" | Measure; $m.Count` för att fastställa antalet app Role-tilldelningar som användaren har beviljat.

### <a name="check-a-users-assigned-licenses"></a>Kontrol lera en användares tilldelade licenser
Följ dessa steg om du vill kontrol lera en användares tilldelade licenser:
1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**
2.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.
4.  Välj **användare och grupper** på navigerings menyn.
5.  Välj **alla användare**.
6.  **Sök** efter den användare som du är intresse rad av och **Välj den rad** som du vill välja.
7.  Välj **licenser** för att se vilka licenser som användaren för närvarande har tilldelat.

### <a name="assign-a-user-a-license"></a>Tilldela en licens för en användare 
Följ dessa steg om du vill tilldela en licens till en användare:
1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**
2.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.
4.  Välj **användare och grupper** på navigerings menyn.
5.  Välj **alla användare**.
6.  **Sök** efter den användare som du är intresse rad av och **Välj den rad** som du vill välja.
7.  Välj **licenser** för att se vilka licenser som användaren för närvarande har tilldelat.
8.  Välj knappen **tilldela** .
9.  Välj **en eller flera produkter** i listan med tillgängliga produkter.
10. **Valfritt** Välj objektet **tilldelnings alternativ** för att dela produkter i detalj. Välj **OK**.
11. Välj knappen **tilldela** för att tilldela dessa licenser till den här användaren.

## <a name="troubleshooting-deep-links"></a>Felsöka djup länkar
Djup länkar eller URL: er för användar åtkomst är länkar som användarna kan använda för att komma åt sina lösen ords-SSO-program direkt från webbläsarens URL-fält. Genom att navigera till den här länken loggas användarna automatiskt in i programmet utan att först behöva gå till Mina appar. Länken är samma som användarna använder för att få åtkomst till dessa program från Microsoft 365 starta programmet.

### <a name="checking-the-deep-link"></a>Kontrollerar djup länken

Kontrol lera att du har rätt djup länk genom att följa dessa steg:
1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**
2. Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.
4. Välj **Företagsprogram** på den vänstra navigeringsmenyn i Azure Active Directory.
5. Välj **Alla program** för att visa en lista över alla dina program.
   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**
6. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**
7. Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.
8. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.
9. Välj **Företagsprogram** på den vänstra navigeringsmenyn i Azure Active Directory.
10. Välj **Alla program** för att visa en lista över alla dina program.
    * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**
11. Välj det program som du vill använda för att kontrol lera djup länken för.
12. Hitta URL: en för etikettens **användar åtkomst**. Din djup länk ska matcha denna URL.

## <a name="contact-support"></a>Kontakta supporten
Öppna ett support ärende med följande information om den är tillgänglig:
-   Korrelations fel-ID
-   UPN (användarens e-postadress)
-   TenantID
-   Typ av webbläsare
-   Tidszon och tid/tidsram vid fel inträffar
-   Fiddler-spår

## <a name="next-steps"></a>Nästa steg
- [Snabb starts serie för program hantering](view-applications-portal.md)
