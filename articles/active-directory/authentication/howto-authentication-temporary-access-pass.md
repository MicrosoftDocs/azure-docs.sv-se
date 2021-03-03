---
title: Konfigurera ett tillfälligt åtkomst pass i Azure AD för att registrera metoder för lösen ords lös autentisering
description: Lär dig hur du konfigurerar och gör det möjligt för användare att registrera metoder för lösen ords lös autentisering med hjälp av ett tillfälligt åtkomst pass (KNACKa)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: justinha
author: inbarckms
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0f49f39e6bc291c3242fe739866a015ac154a8b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651169"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Konfigurera tillfälligt åtkomst pass i Azure AD för att registrera metoder för lösen ords lös autentisering (för hands version)

Metoder för lösen ords lös autentisering, till exempel FIDO2 och lösen ords lös telefonin loggning via Microsoft Authenticator-appen, gör det möjligt för användare att logga in på ett säkert sätt utan lösen ord. Användare kan starta metoder för att använda lösen ord på något av två sätt:

- Använda befintliga metoder för Azure AD Multi-Factor Authentication 
- Använda ett tillfälligt åtkomst pass (KNACKa) 

KNACKa är ett tidsbegränsat lösen ord som utfärdats av en administratör som uppfyller kraven på stark autentisering och som kan användas för att publicera andra autentiseringsmetoder, inklusive lösen ord. Genom att KNACKa kan du göra återställningen enklare när en användare har förlorat eller glömt sin starka autentiserings faktor, till exempel en säkerhets nyckel för FIDO2 eller Microsoft Authenticator app, men du måste logga in för att registrera nya metoder för stark autentisering.


Den här artikeln visar hur du aktiverar och använder en KNACKNING i Azure AD med hjälp av Azure Portal. Du kan också utföra dessa åtgärder med hjälp av REST-API: er. 

>[!NOTE]
>Tillfälligt åtkomst pass är för närvarande en offentlig för hands version. Vissa funktioner kanske inte stöds eller har begränsade funktioner. 

## <a name="enable-the-tap-policy"></a>Aktivera tryck principen

En TRYCKNINGs princip definierar inställningar, till exempel livs längden för pass som skapas i klienten eller de användare och grupper som kan använda en tryck för att logga in. Innan någon kan logga in med en KNACKNING måste du aktivera principen för autentiseringsmetod och välja vilka användare och grupper som kan logga in med hjälp av en tryckning.
Även om du kan skapa ett tryck för alla användare kan bara de som ingår i principen logga in med den.

Rollen global administratör och autentiseringsmetod för princip administratörer kan uppdatera principen tryck på autentiseringsmetod.
Så här konfigurerar du principen för att trycka på autentiseringsmetod:

1. Logga in på Azure Portal som global administratör och klicka på **Azure Active Directory**  >    >  **säkerhetsautentiseringsmetoder**  >  **temporär åtkomst pass**.
1. Klicka på **Ja** om du vill aktivera principen, välja vilka användare som principen ska tillämpas på och eventuella **allmänna** inställningar.

   ![Skärm bild av hur du aktiverar principen tryck på autentiseringsmetod](./media/how-to-authentication-temporary-access-pass/policy.png)

   Standardvärdet och intervallet för tillåtna värden beskrivs i följande tabell.


   | Inställning          | Standardvärden | Tillåtna värden               | Kommentarer                                                                                                                                                                                                                                                                 |   |
   |------------------|----------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    Lägsta livstid | 1 timme         | 10 – 43200 minuter (30 dagar) | Det minsta antal minuter som KNACKen är giltig.                                                                                                                                                                                                                         |   |
   | Högsta livstid | 24 timmar       | 10 – 43200 minuter (30 dagar) | Maximalt antal minuter som TRYCKNINGen är giltig.                                                                                                                                                                                                                         |   |
   | Standard livs längd | 1 timme         | 10 – 43200 minuter (30 dagar) | Standardvärden kan åsidosättas av de enskilda stegen, inom den lägsta och högsta livstid som kon figurer ATS av principen                                                                                                                                                |   |
   | Användning i taget     | Falskt          | True/false                 | När principen är inställd på false kan pass i klienten användas antingen en gång eller mer än en gång under giltighets tiden (högsta livstid). Genom att framtvinga användning av en viss tid i tryck principen skapas alla pass som skapats i klienten som engångs användning. |   |
   | Längd           | 8              | 8-48 tecken              | Definierar längden på lösen ordet.                                                                                                                                                                                                                                      |   |

## <a name="create-a-tap-in-the-azure-ad-portal"></a>Skapa en tryckning i Azure AD-portalen

När du har aktiverat en tryck policy kan du skapa en tryckning för en användare i Azure AD. Dessa roller kan utföra följande åtgärder relaterade till KNACKNING.

- Global administratör kan skapa, ta bort, Visa och trycka på alla användare (förutom själva)
- Administratörer för privilegie rad autentisering kan skapa, ta bort, Visa och trycka på administratörer och medlemmar (förutom själva)
- Autentiserings administratörer kan skapa, ta bort, Visa och trycka på medlemmar (förutom själva)
- Global administratör kan visa information om TRYCKNINGen på användaren (utan att läsa själva koden).

För att skapa en tryckning:

