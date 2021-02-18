---
title: Skala skalnings uppsättningar för virtuella datorer i Azure Portal
description: Så här skapar du regler för autoskalning för skalnings uppsättningar för virtuella datorer i Azure Portal
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms:service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 4bb4084a7ee0d9d719a88c077fa8c63803aa6934
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591374"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Skala automatiskt en skalnings uppsättning för virtuella datorer i Azure Portal
När du skapar en skalningsuppsättning, definierar du antalet virtuella datorinstanser som du vill köra. När ditt program behöver ändras, kan du automatiskt öka eller minska antalet virtuella datorinstanser. Möjligheten att skala automatiskt låter dig hålla dig uppdaterad med kundernas behov eller svara på ändringar i programprestandan under hela livscykeln för din app.

Den här artikeln visar hur du skapar regler för autoskalning i Azure Portal som övervakar prestanda för de virtuella dator instanserna i din skalnings uppsättning. Dessa regler för autoskalning ökar eller minskar antalet virtuella dator instanser som svar på dessa prestanda mått. Du kan också utföra de här stegen med [Azure PowerShell](tutorial-autoscale-powershell.md) eller [Azure CLI](tutorial-autoscale-cli.md).


## <a name="prerequisites"></a>Förutsättningar
Om du vill skapa regler för autoskalning behöver du en befintlig skalnings uppsättning för virtuella datorer. Du kan skapa en skalnings uppsättning med [Azure Portal](quick-create-portal.md), [Azure POWERSHELL](quick-create-powershell.md)eller [Azure CLI](quick-create-cli.md).


## <a name="create-a-rule-to-automatically-scale-out"></a>Skapa en regel för automatisk skalning
Om dina programkrav ökar, ökar även belastningen på de virtuella datorinstanserna i din skalningsuppsättning. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen. När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Du kan styra vilka mått som ska övervakas, som CPU eller disk, hur länge programbelastningen måste uppfylla ett visst tröskelvärde och hur många virtuella datorinstanser som ska läggas till skalningsuppsättningen.

