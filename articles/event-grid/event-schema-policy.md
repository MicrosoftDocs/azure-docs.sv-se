---
title: Azure Policy som en Event Grid källa
description: I den här artikeln beskrivs hur du använder Azure Policy som en Event Grid händelse källa. Det innehåller schemat och länkar till självstudier och instruktions artiklar.
ms.topic: conceptual
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2021
ms.openlocfilehash: 7723b618910f52d58204711468b482db85ab502c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735085"
---
# <a name="azure-policy-as-an-event-grid-source"></a>Azure Policy som en Event Grid källa

Den här artikeln innehåller egenskaper och schema för [Azure policy](../governance/policy/index.yml) händelser. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](./event-schema.md). Du får också en lista med snabb starter och självstudier för att använda Azure Policy som en händelse källa.

## <a name="available-event-types"></a>Tillgängliga händelse typer

Azure Policy avger följande händelse typer:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. PolicyInsights. PolicyStateCreated | Utlöses när ett kompatibilitetstillstånd för principen skapas. |
| Microsoft. PolicyInsights. PolicyStateChanged | Utlöses när ett kompatibilitetstillstånd för principen ändras. |
| Microsoft. PolicyInsights. PolicyStateDeleted | Utlöses när ett kompatibilitetstillstånd för principen tas bort. |

## <a name="example-event"></a>Exempel händelse

# <a name="event-grid-event-schema"></a>[Event Grid-händelseschema](#tab/event-grid-event-schema)
I följande exempel visas schemat för en händelse för ett princip tillstånd som skapats: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateCreated",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```

Schemat för en ändrings händelse för princip tillstånd liknar följande: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "topic": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "eventType": "Microsoft.PolicyInsights.PolicyStateChanged",
    "eventTime": "2021-03-27T18:37:42.5241536Z",
    "dataVersion": "1",
    "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[Molnbaserat händelseschema](#tab/cloud-event-schema)

I följande exempel visas schemat för en händelse för ett princip tillstånd som skapats: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateCreated",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

Schemat för en ändrings händelse för princip tillstånd liknar följande: 

```json
[{
    "id": "5829794FCB5075FCF585476619577B5A5A30E52C84842CBD4E2AD73996714C4C",
    "source": "/subscriptions/<SubscriptionID>",
    "subject": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>",
    "data": {
        "timestamp": "2021-03-27T18:37:42.4496956Z",
        "policyAssignmentId": "<policy-assignment-scope>/providers/microsoft.authorization/policyassignments/<policy-assignment-name>",
        "policyDefinitionId": "<policy-definition-scope>/providers/microsoft.authorization/policydefinitions/<policy-definition-name>",
        "policyDefinitionReferenceId": "",
        "complianceState": "NonCompliant",
        "subscriptionId": "<subscription-id>",
        "complianceReasonCode": ""
    },
    "type": "Microsoft.PolicyInsights.PolicyStateChanged",
    "time": "2021-03-27T18:37:42.5241536Z",
    "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>Händelse egenskaper

# <a name="event-grid-event-schema"></a>[Event Grid-händelseschema](#tab/event-grid-event-schema)

En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `topic` | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| `subject` | sträng | Det fullständigt kvalificerade ID: t för resursen som ändringen av kompatibilitetstillstånd gäller, inklusive resurs namn och resurs typ. Använder formatet, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `eventType` | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| `eventTime` | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| `id` | sträng | Unikt ID för händelsen. |
| `data` | objekt | Azure Policy händelse data. |
| `dataVersion` | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| `metadataVersion` | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

# <a name="cloud-event-schema"></a>[Molnbaserat händelseschema](#tab/cloud-event-schema)

En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `source` | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| `subject` | sträng | Det fullständigt kvalificerade ID: t för resursen som ändringen av kompatibilitetstillstånd gäller, inklusive resurs namn och resurs typ. Använder formatet, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `type` | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| `time` | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| `id` | sträng | Unikt ID för händelsen. |
| `data` | objekt | Azure Policy händelse data. |
| `specversion` | sträng | CloudEvents schema Specifikations version. |

---

Data-objektet har följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `timestamp` | sträng | Den tid (i UTC) som resursen genomsöktes med Azure Policy. För ordnings händelser använder du den här egenskapen i stället för toppnivå- `eventTime` eller `time` egenskaps nivån. |
| `policyAssignmentId` | sträng | Resurs-ID för princip tilldelningen. |
| `policyDefinitionId` | sträng | Resurs-ID för princip definitionen. |
| `policyDefinitionReferenceId` | sträng | Referens-ID för princip definitionen i initiativ definitionen, om princip tilldelningen är för ett initiativ. Kan vara tom. |
| `complianceState` | sträng | Resursens kompatibilitetstillstånd med avseende på princip tilldelningen. |
| `subscriptionId` | sträng | Resursens prenumerations-ID. |
| `complianceReasonCode` | sträng | Orsaks kod för efterlevnad. Kan vara tom. |

## <a name="next-steps"></a>Nästa steg

- En genom gång av routning Azure Policy tillstånds ändrings händelser finns i [använda Event Grid för meddelanden om princip tillstånds ändringar](../governance/policy/tutorials/route-state-change-events.md).
- En översikt över hur du integrerar Azure Policy med Event Grid finns i [reagera på att Azure policy händelser med event Grid](../governance/policy/concepts/event-overview.md).
- En introduktion till Azure Event Grid finns i [Vad är event Grid?](./overview.md)
- Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](./subscription-creation-schema.md).