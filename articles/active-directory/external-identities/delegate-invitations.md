---
title: Aktivera inställningar för externt samarbete i B2B – Azure AD
description: Lär dig hur du aktiverar Active Directory B2B externt samarbete och hanterar vem som kan bjuda in gäst användare. Använd rollen gäst deltagare för att delegera inbjudningar.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 747fa3005930414832878757664f4787157302d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645831"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>Aktivera externt B2B-samarbete och hantera vilka som kan bjuda in gäster

I den här artikeln beskrivs hur du aktiverar Azure Active Directory (Azure AD) B2B-samarbete, anger vem som kan bjuda in gäster och avgör vilka behörigheter som gäst användare har i din Azure AD. 

Som standard kan alla användare och gäster i din katalog bjuda in gäster även om de inte har tilldelats någon administratörs roll. Med inställningar för externa samarbete kan du aktivera eller inaktivera gäst inbjudningar för olika typer av användare i din organisation. Du kan också delegera inbjudningar till enskilda användare genom att tilldela roller som gör det möjligt för dem att bjuda in gäster.

Med Azure AD kan du begränsa vad externa gäst användare kan se i Azure AD-katalogen. Som standard är gäst användare inställda på en begränsad behörighets nivå som blockerar dem från att räkna upp användare, grupper eller andra katalog resurser, men de kan se medlemskap i grupper som inte är dolda. Med en ny förhands gransknings inställning kan du begränsa gäst åtkomst ytterligare, så att gästerna bara kan se sin egen profil information. Mer information finns i [begränsa åtkomst behörighet för gäst (för hands version)](../enterprise-users/users-restrict-guest-permissions.md).

## <a name="configure-b2b-external-collaboration-settings"></a>Konfigurera inställningar för externt samarbete i B2B

Med Azure AD B2B-samarbete kan en klient organisations administratör ange följande Inbjudnings principer:

- Inaktivera inbjudningar
- Endast administratörer och användare i rollen gäst deltagare kan bjuda in
- Administratörer, rollen gäst deltagare och medlemmar kan bjuda in
- Alla användare, inklusive gäster, kan bjuda in

Som standard kan alla användare, inklusive gäster, bjuda in gäst användare.

### <a name="to-configure-external-collaboration-settings"></a>Konfigurera inställningar för externt samarbete:

1. Logga in på [Azure Portal](https://portal.azure.com) som klient organisations administratör.
2. Välj **Azure Active Directory**.
3. Välj **externa identiteter**  >  **externa samarbets inställningar**.

4. Under **åtkomst begränsningar för gäst användare (för hands version)** väljer du den åtkomst nivå som du vill att gäst användare ska ha:
  
   - **Gäst användare har samma åtkomst som medlemmar (oftast)**: det här alternativet ger gästerna samma åtkomst till Azure AD-resurser och katalog data som medlems användare.

   - **Gäst användare har begränsad åtkomst till egenskaper och medlemskap i katalog objekt**: (standard) den här inställningen blockerar gäster från vissa katalog aktiviteter, t. ex. att räkna upp användare, grupper eller andra katalog resurser. Gäster kan se medlemskap i alla grupper som inte är dolda.

   - **Åtkomst till gäst användare är begränsad till egenskaper och medlemskap i sina egna katalog objekt (mest restriktiva)**: med den här inställningen kan gästerna bara komma åt sina egna profiler. Gäster får inte Visa andra användares profiler, grupper eller grupp medlemskap.


5. Under **Inställningar för gäst inbjudan** väljer du lämpliga inställningar:

    ![Inställningar för gäst inbjudning](./media/delegate-invitations/guest-invite-settings.png)

   - **Administratörer och användare i rollen gäst deltagare kan bjuda in**: om du vill tillåta administratörer och användare i rollen "gäst inbjudare" att bjuda in gäster, ställer du in den här principen på **Ja**.

   - **Medlemmar kan bjuda in**: om du vill tillåta icke-administratörers medlemmar i katalogen att bjuda in gäster, ställer du in den här principen på **Ja**.

   - **Gäster kan bjuda in**: om du vill tillåta gäster att bjuda in andra gäster ställer du in den här principen på **Ja**.

   > [!NOTE]
   > Om **medlemmar kan bjuda** in till **Nej** och **Administratörer och användare i rollen gäst deltagare kan bjuda** in till **Ja**, användare i **gäst Inbjudnings** rollen kommer fortfarande att kunna bjuda in gäster.

6. Under **email eng ång slö sen ord för gäster** väljer du lämpliga inställningar (mer information finns i [e-postautentisering med eng ång slö sen ord](one-time-passcode.md)):

   - **Aktivera automatiskt e-postlösenord för gäster i oktober 2021**. Objekt Om e-postfunktionen för eng ång slö sen ord inte redan är aktive rad för din klient, aktive ras den automatiskt i oktober 2021. Ingen ytterligare åtgärd krävs om du vill att funktionen ska vara aktive rad vid den tiden. Om du redan har aktiverat eller inaktiverat funktionen kommer det här alternativet att vara otillgängligt.

   - **Aktivera e-post med eng ång slö sen ord för gäster gällande nu**. Aktiverar funktionen email eng ång slö sen ord för din klient.

   - **Inaktivera e-post med eng ång slö sen ord för gäster**. Inaktiverar funktionen för eng ång slö sen ord för din klient och förhindrar att funktionen aktive ras i oktober 2021.

   > [!NOTE]
   > I stället för alternativen ovan ser du följande växling om du har aktiverat eller inaktiverat den här funktionen eller om du tidigare har valt att förhandsgranska:
   >
   >![Aktivera e-post för eng ång slö sen ord](media/delegate-invitations/enable-email-otp-opted-in.png)

7. Under **Aktivera gäst självbetjänings registrering via användar flöden** väljer du **Ja** om du vill kunna skapa användar flöden som gör det möjligt för användarna att registrera sig för appar. Mer information om den här inställningen finns i [lägga till ett självbetjänings registrerings användar flöde i en app](self-service-sign-up-user-flow.md).

    ![Inställning för självbetjänings registrering via användar flöden](./media/delegate-invitations/self-service-sign-up-setting.png)

7. Under **samarbets begränsningar** kan du välja om du vill tillåta eller neka inbjudningar till de domäner du anger och ange vissa domän namn i text rutorna. För flera domäner anger du varje domän på en ny rad. Mer information finns i [tillåta eller blockera inbjudningar till B2B-användare från vissa organisationer](allow-deny-list.md).

    ![Inställningar för samarbets begränsningar](./media/delegate-invitations/collaboration-restrictions.png)
## <a name="assign-the-guest-inviter-role-to-a-user"></a>Tilldela rollen för rollen gäst deltagare till en användare

Med rollen gäst deltagare kan du ge enskilda användare möjlighet att bjuda in gäster utan att tilldela dem en global administratör eller någon annan administratörs roll. Tilldela rollen för rollen gäst deltagare till enskilda användare. Kontrol lera sedan att du anger **Administratörer och användare i rollen gäst deltagare kan bjuda in** till **Ja**.

Här är ett exempel som visar hur du använder PowerShell för att lägga till en användare i rollen som gäst inbjudare:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägg till gäst användare för B2B-samarbete utan inbjudan](add-user-without-invite.md)
- [Lägga till en B2B-samarbets användare till en roll](add-guest-to-role.md)

