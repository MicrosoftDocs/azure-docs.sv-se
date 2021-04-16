---
title: Förstå lösenordsbaserad enkel inloggning (SSO) för appar i Azure Active Directory
description: Förstå lösenordsbaserad enkel inloggning (SSO) för appar i Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: iangithinji
ms.openlocfilehash: ffa517f068dbc13f2734630216466373d9014ae6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374564"
---
# <a name="understand-password-based-single-sign-on"></a>Förstå lösenordsbaserad enkel inloggning

I [snabbstartsserien](view-applications-portal.md) om programhantering lärde du dig att använda Azure AD som identitetsprovider (IdP) för ett program. I snabbstartsguiden konfigurerar du SAML-baserad eller OIDC-baserad enkel inloggning. Ett annat alternativ är lösenordsbaserad enkel inloggning. Den här artikeln innehåller mer information om alternativet för lösenordsbaserad enkel inloggning. 

Det här alternativet är tillgängligt för alla webbplatser med en HTML-inloggningssida. Lösenordsbaserad enkel inloggning kallas även för lösenordsvalv. Med lösenordsbaserad enkel inloggning kan du hantera användaråtkomst och lösenord till webbprogram som inte stöder identitetsfederation. Det är också användbart när flera användare behöver dela ett enda konto, till exempel till organisationens konton för appar för sociala medier.

Lösenordsbaserad enkel inloggning är ett bra sätt att komma igång med att integrera program i Azure AD snabbt och gör att du kan:

- Aktivera enkel inloggning för dina användare genom att lagra och spela upp användarnamn och lösenord på ett säkert sätt

- Stödprogram som kräver flera inloggningsfält för program som kräver mer än bara användarnamn och lösenord för att logga in

- Anpassa etiketterna för fälten för användarnamn och lösenord som användarna ser [Mina appar](../user-help/my-apps-portal-end-user-access.md) de anger sina autentiseringsuppgifter

- Låt användarna ange sina egna användarnamn och lösenord för alla befintliga programkonton som de skriver in manuellt.

- Tillåt att en medlem i affärsgruppen anger användarnamn och lösenord som tilldelats en användare med hjälp av funktionen Programåtkomst [via självbetjäning](./manage-self-service-access.md)

-   Tillåt att en administratör anger ett användarnamn och lösenord som ska användas av enskilda användare eller grupper när de loggar in i programmet med funktionen Uppdatera autentiseringsuppgifter 

## <a name="before-you-begin"></a>Innan du börjar

Det kan vara enkelt eller komplext att använda Azure AD som identitetsprovider (IdP) och konfigurera enkel inloggning (SSO) beroende på vilket program som används. Vissa program kan konfigureras med bara några få åtgärder. Andra kräver djupgående konfiguration. Om du snabbt vill få kunskap kan du gå igenom [snabbstartsserien](view-applications-portal.md) om programhantering. Om programmet du lägger till är enkelt behöver du förmodligen inte läsa den här artikeln. Om det program som du lägger till kräver anpassad konfiguration och du behöver använda lösenordsbaserad enkel inloggning är den här artikeln något för dig.