1. Öppna Azure Portal och välj **resurs grupper** på menyn till vänster på instrument panelen.
2. Välj den resurs grupp som innehåller din skalnings uppsättning och välj sedan din skalnings uppsättning i listan över resurser.
3. Välj **skalning** på menyn till vänster i fönstret skalnings uppsättning. Välj knappen för att **Aktivera autoskalning**:

    ![Aktivera autoskalning i Azure Portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Ange ett namn för inställningarna, till exempel *autoskalning* och välj sedan alternativet för att **lägga till en regel**.

5. Nu ska vi skapa en regel som ökar antalet virtuella dator instanser i en skalnings uppsättning när den genomsnittliga CPU-belastningen är större än 70% över en 10 minuters period. När regeln utlöses ökar antalet virtuella dator instanser med 20%. I skalnings uppsättningar med ett litet antal VM-instanser kan du ställa in **åtgärden** att *öka antalet med* och sedan ange *1* eller *2* för *antalet instanser*. I skalnings uppsättningar med ett stort antal VM-instanser kan en ökning på 10% eller 20% VM-instanser vara lämpligare.

    Ange följande inställningar för regeln:
    
    | Parameter              | Förklaring                                                                                                         | Värde          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Tids mängd*     | Definierar hur de insamlade mätvärdena ska aggregeras för analys.                                                | Genomsnitt        |
    | *Mått namn*          | Prestandamått för att övervaka och tillämpa åtgärder för skalningsuppsättningar på.                                                   | Processorprocentandel |
    | *Tidsintervallstatistik* | Definierar hur insamlade mått i varje tids kornig het ska aggregeras för analys.                             | Genomsnitt        |
    | *Operator*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                     | Större än   |
    | *Tröskelvärde*            | Procent andelen som gör att den automatiska skalnings regeln utlöser en åtgärd.                                                 | 70             |
    | *Varaktighet*             | Tidsperioden som övervakas innan värdena för måttet och tröskelvärdet jämförs. Inkluderar inte kylnings period.                                   | 10 minuter     |
    | *Åtgärd*            | Definierar om skalnings uppsättningen ska skala upp eller ned när regeln gäller och med vilken ökning.                        | Öka procent med |
    | *Antal instanser*       | Procentandelen av virtuella datorinstanser som ska ändras när regeln utlöser.                                            | 20             |
    | *Väntetid (minuter)*  | Hur lång tid ska gå innan regeln tillämpas igen så att de automatiska skalningsåtgärderna har tid att börja gälla. | 5 minuter      |

    I följande exempel visas en regel som skapats i Azure Portal som matchar de här inställningarna:

    ![Skapa en regel för autoskalning för att öka antalet VM-instanser](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. Välj **Lägg till** för att skapa regeln


## <a name="create-a-rule-to-automatically-scale-in"></a>Skapa en regel för automatisk skalning i
På kvällar eller helger, kan efterfrågan på ditt program minska. Om den här minskade belastningen är konsekvent över en tidsperiod, kan du konfigurera regler för automatisk skalning för att minska antalet virtuella datorinstanser i skalningsuppsättningen. Den här åtgärden för skala in minskar kostnaden för att köra din skalningsuppsättningen eftersom du bara köra de antal instanser som krävs för att uppfylla den aktuella efterfrågan.

1. Välj att **lägga till en regel** igen.
2. Skapa en regel som minskar antalet virtuella dator instanser i en skalnings uppsättning när den genomsnittliga CPU-belastningen sjunker under 30% över en 10 minuters period. När regeln utlöses minskar antalet virtuella dator instanser med 20%.

    Använd samma metod som i föregående regel. Justera följande inställningar för din regel:
    
    | Parameter              | Förklaring                                                                                                          | Värde          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                      | Mindre än   |
    | *Tröskelvärde*            | Procent andelen som gör att den automatiska skalnings regeln utlöser en åtgärd.                                                 | 30             |
    | *Åtgärd*            | Definierar om skalnings uppsättningen ska skala upp eller ned när regeln gäller och med vilken ökning                         | Minska procent med |
    | *Antal instanser*       | Procentandelen av virtuella datorinstanser som ska ändras när regeln utlöser.                                             | 20             |

3. Välj **Lägg till** för att skapa regeln


## <a name="define-autoscale-instance-limits"></a>Definiera gränser för den automatiska skalnings instansen
Profilen för autoskalning måste definiera ett lägsta, högsta och standard antal VM-instanser. När reglerna för autoskalning tillämpas, ser du till att du inte skalar ut mer än det maximala antalet instanser, eller skalar efter minsta antal instanser.

1. Ange följande gränser för instans:

    | Minimum | Maximal | Standardvärde|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Om du vill tillämpa regler för autoskalning och instans gränser väljer du **Spara**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Övervaka antalet instanser i en skalnings uppsättning
Om du vill se antal och status för virtuella dator instanser väljer du **instanser** på menyn till vänster i fönstret skalnings uppsättning. Statusen anger om den virtuella dator instansen *skapas* när skalnings uppsättningen skalas ut automatiskt eller om den *tas bort* när skalan skalas automatiskt i.

![Visa en lista med virtuella dator instanser för skalnings uppsättning](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Autoskalning baserat på ett schema
I föregående exempel skalades automatiskt en skalnings uppsättning in eller ut med grundläggande värd mått som CPU-användning. Du kan också skapa regler för autoskalning baserat på scheman. Med de här schemabaserade reglerna kan du automatiskt skala ut antalet VM-instanser före en förväntad ökning av program efter frågan, till exempel kärn arbets tid och sedan automatiskt skala antalet instanser vid en tidpunkt då du förväntar dig mindre efter frågan, till exempel helgen.

1. Välj **skalning** på menyn till vänster i fönstret skalnings uppsättning. Om du vill ta bort de befintliga reglerna för autoskalning som skapats i föregående exempel, väljer du pappers korgs ikonen.

    ![Ta bort de befintliga reglerna för autoskalning](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Välj om du vill **lägga till ett skalnings villkor**. Välj Penn ikonen bredvid regel namn och ange ett namn som till exempel *skala ut under varje arbets dag*.

    ![Byt namn på standard regeln för autoskalning](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Välj alternativ knappen för att **skala till ett angivet instans antal**.
4. Om du vill skala upp antalet instanser anger du *10* som instans antal.
5. Välj **upprepa vissa dagar** för **schema** typen.
6. Välj alla arbets dagar, måndag till fredag.
7. Välj lämplig tidszon och ange sedan en **Start tid** på *09:00*.
8. Välj att **lägga till ett skalnings villkor** igen. Upprepa processen för att skapa ett schema med namnet *Scale i under kväll* som skalas till *3* instanser, upprepas varje veckodag och börjar vid *18:00*.
9. Välj **Spara** om du vill använda dina schemabaserade regler för autoskalning.

    ![Skapa regler för autoskalning som skalas enligt ett schema](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Om du vill se hur reglerna för automatisk skalning appliceras väljer du **Kör historik** överst i **skalnings** fönstret. I listan med diagram och händelser visas när reglerna för autoskalning och antalet virtuella dator instanser i skalnings uppsättningen ökar eller minskar.


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder regler för autoskalning för att skala vågrätt och öka eller minska *antalet* virtuella dator instanser i din skalnings uppsättning. Du kan också skala lodrätt för att öka eller minska *storleken* på den virtuella dator instansen. Mer information finns i [Lodrät autoskalning med skalnings uppsättningar för virtuella datorer](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Information om hur du hanterar dina VM-instanser finns i [Hantera skalnings uppsättningar för virtuella datorer med Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md).

Information om hur du genererar aviseringar när reglerna för autoskalning utlöses finns i [använda åtgärder för autoskalning för att skicka aviseringar via e-post och webhook i Azure Monitor](../azure-monitor/autoscale/autoscale-webhook-email.md). Du kan också [använda gransknings loggar för att skicka aviseringar om e-post och webhook-aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-log-webhook.md).
