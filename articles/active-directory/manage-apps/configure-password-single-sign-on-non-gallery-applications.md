---
title: Förstå lösenordsbaserad enkel inloggning (SSO) för appar i Azure Active Directory
description: Förstå lösenordsbaserad enkel inloggning (SSO) för appar i Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: bac04bd70469d7b9c4d9485b6a87fd7133967154
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255313"
---
# <a name="understand-password-based-single-sign-on"></a>Förstå lösenordsbaserad enkel inloggning

I [snabb starts serien](view-applications-portal.md) för program hantering har du lärt dig hur du använder Azure AD som identitets leverantör (IdP) för ett program. I snabb starts guiden konfigurerar du SAML-baserad eller OIDC-baserad SSO. Ett annat alternativ är lösenordsbaserad SSO. Den här artikeln går till mer information om det lösenordsbaserade SSO-alternativet. 

Det här alternativet är tillgängligt för alla webbplatser med en HTML-inloggnings sida. Lösenordsbaserad SSO kallas även för lösen ords valv. Med lösenordsbaserad SSO kan du hantera användar åtkomst och lösen ord för webb program som inte har stöd för identitets Federation. Det är också användbart där flera användare behöver dela ett enda konto, t. ex. till din organisations konton för sociala media.

Lösenordsbaserad SSO är ett bra sätt att komma igång med att integrera program i Azure AD snabbt och gör att du kan:

- Aktivera enkel inloggning för dina användare genom säker lagring och omuppspelning av användar namn och lösen ord

- Stöd för program som kräver flera inloggnings fält för program som kräver mer än bara användar namn och lösen ords fält för att logga in

- Anpassa etiketterna för fälten användar namn och lösen ord som användarna ser i [Mina appar](../user-help/my-apps-portal-end-user-access.md) när de anger sina autentiseringsuppgifter

- Tillåt användarna att ange sina egna användar namn och lösen ord för alla befintliga program konton som de skriver i manuellt.

- Tillåt en medlem i företags gruppen att ange användar namn och lösen ord som tilldelats en användare med hjälp av funktionen för [självbetjänings åtkomst](./manage-self-service-access.md)

-   Tillåt en administratör att ange ett användar namn och lösen ord som ska användas av personer eller grupper när de loggar in i programmet med funktionen uppdatera autentiseringsuppgifter 

## <a name="before-you-begin"></a>Innan du börjar

Att använda Azure AD som identitets leverantör (IdP) och konfigurera enkel inloggning (SSO) kan vara enkelt eller komplext beroende på vilket program som används. Vissa program kan konfigureras med bara några få åtgärder. Andra kräver djupgående konfiguration. Om du vill öka kunskapen snabbt kan du gå igenom [snabb starts serien](view-applications-portal.md) för program hantering. Om det program som du lägger till är enkelt, behöver du förmodligen inte läsa den här artikeln. Om programmet som du lägger till kräver anpassad konfiguration och du behöver använda lösenordsbaserad SSO, är den här artikeln för dig.

> [!IMPORTANT] 
> Det finns vissa scenarier där alternativet för **enkel inloggning** inte kommer att ingå i navigeringen för ett program i **företags program**. 
>
> Om programmet registrerades med **Appregistreringar** konfigureras funktionen för enkel inloggning för att använda OIDC OAuth som standard. I det här fallet visas inte alternativet för **enkel inloggning** i navigeringen under **företags program**. När du använder **Appregistreringar** för att lägga till din anpassade app konfigurerar du alternativ i manifest filen. Mer information om manifest filen finns i [Azure Active Directory app manifest](../develop/reference-app-manifest.md). Mer information om SSO-standarder finns i [autentisering och auktorisering med Microsoft Identity Platform](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform). 
>
> Andra scenarier där **enkel inloggning** kommer att saknas i navigeringen är när ett program finns i en annan klient organisation eller om ditt konto inte har de behörigheter som krävs (global administratör, moln program administratör, program administratör eller ägare till tjänstens huvud namn). Behörigheter kan också orsaka ett scenario där du kan öppna **enkel inloggning** men inte kan spara. Mer information om administrativa roller i Azure AD finns i ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Grundläggande konfiguration