> [!IMPORTANT] 
> Det finns vissa scenarier där **alternativet Enkel inloggning inte** finns i navigeringen för ett program i **Företagsprogram**. 
>
> Om programmet registrerades med **Appregistreringar** har funktionen för enkel inloggning konfigurerats för att använda OIDC OAuth som standard. I det här fallet **visas inte alternativet Enkel** inloggning i navigeringen under **Företagsprogram**. När du använder **Appregistreringar för** att lägga till din anpassade app konfigurerar du alternativ i manifestfilen. Mer information om manifestfilen finns i Azure Active Directory [appmanifestet](../develop/reference-app-manifest.md). Mer information om standarder för enkel inloggning finns i Autentisering [och auktorisering med Microsoft Identity Platform.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Andra scenarier **där** enkel inloggning saknas i navigeringen är när ett program finns i en annan klientorganisation eller om ditt konto inte har de behörigheter som krävs (global administratör, molnprogramadministratör, programadministratör eller ägare av tjänstens huvudnamn). Behörigheter kan också orsaka ett scenario där du **kan öppna enkel** inloggning men inte kan spara. Mer information om administrativa roller i Azure AD finns i ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Grundläggande konfiguration

I [snabbstartsserien](view-applications-portal.md)har du lärt dig hur du lägger till en app i din klientorganisation, vilket gör att Azure AD vet att den används som identitetsprovider (IdP) för appen. Vissa appar är redan förkonfigurerade och visas i Azure AD-galleriet. Andra appar finns inte i galleriet och du måste skapa en allmän app och konfigurera den manuellt. Beroende på appen kanske alternativet för lösenordsbaserad enkel inloggning inte är tillgängligt. Om du inte ser listan lösenordsbaserad alternativ på sidan för enkel inloggning för appen är den inte tillgänglig.

> [!IMPORTANT]
> Det Mina appar webbläsartillägget krävs för lösenordsbaserad enkel inloggning. Mer information finns i Planera [en Mina appar distribution.](my-apps-deployment-plan.md)

Konfigurationssidan för lösenordsbaserad enkel inloggning är enkel. Den innehåller endast URL:en för den inloggningssida som appen använder. Den här strängen måste vara den sida som innehåller indatafältet för användarnamn.

När du har anger URL:en väljer du **Spara**. Azure AD parsar HTML-koden på inloggningssidan för fälten användarnamn och lösenord. Om försöket lyckas är du klar.
 
Nästa steg är att [Tilldela användare eller grupper till programmet](./assign-user-or-group-access-portal.md). När du har tilldelat användare och grupper kan du ange autentiseringsuppgifter som ska användas för en användare när de loggar in i programmet. Välj **Användare och** grupper, markera kryssrutan för användarens eller gruppens rad och välj sedan Uppdatera **autentiseringsuppgifter.** Ange slutligen det användarnamn och lösenord som ska användas för användaren eller gruppen. Om du inte gör det uppmanas användarna att själva ange autentiseringsuppgifterna vid start.
 

## <a name="manual-configuration"></a>Manuell konfiguration

Om Azure AD:s parsningsförsök misslyckas kan du konfigurera inloggningen manuellt.

1. Under **\<application name> Konfiguration** väljer du **Konfigurera inställningar för enkel inloggning med \<application name> lösenord** för att **visa sidan Konfigurera** inloggning. 

2. Välj **Identifiera inloggningsfält manuellt.** Ytterligare instruktioner som beskriver den manuella identifieringen av inloggningsfält visas.

   ![Manuell konfiguration av lösenordsbaserad enkel inloggning](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Välj **Avbilda inloggningsfält.** En sida för avskiljstatus öppnas på en ny flik och visar meddelandet **metadata capture is currently in progress**.

4. Om rutan **Mina appar obligatoriskt** tillägg visas på en  ny flik väljer du Installera nu för att **installera** Mina appar webbläsartillägget för säker inloggning. (Webbläsartillägget kräver Microsoft Edge, Chrome eller Firefox.) Installera, starta och aktivera tillägget och uppdatera avinstallationsstatussidan.

   Webbläsartillägget öppnar sedan en annan flik som visar den angivna URL:en.
5. Gå igenom inloggningsprocessen på fliken med den angivna URL:en. Fyll i fälten användarnamn och lösenord och försök att logga in. (Du behöver inte ange rätt lösenord.)

   En uppmaning uppmanar dig att spara de avbildade inloggningsfälten.
6. Välj **OK**. Webbläsartillägget uppdaterar sidan för avskiljningsstatus med **meddelandet Metadata har uppdaterats för programmet**. Webbläsarfliken stängs.

7. På sidan Azure AD **Configure sign-on** (Konfigurera inloggning med Azure AD) väljer du **Ok, jag kunde logga in på appen.**

8. Välj **OK**.

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](./assign-user-or-group-access-portal.md)
- [Konfigurera automatisk användarkontoetablering](../app-provisioning/configure-automatic-user-provisioning-portal.md)
