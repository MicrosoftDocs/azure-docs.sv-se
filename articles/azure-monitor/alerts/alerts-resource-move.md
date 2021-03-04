---
title: Så här uppdaterar du aviserings regler eller åtgärds regler när deras mål resurs flyttas till en annan Azure-region
description: Bakgrund och instruktioner för hur du uppdaterar varnings regler eller åtgärds regler när deras mål resurs flyttas till en annan Azure-region.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: eb6dbb74fe0d345a157049e79f7a3642499d7cfa
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037990"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Så här uppdaterar du aviserings regler eller åtgärds regler när deras mål resurs flyttas till en annan Azure-region

I den här artikeln beskrivs varför befintliga [varnings regler](./alerts-overview.md) och [Åtgärds regler](./alerts-action-rules.md) kan påverkas när du flyttar andra Azure-resurser mellan regioner och hur du identifierar och löser problemen. Mer information om när är resurs förflyttningen mellan olika regioner finns i dokumentationen för huvud [resurs flytt](../../azure-resource-manager/management/move-region.md) .

## <a name="why-the-problem-exists"></a>Varför problemet finns

Aviserings regler och åtgärds regler hänvisar till andra Azure-resurser. Exempel på [Azure VM](../../site-recovery/azure-to-azure-tutorial-migrate.md), [azure SQL](../../azure-sql/database/move-resources-across-regions.md)och [Azure Storage](../../storage/common/storage-account-move.md). När du flyttar resurserna som reglerna refererar till, kommer reglerna förmodligen att sluta fungera korrekt eftersom de inte kan hitta de resurser som de refererar till.

Det finns två huvudsakliga orsaker till varför dina regler slutar att fungera när mål resurserna har flyttats:

- Regelns omfattning refererar uttryckligen till den gamla resursen.
- Varnings regeln baseras på mått.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>Regel definitions område refererar uttryckligen till den gamla resursen

När du flyttar en resurs ändras resurs-ID i de flesta fall. Systemet replikerar resursen till den nya regionen i bakgrunden innan du tar bort den från den gamla regionen. Den här processen kräver att två resurser och därmed två olika resurs-ID: n finns samtidigt under en viss tids period. Eftersom resurs-ID: n måste vara unika måste ett nytt ID skapas under processen. 

**Hur påverkar flyttningen av resursen befintliga regler?**

Aviserings regler och åtgärds regler har en omfattning av de resurser som de gäller för. Omfånget kan vara en hel prenumeration, en resurs grupp eller en eller flera olika resurser.
Här är till exempel en regel med en omfattning med två resurser (två virtuella datorer):

![Aviserings regel för flera resurser](media/alerts-resource-move/multi-resource-alert-rule.png)

Om regel omfånget uttryckligen nämner en resurs, och resursen har flyttat och ändrat sitt resurs-ID, kommer den regeln att söka efter en felaktig eller obefintlig resurs som därför inte kan köras.

**Hur löser du problemet?**

Uppdatera eller återskapa den påverkade regeln så att den pekar på den nya resursen. Processen för att uppdatera omfånget finns längre fram i den här artikeln.

Problemet gäller för följande regel typer:

