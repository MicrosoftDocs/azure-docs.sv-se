---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/15/2020
ms.author: glenga
ms.openlocfilehash: 4cbf179658ddc13aca9bf30934dc28ab0cf5fd9d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081027"
---
| Egenskap | Beskrivning |
|--|--|
|**`<DESTINATION>`**| Målet som loggarna skickas till. Giltiga värden är `AppInsights` och `Blob` .<br/>`AppInsights`Se till att [Application Insights är aktive rad i din Function-app](../articles/azure-functions/configure-monitoring.md#enable-application-insights-integration)när du använder.<br/>När du ställer in målet till `Blob` skapas loggar i en BLOB-behållare med namnet `azure-functions-scale-controller` i standard lagrings kontot som angetts i `AzureWebJobsStorage` program inställningen. |
|**`<VERBOSITY>`** | Anger loggnings nivån. Värden som stöds är `None` , `Warning` och `Verbose` .<br/>När den är inställd på `Verbose` loggar skalnings styrenheten av en orsak för varje ändring i antalet arbets uppgifter, samt information om utlösare som ingår i dessa beslut. Utförliga loggar innehåller Utlös ande varningar och de hash-värden som används av utlösarna innan och efter att styrenheten körs. |

> [!TIP]
> Tänk på att när du lämnar den aktiverade loggningen av skalnings styrenheten, påverkar det [potentiella kostnader för att övervaka din funktions-app](../articles/azure-functions/functions-monitoring.md#application-insights-pricing-and-limits). Överväg att aktivera loggning tills du har samlat in tillräckligt med data för att förstå hur skalnings styrenheten fungerar och sedan inaktivera den.