1. Logga in på portalen som global administratör, Privileged Authentication Administrator eller Authentication Administrator. 
1. Klicka på **Azure Active Directory**, bläddra till användare, Välj en användare, till exempel *Christer grönt*, och välj **autentiseringsmetoder**.
1. Om det behövs väljer du alternativet för att **testa den nya metoden** för användarautentisering.
1. Välj alternativet för att **lägga till autentiseringsmetoder**.
1. Under **Välj metod** klickar du på **tillfälligt åtkomst pass (för hands version)**.
1. Definiera en anpassad aktiverings tid eller varaktighet och klicka på **Lägg till**.

   >[!NOTE]
   >En engångs tryckning används inte när jag låter mig vara inloggad (KMSI avgör) är aktive rad i klient organisationen. Om du skapar en engångs KNACKNING, se till att inaktivera KMSI avgör.

   ![Skärm bild av hur du skapar en KNACKNING](./media/how-to-authentication-temporary-access-pass/create.png)

1. När du har lagt till det visas information om KNACKen. Anteckna det faktiska värdet för tryck. Du anger det här värdet för användaren. Du kan inte Visa det här värdet när du har klickat på **OK**.
   
   ![Skärm bild av tryck information](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-tap"></a>Använd en KNACKNING

Den vanligaste användningen av en tryckning är att en användare kan registrera autentiseringsinformation under den första inloggningen, utan att behöva slutföra ytterligare säkerhets frågor. Autentiseringsmetoder registreras på [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Användare kan också uppdatera befintliga autentiseringsmetoder här.

1. Öppna en webbläsare för att [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Ange UPN för det konto som du skapade KNACKNINGen för, till exempel *tapuser@contoso.com* .
1. Om användaren ingår i policyn för att trycka, ser han eller hon en skärm för att ange deras KNACKNING.
1. Ange den KNACKNING som visades i Azure Portal.

   ![Skärm bild av hur du anger en KNACKNING](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>För federerade domäner föredras en tryckning över federationen. En användare med en tryckning kommer att slutföra autentiseringen i Azure AD och kommer inte att omdirigeras till den federerade identitets leverantören (IdP).

Användaren är nu inloggad och kan uppdatera eller registrera en metod som FIDO2 säkerhets nyckel. Användare som uppdaterar sina autentiseringsmetoder på grund av förlust av sina autentiseringsuppgifter eller enheter bör se till att de tar bort gamla autentiseringsmetoder.

Användarna kan också använda sin tryckning för att registrera sig för inloggning via lösen ord direkt från Authenticator-appen. Mer information finns i [Lägg till ditt arbets-eller skol konto i Microsoft Authenticator-appen](../user-help/user-help-auth-app-add-work-school-account.md).

![Skärm bild av hur du anger ett tryck med arbets-eller skol konto](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-tap"></a>Ta bort en tryckning

Det går inte att använda en utgången KNACKNING. Under **autentiseringsmetoder** för en användare, visar **detalj** kolumnen när tryckningen har upphört att gälla. Du kan ta bort dessa utgångna kranar med följande steg:

1. Bläddra till **användare** på Azure AD-portalen, Välj en användare, t. ex. *Tryck på användare* och välj **autentiseringsmetoder**.
1. På den högra sidan av autentiseringsmetoden **temporär åtkomst pass (förhands granskning)** som visas i listan väljer du **ta bort**.

## <a name="replace-a-tap"></a>Ersätta en KNACKNING 

- En användare kan bara ha en KNACKNING. Lösen ordet kan användas när du trycker på Start-och slut tid.
- Om användaren behöver en ny tryckning:
  - Om den befintliga TRYCKNINGen är giltig måste administratören ta bort den befintliga TRYCKNINGen och skapa ett nytt pass för användaren. Om du tar bort en giltig tryckning återkallas användarens sessioner. 
  - Om den befintliga TRYCKNINGen har upphört att gälla åsidosätter en ny tryckning den befintliga TRYCKNINGen och kommer inte att återkalla användarens sessioner.

Mer information om NIST-standarder för onboarding och återställning finns i [NIST Special Publication 800-63a](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Begränsningar

Tänk på följande begränsningar:

- När du använder en engångs KNACKNING för att registrera en metod för lösen ords lös, till exempel FIDO2 eller telefonin loggning, måste användaren slutföra registreringen inom 10 minuters inloggning med en engångs-KNACKNING. Den här begränsningen gäller inte för en KNACKNING som kan användas mer än en gång.
- Gäst användare kan inte logga in med en KNACKNING.
- Användare i omfång för registrerings principen för självbetjänings återställning (SSPR) måste registrera en av SSPR-metoderna när de har loggat in med tryck. Om användaren bara kommer att använda FIDO2-nyckeln utesluter du dem från SSPR-principen eller inaktiverar registrerings principen för SSPR. 
- Det går inte att använda Tryck på nätverks princip Server (NPS)-tillägget och Active Directory Federation Services (AD FS) (AD FS)-kortet.
- En engångs tryckning används inte när KMSI avgör har Aktiver ATS för klienten.
- När sömlös SSO är aktiverat på klient organisationen uppmanas användarna att ange ett lösen ord. Länken **Använd ditt tillfälliga åtkomst pass** är tillgänglig för användaren att logga in med tryck.

![Skärm bild av Använd en KNACKNING i stället](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Felsökning    

- Om du inte erbjuds en användare under inloggningen kan du kontrol lera följande:
  - Användaren omfattas av principen tryck på autentiseringsmetod.
  - Användaren har en giltig KNACKNING, och om den används i taget används den inte ännu.
- Om **du har blockerat logga in för temporär åtkomst på grund av att användaren inte har** loggat in med trycker du på kontrol lera följande:
  - Användaren har en för användning med flera användnings metoder medan principen för autentisering kräver en engångs tryckning.
  - En engångs tryckning har redan använts.

## <a name="next-steps"></a>Nästa steg

- [Planera distribution av lösenordsskyddad autentisering i Azure Active Directory](howto-authentication-passwordless-deployment.md)

