---
title: Azure Stack Edge Pro hantera bandbredds scheman | Microsoft Docs
description: Beskriver hur du använder Azure Portal för att hantera bandbredds scheman på din Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: e73a02c93807072e30c8ce2a1a7feb30e9d3c8c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91978976"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-pro"></a>Använd Azure Portal för att hantera bandbredds scheman på din Azure Stack Edge Pro  

Den här artikeln beskriver hur du hanterar användare i Azure Stack Edge Pro. Med bandbreddsscheman kan du konfigurera användningen av nätverksbandbredd mellan scheman under olika tider på dagen. Schemana kan användas för upp- och nedladdningar mellan din enhet och molnet.

Du kan lägga till, ändra eller ta bort bandbredds scheman för Azure Stack Edge Pro via Azure Portal.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Lägga till ett schema
> * Ändra schema
> * Ta bort ett schema


## <a name="add-a-schedule"></a>Lägga till ett schema

Lägg till ett schema genom att utföra följande steg i Azure Portal.

1. I Azure Portal för din Azure Stack Edge-resurs går du till **bandbredd**.
2. I den högra rutan väljer du **+ Lägg till schema**.

    ![Välj bandbredd](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. I **Lägg till schema**: 

   1. Ange **Start dag**, **slut dag**, **Start tid** och **slut tid** för schemat.
   2. Markera alternativet **alla dagar** om schemat ska köras hela dagen.
   3. **Bandbredds hastighet** är den bandbredd i megabit per sekund (Mbit/s) som används av enheten i åtgärder som involverar molnet (både uppladdning och nedladdning). Ange ett tal mellan 20 och 1 000 000 007 för det här fältet.
   4. Markera **Obegränsad** bandbredd om du inte vill begränsa upp- och nedladdningen.
   5. Välj **Lägg till**.

      ![Lägg till schema](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Ett schema skapas med de angivna parametrarna. Schemat visas sedan i listan över bandbreddsscheman i portalen.

    ![Uppdaterad lista över bandbredds scheman](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Redigera schema

Gör följande om du vill redigera ett bandbreddsschema.

1. Gå till Azure Stack Edge-resursen i Azure Portal och gå sedan till **bandbredd**. 
2. Välj i listan över bandbredds scheman och välj ett schema som du vill ändra.
    ![Välj schema för bandbredd](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Gör önskade ändringar och spara ändringarna.

    ![Ändra användare](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. När schemat har ändrats uppdateras listan över scheman så att den återspeglar det ändrade schemat.

    ![Ändra användare 2](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Ta bort ett schema

Utför följande steg för att ta bort ett schema för bandbredd som är kopplat till din Azure Stack Edge Pro-enhet.

1. Gå till Azure Stack Edge-resursen i Azure Portal och gå sedan till **bandbredd**.  

2. I listan över bandbreddsscheman väljer du det schema som du vill ta bort. I **Redigera schema** väljer du **ta bort**. Välj **Ja** när du uppmanas att bekräfta.

   ![Ta bort en användare](media/azure-stack-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. När schemat har tagits bort uppdateras listan över scheman.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar resurser](azure-stack-edge-manage-shares.md).
