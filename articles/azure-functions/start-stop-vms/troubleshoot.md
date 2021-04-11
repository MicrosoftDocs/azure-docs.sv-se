---
title: Felsöka starta/stoppa virtuella datorer (för hands version)
description: Den här artikeln beskriver hur du felsöker problem som uppstått med funktionen starta/stoppa virtuella datorer (för hands version) för dina virtuella Azure-datorer.
services: azure-functions
ms.subservice: ''
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 3c379c1eb36fc19368630188f1b584e1d8a7b8ad
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112029"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>Felsök vanliga problem med starta/stoppa virtuella datorer (för hands version)

Den här artikeln innehåller information om hur du felsöker och löser problem som kan uppstå vid försök att installera och konfigurera starta/stoppa virtuella datorer (för hands version). Allmän information finns i [Översikt över starta/stoppa virtuella datorer](overview.md).

## <a name="general-validation-and-troubleshooting"></a>Allmän verifiering och fel sökning

Det här avsnittet beskriver hur du felsöker allmänna problem med schema scenarier och hjälper till att identifiera rotor saken.

### <a name="azure-dashboard"></a>Azure-instrumentpanel

Du kan börja med att granska den delade Azure-instrumentpanelen. Den Azure-delade instrument panelen som distribuerats som en del av starta/stoppa VM v2 (för hands version) är ett snabbt och enkelt sätt att kontrol lera status för varje åtgärd som utförs på de virtuella datorerna. Se de åtgärder som nyligen utförts **på panelen virtuella datorer** för att se alla de senaste åtgärderna som har utförts på dina virtuella datorer. Det finns en fördröjning, runt fem minuter, för data som ska visas i rapporten när den hämtar data från Application Insights resursen.

### <a name="logic-apps"></a>Logic Apps

Beroende på vilka Logic Apps du har aktiverat för att stödja ditt start-/stopp scenario kan du granska dess körnings historik för att identifiera varför det schemalagda start/avslutnings scenariot inte slutfördes korrekt för en eller flera virtuella mål datorer. Information om hur du granskar detta i detalj finns [Logic Apps körnings historik](../../logic-apps/monitor-logic-apps.md#review-runs-history).

### <a name="azure-storage"></a>Azure Storage

Du kan granska informationen för de åtgärder som utförts på de virtuella datorer som skrivs till tabellen **requestsstoretable** i Azure Storage-kontot som används för att starta/stoppa VM v2 (för hands version). Utför följande steg för att visa dessa poster.

1. Navigera till lagrings kontot i Azure Portal och i kontot väljer du * * Storage Explorer (för hands version) i det vänstra fönstret.
1. Välj **tabeller** och välj sedan **requeststoretable**.
1. Varje post i tabellen representerar den start/stopp-åtgärd som utförs mot en virtuell Azure-dator baserat på mål omfånget som definierats i scenariot för Logic app. Du kan filtrera resultaten efter någon av post egenskaperna (till exempel TIMESTAMP, ACTION eller TARGETTOPLEVELRESOURCENAME).

### <a name="azure-functions"></a>Azure Functions

Du kan granska de senaste anrops detaljerna för de Azure Functions som är ansvariga för den virtuella datorns start-och stopp körning. Först ska vi granska körnings flödet.

Körnings flödet för både **schemalagt** och **sekvenserat** scenario styrs av samma funktion. Nytto Last schema är det som avgör vilket scenario som utförs. I det **schemalagda** scenariot har körnings flödet **schemalagts** för http-> **VirtualMachineRequestOrchestrator** Queue > **VirtualMachineRequestExecutor** -kön.

Den **schemalagda** http-funktionen anropas med nytto Last schema från Logic app. När den **schemalagda** http-funktionen tar emot begäran skickar den informationen till **Orchestrator** Queue-funktionen, som i sin tur skapar flera köer för varje virtuell dator för att utföra åtgärden.

Utför följande steg för att se information om anropet.

1. I Azure Portal navigerar du till **Azure Functions**.
1. Välj Function-appen för starta/stoppa VM v2 (för hands version) i listan.
1. Välj **funktioner** i rutan till vänster.
1. I listan visas flera funktioner som är associerade för varje scenario. Välj funktionen **schemalagd** http.
1. Välj **övervaka** i det vänstra fönstret.
1. Välj den senaste körnings spårningen för att se information om anrop och meddelande avsnittet för detaljerad loggning.
1. Upprepa samma steg för varje funktion som beskrivs som en del av att granska körnings flödet tidigare.

Mer information om övervaknings Azure Functions finns [i analysera Azure Functions telemetri i Application Insights](../../azure-functions/analyze-telemetry-data.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att övervaka Azure Functions-och Logic Apps:

* [Övervaka Azure Functions](../../azure-functions/functions-monitoring.md).

* [Så här konfigurerar du övervakning för Azure Functions](../../azure-functions/configure-monitoring.md).

* [Övervaka Logic Apps](../../logic-apps/monitor-logic-apps.md).