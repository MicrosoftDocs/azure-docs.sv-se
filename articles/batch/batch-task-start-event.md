---
title: Start händelse för Azure Batch uppgift
description: Referensinformation för start händelse för batch-aktivitet. Den här händelsen genereras när en aktivitet har schemalagts att starta på en Compute-nod av Scheduler.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: 3a57ffbb1e1659cff54d101aa4b90ca1bd5d3a57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91851024"
---
# <a name="task-start-event"></a>Starthändelse för uppgift

 Den här händelsen genereras när en aktivitet har schemalagts att starta på en Compute-nod av Scheduler. Observera att om aktiviteten har ett nytt försök eller om den här händelsen upprepas, kommer den här händelsen att genereras igen för samma uppgift, men antalet nya försök och system uppgifts versionen kommer att uppdateras därefter.


 I följande exempel visas bröd texten i en start händelse för aktiviteten.

```
{
    "jobId": "myJob",
    "id": "myTask",
    "taskType": "User",
    "systemTaskVersion": 220192842,
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
        "retryCount": 0
    }
}
```

|Elementnamn|Typ|Kommentarer|
|------------------|----------|-----------|
|`jobId`|Sträng|ID för jobbet som innehåller uppgiften.|
|`id`|Sträng|Aktivitetens ID.|
|`taskType`|Sträng|Aktivitetens typ. Detta kan antingen vara ' JobManager ' som indikerar att det är en Job Manager-uppgift eller ' användare ' som indikerar att den inte är en Job Manager-aktivitet.|
|`systemTaskVersion`|Int32|Detta är den interna återförsöks räknaren för en aktivitet. Internt kan batch-tjänsten göra ett nytt försök med en uppgift för att redovisa tillfälliga problem. De här problemen kan omfatta interna schemaläggnings fel eller försök att återställa från datornoder i ett felaktigt tillstånd.|
|`requiredSlots`|Int32|De platser som krävs för att köra aktiviteten.|
|[`nodeInfo`](#nodeInfo)|Komplex typ|Innehåller information om den beräknings nod som aktiviteten kördes på.|
|[`multiInstanceSettings`](#multiInstanceSettings)|Komplex typ|Anger att aktiviteten är en multi-instance-aktivitet som kräver flera datornoder.  Se [multiInstanceSettings](/rest/api/batchservice/get-information-about-a-task) för mer information.|
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
|`numberOfInstances`|Int|Antalet Compute-noder som aktiviteten kräver.|

###  <a name="constraints"></a><a name="constraints"></a> begränsningar

|Elementnamn|Typ|Kommentarer|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|Det maximala antalet gånger som aktiviteten kan göras om. Batch-tjänsten försöker igen med en aktivitet om dess slutkod är skilt från noll.<br /><br /> Observera att det här värdet specifikt styr antalet återförsök. Batch-tjänsten kommer att försöka utföra åtgärden en gång och kan sedan försöka igen till den här gränsen. Om till exempel det maximala antalet försök är 3, försöker batch utföra en aktivitet upp till fyra gånger (ett första försök och tre försök).<br /><br /> Om det maximala antalet försök är 0, gör batch-tjänsten inte om aktiviteterna.<br /><br /> Om det maximala antalet försök är-1, kommer batch-tjänsten att försöka utföra aktiviteter utan begränsning.<br /><br /> Standardvärdet är 0 (inga återförsök).|

###  <a name="executioninfo"></a><a name="executionInfo"></a> executionInfo

|Elementnamn|Typ|Kommentarer|
|------------------|----------|-----------|
|`retryCount`|Int32|Antalet gånger som aktiviteten har fått nytt försök av batch-tjänsten. Aktiviteten provas igen om den avslutas med en slutkod som inte är noll, upp till den angivna MaxTaskRetryCount|
