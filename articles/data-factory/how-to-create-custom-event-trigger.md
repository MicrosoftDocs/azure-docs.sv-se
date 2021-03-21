---
title: Skapa anpassade händelse utlösare i Azure Data Factory
description: Lär dig hur du skapar en anpassad utlösare i Azure Data Factory som kör en pipeline som svar på en anpassad händelse som publiceras till Event Grid.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: e372808250b1d5b21cd5f6c5226922d5d6ad0f77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610055"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>Skapa en utlösare som kör en pipeline som svar på en anpassad händelse (förhands granskning)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs de anpassade händelse utlösare som du kan skapa i Data Factory pipeliner.

Händelse driven arkitektur (EDA) är ett gemensamt data integrerings mönster som omfattar produktion, identifiering, konsumtion och reaktion på händelser. Data integrerings scenarier kräver ofta Data Factory kunder som utlöser pipelines baserat på vissa händelser. Data Factory inbyggd integrering med [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) täcker nu [anpassade händelser](../event-grid/custom-topics.md): kunderna skickar valfria händelser till ett event Grid-ämne och Data Factory prenumererar på och lyssnar på avsnittet och utlöser pipelines på motsvarande sätt.

> [!NOTE]
> Den integrering som beskrivs i den här artikeln är beroende av [Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Se till att din prenumeration är registrerad hos Event Grid Resource Provider. Mer information finns i [resurs leverantörer och typer](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Du måste kunna utföra åtgärden *Microsoft. EventGrid/eventSubscriptions/**. Den här åtgärden är en del av den inbyggda rollen EventGrid EventSubscription Contributor.

Dessutom kan kunder som kombinerar pipeline-parametrar och en anpassad händelse utlösare parsa och referera till anpassad _data_ nytto Last i pipeline-körningar. _data_ fält i en anpassad händelse nytto Last är en JSON-standardstruktur med fri form, vilket ger kunderna maximal kontroll över händelse drivna pipeline-körningar.

> [!IMPORTANT]
> Varje så ofta kan en nyckel som refereras i Parameterisering saknas i en anpassad händelse nytto Last. Det går inte att _köra utlösarens körning_ med ett fel som anger att uttrycket inte kan utvärderas eftersom egenskaps nyckel _namnet_ inte finns. __Ingen__ _pipeline-körning_ kommer att utlösas av händelsen.

## <a name="setup-event-grid-custom-topic"></a>Installations Event Grid anpassat ämne

Om du vill använda den anpassade händelse utlösaren i Data Factory måste du _först_ konfigurera ett [anpassat avsnitt i Event Grid](../event-grid/custom-topics.md). Arbets flödet skiljer sig från lagrings händelse utlösare, där Data Factory konfigurerar artikeln åt dig. Här måste du navigera i Azure Event Grid och skapa ämnet själv. Mer information om hur du skapar det anpassade avsnittet finns i Azure Event Grid [Portal självstudier](../event-grid/custom-topics.md#azure-portal-tutorials) och [CLI-självstudier](../event-grid/custom-topics.md#azure-cli-tutorials)

Data fabriker förväntar sig att händelserna ska följa [Event Grid händelse schema](../event-grid/event-schema.md). Se till att händelse nytto laster har följande fält.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="data-factory-ui"></a>Data Factory-användargränssnitt

I det här avsnittet visas hur du skapar en utlösare för lagrings händelser i Azure Data Factory användar gränssnitt.

1. Växla till fliken **Redigera** , som visas med en Penn symbol.

1. Välj **utlösare** på menyn och välj sedan **ny/redigera**.

1. På sidan **Lägg till utlösare** väljer du **Välj utlösare...** och väljer sedan **+ ny**.

1. Välj utlösare typ **anpassade händelser**

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Skärm bild av sidan författare för att skapa en ny anpassad händelse utlösare i Data Factory gränssnittet." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Välj ditt anpassade ämne i list rutan för Azure-prenumeration eller ange händelse ämnets omfattning manuellt.

   > [!NOTE]
   > Om du vill skapa en ny eller ändra en befintlig anpassad händelse utlösare måste det Azure-konto som används för att logga in på Data Factory och publicera lagrings händelse utlösaren ha lämplig rollbaserad åtkomst kontroll (Azure RBAC)-behörighet för ämnet. Ingen ytterligare behörighet krävs: tjänstens huvud namn för Azure Data Factory behöver _inte_ särskild behörighet för att event Grid. Mer information om åtkomst kontroll finns i avsnittet [rollbaserad åtkomst kontroll](#role-based-access-control) .

1. **Ämnet börjar med** och **ämnet slutar med** egenskaper kan du filtrera händelser som du vill utlösa pipelinen för. Båda egenskaperna är valfria.

1. Använd **+ ny** för att lägga till **händelse typer** som du vill filtrera på. Anpassad händelse utlösare anställda en eller-relation för listan: om en anpassad händelse har en _eventType_ -egenskap som matchar någon lista här, utlöses en pipeline-körning. Händelse typen är Skift läges okänslig. I skärm bilden nedan matchar utlösaren alla _copycompleted_ -eller _copysucceeded_ -händelser med ämnet börjar med _fabriker_

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Skärm bild av sidan Redigera utlösare som förklarar händelse typer och ämnes filtrering i Data Factory UI.":::

1. En anpassad händelse utlösare kan parsa och skicka anpassad _data_ nytto Last till din pipeline. Skapa först pipeline-parametrarna och fyll i värdena på sidan **parametrar** . Använd format **@triggerBody (). event. data._Namn_** för att parsa data nytto lasten och överför värden till pipeline-parametrar. Detaljerad förklaring finns i [referens utlösa metadata i pipelines](how-to-use-trigger-parameterization.md) och [Systemvariabler i en anpassad händelse utlösare](control-flow-system-variables.md#custom-event-trigger-scope)

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Skärm bild av inställningen för pipeline-parametrar.":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Skärm bild av parameter sidan till referens data nytto Last i en anpassad händelse.":::

1. Klicka på **OK** när du är färdig.

## <a name="json-schema"></a>JSON-schema

Följande tabell innehåller en översikt över de schema element som är relaterade till anpassade händelse utlösare:

| **JSON-element** | **Beskrivning** | **Typ** | **Tillåtna värden** | **Obligatoriskt** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **utrymme** | Det Azure Resource Manager resurs-ID för Event Grid-ämnet. | Sträng | Azure Resource Manager-ID | Yes |
| **planering** | Den typ av händelser som orsakar utlösaren att utlösa. | Strängmatris    |  | Ja, minst ett värde förväntas |
| **subjectBeginsWith** | Ämnes fältet måste börja med det mönster som tillhandahölls för utlösaren för att utlösa. Till exempel `factories` utlöses endast utlösaren för händelse ämne som börjar med `factories` . | Sträng   | | No |
| **subjectEndsWith** | Ämnes fältet måste sluta med det mönster som tillhandahölls för utlösaren för att utlösa. | Sträng   | | No |

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Azure Data Factory använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att säkerställa att obehörig åtkomst att lyssna på, prenumererar på uppdateringar från och utlöser pipeliner som är länkade till anpassade händelser är strikt förbjudna.

* För att kunna skapa en ny eller uppdatera en befintlig anpassad händelse utlösare måste Azure-kontot som är inloggat på Data Factory ha lämplig åtkomst till det relevanta lagrings kontot. Annars _nekas åtgärden med nekad åtkomst_.
* Data Factory behöver ingen särskild behörighet till din Event Grid och du behöver _inte_ tilldela en särskild Azure RBAC-behörighet för att Data Factory tjänstens huvud namn för åtgärden.

Mer specifikt måste kunden ha _Microsoft. EventGrid/EventSubscriptions/Write-_ behörighet för _/Subscriptions/# # # #/resourceGroups//#_ # #/providers/Microsoft.EventGrid/topics/someTopics

## <a name="next-steps"></a>Nästa steg

* Detaljerad information om utlösare finns i [pipeline-körning och utlösare](concepts-pipeline-execution-triggers.md#trigger-execution).
* Lär dig hur du refererar till Utlös ande metadata i pipelinen finns i [referens utlösare metadata i pipeline-körningar](how-to-use-trigger-parameterization.md)
