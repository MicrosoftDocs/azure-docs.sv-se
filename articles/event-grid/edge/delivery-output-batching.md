---
title: Batching av utdata i Azure Event Grid IoT Edge | Microsoft Docs
description: Batching av utdata i Event Grid på IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 0ae2261f8278c4d5e1944b01a9731afd293df20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171643"
---
# <a name="output-batching"></a>Batchbearbetning av utdata

Event Grid har stöd för att leverera mer än en händelse i en enda leverans förfrågan. Den här funktionen gör det möjligt att öka det övergripande leverans flödet utan att betala HTTP per begäran-omkostnader. Batching är inaktiverat som standard och kan aktive ras per prenumeration.

> [!WARNING]
> Den längsta tillåtna varaktigheten för att bearbeta varje leverans förfrågan ändras inte, även om abonnent koden kan vara mer arbete per batch-begäran. Tids gränsen för leverans är 60 sekunder.

## <a name="batching-policy"></a>Batching-princip

Event Gridens batching-beteende kan anpassas per prenumerant genom att ändra följande två inställningar:

* Maximalt antal händelser per batch

  Den här inställningen anger en övre gräns för antalet händelser som kan läggas till i en batch-begäran om leverans.

* Önskad batchstorlek i kilobyte

  Den här ratten används för att ytterligare styra det maximala antalet kilobyte som kan skickas via leverans förfrågan

## <a name="batching-behavior"></a>Kommando beteende

* Alla eller inga

  Event Grid fungerar med alla-eller-none-semantik. Den har inte stöd för delvis lyckad till gång till en batch-leverans. Prenumeranter bör vara noga med att bara fråga efter många händelser per batch eftersom de kan hanteras på ett rimligt sätt i 60 sekunder.

* Optimistisk batching

  Inställningarna för batch-principen är inte strikta gränser i batching-beteendet, och de följs av bästa möjliga ansträngning. Med låga frekvenser ser du ofta att batchstorleken är mindre än den begärda maximala händelsen per batch.

* Standardvärdet är inställt på av

  Som standard lägger Event Grid bara till en händelse i varje leverans förfrågan. Sättet att aktivera batching är att ange någon av de inställningar som nämns tidigare i artikeln i JSON för händelse prenumerationen.

* Standardvärden

  Du behöver inte ange båda inställningarna (maximalt antal händelser per batch och ungefärlig batchstorlek i kilobyte byte) när du skapar en händelse prenumeration. Om endast en inställning anges används standardvärden för Event Grid (konfigurerbara). Se följande avsnitt för standardvärdena och hur du åsidosätter dem.

## <a name="turn-on-output-batching"></a>Aktivera batching av utdata

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="configuring-maximum-allowed-values"></a>Konfigurera maximalt antal tillåtna värden

Följande distributions tids inställningar styr det högsta tillåtna värdet när en händelse prenumeration skapas.

| Egenskapens namn | Beskrivning |
| ------------- | ----------- | 
| `api__deliveryPolicyLimits__maxpreferredBatchSizeInKilobytes` | Högsta tillåtna värde för `PreferredBatchSizeInKilobytes` ratten. Standard `1033` .
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Högsta tillåtna värde för `MaxEventsPerBatch` ratten. Standard `50` .

## <a name="configuring-runtime-default-values"></a>Konfigurera standardvärden för körning

Följande distributions tids inställningar styr körnings standardvärdet för varje ratt när det inte anges i händelse prenumerationen. För att kunna upprepas måste minst en ratt anges för händelse prenumerationen för att aktivera batching-beteendet.

| Egenskapens namn | Beskrivning |
| ------------- | ----------- |
| `broker__defaultMaxBatchSizeInBytes` | Maximal storlek för leverans begär anden när endast `MaxEventsPerBatch` anges. Standard `1_058_576` .
| `broker__defaultMaxEventsPerBatch` | Maximalt antal händelser som ska läggas till i en batch när endast `MaxBatchSizeInBytes` anges. Standard `10` .
