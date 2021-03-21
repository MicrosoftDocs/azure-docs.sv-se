---
title: Anpassa lösen ords återställning via självbetjäning – Azure Active Directory
description: Lär dig hur du anpassar användarens visnings-och miljö alternativ för återställning av lösen ord för Azure AD Self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3766db0d5070f15216ac612353e2c25737ad092a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741634"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Anpassa användar upplevelsen för Azure Active Directory återställning av lösen ord för självbetjäning

Lösen ords återställning via självbetjäning (SSPR) ger användare i Azure Active Directory (Azure AD) möjlighet att ändra eller återställa sitt lösen ord, utan administratörs-eller Supportens medverkan. Om ett användar konto är låst eller om det glömmer sitt lösen ord, kan de följa prompter för att avblockera sig själva och komma tillbaka till arbetet. Detta minskar risken för support och produktivitets förlust när en användare inte kan logga in på sin enhet eller ett program.

För att förbättra SSPR-upplevelsen för användare kan du anpassa utseendet på sidan för lösen ords återställning, e-postaviseringar eller inloggnings sidor. Med dessa anpassnings alternativ kan du göra det tydligt för användaren som de befinner sig på rätt plats och ge dem förtroende för att få åtkomst till företags resurser.
    
Den här artikeln visar hur du anpassar e-postlänken SSPR för användare, företags anpassning och AD FS inloggnings sida.

## <a name="customize-the-contact-your-administrator-link"></a>Anpassa länken "kontakta din administratör"

För att hjälpa användarna att kontakta för att få hjälp med lösen ords återställning via självbetjäning visas länken Kontakta administratören i portalen för återställning av lösen ord. Om en användare väljer den här länken görs en av två saker:

* Om den här kontakt länken är kvar i standard läget skickas ett e-postmeddelande till dina administratörer och ber dem att ge hjälp att ändra användarens lösen ord. I följande exempel visas e-postmeddelandet som standard:

    ![Exempel förfrågan om att återställa e-post som skickas till administratör](./media/howto-sspr-customization/sspr-contact-admin.png)

* Om den är anpassad skickas användaren till en webb sida eller e-postadress som anges av administratören för hjälp.
    * Om du anpassar detta rekommenderar vi att du anger det till något som användarna redan är bekant med för support.

    > [!WARNING]
    > Om du anpassar den här inställningen med en e-postadress och ett konto som behöver återställa lösen ord kanske användaren inte behöver be om hjälp.

### <a name="default-email-behavior"></a>Standard beteende för e-post

E-postadressen till standard kontakten skickas till mottagarna i följande ordning:

1. Om *Administratörs* rollen för supportavdelningen eller rollen *lösen ords administratör* tilldelas, meddelas administratörer med dessa roller.
1. Om ingen administratör för supportavdelningen eller lösen ords administratören tilldelas, meddelas administratörer med rollen *användar administratör* .
1. Om ingen av de tidigare rollerna tilldelas, meddelas de *globala administratörerna* .

I samtliga fall meddelas högst 100 mottagare.

Läs mer om de olika administratörs rollerna och hur du tilldelar dem i [tilldela administratörs roller i Azure Active Directory](../roles/permissions-reference.md).

### <a name="disable-contact-your-administrator-emails"></a>Inaktivera "kontakta administratören"-e-post

Om din organisation inte vill meddela administratörer om begär Anden om lösen ords återställning kan du använda följande konfigurations alternativ:

* Anpassa supportavdelningen-länken för att tillhandahålla en webb-URL eller mailto:-adress som användarna kan använda för att få hjälp. Det här alternativet är under anpassning av **lösen ords återställning**  >    >  **anpassad supportavdelningen e-post eller URL**.
* Aktivera självbetjäning för återställning av lösen ord för alla användare. Det här alternativet är under egenskaper för **lösen ords återställning**  >  . Om du inte vill att användarna ska kunna återställa sina egna lösen ord kan du begränsa åtkomsten till en tom grupp. *Vi rekommenderar inte det här alternativet.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Anpassa inloggnings sidan och åtkomst panelen

Du kan anpassa inloggnings sidan, till exempel för att lägga till en logo typ som visas tillsammans med den bild som passar ditt företags varumärke. Mer information om hur du konfigurerar företags anpassning finns i avsnittet [lägga till företags anpassning till din inloggnings sida i Azure AD](../fundamentals/customize-branding.md).

De bilder du väljer visas i följande fall:

* När en användare har angett sitt användar namn
* Om användaren har åtkomst till den anpassade URL: en:
   * Genom att skicka `whr` parametern till sidan för lösen ords återställning, t. ex. `https://login.microsoftonline.com/?whr=contoso.com`
   * Genom att skicka `username` parametern till sidan för lösen ords återställning, t. ex. `https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Katalog namn

Om du vill göra saker mer användarvänliga kan du ändra organisationens namn i portalen och i den automatiserade kommunikationen. Om du vill ändra attributet katalog namn i Azure Portal bläddrar du till **Azure Active Directory**  >  **Egenskaper**. Det här egna organisations namn alternativet är det som visas i automatiserade e-postmeddelanden, som i följande exempel:

* Det egna namnet i e-postmeddelandet, till exempel "*Microsoft på uppdrag av contoso demo*"
* Ämnes raden i e-postmeddelandet, till exempel "*contoso demonstrations konto e-postverifierings kod*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Anpassa AD FS inloggnings sida

Om du använder Active Directory Federation Services (AD FS) (AD FS) för användar inloggnings händelser kan du lägga till en länk till inloggnings sidan genom att följa anvisningarna i artikeln för att [lägga till beskrivningen av inloggnings sidan](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Förse användarna med en länk till sidan för att ange SSPR-arbetsflödet, till exempel *https://passwordreset.microsoftonline.com* . Om du vill lägga till en länk till AD FS inloggnings sidan använder du följande kommando på AD FS-servern:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Nästa steg

Information om användningen av SSPR i din miljö finns i [rapporterings alternativ för lösen ords hantering i Azure AD](howto-sspr-reporting.md).

Om du eller användare har problem med SSPR kan du läsa mer i [Felsöka lösen ords återställning via självbetjäning](./troubleshoot-sspr.md)