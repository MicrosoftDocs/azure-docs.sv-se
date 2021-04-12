---
title: Fråga efter loggar från Azure Automation Starta/stoppa virtuella datorer när de inte används
description: Den här artikeln beskriver hur du använder Azure Monitor för att fråga logg data som genereras av Starta/stoppa virtuella datorer när de inte används.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 3e9e924d6626d9f0dcd2db8a5e8b8f90a0aa01ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593847"
---
# <a name="query-logs-from-startstop-vms-during-off-hours"></a>Frågeloggar från Starta/stoppa virtuella datorer när de inte används

Azure Automation vidarebefordrar två typer av poster till den länkade Log Analytics arbets ytan: jobb loggar och jobb strömmar. Den här artikeln granskar de data som är tillgängliga för [fråga](../azure-monitor/logs/log-query-overview.md) i Azure Monitor.

## <a name="job-logs"></a>Jobbloggar

|Egenskap | Beskrivning|
|----------|----------|
|Anropare |  Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
|Kategori | Klassificering av typ av data. För Automation är värdet JobLogs.|
|CorrelationId | GUID som är korrelations-ID för Runbook-jobbet.|
|JobId | GUID som är ID för Runbook-jobbet.|
|operationName | Anger åtgärdstypen i Azure. För Automation är värdet jobb.|
|resourceId | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
|ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera. För Automation är värdet Azure Automation.|
|ResourceType | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|resultType | Status för runbookjobbet. Möjliga värden:<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>- Slutförd|
|resultDescription | Beskriver jobbstatusen för runbook. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes|
|RunbookName | Anger namnet på runbooken.|
|SourceSystem | Anger källsystemet för data som skickats. För Automation är värdet OpsManager|
|StreamType | Anger händelsetypen. Möjliga värden:<br>- Verbose<br>- Utdata<br>- Fel<br>- Varning|
|SubscriptionId | Anger prenumerations-ID för jobbet.
|Tid | Datum och tid då runbook-jobbet körs.|

## <a name="job-streams"></a>Arbetsflöden

|Egenskap | Beskrivning|
|----------|----------|
|Anropare |  Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
|Kategori | Klassificering av typ av data. För Automation är värdet JobStreams.|
|JobId | GUID som är ID för Runbook-jobbet.|
|operationName | Anger åtgärdstypen i Azure. För Automation är värdet jobb.|
|ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
|resourceId | Anger resurs-ID i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera. För Automation är värdet Azure Automation.|
|ResourceType | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
|resultType | Resultatet av runbook-jobbet då händelsen skapades. Ett möjligt värde är:<br>- Ärendeposter|
|resultDescription | Innehåller utdataströmmen från runbook.|
|RunbookName | Anger namnet på runbooken.|
|SourceSystem | Anger källsystemet för data som skickats. För Automation är värdet OpsManager.|
|StreamType | Typ av jobbström. Möjliga värden:<br>– Förlopp<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose|
|Tid | Datum och tid då runbook-jobbet körs.|

När du utför en loggs ökning som returnerar kategori poster för **JobLogs** eller **JobStreams** kan du välja vyn **JobLogs** eller **JobStreams** , som visar en uppsättning paneler som sammanfattar de uppdateringar som returneras av sökningen.

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Följande tabell innehåller exempel på loggs ökningar för jobb poster som samlas in av Starta/stoppa virtuella datorer när de inte används.

|Fråga | Beskrivning|
|----------|----------|
|Hitta jobb för Runbook ScheduledStartStop_Parent som har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Hitta jobb för Runbook-ScheduledStartStop_Parent som inte har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Hitta jobb för Runbook SequencedStartStop_Parent som har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Hitta jobb för Runbook-SequencedStartStop_Parent som inte har slutförts | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Nästa steg

* Om du vill ställa in funktionen läser du [Konfigurera stoppa/starta virtuella datorer under låg belastning](automation-solution-vm-management-config.md).
* Information om logg aviseringar under funktions distributionen finns i [Skapa logg aviseringar med Azure Monitor](../azure-monitor/alerts/alerts-log.md).
* Information om hur du löser funktions fel finns i [felsöka starta/stoppa virtuella datorer när de inte används problem](troubleshoot/start-stop-vm.md).