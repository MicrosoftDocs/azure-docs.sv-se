---
title: Autoskala VM-skalningsuppsättningar i Azure Portal
description: Så här skapar du autoskalningsregler för VM-skalningsuppsättningar i Azure Portal
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 2ee2db62cf43dc191da2b92f7d4b67ff775f628f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537528"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Skala en VM-skalningsuppsättning automatiskt i Azure Portal
När du skapar en skalningsuppsättning, definierar du antalet virtuella datorinstanser som du vill köra. När ditt program behöver ändras, kan du automatiskt öka eller minska antalet virtuella datorinstanser. Möjligheten att skala automatiskt låter dig hålla dig uppdaterad med kundernas behov eller svara på ändringar i programprestandan under hela livscykeln för din app.

Den här artikeln visar hur du skapar regler för automatisk skalning i Azure Portal som övervakar prestanda för de virtuella datorinstanserna i din skalningsuppsättning. Dessa regler för automatisk skalning ökar eller minskar antalet VM-instanser som svar på dessa prestandamått. Du kan också utföra de här stegen [med Azure PowerShell](tutorial-autoscale-powershell.md) eller [Azure CLI.](tutorial-autoscale-cli.md)


## <a name="prerequisites"></a>Förutsättningar
Om du vill skapa regler för automatisk skalning behöver du en befintlig VM-skalningsuppsättning. Du kan skapa en skalningsuppsättning [med Azure Portal,](quick-create-portal.md) [Azure PowerShell](quick-create-powershell.md)eller [Azure CLI.](quick-create-cli.md)


## <a name="create-a-rule-to-automatically-scale-out"></a>Skapa en regel för att automatiskt skala ut
Om dina programkrav ökar, ökar även belastningen på de virtuella datorinstanserna i din skalningsuppsättning. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen. När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Du kan styra vilka mått som ska övervakas, som CPU eller disk, hur länge programbelastningen måste uppfylla ett visst tröskelvärde och hur många virtuella datorinstanser som ska läggas till skalningsuppsättningen.

