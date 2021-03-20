---
title: Azure Batch händelse för aktivitets schema
description: Referens för händelse av aktivitets schema för batch-aktivitet. Den här händelsen genereras när en aktivitet inte kunde schemaläggas och kommer att försöka igen senare.
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91852189"
---
# <a name="task-schedule-fail-event"></a>Händelse för misslyckad aktivitetsschemaläggning

 Den här händelsen genereras när en aktivitet inte kan schemaläggas och kommer att göras om senare. Detta är ett tillfälligt haveri vid schemaläggning av aktiviteter på grund av resurs begränsning, till exempel att det inte finns tillräckligt många platser på noder för att köra en uppgift med `requiredSlots` angiven.

 I följande exempel visas innehållet i en händelse för misslyckad aktivitets schemaläggning.

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
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
|[`schedulingError`](#schedulingError)|Komplex typ|Innehåller information om schemaläggnings felet för aktiviteten.|

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


###  <a name="schedulingerror"></a><a name="schedulingError"></a> schedulingError

|Elementnamn|Typ|Kommentarer|
|------------------|----------|-----------|
|`category`|Sträng|Fel kategori.|
|`code`|Sträng|En identifierare för aktivitets schemaläggnings felet. Koder är invarianter och är avsedda att konsumeras program mässigt.|
|`message`|Sträng|Ett meddelande som beskriver schemaläggningen av aktiviteter som är avsett att vara lämpligt för visning i ett användar gränssnitt.|
