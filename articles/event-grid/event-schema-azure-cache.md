---
title: Azure cache för Redis som Event Grid källa
description: Beskriver de egenskaper som har angetts för Azure cache för Redis-händelser med Azure Event Grid
ms.topic: conceptual
ms.date: 12/21/2020
author: curib
ms.author: cauribeg
ms.openlocfilehash: f446f3f469a7404e6e74ba67ee24bf32578fe9d8
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056223"
---
# <a name="azure-cache-for-redis-as-an-event-grid-source"></a>Azure cache för Redis som en Event Grid källa

Den här artikeln innehåller egenskaper och schema för Azure cache för Redis-händelser. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md). 

## <a name="event-grid-event-schema"></a>Event Grid-händelseschema

### <a name="list-of-events-for-azure-cache-for-redis-rest-apis"></a>Lista över händelser för Azure cache för Redis REST API: er

Dessa händelser utlöses när en klient exporterar, importerar eller skalar genom att anropa Azure cache för Redis REST API: er. Uppdaterings händelsen utlöses av Redis Update.

 |Händelse namn |Description|
 |----------|-----------|
 |**Microsoft. cache. ExportRDBCompleted** |Utlöses när cachelagrade data exporteras. |
 |**Microsoft. cache. ImportRDBCompleted** |Utlöses när cachelagrade data importeras. |
 |**Microsoft. cache. PatchingCompleted** |Utlöses när korrigeringen har slutförts. |
 |**Microsoft. cache. ScalingCompleted** |Utlöses när skalningen är klar. |

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>Innehållet i ett händelse svar

När en händelse utlöses skickar Event Grid-tjänsten data om händelsen för att prenumerera på slut punkten.

Det här avsnittet innehåller ett exempel på hur data ska se ut för varje Azure-cache för Redis-händelse.

### <a name="microsoftcachepatchingcompleted-event"></a>Microsoft. cache. PatchingCompleted-händelse

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.PatchingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"PatchingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"PatchingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheimportrdbcompleted-event"></a>Microsoft. cache. ImportRDBCompleted-händelse

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ImportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ImportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ImportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcacheexportrdbcompleted-event"></a>Microsoft. cache. ExportRDBCompleted-händelse

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ExportRDBCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ExportRDBCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ExportRDBCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="microsoftcachescalingcompleted"></a>Microsoft. cache. ScalingCompleted

```json
[{
"id":"9b87886d-21a5-4af5-8e3e-10c4b8dac73b",
"eventType":"Microsoft.Cache.ScalingCompleted",
"topic":"/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
"data":{
    "name":"ScalingCompleted",
    "timestamp":"2020-12-09T21:50:19.9995668+00:00",
    "status":"Succeeded"},
"subject":"ScalingCompleted",
"dataversion":"1.0",
"metadataVersion":"1",
"eventTime":"2020-12-09T21:50:19.9995668+00:00"}]
```

### <a name="event-properties"></a>Händelse egenskaper

En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| id | sträng | Unikt ID för händelsen. |
| data | objekt | Azure cache för Redis-händelse data. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Data-objektet har följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| timestamp | sträng | Tiden då händelsen inträffade. |
| name | sträng | Händelsens namn. |
| status | sträng | Händelsens status. Misslyckades eller lyckades. |


## <a name="quickstarts"></a>Snabbstarter

Om du vill testa Azure cache för Redis-händelser kan du läsa följande snabb starts artiklar:

|Om du vill använda det här verktyget:    |Se den här artikeln: |
|--|-|
|Azure-portalen    |[Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkten med Azure Portal](../azure-cache-for-redis/cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkt med PowerShell](../azure-cache-for-redis/cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkt med Azure CLI](../azure-cache-for-redis/cache-event-grid-quickstart-cli.md)|

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).