I [snabb starts serien](view-applications-portal.md)har du lärt dig hur du lägger till en app till din klient, vilket gör att Azure AD vet att den används som identitets leverantör (IdP) för appen. Vissa appar är redan förkonfigurerade och de visas i Azure AD-galleriet. Andra appar finns inte i galleriet och du måste skapa en allmän app och konfigurera den manuellt. Beroende på appen kanske det lösen ordsbaserade SSO-alternativet inte är tillgängligt. Om du inte ser den lösenordsbaserade alternativ listan på sidan för enkel inloggning för appen, är den inte tillgänglig.

> [!IMPORTANT]
> Webb läsar tillägget My Apps krävs för lösenordsbaserad SSO. Mer information finns i [Planera distribution av mina appar](my-apps-deployment-plan.md).

Konfigurations sidan för lösenordsbaserad enkel inloggning är enkel. Den innehåller bara webb adressen till inloggnings sidan som appen använder. Den här strängen måste vara sidan som innehåller fältet username.

När du har angett URL: en väljer du **Spara**. Azure AD parsar HTML-koden för inloggnings sidan för fälten användar namn och lösen ord. Om försöket lyckas är du klar.
 
Nästa steg är att [tilldela användare eller grupper till programmet](./assign-user-or-group-access-portal.md). När du har tilldelat användare och grupper kan du ange autentiseringsuppgifter som ska användas för en användare när de loggar in i programmet. Välj **användare och grupper**, markera kryss rutan för användarens eller gruppens rad och välj sedan **uppdatera autentiseringsuppgifter**. Ange slutligen det användar namn och lösen ord som ska användas för användaren eller gruppen. Om du inte gör det uppmanas användarna att ange autentiseringsuppgifterna vid start.
 

## <a name="manual-configuration"></a>Manuell konfiguration

Om Azure AD parsing-försöket Miss lyckas kan du konfigurera inloggning manuellt.

1. Under **\<application name> konfiguration** väljer du **Konfigurera \<application name> Inställningar för enkel inloggning med lösen ord** för att visa sidan **Konfigurera inloggning** . 

2. Välj **identifiera inloggnings fält manuellt**. Ytterligare instruktioner som beskriver manuell identifiering av inloggnings fält visas.

   ![Manuell konfiguration av lösenordsbaserad enkel inloggning](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Välj **registrerings fält för registrering**. Sidan status för hämtning öppnas på en ny flik som visar att meddelande om att **metadata har fångats pågår**.

4. Om rutan **Mina appar som krävs** visas i en ny flik väljer du **Installera nu** för att installera webb läsar tillägget **Mina appar säker inloggnings tillägg** . (Webb läsar tillägget kräver Microsoft Edge, Chrome eller Firefox.) Installera, starta och aktivera tillägget och uppdatera sidan för hämtnings status.

   Webb läsar tillägget öppnar sedan en annan flik som visar den angivna URL: en.
5. Gå igenom inloggnings processen på fliken med den angivna URL: en. Fyll i fälten användar namn och lösen ord och försök logga in. (Du behöver inte ange rätt lösen ord.)

   Du uppmanas att spara de insamlade inloggnings fälten.
6. Välj **OK**. Webb läsar tillägget uppdaterar sidan för hämtnings status med meddelandets **metadata har uppdaterats för programmet**. Fliken webbläsare stängs.

7. På sidan **Konfigurera inloggning på** Azure AD väljer **du OK, jag kunde logga in på appen**.

8. Välj **OK**.

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](./assign-user-or-group-access-portal.md)
- [Konfigurera automatisk etablering av användar konto](../app-provisioning/configure-automatic-user-provisioning-portal.md)
