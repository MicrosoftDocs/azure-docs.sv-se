---
title: Händelse för Azure Batch aktivitet
description: Referens för händelse vid misslyckad batch-aktivitet. Den här händelsen kommer att genereras förutom en aktivitet som slutförs och kan användas för att identifiera när en aktivitet har misslyckats.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e13692b45ff5a049d0b724525ad6565d2b894a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91850820"
---
# <a name="task-fail-event"></a>Händelse för misslyckad uppgift

 Den här händelsen genereras när en aktivitet slutförs med ett haveri. Alla avslutnings koder som inte är noll betraktas som felaktiga. Den här händelsen kommer att genereras *förutom* en aktivitet som slutförs och kan användas för att identifiera när en aktivitet har misslyckats.


 I följande exempel visas bröd texten i en aktivitet som inte går att logga.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 0,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "startTime": "2016-09-08T16:32:23.799Z",
        "endTime": "2016-09-08T16:34:00.666Z",
        "exitCode": 1,
        "retryCount": 2,
        "requeueCount": 0
    }
}
```

|Elementnamn|Typ|Kommentarer|
|------------------|----------|-----------|
|`jobId`|Sträng|ID för jobbet som innehåller uppgiften.|
|`id`|Sträng|Aktivitetens ID.|
|`taskType`|Sträng|Aktivitetens typ. Detta kan antingen vara ' JobManager ' som indikerar att det är en Job Manager-uppgift eller ' användare ' som indikerar att den inte är en Job Manager-aktivitet. Den här händelsen genereras inte för jobb förberedelse aktiviteter, jobb publicerings aktiviteter eller start uppgifter.|
|`systemTaskVersion`|Int32|Detta är den interna återförsöks räknaren för en aktivitet. Internt kan batch-tjänsten göra ett nytt försök med en uppgift för att redovisa tillfälliga problem. De här problemen kan omfatta interna schemaläggnings fel eller försök att återställa från datornoder i ett felaktigt tillstånd.|
|`requiredSlots`|Int32|De platser som krävs för att köra aktiviteten.|
|[`nodeInfo`](#nodeInfo)|Komplex typ|Innehåller information om den beräknings nod som aktiviteten kördes på.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Komplex typ|Anger att aktiviteten är en multi-instance-aktivitet som kräver flera datornoder.  [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task)Mer information finns i.|
|[`constraints`](#constraints)|Komplex typ|De körnings begränsningar som gäller för den här uppgiften.|
|[`executionInfo`](#executionInfo)|Komplex typ|Innehåller information om körningen av uppgiften.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|Elementnamn|Typ|Kommentarer|
|------------------|----------|-----------|
|`poolId`|Sträng|ID för den pool där aktiviteten kördes.|
|`nodeId`|Sträng|ID: t för noden som aktiviteten kördes på.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|Elementnamn|Typ|Kommentarer|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|Antalet Compute-noder som aktiviteten kräver.|

###  <a name="constraints"></a><a name="constraints"></a> begränsningar

|Elementnamn|Typ|Kommentarer|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Det maximala antalet gånger som aktiviteten kan göras om. Batch-tjänsten försöker igen med en aktivitet om dess slutkod är skilt från noll.<br /><br /> Observera att det här värdet specifikt styr antalet återförsök. Batch-tjänsten kommer att försöka utföra åtgärden en gång och kan sedan försöka igen till den här gränsen. Om till exempel det maximala antalet försök är 3, försöker batch utföra en aktivitet upp till fyra gånger (ett första försök och tre försök).<br /><br /> Om det maximala antalet försök är 0, gör batch-tjänsten inte om aktiviteterna.<br /><br /> Om det maximala antalet försök är-1, kommer batch-tjänsten att försöka utföra aktiviteter utan begränsning.<br /><br /> Standardvärdet är 0 (inga återförsök).|


###  <a name="executioninfo"></a><a name="executionInfo"></a> executionInfo

|Elementnamn|Typ|Kommentarer|
|------------------|----------|-----------|
|`startTime`|DateTime|Tiden då uppgiften startade. "Körs" motsvarar **körnings** tillstånd, så om aktiviteten anger resursfiler eller programpaket, visar start tiden tiden då uppgiften startade nedladdningen eller distributionen av dessa.  Om aktiviteten har startats om eller gjorts om, är det här den senaste tiden då uppgiften startades.|
|`endTime`|DateTime|Tiden då uppgiften slutfördes.|
|`exitCode`|Int32|Avslutnings koden för aktiviteten.|
|`retryCount`|Int32|Antalet gånger som aktiviteten har fått nytt försök av batch-tjänsten. Aktiviteten provas igen om den avslutas med en slutkod som inte är noll, upp till den angivna MaxTaskRetryCount.|
|`requeueCount`|Int32|Antalet gånger som aktiviteten har ställts i kö av batch-tjänsten som ett resultat av en användarbegäran.<br /><br /> När användaren tar bort noder från en pool (genom att ändra storlek på eller krympa poolen) eller när jobbet har inaktiverats, kan användaren ange att aktiviteter som körs på noderna ska köas för körning. Antalet spårar hur många gånger aktiviteten har ställts i kö av dessa orsaker.|
