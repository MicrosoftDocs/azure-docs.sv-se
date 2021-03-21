---
title: Spåra användning av ett labb i Azure Lab Services | Microsoft Docs
description: I den här självstudien spårar du som labbskapare/ägare användningen av ditt labb.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 8a3ca9e011eb89b3db8b202bab11d14f10d74e7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434625"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Självstudie: Spåra användning av ett labb i Azure Lab Services
Den här kursen visar hur en labbskapare/ägare kan spåra användningen av ett labb.

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Visa användare som har registrerats med ditt labb
> * Visa användningen av virtuella datorer i labbet
> * Hantera virtuella studentdatorer 


## <a name="view-registered-users"></a>Visa registrerade användare

1. Gå till [webbplatsen för Azure Lab Services](https://labs.azure.com). 
2. Välj **Logga in** och ange dina autentiseringsuppgifter. Azure Lab Services har stöd för organisationskonton och Microsoft-konton.
3. På sidan **My labs** (Mina labb) väljer du det labb som du vill spåra användningen för. 
4. Välj **Användare** på den vänstra menyn eller i panelen **Användare**. Du kan se studenter som har registrerats med ditt labb.  

    ![Registrerade användare](./media/tutorial-track-usage/registered-users.png)

    Mer information om hur du lägger till och hanterar användare för labbet finns i [lägga till och hantera labb användare](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms"></a>Visa användningen av virtuella datorer

1. Välj **Virtuella datorer** på menyn till vänster. 
2. Kontrollera att du ser status för virtuella datorer och det antal timmar som de virtuella datorerna har körts. Den tid som en labbägare tillbringar på en virtuell studentdator räknas inte mot den användningstid som visas i den sista kolumnen. 

    ![VM-användning](./media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Hantera virtuella studentdatorer 
På den här sidan kan du starta, stoppa eller återställa elev-VM: ar med hjälp av kontrollerna i kolumnen **tillstånd** eller i verktygsfältet.

![VM-åtgärder](./media/tutorial-track-usage/vm-controls.png)

Mer information om hur du hanterar en pool för virtuella datorer för labbet finns i [Konfigurera och hantera en pool för virtuella datorer](how-to-set-virtual-machine-passwords.md).

> [!NOTE]
> När en lärare aktive ras på en elev-VM påverkas inte kvoten för studenten. Kvoten för en användare anger antalet Labb timmar som är tillgängliga för användaren utanför den schemalagda klass tiden. Mer information om kvoter finns i [Ange kvoter för användare](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="next-steps"></a>Nästa steg
Mer information om labb finns i artiklar under [instruktions guider](how-to-manage-lab-accounts.md).
