---
title: Skapa schema för labb i Azure Lab Services | Microsoft Docs
description: Lär dig hur du skapar scheman för labb i Azure Lab Services så att virtuella datorer i labbet startar och stängs ned vid en viss tidpunkt.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2a827c3d9f3022cb7d27ee43c9c95227c44f97e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434034"
---
# <a name="create-and-manage-schedules-for-labs-in-azure-lab-services"></a>Skapa och hantera scheman för labb i Azure Lab Services 
Med scheman kan du konfigurera ett klass rums labb så att virtuella datorer i labbet startar och stängs av automatiskt vid en viss tidpunkt. Du kan definiera ett eng ång slö schema eller ett återkommande schema. Följande procedurer ger dig anvisningar om hur du skapar och hanterar scheman för ett klass rums labb: 

> [!IMPORTANT]
> Den schemalagda körningen av virtuella datorer räknas inte mot den [kvot som tilldelats till en användare](how-to-configure-student-usage.md#set-quotas-for-users). Kvoten är för tiden utanför de schema timmar som en student tillbringar på virtuella datorer. 

## <a name="set-a-schedule-for-the-lab"></a>Ange ett schema för labbet
Skapa en schemalagd händelse för labbet så att virtuella datorer i labbet startas/stoppas automatiskt vid vissa tidpunkter. Användar kvoten du angav tidigare är den ytterligare tid som tilldelats varje användare utanför den schemalagda tiden. 

> [!NOTE]
> Innan vi börjar ser du hur scheman påverkar virtuella labb datorer: 
>- Den virtuella datorns mall ingår inte i scheman. 
>- Endast tilldelade virtuella datorer har startats. Det innebär att om en dator inte begärs av en slutanvändare (student) kommer datorn inte att starta under de schemalagda timmarna. 
>- Alla virtuella datorer (om de begärs av en användare eller inte) stoppas baserat på labb schema. 

1. Växla till sidan **scheman** och välj **Lägg till schemalagd händelse** i verktygsfältet. 

    ![Skärm bild som visar sidan för Azure Lab Services "schema" med knappen Lägg till schema markerat.](./media/how-to-create-schedules/add-schedule-button.png)
2. Bekräfta att **standard** är valt **händelse typ**. Du väljer **Start bara** för att ange start tiden för de virtuella datorerna. Du väljer **stoppa endast** om du bara vill ange stopp tiden för de virtuella datorerna. 
7. I avsnittet **Upprepa** väljer du det aktuella schemat. 

    ![Knappen Lägg till schema på sidan scheman](./media/how-to-create-schedules/select-current-schedule.png)
5. Utför följande steg i dialog rutan **Upprepa** :
    1. Bekräfta att **varje vecka** har angetts för fältet **Upprepa** . 
    3. Ange **start datum**.
    4. Ange **Start tiden** då du vill att de virtuella datorerna ska startas.
    5. Ange **stopp tiden** som de virtuella datorerna ska stängas av. 
    6. Ange **tids zonen** för start-och stopp tider som du har angett. 
    2. Välj de dagar som du vill att schemat ska börja gälla. I följande exempel är Monday-Thursday markerat. 
    8. Välj **Spara**. 

        ![Ange upprepnings schema](./media/how-to-create-schedules/set-repeat-schedule.png)

3. På sidan **Lägg till schemalagd händelse** för **anteckningar (valfritt)** anger du eventuellt en beskrivning eller information om schemat. 
4. På sidan **Lägg till schemalagd händelse** väljer du **Spara**. 

    ![Vecko schema](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Visa scheman i kalendern
Du kan se schemalagda datum och tider som marker ATS i vyn Kalender enligt följande bild:

![Scheman i vyn Kalender](./media/how-to-create-schedules/schedules-calendar.png)

Välj knappen **idag** i det övre högra hörnet för att växla till det aktuella datumet i kalendern. Välj **vänsterpilen** för att växla till föregående **vecka och HÖGERPIL för att växla** till nästa vecka i kalendern. 

## <a name="edit-a-schedule"></a>Redigera ett schema
När du väljer ett markerat schema i kalendern visas knappar för att **Redigera** eller **ta bort** schemat. 

![Sidan Redigera schema](./media/how-to-create-schedules/schedule-edit-button.png)

På sidan **Redigera schemalagd händelse** kan du uppdatera schemat och välja **Spara**. 

## <a name="delete-a-schedule"></a>Ta bort ett schema

1. Om du vill ta bort ett schema väljer du ett markerat schema i kalendern och väljer pappers korgs ikonen (ta bort):

    ![Knappen Ta bort i verktygsfältet](./media/how-to-create-schedules/schedule-delete-button.png)
2. I dialog rutan **ta bort schemalagd händelse** väljer du **Ja** för att bekräfta borttagningen. 



## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labbkonton som administratör](how-to-manage-lab-accounts.md)
- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labb användare, åtkomst labb](how-to-use-classroom-lab.md)
