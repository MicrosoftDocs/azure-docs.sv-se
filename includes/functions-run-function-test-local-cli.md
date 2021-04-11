---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d400533039ea74a878cb8e543c22de02ee77e4f5
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283096"
---
## <a name="run-the-function-locally"></a>Köra funktionen lokalt

1. Kör din funktion genom att starta den lokala Azure Functions runtime-värden från mappen *LocalFunctionProj* :

    ```
    func start
    ```

    I slutet av utdata bör följande rader visas: 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Om HttpExample inte visas som du ser ovan, startade du förmodligen värden från utsidan av projektets rotmapp. I så fall kan du använda **CTRL** + **C** för att stoppa värden, navigera till projektets rotmapp och köra föregående kommando igen.

1. Kopiera URL: en för din `HttpExample` funktion från utdata till en webbläsare och Lägg till frågesträngen och `?name=<YOUR_NAME>` gör den fullständiga URL: en som `http://localhost:7071/api/HttpExample?name=Functions` . I webbläsaren ska ett svarsmeddelande visas som visar att värdet för frågesträngen har återställts. Terminalen där du startade projektet visar också logg data när du gör förfrågningar.

1. När du är klar använder du **CTRL** + **C** och väljer `y` att stoppa funktions värden.
