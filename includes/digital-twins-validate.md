---
author: baanders
description: ta med fil som beskriver hur du verifierar Azure Digitals dubbla modeller
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 32c02750f99f1aa6733d9c6dd673ffe4964978f4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303775"
---
> [!TIP]
> När du har skapat en modell rekommenderar vi att du validerar dina modeller offline innan du laddar upp dem till din Azure Digital-instansen.

Det finns ett oberoende exempel som kan användas för att validera modell dokument för att kontrol lera att DTDL är rätt innan du laddar upp den till din instans. Den finns här: [**DTDL validator-exempel**](/samples/azure-samples/dtdl-validator/dtdl-validator).

DTDL validator-exemplet bygger på ett .NET DTDL parser-bibliotek, som är tillgängligt på NuGet som ett bibliotek på klient sidan: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Du kan också använda biblioteket direkt för att skapa en egen verifierings lösning. När du använder parser-biblioteket ska du se till att använda en version som är kompatibel med den version som Azure Digital är igång. Detta är för närvarande version *3.12.4*.

Du kan lära dig mer om verifierings exemplet och tolknings biblioteket, inklusive användnings exempel, i [*How-to: parsa och validera modeller*](../articles/digital-twins/how-to-parse-models.md).