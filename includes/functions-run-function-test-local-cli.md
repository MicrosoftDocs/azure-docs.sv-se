---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 11d426016cfe1a8a9ff843da518f57c08881be5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96842354"
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

1. Kopiera URL: en för din `HttpExample` funktion från utdata till en webbläsare och Lägg till frågesträngen och `?name=<YOUR_NAME>` gör den fullständiga URL: en som `http://localhost:7071/api/HttpExample?name=Functions` . Webbläsaren ska visa ett meddelande som `Hello Functions` :

    ![Resultatet av funktionen körs lokalt i webbläsaren](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Terminalen där du startade projektet visar också logg data när du gör förfrågningar.

1. När du är klar använder du **CTRL** + **C** och väljer `y` att stoppa funktions värden.
