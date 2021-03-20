---
title: Använda labb för utbildning – Azure Lab Services
description: I den här artikeln beskrivs hur du använder Azure DevTest Labs för att skapa labb i Azure för inlärnings scenarier.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f9eb3d44d470a978e676f6268cd693b283dd2703
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96433565"
---
# <a name="use-labs-for-trainings"></a>Använda labb för utbildningar
Azure Labs Services gör det möjligt för lärare (lärare, lärare, lärare eller undervisnings assistenter osv.) att snabbt och enkelt skapa ett online-labb för att etablera förkonfigurerade inlärnings miljöer för eleverna. Varje ombordpersonal skulle kunna använda identiska och isolerade miljöer för utbildningen. Principer kan användas för att säkerställa att inlärnings miljöerna endast är tillgängliga för varje ombordpersonal när de behöver dem och innehåller tillräckligt med resurser, till exempel virtuella datorer – krävs för utbildningen. 

![Klass rums labb](./media/classroom-labs-scenarios/classroom.png)

labb uppfyller följande krav som krävs för att utföra utbildning i alla virtuella miljöer: 

- Eleverna kan snabbt etablera sina utbildnings miljöer
- Varje utbildnings dator bör vara identisk
- Eleverna kan inte se virtuella datorer som skapats av andra eleverna
- Kontrol lera kostnaden genom att se till att eleverna inte kan hämta fler virtuella datorer än de behöver för utbildningen och Stäng virtuella datorer när de inte använder dem
- Dela utbildnings labbet enkelt med varje fordonsbaserad
- Återanvänd utbildnings labbet igen och återigen

I den här artikeln får du lära dig mer om olika Azure Lab Services funktioner som kan användas för att uppfylla de tidigare beskrivna utbildnings kraven och detaljerade steg som du kan följa för att ställa in ett labb för utbildning.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Skapa labb kontot som en labb konto administratör
Det första steget i att använda Azure Lab Services är att skapa ett labb konto i Azure Portal. När en labb konto administratör skapar labb kontot, lägger administratören till användare som vill skapa labb till rollen **labb skapare** . Läraren skapar labb med virtuella datorer för studenter för att göra övningar för den kurs som de lär sig. Mer information finns i [skapa och hantera labb konto](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-labs"></a>Skapa och hantera labb
En lärare, som är medlem i rollen labb skapare i ett labb konto, kan skapa ett eller flera labb i labb kontot. Du skapar och konfigurerar en mall för virtuella datorer med all nödvändig program vara för att utföra övningar i kursen. Du väljer en färdig avbildning från de tillgängliga avbildningarna för att skapa ett klass rums labb och anpassar det genom att installera den program vara som krävs för labbet. Mer information finns i [skapa och hantera labb](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Konfigurera användnings inställningar och principer
Labb skaparen kan lägga till eller ta bort användare i labbet, Hämta registrerings länk för att skicka till labb användare, konfigurera principer, till exempel ställa in enskilda kvoter per användare, uppdatera antalet tillgängliga virtuella datorer i labbet och mycket mer. Mer information finns i [Konfigurera användnings inställningar och principer](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Skapa och hantera scheman
Med scheman kan du konfigurera ett klass rums labb så att virtuella datorer i labbet startar och stängs av automatiskt vid en viss tidpunkt. Du kan definiera ett eng ång slö schema eller ett återkommande schema. Mer information finns i [skapa och hantera scheman för labb](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Konfigurera och publicera en mall för virtuella datorer
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för den virtuella datorn så att den konfigureras med exakt det du vill ge till utbildnings deltagarna. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Sedan publicerar du mallen för att skapa instanser av den virtuella mallen som är tillgänglig för dina labb användare. När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen. Mer information finns i [Konfigurera och publicera mall för virtuella datorer](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Använda virtuella datorer i klass rums labbet
En student-eller tränings deltagare registrerar sig för labbet och ansluter till den virtuella datorn för att göra övningar för kursen. Mer information finns i [så här får du åtkomst till ett klass rums labb](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Nästa steg
Börja med att skapa ett labb konto i labbet genom att följa anvisningarna i artikeln: [Självstudier: Konfigurera ett labb konto med Azure Lab Services](tutorial-setup-lab-account.md).