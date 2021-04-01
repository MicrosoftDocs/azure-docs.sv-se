---
title: Starta ett Azure Stream Analytics jobb
description: Den här artikeln beskriver hur du startar ett Stream Analytics jobb från Azure Portal, PowerShell och Visual Studio.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/03/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 79149d8e9862ece24b4b2da4c2ca4afcceb23d63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016260"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Starta ett Azure Stream Analytics jobb

Du kan starta ditt Azure Stream Analytics-jobb med hjälp av Azure Portal, Visual Studio och PowerShell. När du startar ett jobb väljer du en tid då jobbet ska börja skapa utdata. Azure Portal, Visual Studio och PowerShell har olika metoder för att ange start tiden. Dessa metoder beskrivs nedan.

## <a name="start-options"></a>Start alternativ
Följande tre alternativ är tillgängliga för att starta ett jobb. Observera att alla de tider som anges nedan är de som anges i [timestamp by](/stream-analytics-query/timestamp-by-azure-stream-analytics). Om tidsstämpel BY inte anges används införsel tiden.
* **Nu**: gör start punkten för utdata-händelse strömmen densamma som när jobbet startas. Om en temporal operator används (t. ex. tidsfönster, fördröjning eller koppling) ser Azure Stream Analytics automatiskt tillbaka till data i Indatakällan. Om du till exempel startar ett jobb "nu" och om din fråga använder ett rullande-fönster för 5 minuter söker Azure Stream Analytics efter data från 5 minuter sedan i indata.
Den första möjliga utdata-händelsen skulle ha en tidsstämpel som är lika med eller större än den aktuella tiden, och ASA garanterar att alla indata-händelser som logiskt kan bidra till utdata har redovisats. Till exempel genereras inte partiella fönster mängder. Det är alltid hela det sammanlagda värdet.

* **Anpassad**: du kan välja Start punkten för utdata. På samma sätt som alternativet **nu** kommer Azure Stream Analytics automatiskt att läsa data före den här tiden om en temporal operator används 

* **När den senast stoppades**. Det här alternativet är tillgängligt när jobbet startades tidigare, men stoppades manuellt eller misslyckades. När du väljer det här alternativet Azure Stream Analytics kommer att använda den senaste utgångs tiden för att starta om jobbet så att inga data förloras. På samma sätt som föregående alternativ kommer Azure Stream Analytics automatiskt att läsa data före den här tiden om en temporal operator används. Eftersom flera indata-partitioner kan ha olika tid, används den tidigaste stopp tiden för alla partitioner, vilket innebär att vissa dubbletter kan visas i utdata. Mer information om exakt en bearbetning finns på sidan om sid [händelse leverans garantier](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Azure Portal

Gå till jobbet i Azure Portal och välj **Starta** på översikts sidan. Välj en **Start tid för jobb utdata** och välj sedan **Starta**.

Välj ett av alternativen för **Start tid för jobb utskrift**. Alternativen är *nu* *anpassade* och, om jobbet kördes tidigare,  *när det senast stoppades*. Se ovan för mer information om de här alternativen.

## <a name="visual-studio"></a>Visual Studio

I vyn jobb väljer du den gröna pilknappen för att starta jobbet. Ange **Start läget för jobb utdata** och välj **Starta**. Jobbets status ändras till **körs**.

Det finns tre alternativ för **jobb start läge**: *JobStartTime*, *CustomTime* och *LastOutputEventTime*. Om den här egenskapen saknas är standardvärdet *JobStartTime*. Se ovan för mer information om de här alternativen.


## <a name="powershell"></a>PowerShell

Använd följande cmdlet för att starta jobbet med hjälp av PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Det finns tre alternativ för **OutputStartMode**: *JobStartTime*, *CustomTime* och *LastOutputEventTime*. Om den här egenskapen saknas är standardvärdet *JobStartTime*. Se ovan för mer information om de här alternativen.

Mer information om `Start-AzStreamAnalyitcsJob` cmdleten finns i [Start-AzStreamAnalyticsJob-referensen](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Snabb start: skapa ett Stream Analytics jobb med Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-verktygen för Visual Studio](stream-analytics-quick-create-vs.md)