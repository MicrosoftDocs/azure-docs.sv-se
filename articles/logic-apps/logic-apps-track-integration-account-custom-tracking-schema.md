---
title: Anpassade spårnings scheman för B2B-meddelanden
description: Skapa anpassade spårnings scheman för att övervaka B2B-meddelanden i Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "76903064"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>Skapa anpassade spårnings scheman som övervakar arbets flöden från slut punkt till slut punkt i Azure Logic A

Azure Logic Apps har inbyggd spårning som du kan aktivera för delar av arbets flödet. Du kan dock konfigurera anpassad spårning som loggar händelser från början till slutet av arbets flöden, till exempel arbets flöden som innehåller en Logic app, BizTalk Server, SQL Server eller något annat lager. Den här artikeln innehåller anpassad kod som du kan använda i lager utanför din Logic app.

## <a name="custom-tracking-schema"></a>Anpassat spårningsschema

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| Egenskap | Krävs | Typ | Beskrivning |
|----------|----------|------|-------------|
| sourceType | Ja | Sträng | Typ av körnings källa med följande tillåtna värden: `Microsoft.Logic/workflows` , `custom` |
| källa | Ja | Sträng eller JToken | Om käll typen är måste `Microsoft.Logic/workflows` käll informationen följa det här schemat. Om käll typen är är `custom` schemat ett JToken. |
| systemId | Ja | Sträng | Logic app system-ID |
| runId | Ja | Sträng | Körnings-ID för Logic app |
| operationName | Ja | Sträng | Namn på åtgärden, till exempel åtgärd eller utlösare |
| repeatItemScopeName | Ja | Sträng | Upprepa objekt namn om åtgärden finns inuti en `foreach` or- `until` slinga |
| repeatItemIndex | Ja | Integer | Anger att åtgärden finns inuti en `foreach` eller- `until` slinga och är det upprepade objekt indexet. |
| trackingId | Inga | Sträng | Spårnings-ID för att korrelera meddelandena |
| correlationId | Inga | Sträng | Korrelations-ID för att korrelera meddelandena |
| clientRequestId | Inga | Sträng | Klienten kan fylla i den här egenskapen för att korrelera meddelanden |
| eventLevel | Ja | Sträng | Händelsens nivå |
| Händelsetid | Ja | DateTime | Tid för händelsen i UTC-format: *ÅÅÅÅ-MM-ddTHH: mm: SS. 00000Z* |
| recordType | Ja | Sträng | Typ av spårnings post med endast detta tillåtna värde: `custom` |
| spela in | Ja | JToken | Anpassad posttyp med endast JToken-format |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Spårnings scheman för B2B-protokoll

Information om B2B-protokoll spårnings scheman finns i:

* [AS2-spårningsscheman](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-spårningsscheman](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att [övervaka B2B-meddelanden med Azure Monitor loggar](../logic-apps/monitor-b2b-messages-log-analytics.md)