- Aviseringsregler för aktivitetsloggar
- Åtgärdsregler
- Mått aviseringar – mer information finns i nästa avsnitt [aviserings regler baserat på mått](#alert-rules-based-on-metrics).

> [!NOTE]
> Loggs ökning varnings regler och varnings regler för smarta signaler påverkas inte eftersom deras omfattning är antingen en arbets yta eller en Application Insights. Ingen av de här omfången som för närvarande stöder regions flytt.

## <a name="alert-rules-based-on-metrics"></a>Aviserings regler baserade på mått

De mått som Azure-resurserna genererar är regionala. När en resurs flyttas till en ny region börjar den att sända sina mått i den nya regionen. Därför måste eventuella varnings regler som baseras på mått uppdateras eller återskapas så att de pekar på den aktuella mått strömmen i rätt region.

Den här förklaringen gäller både varnings regler för både [mått](alerts-metric-overview.md) och [tillgänglighets test](../app/monitor-web-app-availability.md).

Om **alla** resurser i omfattningen har flyttats behöver du inte skapa om regeln. Du kan bara uppdatera ett fält i varnings regeln, till exempel beskrivningen av varnings regeln och spara den.
Om **bara några** av resurserna i omfånget har flyttats måste du ta bort de flyttade resurserna från den befintliga regeln och skapa en ny regel som endast täcker de flyttade resurserna.

## <a name="procedures-to-fix-problems"></a>Procedurer för att åtgärda problem

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Identifiera regler som är associerade med en flyttad resurs från Azure Portal

- **För varnings regler** – gå till aviseringar > hantera varnings regler > filtrera efter den som innehåller prenumerationen och den flyttade resursen.
> [!NOTE]
> Aviserings regler för aktivitets loggen stöder inte den här processen. Det går inte att uppdatera omfattningen för en aktivitets logg aviserings regel och låta den peka på en resurs i en annan prenumeration. I stället kan du skapa en ny regel som ersätter den gamla.

- **För åtgärds regler** – navigera till aviseringar > hantera åtgärder > åtgärds regler (för hands version) > filtrera efter den som innehåller prenumerationen och den flyttade resursen.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Ändra en regels omfång från Azure Portal

1. Öppna regeln som du identifierade i föregående steg genom att klicka på den.
2. Under **resurs** klickar du på **Redigera** och justerar omfånget efter behov.
3. Justera andra egenskaper för regeln efter behov.
4. Klicka på **Spara**.

![Ändra definitions område för aviserings regel](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Ändra omfånget för en regel med hjälp av Azure Resource Manager mallar

1. Hämta regelns Azure Resource Manager mall.  Så här exporterar du en regel regel från Azure Portal:
   1. Gå till avsnittet resurs grupper i portalen och öppna den resurs grupp som innehåller regeln.
   2. I avsnittet Översikt markerar du kryss rutan **Visa dold typ** och filtrerar efter relevant typ av regel.
   3. Välj den relevanta regeln om du vill visa information om den.
   4. Under **Inställningar** väljer du **Exportera mall**.
2. Ändra mallen. Om det behövs kan du dela upp i två regler (relevanta för vissa fall av mått aviseringar, som anges ovan).
3. Distribuera om mallen.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Ändra omfattningen för en regel med hjälp av REST API

1. Hämta den befintliga regeln ([mått aviseringar](/rest/api/monitor/metricalerts/get), [aktivitets logg aviseringar](/rest/api/monitor/activitylogalerts/get))
2. Ändra omfattningen ([aktivitets logg aviseringar](/rest/api/monitor/activitylogalerts/update))
3. Distribuera om regeln ([mått aviseringar](/rest/api/monitor/metricalerts/createorupdate), [aktivitets logg aviseringar](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>Ändra omfattning för en regel med hjälp av PowerShell

1. Hämta den befintliga regeln ([mått aviseringar](/powershell/module/az.monitor/get-azmetricalertrulev2), [aktivitets logg aviseringar](/powershell/module/az.monitor/get-azactivitylogalert), [Åtgärds regler](/powershell/module/az.alertsmanagement/get-azactionrule)).
2. Ändra omfånget. Om det behövs kan du dela upp i två regler (relevanta för vissa fall av mått aviseringar, som anges ovan).
3. Distribuera om regeln ([mått aviseringar](/powershell/module/az.monitor/add-azmetricalertrulev2), [aktivitets logg aviseringar](/powershell/module/az.monitor/enable-azactivitylogalert), [Åtgärds regler](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Ändra omfånget för en regel med hjälp av Azure CLI

1.  Hämta den befintliga regeln ([mått aviseringar](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-show), [aktivitets logg aviseringar](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)).
2.  Uppdatera regel omfånget direkt ([mått aviseringar](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update), [aktivitets logg aviseringar](/cli/azure/monitor/activity-log/alert/scope))
3.  Om det behövs kan du dela upp i två regler (relevanta för vissa fall av mått aviseringar, som anges ovan).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att åtgärda andra problem med [aviserings meddelanden](alerts-troubleshoot.md), [mått varningar](alerts-troubleshoot-metric.md)och [logg aviseringar](alerts-troubleshoot-log.md).