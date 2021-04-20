---
title: Så här uppdaterar du aviseringsregler eller åtgärdsregler när målresursen flyttas till en annan Azure-region
description: Bakgrund och instruktioner för hur du uppdaterar aviseringsregler eller åtgärdsregler när målresursen flyttas till en annan Azure-region.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: 727196f274db3abae75a38d3ecdf31a78dec0fab
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725952"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Så här uppdaterar du aviseringsregler eller åtgärdsregler när målresursen flyttas till en annan Azure-region

I den här artikeln beskrivs [](./alerts-action-rules.md) varför [befintliga](./alerts-overview.md) aviseringsregler och åtgärdsregler kan påverkas när du flyttar andra Azure-resurser mellan regioner och hur du identifierar och löser dessa problem. Mer information om när [resursflyttning mellan](../../azure-resource-manager/management/move-resources-overview.md) regioner är användbar och en checklista för att utforma en flyttprocess finns i huvuddokumentationen för resursflyttning.

## <a name="why-the-problem-exists"></a>Varför problemet finns

Aviseringsregler och åtgärdsregler refererar till andra Azure-resurser. Exempel är [virtuella Azure-datorer,](../../site-recovery/azure-to-azure-tutorial-migrate.md) [Azure SQL](../../azure-sql/database/move-resources-across-regions.md)och [Azure Storage](../../storage/common/storage-account-move.md). När du flyttar de resurser som dessa regler refererar till slutar reglerna troligen att fungera korrekt eftersom de inte kan hitta de resurser som de refererar till.

Det finns två huvudorsaker till varför reglerna kan sluta fungera när du har flyttat målresurserna:

- Omfånget för regeln refererar uttryckligen till den gamla resursen.
- Din aviseringsregel baseras på mått.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>Regelomfång refererar uttryckligen till den gamla resursen

När du flyttar en resurs ändras resurs-ID:t i de flesta fall. I bakgrunden replikerar systemet resursen till den nya regionen innan den tas bort från den gamla regionen. Den här processen kräver att två resurser och därmed två olika resurs-ID:er finns samtidigt under en liten tidsperiod. Eftersom resurs-ID:n måste vara unika måste ett nytt ID skapas under processen. 

**Hur påverkar flytten av resursen befintliga regler?**

Aviseringsregler och åtgärdsregler har en omfattning av resurser som de gäller för. Omfånget kan vara en hel prenumeration, en resursgrupp eller en eller flera specifika resurser.
Här är till exempel en regel med ett omfång med två resurser (två virtuella datorer):

![Varningsregel för flera resurser](media/alerts-resource-move/multi-resource-alert-rule.png)

Om regelomfånget uttryckligen nämner en resurs, och resursen har flyttats och ändrat dess resurs-ID, letar regeln efter en felaktig eller obefintlig resurs och misslyckas därför.

**Hur löser jag problemet?**

Uppdatera eller återskapa den berörda regeln så att den pekar på den nya resursen. Processen för att uppdatera omfånget finns senare i den här artikeln.

Problemet gäller för dessa regeltyper:

