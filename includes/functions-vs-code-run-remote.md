---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 4b15fec0f22db740bbd7c24fcc0acf2ad1a2d1cd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493511"
---
## <a name="run-the-function-in-azure"></a>Kör funktionen i Azure

1. Gå tillbaka till avsnittet **Azure: Functions** i sido fältet och Visa funktioner för **lokala projekt**  >  . Högerklicka (Windows) eller <kbd>CTRL-</kbd> klicka (MacOS) på `HttpExample` funktionen och välj **Kör funktion nu..**..

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Kör funktionen nu i Azure från Visual Studio Code":::

1. I **Ange brödtext för begäran** visas bröd texten för begär ande meddelandet `{ "name": "Azure" }` . Skicka meddelandet till din funktion genom att trycka på RETUR.  

1. När funktionen körs i Azure och returnerar ett svar, aktive ras ett meddelande i Visual Studio Code.
