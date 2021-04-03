---
title: Hantera en VM-pool i Azure Lab Services från team
description: Lär dig hur du hanterar en VM-pool i Azure Lab Services från team.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946857"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Hantera en VM-pool i labb tjänster från team

Skapandet av den virtuella datorn (VM) startar så snart mallen VM publiceras. Virtuella datorer som är lika med antalet användare i labb användar listan kommer att skapas. Virtuella datorer tilldelas automatiskt till studenter vid första inloggningen till Azure Lab Services. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>Publicera en mall och hantera en VM-pool

Om du vill publicera mallen går du till fönstret team labb tjänster, väljer fliken **mall** -> **...**  ->  **Publicera**.

När mallen VM har kon figurer ATS och när lärare väljer att publicera mallen, skapas antalet virtuella datorer som motsvarar antalet användare i Labbets användar lista. När labbet har publicerats och de virtuella datorerna har skapats registreras användarna automatiskt i labbet och de virtuella datorerna tilldelas dem vid första inloggningen till Azure Lab Services det vill, när de först ansluter till fliken med **Azure Lab Services** app. 

När en användar List-synkronisering utlöses, uppdateras labb kapaciteten (antal virtuella datorer i labbet) automatiskt utifrån ändringarna i team medlemskapet. Nya virtuella datorer skapas när nya användare läggs till och de virtuella datorer som är tilldelade till de användare som tas bort från teamet också tas bort. Mer information finns i [Hantera användare i team](how-to-manage-user-lists-within-teams.md). 

Lärare kan fortsätta att komma åt elevens virtuella datorer direkt från fliken VM-pool. Och lärare kan komma åt virtuella datorer som tilldelats antingen från fliken **virtuell dator pool** eller genom att klicka på knappen **Mina Virtual Machines** (övre/högra hörnet på skärmen). 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="VM-pool":::

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Använda Azure Lab Services i team översikten](lab-services-within-teams-overview.md)
- [Kom igång och skapa ett labb tjänst labb från team](how-to-get-started-create-lab-within-teams.md)
- [Hantera användar listor i labb tjänster från team](how-to-manage-user-lists-within-teams.md)
- [Skapa scheman för labb tjänster från team](how-to-create-schedules-within-teams.md)
- [Åtkomst till en virtuell dator (elev visning) i labb tjänster från team](how-to-access-vm-for-students-within-teams.md)


