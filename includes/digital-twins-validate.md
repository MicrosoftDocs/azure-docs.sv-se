---
author: baanders
description: ta med fil som beskriver hur du verifierar Azure Digitals dubbla modeller
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 58ae9c187f50bb7eb3f1ccc2a618275885121267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96027208"
---
> [!TIP]
> När du har skapat en modell rekommenderar vi att du validerar dina modeller offline innan du laddar upp dem till din Azure Digital-instansen.

Det finns ett oberoende exempel som kan användas för att validera modell dokument för att kontrol lera att DTDL är rätt innan du laddar upp den till din instans. Den finns här: [**DTDL validator-exempel**](/samples/azure-samples/dtdl-validator/dtdl-validator).

* DTDL validator-exemplet bygger på ett .NET DTDL parser-bibliotek, som är tillgängligt på NuGet som ett bibliotek på klient sidan: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Du kan också använda biblioteket direkt för att skapa en egen verifierings lösning. När du använder parser-biblioteket ska du se till att använda en version som är kompatibel med den version som Azure Digital är igång. Detta är för närvarande version *3.12.4*.

Du kan lära dig mer om verifierings exemplet och tolknings biblioteket, inklusive användnings exempel, i [*How-to: parsa och validera modeller*](../articles/digital-twins/how-to-parse-models.md).