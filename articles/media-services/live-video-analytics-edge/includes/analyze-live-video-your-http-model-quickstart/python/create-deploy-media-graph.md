---
ms.openlocfilehash: 6aeded077c20e59b3f9b3863a9956596382d3a82
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97531785"
---
### <a name="examine-and-edit-the-sample-files"></a>Granska och redigera exempelfilerna

Som en del av förutsättningarna hämtade du exempel koden till en mapp. Följ dessa steg om du vill granska och redigera exempelfilerna.

1. I Visual Studio Code går du till *src/Edge*. Du ser din *. kuvert* -fil och några mallar för distributionsmall.

    Distributions mal len refererar till distributions manifestet för gräns enheten. Den innehåller några värden för plats hållare. *. Miljö* filen innehåller värdena för variablerna.

1. Gå till mappen *src/Cloud-to-Device-console-app* . Här ser du *appsettings.jspå* filen och några andra filer:

    * ***C2D-console-app. CSPROJ** _-projekt filen för Visual Studio Code.
    _ ***operations.jspå** _ – en lista över de åtgärder som du vill att programmet ska köra.
    _ ***Program.cs** _ – exempel koden. Den här koden:

        _ Läser in appinställningar.
        * Anropar direkta metoder som visar IoT Edge modulen för video analys i real tid. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](../../../direct-methods.md).
        * Pausar så att du kan granska programmets utdata i **terminalfönstret** och granska de händelser som har genererats av modulen i fönstret **utdata** .
        * Anropar direkta metoder för att rensa resurser.


1. Redigera *operations.jspå* filen:
    * Ändra länken till graf-topologin:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`

    * Under `GraphInstanceSet` redigerar du namnet på diagram sto pol Ogin så att det matchar värdet i föregående länk:

      `"topologyName" : "InferencingWithHttpExtension"`

    * Under `GraphTopologyDelete` redigerar du namnet:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Skapa och distribuera distributions manifestet för IoT Edge

1. Högerklicka på filen *src/Edge/deployment.yolov3.template.js* och välj sedan **skapa IoT Edge distributions manifest**.

    ![Generera IoT Edge distributions manifest](../../../media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    Filen *deployment.yolov3.amd64.jspå* manifest filen skapas i mappen *src/Edge/config* .

1. Hoppa över det här steget om du har slutfört snabb starten för att [identifiera rörelse och generera händelser](../../../detect-motion-emit-events-quickstart.md) . 

    Annars, nära **Azure IoT Hub** -fönstret i det nedre vänstra hörnet, väljer du ikonen **fler åtgärder** och väljer sedan **Ange IoT Hub anslutnings sträng**. Du kan kopiera strängen från *appsettings.jsi* filen. Eller, för att se till att du har konfigurerat rätt IoT-hubb i Visual Studio Code, använder du [kommandot SELECT IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Ange IoT-hubbens anslutningssträng](../../../media/quickstarts/set-iotconnection-string.png)

1. Högerklicka på *src/Edge/config/deployment.yolov3.amd64.jspå* och välj **skapa distribution för en enskild enhet**. 

    ![Skapa distribution för en enskild enhet](../../../media/quickstarts/create-deployment-single-device.png)

1. När du uppmanas att välja en IoT Hub-enhet väljer du **lva-Sample-Device**.
1. Efter cirka 30 sekunder, i det nedre vänstra hörnet i fönstret, uppdaterar du Azure IoT Hub. Gräns enheten visar nu följande distribuerade moduler:

    * Video analys-modulen i real tid med namnet **lvaEdge**
    * Modulen **rtspsim** , som simulerar en RTSP-server och fungerar som källa för en Live-videofeed
    * **Yolov3** -modulen, som är den yolov3 objekt identifierings modell som tillämpar dator syn på avbildningarna och returnerar flera klasser av objekt typer
 
      ![Moduler som distribueras i gräns enheten](../../../media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Förbereda övervakning av händelser

1. Öppna fliken **tillägg** i Visual Studio Code (eller tryck på Ctrl + Shift + X) och Sök efter Azure-IoT Hub.
1. Högerklicka och välj **Inställningar för tillägg**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Tilläggs inställningar":::
1. Sök och aktivera "Visa utförligt meddelande".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Visa utförligt meddelande":::
1. Högerklicka på Live Video Analytics-enheten och välj **starta övervakning inbyggd händelse slut punkt**. Du behöver det här steget för att övervaka IoT Hub händelser i fönstret **utdata** i Visual Studio Code. 

   ![Starta övervakning](../../../media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Kör exempel programmet

1. Om du vill starta en felsökningssession väljer du F5-tangenten. Du ser meddelanden som skrivs ut i **terminalfönstret** .
1. *operations.jsvid* kod börjar med anrop till direkta metoder `GraphTopologyList` och `GraphInstanceList` . Om du har rensat resurser efter att du har slutfört tidigare snabb starter, returnerar den här processen tomma listor och pausar sedan. Fortsätt genom att välja retur nyckeln.

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

     * Ett anrop till `GraphTopologySet` som använder föregående `topologyUrl`
     * Ett anrop till `GraphInstanceSet` som använder följande text:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithHttpExtension",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
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

     * Ett anrop till `GraphInstanceActivate` som startar graf-instansen och video flödet
     * Ett andra anrop till `GraphInstanceList` som visar att graf-instansen är i körnings tillstånd
1. Utdata i **terminalfönstret** pausas vid en `Press Enter to continue` prompt. Välj inte retur än. Rulla upp för att se nytto laster för JSON-svar för de direkta metoder du anropade.
1. Växla till fönstret **utdata** i Visual Studio Code. Du ser meddelanden om att live video analys på IoT Edge modul skickas till IoT Hub. I följande avsnitt i den här snabb starten beskrivs dessa meddelanden.
1. Medie diagrammet fortsätter att köra och skriva ut resultat. RTSP-simulatorn håller på att upprepa käll videon. Om du vill stoppa medie diagrammet går du tillbaka till **terminalfönstret** och väljer RETUR. 

    Nästa serie anrop rensar resurser:
      * Ett anrop för att `GraphInstanceDeactivate` inaktivera graf-instansen.
      * Ett anrop för att `GraphInstanceDelete` ta bort instansen.
      * Ett anrop för att `GraphTopologyDelete` ta bort topologin.
      * Ett sista anrop till `GraphTopologyList` visar att listan är tom.
