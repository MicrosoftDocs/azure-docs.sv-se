---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493961"
---
Med Visual Studio Code kan du lägga till bindningar till function.jsi filen genom att följa en behändig uppsättning prompter. 

Om du vill lägga till en bindning öppnar du kommandot pall (F1) och skriver **Azure Functions: Lägg till bindning...**, Välj funktionen för den nya bindningen och följ sedan anvisningarna, som varierar beroende på vilken typ av bindning som läggs till i funktionen. 

I följande exempel uppmanas du att definiera en ny bindning för lagring av utdata:

| Prompt | Värde | Beskrivning |
| -------- | ----- | ----------- |
| **Välj bindnings riktning** | `out` | Bindningen är en utgående bindning. |
| **Välj bindning med riktning** | `Azure Queue Storage` | Bindningen är en Azure Storage Queue-bindning. |
| **Namnet som används för att identifiera den här bindningen i din kod** | `msg` | Namn som identifierar den bindnings parameter som refereras till i din kod. |
| **Kön som meddelandet ska skickas till** | `outqueue` | Namnet på kön som bindningen skriver till. När *queueName* inte finns skapar bindningen den när den används första gången. |
| **Välj inställning från "local.settings.jspå"** | `MyStorageConnection` | Namnet på en program inställning som innehåller anslutnings strängen för lagrings kontot. `AzureWebJobsStorage`Inställningen innehåller anslutnings strängen för det lagrings konto som du skapade med Function-appen. |

Du kan också högerklicka (Ctrl + klicka på macOS) direkt på **function.jspå** filen i mappen funktion, Välj **Lägg till bindning** och följ samma prompter.

I det här exemplet läggs följande bindning till i `bindings` matrisen i function.jsfilen:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```