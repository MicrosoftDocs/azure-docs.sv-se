---
title: Lägg till B2B-samarbets användare i Azure Portal – Azure AD
description: Visar hur en administratör kan lägga till gäst användare i sin katalog från en partner organisation som använder Azure Active Directory (Azure AD) B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2566b427777aec273df17863f06040de20649d97
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581855"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal

Som en användare som har tilldelats någon av de begränsade administratörs katalog rollerna kan du använda Azure Portal för att bjuda in B2B-samarbets användare. Du kan bjuda in gäst användare till katalogen, till en grupp eller till ett program. När du har bjudit in en användare med någon av dessa metoder läggs den inbjudna användarens konto till i Azure Active Directory (Azure AD), med en användar typ av *gäst*. Gäst användaren måste sedan lösa in sin inbjudan för att få åtkomst till resurser. Ingen inbjudan av en användare upphör att gälla.

När du har lagt till en gäst användare i katalogen kan du antingen skicka gäst användaren en direkt länk till en delad app, eller gäst användaren kan klicka på inlösnings-URL: en i inbjudan via e-post. Mer information om inlösnings processen finns i [B2B-samverkan med inbjudan](redemption-experience.md).

> [!IMPORTANT]
> Följ stegen i [så här: Lägg till din organisations sekretess information i Azure Active Directory](../fundamentals/active-directory-properties-area.md) för att lägga till URL: en för din organisations sekretess policy. Som en del av den första gången inlösnings processen för inbjudan måste en inbjuden användare godkänna dina sekretess villkor för att kunna fortsätta. 

## <a name="before-you-begin"></a>Innan du börjar

