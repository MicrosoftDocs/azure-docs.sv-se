---
ms.openlocfilehash: 426c735dfd0d015cdc1a734edde9d336fb88cfbc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97486814"
---
Som en del av kraven för den här snabb starten laddade du ned exempel koden till en mapp. Följ dessa steg om du vill undersöka och redigera exempel koden.

1. I Visual Studio Code går du till *src/Edge*. Du ser din *. kuvert* -fil och några mallar för distributionsmall.

    Distributions mal len refererar till distributions manifestet för gräns enheten, där variabler används för vissa egenskaper. *. Miljö* filen innehåller värdena för variablerna.
1. Gå till mappen *src/Cloud-to-Device-console-app* . Här ser du *appsettings.jspå* filen och några andra filer:
    * ***C2D-console-app. CSPROJ*** – projekt filen för Visual Studio Code.
    * ***operations.jsi*** listan över åtgärder som du vill att programmet ska köra.
    * ***Program. cs*** -kod för exempel program. Den här koden:

        * Läser in appinställningar.
        * Anropar direkta metoder som visar IoT Edge modulen för video analys i real tid. Du kan använda modulen för att analysera direktuppspelade video strömmar genom att anropa dess [direkta metoder](../../../direct-methods.md). 
        * Pausar så att du kan granska utdata från programmet i **terminalfönstret** och granska de händelser som genererats av modulen i fönstret **utdata** .
        * Anropar direkta metoder för att rensa resurser.

1. Redigera *operations.jspå* filen:
    * Ändra länken till graf-topologin:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json"`
    * Under `GraphInstanceSet` redigerar du namnet på diagram sto pol Ogin så att det matchar värdet i föregående länk:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Redigera RTSP-URL: en för att peka på video filen:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * Under `GraphTopologyDelete` redigerar du namnet:

        `"name": "EVRToFilesOnMotionDetection"`
