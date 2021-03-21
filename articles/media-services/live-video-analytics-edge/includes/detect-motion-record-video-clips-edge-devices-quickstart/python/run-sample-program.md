---
ms.openlocfilehash: c99d2489efe7c46b8d50b08861fcbbcd6f8a1966
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97531922"
---
1. Öppna fliken **tillägg** i Visual Studio Code (eller tryck på Ctrl + Shift + X) och Sök efter Azure-IoT Hub.
1. Högerklicka och välj **Inställningar för tillägg**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Tilläggs inställningar":::
1. Sök och aktivera "Visa utförligt meddelande".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Visa utförligt meddelande":::
1. Starta en felsökningssession genom att välja F5-nyckeln. **Terminalfönstret** skriver ut vissa meddelanden.
1. *operations.js* koden anropar de direkta metoderna `GraphTopologyList` och `GraphInstanceList` . Om du har rensat resurser efter tidigare snabb starter kommer den här processen att returnera tomma listor och sedan pausa. Välj Retur.
    
    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
  
  **Terminalfönstret** visar nästa uppsättning med direkta metod anrop:  
  
  * Ett anrop till `GraphTopologySet` som använder `topologyUrl` 
  * Ett anrop till `GraphInstanceSet` som använder följande text:
  
  ```
  {
    "@apiVersion": "2.0",
    "name": "Sample-Graph",
    "properties": {
      "topologyName": "EVRToFilesOnMotionDetection",
      "description": "Sample graph description",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        },
        {
          "name": "rtspPassword",
          "value": "testpassword"
        }
      ]
    }
  }
  ```
    
  * Ett anrop till `GraphInstanceActivate` som startar graf-instansen och film flödet.
  * Ett andra anrop till `GraphInstanceList` som visar att graf-instansen är i körnings tillstånd.
1. Utdata i **terminalfönstret** pausas vid `Press Enter to continue` . Välj inte retur än. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder som du har anropat.
1. Växla till fönstret **utdata** i Visual Studio Code. Du ser meddelanden om att live video analys på IoT Edge modul skickas till IoT Hub. I följande avsnitt i den här snabb starten beskrivs dessa meddelanden.
1. Medie diagrammet fortsätter att köra och skriva ut resultat. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa medie diagrammet går du tillbaka till **terminalfönstret** och väljer RETUR. 

    Nästa serie anrop rensar resurserna:

    * Ett anrop för att `GraphInstanceDeactivate` inaktivera graf-instansen.
    * Ett anrop för att `GraphInstanceDelete` ta bort instansen.
    * Ett anrop för att `GraphTopologyDelete` ta bort topologin.
    * Ett sista anrop till `GraphTopologyList` visar att listan nu är tom.
