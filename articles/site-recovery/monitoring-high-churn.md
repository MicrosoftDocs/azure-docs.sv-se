---
title: Övervaka omsättnings mönster på virtuella datorer
description: Lär dig hur du övervakar omsättnings mönster på Virtual Machines som skyddas med Azure Site Recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: aeb89a9d18e4550fa1d6162920d60507fd50c208
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92359874"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>Övervaka omsättnings mönster på virtuella datorer

Den här artikeln innehåller en översikt över olika verktyg som kan användas för att övervaka omsättnings mönster på en virtuell dator. Genom att använda rätt verktyg är det enkelt att ta reda på exakt vilket program som orsakar hög omsättning och sedan kan ytterligare åtgärder för programmet vidtas.

## <a name="for-azure-virtual-machines-windows-or-linux"></a>För Azure Virtual Machines (Windows eller Linux)

Om din dator finns i Azure och använder en hanterad eller ohanterad disk för lagring, kan du enkelt spåra prestanda genom att spåra disk mått. På så sätt kan du övervaka och göra rätt val av diskar som passar ditt program användnings mönster. Du kan också använda den för att skapa aviseringar, diagnoser och skapa automatisering. [Läs mer](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

När du har skyddat dina datorer med Azure Site Recovery kan du övervaka dina datorer genom att använda Azure Monitor loggar och Log Analytics. [Läs mer](./monitor-log-analytics.md).

Det finns även vissa olika verktyg för operativ system som du kan använda.

## <a name="for-windows-machines"></a>För Windows-datorer

Om du har en dator, var den lokalt eller inte, som kör operativ systemet Windows, finns det några fler tillgängliga verktyg.

Förutom att kontrol lera disk användningen i aktivitets hanteraren kan du alltid se **resursövervakare** -och **prestanda övervakaren**. Dessa verktyg finns redan på Windows-datorer.

### <a name="resource-monitor"></a>Resursövervakaren

**Resursövervakare** visar information om användningen av maskin-och program varu resurser i real tid. Följ stegen nedan om du vill köra resursövervakare på en Windows-dator –

1. Tryck på Win + R och skriv _resmon_.
1. När resmon, d.v.s. resursövervakare, öppnas växlar du till fliken disk. Den ger följande vy-

    ![Fliken resursövervakare disk](./media/monitoring-high-churn/resmon-disk-tab.png)

1. Den här fliken måste övervakas kontinuerligt under en tid för att få en tydlig bild. I exemplet ovan ser vi att _wmiprv.exe_ håller på att delas högt.

När du har identifierat programmen som orsakar hög omsättning på din dator kan du vidta nödvändiga åtgärder för att kunna hantera den omsättning som är relaterad till dessa program.

### <a name="performance-monitor"></a>Prestandaövervakaren

**Prestanda övervakaren** övervakar olika aktiviteter på en dator, till exempel processor-eller minnes användning. Om du vill köra prestanda övervakaren på en Windows-dator följer du stegen nedan –

1. Tryck på Win + R och skriv _perfmon_.
1. När perfmon, det vill säga prestanda övervakaren, öppnas fönstret, som innehåller följande vy

    ![Prestanda övervakaren steg 1](./media/monitoring-high-churn/perfmon-step1.png)

1. Expandera mappen **övervaknings verktyg** till höger och klicka på prestanda övervakaren. Detta öppnar vyn nedan och ger dig en real tids information om den aktuella prestandan –

    ![Prestanda övervakaren Step2](./media/monitoring-high-churn/perfmon-step1.png)

1. Det här diagrammet övervakar för närvarande bara en övervakare, det vill säga% processor tid, som anges av tabellen direkt under grafen. Du kan lägga till fler objekt för övervakning genom att klicka på **"+"** överst i verktyget.
1. Nedan visas ett visuellt objekt om hur prestanda övervakaren ser ut när vi lägger till fler räknare till den –

    ![Prestanda övervakaren steg 3](./media/monitoring-high-churn/perfmon-step3.png)

Läs mer om prestanda övervakaren [här](/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data).

## <a name="for-linux-machines"></a>För Linux-datorer

Om du har en dator, är den lokalt eller inte, som kör Linux-operativsystemet, finns det några fler verktyg som du kan använda för att övervaka omsättnings mönstren.

### <a name="iotop"></a>Iotop

Ett av de mest använda verktygen är _iotop_. Det är ett verktyg för att Visa real tids disk aktivitet. Den kan visa en lista över processer som utför I/O, tillsammans med den disk bandbredd som de använder.

Öppna kommando tolken och kör kommandot `iotop` .

### <a name="iostat"></a>IoStat

IoStat är ett enkelt verktyg som samlar in och visar statistik över enhets data och utmatnings lagrings enheter. Det här verktyget används ofta för att spåra prestanda problem i lagrings enheter, inklusive enheter, lokala diskar och fjärrdiskar.

Öppna kommando tolken och kör kommandot `iostat` .

## <a name="next-steps"></a>Nästa steg

Lär dig hur du övervakar med [Azure Monitor](monitor-log-analytics.md).