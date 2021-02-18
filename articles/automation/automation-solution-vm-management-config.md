---
title: Konfigurera Azure Automation Starta/stoppa virtuella datorer när de inte används
description: Den här artikeln beskriver hur du konfigurerar Starta/stoppa virtuella datorer när de inte används-funktionen så att den stöder olika användnings fall eller scenarier.
services: automation
ms.subservice: process-automation
ms.date: 06/01/2020
ms.topic: conceptual
ms.openlocfilehash: b52b51133f059f028baf470515e886d17077af6a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593940"
---
# <a name="configure-startstop-vms-during-off-hours"></a>Konfigurera Starta/stoppa virtuella datorer när de inte används

Den här artikeln beskriver hur du konfigurerar [Starta/stoppa virtuella datorer när de inte används](automation-solution-vm-management.md) -funktionen så att den stöder de scenarier som beskrivs. Du kan också lära dig att:

* [Konfigurera e-postaviseringar](#configure-email-notifications)
* [Lägga till en virtuell dator](#add-a-vm)
* [Undanta en virtuell dator](#exclude-a-vm)
* [Ändra scheman för start och avstängning](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Scenario 1: Starta/stoppa virtuella datorer enligt ett schema

Det här scenariot är standard konfigurationen när du först distribuerar Starta/stoppa virtuella datorer när de inte används. Du kan till exempel konfigurera funktionen för att stoppa alla virtuella datorer i en prenumeration när du lämnar arbetet på kvällen och starta dem i morgon när du är tillbaka på kontoret. När du konfigurerar scheman för **schemalagda-StartVM** och **schemalagda StopVM** under distributionen, startar och slutar riktade virtuella datorer. 

Det finns stöd för att konfigurera funktionen till att bara stoppa virtuella datorer. Se [ändra scheman för start och avstängning](#modify-the-startup-and-shutdown-schedules) för att lära dig hur du konfigurerar ett anpassat schema.

> [!NOTE]
> Tids zonen som används av funktionen är din aktuella tidszon när du konfigurerar parametern för schema tid. Azure Automation lagras dock i UTC-format i Azure Automation. Du behöver inte göra någon tids zons konvertering eftersom detta hanteras under dator distributionen.

Konfigurera variablerna:, och för att kontrol lera de virtuella datorer som finns inom omfånget `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` .

Du kan aktivera antingen åtgärden för en prenumeration och en resurs grupp, eller rikta in en speciell lista över virtuella datorer, men inte båda.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta in dig på Start-och stopp åtgärder mot en prenumeration och resurs grupp

1. Konfigurera `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` variablerna och för att ange de virtuella mål datorerna.

2. Aktivera och uppdatera **schemalagda StartVM** -och **schemalagda StopVM-** scheman.

3. Kör **ScheduledStartStop_Parent** Runbook med **Åtgärds** parameter fältet inställt på **Start** och fältet **WHATIF** parameter är inställt på sant för att förhandsgranska ändringarna.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Ange start-och stopp åtgärd per VM-lista

1. Kör **ScheduledStartStop_Parent** Runbook med **åtgärd** inställd på **Starta**.

2. Lägg till en kommaavgränsad lista över virtuella datorer (utan blank steg) i parameter fältet **VMList** . En exempel lista är `vm1,vm2,vm3` .

3. Ställ in värdet för fältet **WHATIF** -parameter till sant för att förhandsgranska ändringarna.

4. Konfigurera `External_ExcludeVMNames` variabeln med en kommaavgränsad lista över virtuella datorer (VM1, VM2, VM3), utan blank steg mellan kommaavgränsade värden.

5. Det här scenariot följer inte `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` variablerna och. I det här scenariot måste du skapa ett eget Automation-schema. Mer information finns i [Schemalägga en Runbook i Azure Automation](shared-resources/schedules.md).

    > [!NOTE]
    > Värdet för **mål ResourceGroup-namn** lagras som värden för både `External_Start_ResourceGroupNames` och `External_Stop_ResourceGroupNames` . För ytterligare granularitet kan du ändra var och en av dessa variabler för att rikta in sig på olika resurs grupper. Använd `External_Start_ResourceGroupNames` och Använd för åtgärden stoppa för att starta `External_Stop_ResourceGroupNames` . Virtuella datorer läggs automatiskt till i Start-och stopp scheman.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Scenario 2: Starta/stoppa virtuella datorer i följd med hjälp av Taggar

I en miljö som innehåller två eller flera komponenter på flera virtuella datorer som har stöd för en distribuerad arbets belastning, är det viktigt med stöd för den sekvens i vilken komponenter startas och stoppas i ordning. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta in dig på Start-och stopp åtgärder mot en prenumeration och resurs grupp

1. Lägg till en `sequencestart` och en- `sequencestop` tagg med positiva heltals värden för virtuella datorer som är riktade till- `External_Start_ResourceGroupNames` och `External_Stop_ResourceGroupNames` variabler. Start-och stopp åtgärderna utförs i stigande ordning. Information om hur du taggar en virtuell dator finns i [tagga en virtuell Windows-dator i Azure](../virtual-machines/tag-portal.md) och [tagga en virtuell Linux-dator i Azure](../virtual-machines/tag-cli.md).

2. Ändra schemana **Sequenced-StartVM** och **Sequenced-StopVM** till det datum och den tid som uppfyller dina krav och aktivera schemat.

3. Kör den **SequencedStartStop_Parent** Runbook med **åtgärd** inställd på **Starta** och **WHATIF** inställd på sant för att förhandsgranska ändringarna.

4. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar de virtuella datorerna i produktionen. När du är klar kan du köra runbooken manuellt med parametern inställt på **falskt**, eller låta automatiserings scheman för **sekvenserade-StartVM** och **sekvenserat-StopVM** köras automatiskt efter ditt schema.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>Anpassa listan med start-och stopp åtgärder efter VM

1. Lägg till en `sequencestart` och en- `sequencestop` tagg med positiva heltals värden till de virtuella datorer som du planerar att lägga till i `VMList` parametern.

2. Kör **SequencedStartStop_Parent** Runbook med **åtgärd** inställd på **Starta**.

3. Lägg till en kommaavgränsad lista över virtuella datorer (utan blank steg) i parameter fältet **VMList** . En exempel lista är `vm1,vm2,vm3` .

4. Ange **WHATIF** till true för att förhandsgranska ändringarna. 

5. Konfigurera `External_ExcludeVMNames` variabeln med en kommaavgränsad lista över virtuella datorer, utan blank steg mellan kommaavgränsade värden.

6. Det här scenariot följer inte `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` variablerna och. I det här scenariot måste du skapa ett eget Automation-schema. Mer information finns i [Schemalägga en Runbook i Azure Automation](shared-resources/schedules.md).

7. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar de virtuella datorerna i produktionen. När du är klar kan du köra **övervakning-och-diagnostik/Monitor-Action-groupsrunbook** med parametern inställt på **falskt**. Du kan också låta Automation-scheman **sekvenserade-StartVM** och **sekvenserat – StopVM** köra automatiskt efter ditt schema.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Scenario 3: starta eller sluta automatiskt baserat på processor användning

Starta/stoppa virtuella datorer när de inte används kan hjälpa till att hantera kostnaden för att köra Azure Resource Manager och klassiska virtuella datorer i din prenumeration genom att utvärdera datorer som inte används under perioder med låg belastning, till exempel efter timmar, och stänga ned dem automatiskt om processor belastningen är lägre än en angiven procent andel.

Som standard är funktionen förkonfigurerad för att utvärdera procent andelen av processor mått för att se om den genomsnittliga användningen är 5 procent eller mindre. Det här scenariot styrs av följande variabler och kan ändras om standardvärdena inte uppfyller dina krav:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Du kan aktivera och rikta åtgärden mot en prenumeration och resurs grupp, eller rikta en speciell lista över virtuella datorer.

När du kör **AutoStop_CreateAlert_Parent** Runbook verifierar den att mål prenumerationen, resurs grupperna och de virtuella datorerna finns. Om de virtuella datorerna finns anropar runbooken **AutoStop_CreateAlert_Child** Runbook för varje virtuell dator som verifieras av den överordnade runbooken. Denna underordnade Runbook:

* Skapar en mått varnings regel för varje verifierad virtuell dator.
* Utlöser **AutoStop_VM_Child** Runbook för en viss virtuell dator om processorn sjunker under det konfigurerade tröskelvärdet för angivet tidsintervall. 
* Försöker stoppa den virtuella datorn.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>Rikta åtgärden mot alla virtuella datorer i en prenumeration

1. Se till att `External_Stop_ResourceGroupNames` variabeln är tom eller anges till * (jokertecken).

2. Valfritt Om du vill undanta vissa virtuella datorer från åtgärden för Autostopp kan du lägga till en kommaavgränsad lista med namn på virtuella datorer i `External_ExcludeVMNames` variabeln.

3. Aktivera **Schedule_AutoStop_CreateAlert_Parent** schema för att köra för att skapa de nödvändiga varnings reglerna för att stoppa virtuell dator för alla virtuella datorer i din prenumeration. Genom att köra den här typen av schema kan du skapa nya mått regler för aviseringar när nya virtuella datorer läggs till i prenumerationen.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Rikta åtgärden mot alla virtuella datorer i en resurs grupp eller flera resurs grupper.

1. Lägg till en kommaavgränsad lista över resurs grupp namn till `External_Stop_ResourceGroupNames` variabeln.

2. Om du vill utesluta några av de virtuella datorerna från autostoppet kan du lägga till en kommaavgränsad lista med namn på virtuella datorer i `External_ExcludeVMNames` variabeln.

3. Aktivera **Schedule_AutoStop_CreateAlert_Parent** schema för att köra för att skapa de nödvändiga varnings reglerna för att stoppa virtuell dator för alla virtuella datorer i dina resurs grupper. Genom att köra den här åtgärden enligt ett schema kan du skapa nya mått regler som nya virtuella datorer läggs till i resurs grupperna.

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>Rikta åtgärden till en lista över virtuella datorer som mål

1. Skapa ett nytt [schema](shared-resources/schedules.md#create-a-schedule) och länka det till **AutoStop_CreateAlert_Parent** Runbook och Lägg till en kommaavgränsad lista över namn på virtuella datorer i `VMList` parametern.

2. Om du vill undanta vissa virtuella datorer från åtgärden för autostoppa kan du lägga till en kommaavgränsad lista över namn på virtuella datorer (utan blank steg) till `External_ExcludeVMNames` variabeln.

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Om du vill ändra e-postaviseringar när Starta/stoppa virtuella datorer när de inte används har distribuerats kan du ändra åtgärds gruppen som skapas under distributionen.  

> [!NOTE]
> Prenumerationer i Azure Government molnet stöder inte e-postfunktionen för den här funktionen.

1. I Azure Portal går du till **övervaka** och sedan **Åtgärds grupper**. Välj den åtgärds grupp som heter **StartStop_VM_Notication**.

    :::image type="content" source="media/automation-solution-vm-management/azure-monitor.png" alt-text="Skärm bild av sidan övervaka åtgärds grupper.":::

2. På sidan StartStop_VM_Notification klickar du på **Redigera information** under **information**. Då öppnas sidan e-post/SMS/push/Voice. Uppdatera e-postadressen och spara ändringarna genom att klicka på **OK** .

    :::image type="content" source="media/automation-solution-vm-management/change-email.png" alt-text="Skärm bild av sidan e-post/SMS/push/Voice som visar ett exempel på en e-postadress som uppdaterats.":::

    Alternativt kan du lägga till ytterligare åtgärder i åtgärds gruppen och läsa mer om åtgärds grupper i [Åtgärds grupper](../azure-monitor/alerts/action-groups.md)

Följande är ett exempel på ett e-postmeddelande som skickas när funktionen stänger av virtuella datorer.

:::image type="content" source="media/automation-solution-vm-management/email.png" alt-text="Skärm bild av ett exempel på ett e-postmeddelande som skickas när funktionen stänger av virtuella datorer.":::

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>Lägg till eller exkludera virtuella datorer

Med funktionen kan du lägga till virtuella datorer som ska vara riktade eller exkluderade. 

### <a name="add-a-vm"></a>Lägga till en virtuell dator

Det finns två sätt att se till att en virtuell dator ingår när funktionen körs:

* Varje överordnad [Runbooks](automation-solution-vm-management.md#runbooks) i funktionen har en- `VMList` parameter. Du kan skicka en kommaavgränsad lista över namn på virtuella datorer (utan blank steg) till den här parametern när du schemalägger lämplig överordnad Runbook för din situation, och de virtuella datorerna kommer att inkluderas när funktionen körs.

* Om du vill välja flera virtuella datorer anger du `External_Start_ResourceGroupNames` och `External_Stop_ResourceGroupNames` med namnen på de resurs grupper som innehåller de virtuella datorer som du vill starta eller stoppa. Du kan också ställa in variablerna till värdet för `*` att köra funktionen mot alla resurs grupper i prenumerationen.

### <a name="exclude-a-vm"></a>Undanta en virtuell dator

Om du vill undanta en virtuell dator från stoppa/starta virtuella datorer vid andra tider kan du lägga till dess namn i `External_ExcludeVMNames` variabeln. Den här variabeln är en kommaavgränsad lista med vissa virtuella datorer (utan blank steg) som ska undantas från funktionen. Den här listan är begränsad till 140 virtuella datorer. Om du lägger till fler än 140 virtuella datorer i den här listan kan virtuella datorer som är inställda på att uteslutas oavsiktligt startas eller stoppas.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Ändra scheman för start och avstängning

Hantering av start-och avstängnings scheman i den här funktionen följer samma steg som beskrivs i [Schemalägga en Runbook i Azure Automation](shared-resources/schedules.md). Separata scheman krävs för att starta och stoppa virtuella datorer.

Det finns stöd för att konfigurera funktionen för att bara stoppa virtuella datorer vid en viss tid. I det här scenariot skapar du bara ett stopp schema och inget motsvarande start schema. 

1. Se till att du har lagt till resurs grupperna för de virtuella datorerna som ska stängas av i `External_Stop_ResourceGroupNames` variabeln.

2. Skapa ett eget schema för tiden när du vill stänga av de virtuella datorerna.

3. Navigera till **ScheduledStartStop_Parent** Runbook och klicka på **Schemalägg**. På så sätt kan du välja det schema som du skapade i föregående steg.

4. Välj **parametrar och kör inställningar** och Ställ in **Åtgärds** fältet på **stoppa**.

5. Klicka på **OK** för att spara ändringarna.

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar funktionen finns i [fråga efter loggar från starta/stoppa virtuella datorer när de inte används](automation-solution-vm-management-logs.md).
* Information om hur du hanterar problem under hantering av virtuella datorer finns i [felsöka starta/stoppa virtuella datorer när de inte används problem](troubleshoot/start-stop-vm.md).