- Aviseringsregler för aktivitetsloggar
- Åtgärdsregler
- Måttaviseringar – Mer information finns i nästa avsnitt [Aviseringsregler baserat på mått.](#alert-rules-based-on-metrics)

> [!NOTE]
> Loggsökningsaviseringsregler och varningsregler för smart detektor påverkas inte eftersom deras omfattning antingen är en arbetsyta eller Application Insights. Inget av dessa omfång stöder för närvarande regionflyttningar.

## <a name="alert-rules-based-on-metrics"></a>Aviseringsregler baserade på mått

De mått som Azure-resurser ger är regionala. När en resurs flyttas till en ny region börjar den generera sina mått i den nya regionen. Därför måste alla aviseringsregler som baseras på mått uppdateras eller återskapas så att de pekar på den aktuella måttströmmen i rätt region.

Den här förklaringen gäller för både [måttaviseringsregler och](alerts-metric-overview.md) [aviseringsregler för tillgänglighetstest.](../app/monitor-web-app-availability.md)

Om **alla** resurser i omfånget har flyttats behöver du inte återskapa regeln. Du kan bara uppdatera alla fält i aviseringsregeln, till exempel beskrivningen av aviseringsregeln, och spara den.
Om **bara vissa** av resurserna i omfånget har flyttats måste du ta bort de flyttade resurserna från den befintliga regeln och skapa en ny regel som endast omfattar de flyttade resurserna.

## <a name="procedures-to-fix-problems"></a>Procedurer för att åtgärda problem

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Identifiera regler som är associerade med en flyttad resurs från Azure Portal

- **För aviseringsregler** – Gå till Aviseringar > Hantera aviseringsregler > filtrera efter den prenumeration som innehåller och den flyttade resursen.
> [!NOTE]
> Aktivitetsloggens aviseringsregler stöder inte den här processen. Det går inte att uppdatera omfånget för en aktivitetsloggaviseringsregel och få den att peka på en resurs i en annan prenumeration. I stället kan du skapa en ny regel som ersätter den gamla.

- **För åtgärdsregler** – Gå till Aviseringar > Hantera åtgärder > Åtgärdsregler (förhandsversion) > filtrera efter den prenumeration som innehåller och den flyttade resursen.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Ändra omfånget för en regel från Azure Portal

1. Öppna regeln som du har identifierat i föregående steg genom att klicka på den.
2. Under **Resurs** klickar du **på** Redigera och justerar omfånget efter behov.
3. Justera andra egenskaper för regeln efter behov.
4. Klicka på **Spara**.

![Ändra omfång för aviseringsregel](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Ändra omfånget för en regel med hjälp Azure Resource Manager mallar

1. Hämta Azure Resource Manager mall för regeln.   Så här exporterar du mallen för en regel från Azure Portal:
   1. Gå till avsnittet Resursgrupper i portalen och öppna den resursgrupp som innehåller regeln.
   2. I avsnittet Översikt markerar du **kryssrutan Visa dold** typ och filtrerar efter relevant typ av regel.
   3. Välj relevant regel för att visa dess information.
   4. Under **Inställningar** väljer du **Exportera mall**.
2. Ändra mallen. Om det behövs kan du dela upp i två regler (relevanta för vissa fall av måttaviseringar, enligt vad som anges ovan).
3. Distribuera om mallen.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Ändra omfånget för en regel med REST API

1. Hämta den befintliga regeln ([måttaviseringar,](/rest/api/monitor/metricalerts/get) [aktivitetsloggaviseringar](/rest/api/monitor/activitylogalerts/get))
2. Ändra omfånget ([aktivitetsloggaviseringar](/rest/api/monitor/activitylogalerts/update))
3. Distribuera om regeln ([måttaviseringar,](/rest/api/monitor/metricalerts/createorupdate) [aktivitetsloggaviseringar](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>Ändra omfång för en regel med hjälp av PowerShell

1. Hämta den befintliga regeln ([måttaviseringar,](/powershell/module/az.monitor/get-azmetricalertrulev2) [aktivitetsloggaviseringar](/powershell/module/az.monitor/get-azactivitylogalert), [åtgärdsregler](/powershell/module/az.alertsmanagement/get-azactionrule)).
2. Ändra omfånget. Om det behövs kan du dela upp i två regler (relevanta för vissa fall av måttaviseringar, enligt vad som anges ovan).
3. Distribuera om regeln ([måttaviseringar,](/powershell/module/az.monitor/add-azmetricalertrulev2) [aktivitetsloggaviseringar](/powershell/module/az.monitor/enable-azactivitylogalert), [åtgärdsregler](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Ändra omfånget för en regel med hjälp av Azure CLI

1.  Hämta den befintliga regeln ([måttaviseringar,](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-show) [aktivitetsloggaviseringar](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)).
2.  Uppdatera regelomfånget direkt ([måttaviseringar,](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update) [aktivitetsloggaviseringar](/cli/azure/monitor/activity-log/alert/scope))
3.  Om det behövs kan du dela upp i två regler (relevanta för vissa fall av måttaviseringar, enligt vad som anges ovan).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att åtgärda andra problem [med aviseringsmeddelanden,](alerts-troubleshoot.md) [måttaviseringar](alerts-troubleshoot-metric.md)och [loggaviseringar.](alerts-troubleshoot-log.md)