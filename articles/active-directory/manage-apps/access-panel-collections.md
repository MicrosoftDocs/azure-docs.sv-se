---
title: Skapa samlingar för Mina appar-portaler i Azure Active Directory | Microsoft Docs
description: Använd samlingarna Mina appar för att anpassa mina apps-sidor för att få en enklare upplevelse av mina appar för dina slutanvändare. Organisera program i grupper med separata flikar.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7743a1af54b01f848c4ac6f0cb1d4526d66f8132
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254990"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Skapa samlingar i portalen Mina appar

Användarna kan använda portalen Mina appar för att visa och starta de molnbaserade program som de har åtkomst till. Som standard visas alla program som en användare har åtkomst till tillsammans på en enda sida. För att bättre organisera den här sidan för dina användare, om du har en Azure AD Premium P1-eller P2-licens kan du konfigurera samlingar. Med en samling kan du gruppera program som är relaterade (till exempel efter jobb roll, uppgift eller projekt) och visa dem på en separat flik. En samling tillämpar i princip ett filter på de program som en användare redan har åtkomst till, så att användaren bara ser de programmen i samlingen som har tilldelats dem.

> [!NOTE]
> Den här artikeln beskriver hur en administratör kan aktivera och skapa samlingar. Information för slutanvändaren om hur du använder portalen för Mina appar och samlingar finns i [åtkomst-och användnings samlingar](../user-help/my-applications-portal-workspaces.md).

## <a name="enable-the-latest-my-apps-features"></a>Aktivera de senaste Mina Apps-funktionerna

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som användar administratör eller global administratör.

2. Gå till **Azure Active Directory**  >  **användar inställningar**.

3. Under förhands **granskningar av användar funktioner** väljer du **Hantera för hands versions inställningar för användar funktionen**.

4. Under **användare kan använda för hands versions funktioner för Mina appar** väljer du något av följande alternativ:
   * **Markerat** – aktiverar funktioner för en viss grupp. Använd alternativet **Välj en grupp** för att välja den grupp som du vill aktivera funktionerna för.  
   * **Alla** – aktiverar funktionerna för alla användare.

> [!NOTE]
> För att öppna portalen Mina appar kan användare använda länken `https://myapps.microsoft.com` eller den anpassade länken för din organisation, till exempel `https://myapps.microsoft.com/contoso.com` . När du har aktiverat den nya appen för Mina appar visas **en uppdaterad** banderoll längst upp på sidan Mina appar och användarna kan välja **testa** den för att visa den nya upplevelsen. För att sluta använda den nya upplevelsen kan användarna välja **Ja** i den **nya upplevelsens** banderoll längst upp på sidan.

## <a name="create-a-collection"></a>Skapa en samling

Om du vill skapa en samling måste du ha en Azure AD Premium P1-eller P2-licens.

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som administratör med en Azure AD Premium P1-eller P2-licens.

2. Gå till **Azure Active Directory**  >  **företags program**.

3. Under **Hantera** väljer du **samlingar**.

4. Välj **ny samling**. På sidan **ny samling** anger du ett **namn** för samlingen (vi rekommenderar att du inte använder "samling" i namnet. Ange sedan en **Beskrivning**.

   ![Sidan ny samling](media/acces-panel-collections/new-collection.png)

5. Välj fliken **program** . Välj **+ Lägg till program** och välj sedan alla program som du vill lägga till i samlingen på sidan **Lägg till program** , eller Använd **sökrutan för** att hitta program.

   ![Lägg till ett program i samlingen](media/acces-panel-collections/add-applications.png)

6. När du är klar med att lägga till program väljer du **Lägg till**. Listan med valda program visas. Du kan ändra ordningen på programmen i listan med hjälp av pilarna. Om du vill flytta ett program nedåt eller ta bort det från samlingen väljer du menyn **mer** (**...**).

7. Välj fliken **ägare** . Välj **+ Lägg till användare och grupper**. på sidan **Lägg till användare och grupper** väljer du sedan de användare eller grupper som du vill tilldela ägarskap till. När du är klar med att välja användare och grupper väljer du **Välj**.

9. Välj fliken **användare och grupper** . Välj **+ Lägg till användare och grupper**. på sidan **Lägg till användare och grupper** väljer du sedan de användare eller grupper som du vill tilldela samlingen till. Eller Använd **sökrutan för** att hitta användare eller grupper. När du är klar med att välja användare och grupper väljer du **Välj**.

   ![Lägg till användare och grupper](media/acces-panel-collections/add-users-and-groups.png)

11. Välj **Granska + skapa**. Egenskaperna för den nya samlingen visas.


## <a name="view-audit-logs"></a>Visa granskningsloggar

I gransknings loggarna registreras mina program samlingar, inklusive åtgärder för slutanvändare av samling. Följande händelser genereras från Mina appar:

* Skapa samling
* Redigera samling
* Ta bort samling
* Starta ett program (slutanvändare)
* Lägg till självbetjänings program (slutanvändare)
* Borttagning av självbetjänings program (slutanvändare)

Du kan komma åt gransknings loggar i [Azure Portal](https://portal.azure.com) genom att välja **Azure Active Directory**  >    >  **gransknings loggar** för företags program i avsnittet aktivitet. För **tjänst** väljer du **Mina appar**.

## <a name="get-support-for-my-account-pages"></a>Få support för mina konto sidor

På sidan Mina appar kan en användare välja mitt konto   >  **Visa mitt konto** för att öppna sina konto inställningar. På sidan Azure AD **mitt konto** kan användare hantera säkerhets information, enheter, lösen ord och mycket annat. De kan också komma åt sina konto inställningar för Office.

Om du behöver skicka en support förfrågan om ett problem med sidan Azure AD-konto eller sidan Office-konto följer du dessa steg så att din begäran dirigeras korrekt: 

* För problem med sidan **"mitt konto" i Azure AD** öppnar du en supportbegäran i Azure Portal. Gå till **Azure Portal**  >  **Azure Active Directory**  >  **nya support förfrågan**.

* Om du har problem med sidan **"mitt konto" på kontoret** öppnar du en supportbegäran i Microsoft 365 administrations centret. Gå till **Microsoft 365 Admin Center**-  >  **stöd**. 

## <a name="next-steps"></a>Nästa steg
[Slut användar upplevelse för program i Azure Active Directory](end-user-experiences.md)