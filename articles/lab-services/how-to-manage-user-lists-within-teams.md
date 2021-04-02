---
title: Hantera Azure Lab Services användar listor från team
description: Lär dig hur du hanterar Azure Lab Services användar listor från team.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946716"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Hantera användar listor i labb tjänster från team

När ett labb skapas i team (se [Kom igång och skapa ett labb tjänst labb från Team](how-to-get-started-create-lab-within-teams.md)) fylls användar listan i labbet automatiskt och synkroniseras med team medlemskapet. Alla i teamet, inklusive ägare, medlemmar och gäster, läggs automatiskt till i labb användar listan. Azure Lab Services upprätthåller en synkronisering med grupp medlemskapet och en automatisk synkronisering utlöses var 24: e timme. 

## <a name="sync-users"></a>Synkronisera användare

Lärare kan använda knappen **Synkronisera** för att utlösa en manuell synkronisering när grupp medlemskapet har uppdaterats. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="Synkronisera användare":::

När den automatiska eller manuella synkroniseringen är klar är följande sant, beroende på om labbet har publicerats eller inte.

* Om labbet inte har publicerats minst en gång:
    * Användare läggs till eller tas bort från labb användar listan enligt ändringar i team medlemskapet. 
* Om labbet har publicerats minst en gång, kommer labb kapaciteten att uppdateras automatiskt om du lägger till eller tar bort användare.
    * Om det finns nya tillägg till teamet skapas nya virtuella datorer.
    * Om någon användare har tagits bort från teamet tas även den tillhör ande virtuella datorn bort.

## <a name="next-steps"></a>Nästa steg

När mallen VM har kon figurer ATS och när lärare väljer att publicera mallen, skapas antalet virtuella datorer som motsvarar antalet användare i Labbets användar lista. När labbet har publicerats och de virtuella datorerna har skapats registreras användarna automatiskt i labbet och de virtuella datorerna tilldelas dem vid första inloggningen till Azure Lab Services det vill, när de först ansluter till fliken med **Azure Lab Services** app. 

Om du vill publicera mallen VM går du till fönstret team labb tjänster, väljer fliken **mall** -> **...**  ->  **Publicera**.

Information om hur du hanterar VM-pooler finns i [hantera en VM-pool i labb tjänster från Team](how-to-manage-vm-pool-within-teams.md).

### <a name="also-review"></a>Granska även

Se följande artiklar:

- [Använda Azure Lab Services i team översikten](lab-services-within-teams-overview.md)
- [Kom igång och skapa ett labb tjänst labb från team](how-to-get-started-create-lab-within-teams.md)
- [Skapa scheman för labb tjänster från team](how-to-create-schedules-within-teams.md)
- [Åtkomst till en virtuell dator (elev visning) i labb tjänster från team](how-to-access-vm-for-students-within-teams.md)

