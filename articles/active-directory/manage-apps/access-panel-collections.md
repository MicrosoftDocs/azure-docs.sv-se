---
title: Skapa samlingar för Mina appar portaler i Azure Active Directory | Microsoft Docs
description: Använd Mina appar samlingar för att anpassa Mina appar för en enklare Mina appar för dina slutanvändare. Ordna program i grupper med separata flikar.
services: active-directory
documentationcenter: ''
author: iantheninja
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iangithinji
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc79e8026cb91b8ef3eac2addbb097b9db83afa7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377692"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Skapa samlingar i portalen Mina appar

Användarna kan använda Mina appar portalen för att visa och starta de molnbaserade program som de har åtkomst till. Som standard visas alla program som en användare har åtkomst till tillsammans på en enda sida. Om du har en P1- eller P2-licens kan du konfigurera samlingar för att ordna den här Azure AD Premium sidan bättre för dina användare. Med en samling kan du gruppera program som är relaterade (till exempel efter jobbroll, uppgift eller projekt) och visa dem på en separat flik. En samling tillämpar i princip ett filter på de program som en användare redan har åtkomst till, så att användaren bara ser de program i samlingen som har tilldelats dem.

> [!NOTE]
> Den här artikeln beskriver hur en administratör kan aktivera och skapa samlingar. Information för slutanvändaren om hur man använder Mina appar portalen och samlingar finns i [Åtkomst och användning av samlingar.](../user-help/my-applications-portal-workspaces.md)

## <a name="enable-the-latest-my-apps-features"></a>Aktivera de senaste Mina appar funktionerna

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som användaradministratör eller global administratör.

2. Gå till **Azure Active Directory**  >  **användarinställningar.**

3. Under **Förhandsgranskning av användarfunktion** väljer du **Hantera inställningar för förhandsgranskning av användarfunktion.**

4. Under **Användare kan använda förhandsgranskningsfunktioner för Mina appar** väljer du något av följande alternativ:
   * **Selected** – Aktiverar funktionerna för en viss grupp. Använd **alternativet Välj en** grupp för att välja den grupp som du vill aktivera funktionerna för.  
   * **Alla** – Aktiverar funktionerna för alla användare.

> [!NOTE]
> För att Mina appar portalen kan användarna använda länken `https://myapps.microsoft.com` eller den anpassade länken för din organisation, till exempel `https://myapps.microsoft.com/contoso.com` . När du aktiverar den nya Mina appar-upplevelsen visas banderollen An **updated My Applications (An updated My Applications experience is available)** längst upp på Mina appar-sidan och användarna kan välja Testa för att visa den nya upplevelsen.  Om du vill sluta använda den nya upplevelsen kan användarna **välja Ja** från **banderollen Lämna ny** upplevelse överst på sidan.

## <a name="create-a-collection"></a>Skapa en samling

Om du vill skapa en samling måste du ha en Azure AD Premium P1- eller P2-licens.

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som administratör med en P1 Azure AD Premium- eller P2-licens.

2. Gå till **Azure Active Directory**  >  **Företagsprogram**.

3. Under **Hantera** väljer du **Samlingar.**

4. Välj **Ny samling.** På sidan **Ny samling** anger du ett **Namn för** samlingen (vi rekommenderar att du inte använder "samling" i namnet. Ange sedan en **Beskrivning.**

   ![Sidan Ny samling](media/acces-panel-collections/new-collection.png)

5. Välj **fliken** Program. Välj **+ Lägg till** program  och välj sedan alla program som du vill lägga till i samlingen på sidan Lägg till program eller använd **sökrutan** för att hitta program.

   ![Lägga till ett program i samlingen](media/acces-panel-collections/add-applications.png)

6. När du är klar med att lägga till program väljer du **Lägg till**. Listan över valda program visas. Du kan använda uppåtpilarna för att ändra ordningen på programmen i listan. Om du vill flytta ned ett program eller ta bort det från samlingen väljer du **menyn Mer** (**...**).

7. Välj **fliken** Ägare. Välj **+ Lägg till användare** och  grupper och välj sedan de användare eller grupper som du vill tilldela ägarskap till på sidan Lägg till användare och grupper. När du är klar med att välja användare och grupper väljer du **Välj**.

9. Välj **fliken Användare och** grupper. Välj **+ Lägg till användare** och  grupper och välj sedan de användare eller grupper som du vill tilldela samlingen på sidan Lägg till användare och grupper. Eller använd **sökrutan** för att hitta användare eller grupper. När du är klar med att välja användare och grupper väljer du **Välj**.

   ![Lägga till användare och grupper](media/acces-panel-collections/add-users-and-groups.png)

11. Välj **Granska + skapa**. Egenskaperna för den nya samlingen visas.


## <a name="view-audit-logs"></a>Visa granskningsloggar

Granskningsloggarna registrerar Mina appar åtgärder för samlingar, inklusive slutanvändaråtgärder för att skapa samlingar. Följande händelser genereras från Mina appar:

* Skapa samling
* Redigera samling
* Ta bort samling
* Starta ett program (slutanvändare)
* Tillägg av självbetjäningsprogram (slutanvändare)
* Borttagning av självbetjäningsprogram (slutanvändare)

Du kan komma åt granskningsloggar [i Azure Portal](https://portal.azure.com) genom **att Azure Active Directory**  >  **granskningsloggar**  >  **för företagsprogram** i avsnittet Aktivitet. För **Tjänst** väljer du **Mina appar**.

## <a name="get-support-for-my-account-pages"></a>Få support för sidorna Mitt konto

På Mina appar kan en användare välja Mitt konto **Visa mitt konto** för  >  **att** öppna sina kontoinställningar. På sidan Mitt **konto i** Azure AD kan användarna hantera sin säkerhetsinformation, sina enheter, sina lösenord med mera. De kan också komma åt sina Office-kontoinställningar.

Om du behöver skicka en supportbegäran om ett problem med Azure AD-kontosidan eller Office-kontosidan följer du dessa steg så att din begäran dirigeras korrekt: 

* För problem med **sidan Mitt konto i Azure AD** öppnar du en supportbegäran från Azure Portal. Gå till **Azure Portal**  >  **Azure Active Directory**  >  **Ny supportbegäran**.

* För problem med **sidan Office "Mitt konto"** öppnar du en supportbegäran från Administrationscenter för Microsoft 365. Gå till **Administrationscenter för Microsoft 365**  >  **support**. 

## <a name="next-steps"></a>Nästa steg
[Slutanvändarupplevelser för program i Azure Active Directory](end-user-experiences.md)