1. Öppna Azure Portal och välj **Resursgrupper** på menyn till vänster på instrumentpanelen.
2. Välj den resursgrupp som innehåller din skalningsuppsättning och välj sedan din skalningsuppsättning i listan över resurser.
3. Välj **Skalning** på menyn till vänster i skalningsuppsättningsfönstret. Välj knappen aktivera **autoskalning:**

    ![Aktivera autoskalning i Azure Portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Ange ett namn för dina inställningar, till exempel *autoskalning,* och välj sedan alternativet för att **lägga till en regel**.

5. Nu ska vi skapa en regel som ökar antalet virtuella datorinstanser i en skalningsuppsättning när den genomsnittliga CPU-belastningen är större än 70 % under en 10-minutersperiod. När regeln utlöses ökar antalet virtuella datorinstanser med 20 %. I skalningsuppsättningar med ett litet antal VM-instanser  kan du ange Åtgärden till Öka antal med och sedan ange *1* eller *2* för  *Instansantal*. I skalningsuppsättningar med ett stort antal VM-instanser kan en ökning på 10 % eller 20 % av VM-instanserna vara lämpligare.

    Ange följande inställningar för regeln:
    
    | Parameter              | Förklaring                                                                                                         | Värde          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Tidsaggregering*     | Definierar hur de insamlade mätvärdena ska aggregeras för analys.                                                | Genomsnitt        |
    | *Måttnamn*          | Prestandamått för att övervaka och tillämpa åtgärder för skalningsuppsättningar på.                                                   | Processorprocentandel |
    | *Tidsintervallstatistik* | Definierar hur insamlade mått i varje tidsperiod ska aggregeras för analys.                             | Genomsnitt        |
    | *Operator*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                     | Större än   |
    | *Tröskelvärde*            | Procentandelen som gör att autoskalningsregeln utlöser en åtgärd.                                                 | 70             |
    | *Varaktighet*             | Tidsperioden som övervakas innan värdena för måttet och tröskelvärdet jämförs. Inkluderar inte nedsydd period.                                   | 10 minuter     |
    | *Åtgärd*            | Definierar om skalningsuppsättningen ska skalas upp eller ned när regeln tillämpas och i vilken ökning.                        | Öka procent med |
    | *Antal instanser*       | Procentandelen av virtuella datorinstanser som ska ändras när regeln utlöser.                                            | 20             |
    | *Väntetid (minuter)*  | Hur lång tid ska gå innan regeln tillämpas igen så att de automatiska skalningsåtgärderna har tid att börja gälla. | 5 minuter      |

    I följande exempel visas en regel som skapats i Azure Portal som matchar dessa inställningar:

    ![Skapa en autoskalningsregel för att öka antalet VM-instanser](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Skapa regeln genom att välja Lägg **till**


## <a name="create-a-rule-to-automatically-scale-in"></a>Skapa en regel för automatisk inskalning
På kvällar eller helger, kan efterfrågan på ditt program minska. Om den här minskade belastningen är konsekvent över en tidsperiod, kan du konfigurera regler för automatisk skalning för att minska antalet virtuella datorinstanser i skalningsuppsättningen. Den här åtgärden för skala in minskar kostnaden för att köra din skalningsuppsättningen eftersom du bara köra de antal instanser som krävs för att uppfylla den aktuella efterfrågan.

1. Välj att **lägga till en regel** igen.
2. Skapa en regel som minskar antalet virtuella datorinstanser i en skalningsuppsättning när den genomsnittliga CPU-belastningen sedan sjunker under 30 % under en 10-minutersperiod. När regeln utlöses minskar antalet virtuella datorinstanser med 20 %.

    Använd samma metod som med den föregående regeln. Justera följande inställningar för din regel:
    
    | Parameter              | Förklaring                                                                                                          | Värde          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                      | Mindre än   |
    | *Tröskelvärde*            | Procentandelen som gör att autoskalningsregeln utlöser en åtgärd.                                                 | 30             |
    | *Åtgärd*            | Definierar om skalningsuppsättningen ska skalas upp eller ned när regeln tillämpas och med vilken ökning                         | Minska procent med |
    | *Antal instanser*       | Procentandelen av virtuella datorinstanser som ska ändras när regeln utlöser.                                             | 20             |

3. Skapa regeln genom att välja Lägg **till**


## <a name="define-autoscale-instance-limits"></a>Definiera begränsningar för instans för automatisk skalning
Din autoskalningsprofil måste definiera ett minsta, högsta och standardantal virtuella datorinstanser. När dina regler för automatisk skalning tillämpas ser dessa instansgränser till att du inte skalar ut utöver det maximala antalet instanser eller skalar in utöver det minsta antalet instanser.

1. Ange följande instansgränser:

    | Minimum | Maximal | Standardvärde|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Om du vill tillämpa autoskalningsregler och instansgränser väljer du **Spara**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Övervaka antalet instanser i en skalningsuppsättning
Om du vill se antalet och  statusen för VM-instanser väljer du Instanser på menyn till vänster i skalningsuppsättningsfönstret. Statusen anger om den virtuella datorinstansen är *Skapa* eftersom  skalningsuppsättningen automatiskt skalar ut, eller om den tar bort när skalan skalas in automatiskt.

![Visa en lista över vm-instanser för skalningsuppsättning](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Autoskalning baserat på ett schema
I föregående exempel skalades en skalningsuppsättning automatiskt in eller ut med grundläggande värdmått, till exempel CPU-användning. Du kan också skapa regler för automatisk skalning baserat på scheman. Med dessa schemabaserade regler kan du automatiskt skala ut antalet VIRTUELLA datorinstanser före en förväntad ökning av programefterfrågan, till exempel kärntimmar, och sedan automatiskt skala in antalet instanser vid en tidpunkt då du förväntar dig mindre efterfrågan, till exempel helger.

1. Välj **Skalning** på menyn till vänster i skalningsuppsättningsfönstret. Om du vill ta bort de befintliga autoskalningsregler som skapades i föregående exempel väljer du papperskorgsikonen.

    ![Ta bort befintliga regler för automatisk skalning](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Välj att **lägga till ett skalningsvillkor**. Välj pennikonen bredvid regelnamnet och ange ett namn, till exempel *Skala ut under varje arbetsdag.*

    ![Byt namn på standardregeln för autoskalning](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Välj alternativknappen för att **skala till ett specifikt instansantal.**
4. Om du vill skala upp antalet instanser anger du *10* som instansantal.
5. Välj **Upprepa specifika dagar** som **Schematyp.**
6. Välj alla arbetsdagar, måndag till fredag.
7. Välj lämplig tidszon och ange sedan **starttiden** *09:00*.
8. Välj att **lägga till ett skalningsvillkor** igen. Upprepa processen för att skapa ett schema med namnet *Skala in* under kvällen som skalar till *3* instanser, upprepas varje veckodag och börjar *kl. 18:00*.
9. Om du vill tillämpa dina schemabaserade regler för automatisk skalning väljer du **Spara**.

    ![Skapa regler för autoskalning som skalar enligt ett schema](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Om du vill se hur reglerna för automatisk skalning tillämpas **väljer du** Körningshistorik högst upp i **fönstret Skalning.** Diagrammet och händelselistan visar när reglerna för automatisk skalning utlöses och antalet VM-instanser i din skalningsuppsättning ökar eller minskar.


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder  regler för automatisk skalning för att skala horisontellt och öka eller minska antalet virtuella datorinstanser i din skalningsuppsättning. Du kan också skala lodrätt för att öka eller minska storleken på den virtuella *datorinstansen.* Mer information finns i Vertikal [autoskalning med VM-skalningsuppsättningar.](virtual-machine-scale-sets-vertical-scale-reprovision.md)

Information om hur du hanterar dina VM-instanser finns i [Hantera VM-skalningsuppsättningar med Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md).

Information om hur du genererar aviseringar när autoskalningsregler utlöses finns i Använda autoskalningsåtgärder för att skicka e-post- och [webhook-aviseringsmeddelanden i Azure Monitor](../azure-monitor/autoscale/autoscale-webhook-email.md). Du kan också använda [granskningsloggar för att skicka e-post- och webhook-aviseringsmeddelanden i Azure Monitor](../azure-monitor/alerts/alerts-log-webhook.md).
