---
title: Reagerar på händelser för Azure Policy tillstånds ändring
description: Använd Azure Event Grid för att prenumerera på App policy-händelser, vilket gör att program kan reagera på tillstånds ändringar utan behov av komplicerad kod.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: c100d5038a8c2506f5339ea0962012a8c32e22cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105735116"
---
# <a name="reacting-to-azure-policy-state-change-events"></a>Reagerar på händelser för Azure Policy tillstånds ändring

Azure Policy händelser gör det möjligt för program att reagera på status ändringar. Den här integrationen görs utan behov av komplicerad kod eller dyra och ineffektiva avsöknings tjänster. I stället flyttas händelser via [Azure Event Grid](../../../event-grid/index.yml) till prenumeranter som [Azure Functions](../../../azure-functions/index.yml), [Azure Logic Apps](../../../logic-apps/index.yml)eller till och med din egen anpassade http-lyssnare.
Du betalar mycket bara för det du använder.

Azure Policy händelser skickas till Azure Event Grid, som tillhandahåller pålitliga leverans tjänster till dina program genom omfattande principer för återförsök och leverans av obeställbara meddelanden. Läs mer i [Event Grid meddelande leverans och försök igen](../../../event-grid/delivery-and-retry.md).

Den vanliga Azure Policy händelse scenariot spårar när kompatibilitetstillstånd för en resurs ändras under utvärdering av principer. Händelse-baserad arkitektur är ett effektivt sätt att reagera på dessa ändringar i stället för att genomsöka resursernas kompatibilitetstillstånd enligt ett fast schema.

> [!NOTE]
> Azure Policy tillstånds ändrings händelser skickas till Event Grid när en [utvärderings](../how-to/get-compliance-data.md#evaluation-triggers) version Slutför resurs utvärderingen.

Se [princip tillstånds ändrings händelser för att event Grid med Azure CLI](../tutorials/route-state-change-events.md) för en fullständig själv studie kurs.

:::image type="content" source="../../../event-grid/media/overview/functional-model.png" alt-text="Event Grid-modell för källor och hanterare" lightbox="../../../event-grid/media/overview/functional-model-big.png":::

## <a name="available-azure-policy-events"></a>Tillgängliga Azure Policy händelser

Event Grid använder [händelse prenumerationer](../../../event-grid/concepts.md#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Azure Policy händelse prenumerationer kan innehålla tre typer av händelser:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft. PolicyInsights. PolicyStateCreated | Utlöses när ett kompatibilitetstillstånd för principen skapas. |
| Microsoft. PolicyInsights. PolicyStateChanged | Utlöses när ett kompatibilitetstillstånd för principen ändras. |
| Microsoft. PolicyInsights. PolicyStateDeleted | Utlöses när ett kompatibilitetstillstånd för principen tas bort. |

## <a name="event-schema"></a>Händelseschema

Azure Policy händelser innehåller all information du behöver för att svara på ändringar i dina data. Du kan identifiera en Azure Policy händelse när `eventType` egenskapen börjar med "Microsoft. PolicyInsights".
Ytterligare information om användningen av Event Grid händelse egenskaper finns dokumenterade i  
[Event Grid händelse schema](../../../event-grid/event-schema.md).

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| `id` | sträng | Unikt ID för händelsen. |
| `topic` | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| `subject` | sträng | Det fullständigt kvalificerade ID: t för resursen som ändringen av kompatibilitetstillstånd gäller, inklusive resurs namn och resurs typ. Använder formatet, `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>/providers/<ProviderNamespace>/<ResourceType>/<ResourceName>` |
| `data` | objekt | Azure Policy händelse data. |
| `data.timestamp` | sträng | Den tid (i UTC) som resursen genomsöktes med Azure Policy. För ordnings händelser använder du den här egenskapen i stället för toppnivå- `eventTime` eller `time` egenskaps nivån. |
| `data.policyAssignmentId` | sträng | Resurs-ID för princip tilldelningen. |
| `data.policyDefinitionId` | sträng | Resurs-ID för princip definitionen. |
| `data.policyDefinitionReferenceId` | sträng | Referens-ID för princip definitionen i initiativ definitionen, om princip tilldelningen är för ett initiativ. Kan vara tom. |
| `data.complianceState` | sträng | Resursens kompatibilitetstillstånd med avseende på princip tilldelningen. |
| `data.subscriptionId` | sträng | Resursens prenumerations-ID. |
| `data.complianceReasonCode` | sträng | Orsaks kod för efterlevnad. Kan vara tom. |
| `eventType` | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| `eventTime` | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| `dataVersion` | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| `metadataVersion` | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Här är ett exempel på en ändrings händelse för princip tillstånd:

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

Mer information finns i [Azure policy Events schema](../../../event-grid/event-schema-policy.md).

## <a name="practices-for-consuming-events"></a>Metoder för att konsumera händelser

Program som hanterar Azure Policy händelser bör följa dessa rekommendationer:

> [!div class="checklist"]
> - Flera prenumerationer kan konfigureras för att dirigera händelser till samma händelse hanterare, så anta inte att händelser kommer från en viss källa. Kontrol lera i stället avsnittet i meddelandet för att säkerställa princip tilldelningen, princip definitionen och resursen som status ändrings händelsen är för.
> - Markera alternativet `eventType` och anta inte att alla händelser som du tar emot är de typer som du förväntar dig.
> - Används `data.timestamp` för att bestämma ordningen på händelserna i Azure policy, i stället för på toppnivå- `eventTime` eller `time` egenskaps nivån.
> - Använd fältet ämne för att få åtkomst till resursen som hade en ändring i princip tillstånd.

## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Azure Policy tillstånds ändrings händelser a try:

- [Flödes princip tillstånd ändra händelser till Event Grid med Azure CLI](../tutorials/route-state-change-events.md)
- [Azure Policy schema information för Event Grid](../../../event-grid/event-schema-policy.md)
- [Om Event Grid](../../../event-grid/overview.md)