---
title: Ta bort ett Azure Lab Services labb från team
description: Lär dig hur du tar bort ett Azure Lab Services labb från team.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 8d1e20f8f676eb9863187b550a3c0400871d670c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433962"
---
# <a name="delete-labs-within-teams"></a>Ta bort labb i team

Den här artikeln visar hur du tar bort ett labb från **Azure Lab Services** -appen.

## <a name="prerequisites"></a>Förutsättningar

* [Skapa ett labb tjänst konto](tutorial-setup-lab-account.md#create-a-lab-account) i Azure Portal.
* [Kom igång och skapa ett labb tjänst labb i team](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>Ta bort labb

Ett labb som skapats i team kan tas bort på [webbplatsen labb tjänster](https://labs.azure.com) genom att ta bort labbet direkt, enligt beskrivningen i [hantera labb i Azure Lab Services](how-to-manage-classroom-labs.md). 

Labb borttagning utlöses också när teamet tas bort. Om teamet som labbet skapas i tas bort, skulle labbet tas bort automatiskt 24 timmar efter att synkroniseringen av den automatiska användar listan har utlösts. 

> [!IMPORTANT]
> Borttagning av fliken eller avinstallation av appen innebär inte att labbet tas bort. 

Om fliken tas bort kommer användare i listan över team medlemskap fortfarande att kunna komma åt de virtuella datorerna på [webbplatsen labb tjänster](https://labs.azure.com) , såvida inte borttagningen av labbet uttryckligen utlöses genom att du tar bort labbet på webbplatsen eller tar bort teamet. 

## <a name="next-steps"></a>Nästa steg

- [Använda Azure Lab Services i team översikten](lab-services-within-teams-overview.md)
- [Hantera användar listor för labb i team](how-to-manage-user-lists-within-teams.md)
- [Hantera Labbets VM-pool i Teams](how-to-manage-vm-pool-within-teams.md)
- [Skapa och hantera labb scheman i team](how-to-create-schedules-within-teams.md)
- [Åtkomst till en virtuell dator i Teams – elev visning](how-to-access-vm-for-students-within-teams.md)