Kontrol lera att organisationens inställningar för externt samarbete är konfigurerade så att du får bjuda in gäster. Som standard kan alla användare och administratörer bjuda in gäster. Men din organisations externa samarbets principer kan konfigureras för att förhindra vissa typer av användare eller administratörer från att bjuda in gäster. Information om hur du visar och ställer in dessa principer finns i [Aktivera externt samarbete i B2B och hantera vem som kan bjuda in gäster](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Lägg till gäst användare i katalogen

Följ dessa steg om du vill lägga till B2B-samarbets användare i katalogen:

1. Logga in på [Azure Portal](https://portal.azure.com) som en användare som har tilldelats en begränsad administratörs katalog roll eller rollen som gäst deltagare.
2. Sök efter och välj **Azure Active Directory** från vilken sida som helst.
3. Under **Hantera** väljer du **Användare**.
4. Välj **Ny gästanvändare**.

   ![Visar var nya gäst användare finns i användar gränssnittet](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
5. På sidan **ny användare** väljer du **Bjud in användare** och lägger sedan till gäst användarens information. 

    > [!NOTE]
    > Gruppens e-postadresser stöds inte. Ange en persons e-postadress. Vissa e-postleverantörer låter också användare lägga till plus tecken (+) och ytterligare text till sina e-postadresser för att hjälpa till med saker som filtrering av Inkorgen. Azure AD stöder dock för närvarande inte plus tecken i e-postadresser. Undvik leverans problem genom att utelämna plus symbolen och eventuella tecken som följer det upp till @-symbolen.

   - **Namn.** Det första och sista namnet på gäst användaren.
   - **E-postadress (krävs)**. E-postadressen till gäst användaren.
   - **Personligt meddelande (valfritt)** Ta med ett personligt välkomst meddelande till gäst användaren.
   - **Grupper**: du kan lägga till gäst användaren i en eller flera befintliga grupper, eller så kan du göra det senare.
   - **Katalog roll**: om du kräver administratörs behörighet för Azure AD för användaren kan du lägga till dem i en Azure AD-roll. 

7. Välj **Bjud in** för att skicka inbjudan till gästanvändaren automatiskt. 
 
När du har skickat inbjudan läggs användarkontot automatiskt till i katalogen som gäst.


![Visar B2B-användare med typ av gäst användare](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Lägga till gästanvändare i en grupp
Följ dessa steg om du behöver lägga till B2B-samarbets användare manuellt i en grupp:

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Sök efter och välj **Azure Active Directory** från vilken sida som helst.
3. Under **Hantera** väljer du **grupper**.
4. Välj en grupp (eller klicka på **ny grupp** för att skapa en ny). Det är en bra idé att inkludera i grupp beskrivningen att gruppen innehåller B2B-gäst användare.
5. Välj **medlemmar**. 
6. Gör något av följande:
   - Om gäst användaren redan finns i katalogen söker du efter B2B-användaren. Välj användaren och klicka sedan på **Välj** för att lägga till användaren i gruppen.
   - Om gäst användaren inte redan finns i katalogen, Bjud in dem till gruppen genom att skriva deras e-postadress i sökrutan, skriva ett valfritt personligt meddelande och sedan klicka på **Välj**. Inbjudan skickas automatiskt till den inbjudna användaren.
     
     ![Knappen Lägg till inbjudan för att lägga till gäst medlemmar](./media/add-users-administrator/GroupInvite.png)
   
Du kan också använda dynamiska grupper med Azure AD B2B-samarbete. Mer information finns i [dynamiska grupper och Azure Active Directory B2B-samarbete](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Lägga till gästanvändare i ett program

Följ dessa steg om du vill lägga till B2B-samarbets användare till ett program:

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Sök efter och välj **Azure Active Directory** från vilken sida som helst.
3. Under **Hantera** väljer du **företags program**  >  **alla program**.
4. Välj det program som du vill lägga till gäst användare i.
5. På programmets instrument panel väljer du **Totalt antal användare** för att öppna fönstret **användare och grupper** .

    ![Knappen totalt antal användare för att lägga till öppna användare och grupper](./media/add-users-administrator/AppUsersAndGroups.png)

6. Välj **Lägg till användare**.
7. Under **Lägg till tilldelning** väljer du **användare och grupper**.
8. Gör något av följande:
   - Om gäst användaren redan finns i katalogen söker du efter B2B-användaren. Välj användaren, klicka på **Välj** och sedan på **tilldela** för att lägga till användaren i appen.
   - Om gäst användaren inte redan finns i katalogen, under **Välj medlem eller Bjud in en extern användare**, anger du användarens e-postadress. Skriv ett valfritt personligt meddelande i rutan meddelande. Klicka på **Bjud in** i rutan meddelande.
           
       ![Skärm bild som visar var du kan lägga till användarens e-postadress, det anpassade meddelandet och markerar knappen Bjud in.](./media/add-users-administrator/AppInviteUsers.png)
   
      Klicka på **Välj** och sedan på **tilldela** för att lägga till användaren i appen. En inbjudan skickas automatiskt till den inbjudna användaren.

9. Gäst användaren visas i listan över program **användare och grupper** med den tilldelade rollen som **standard åtkomst**. Om du vill ändra rollen gör du följande:
   - Välj gäst användaren och välj sedan **Redigera**. 
   - Under **Redigera tilldelning** klickar du på **Välj roll** och väljer den roll som du vill tilldela den valda användaren.
   - Klicka på **Välj**.
   - Klicka på **Tilldela**.
 
## <a name="resend-invitations-to-guest-users"></a>Skicka om inbjudningar till gästanvändare

Om en gäst användare ännu inte har löst in sin inbjudan kan du skicka e-postinbjudan igen.

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Sök efter och välj **Azure Active Directory** från vilken sida som helst.
3. Under **Hantera** väljer du **Användare**.
5. Välj användarkontot.
6. Under **Hantera** väljer du **profil**.
7. Om användaren ännu inte har accepterat inbjudan i avsnittet **identitet** , kommer inbjudan att **accepteras** att anges till **Nej**. Om du vill skicka inbjudan igen väljer du **(hantera)**. På sidan **Hantera inbjudningar** bredvid * * skickar du inbjudan igen. Välj **Ja** och välj **färdig**.

> [!NOTE]
> Om du skickar en inbjudan som ursprungligen dirigerade användaren till en speciell app förstår du att länken i den nya inbjudan tar användaren till den översta åtkomst panelen i stället.
> Dessutom kan endast användare med bjuda in behörigheter skicka inbjudningar igen.

## <a name="next-steps"></a>Nästa steg

- Information om hur icke-Azure AD-administratörer kan lägga till B2B-gäst användare finns i [Hur kan informations arbetare lägga till B2B-samarbets användare?](add-users-information-worker.md)
- Information om e-postinbjudan finns i [elementen i e-postinbjudan e-inbjudan](invitation-email-elements.md).
