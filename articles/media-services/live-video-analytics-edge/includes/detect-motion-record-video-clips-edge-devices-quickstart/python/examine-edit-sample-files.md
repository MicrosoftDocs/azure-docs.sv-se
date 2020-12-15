---
ms.openlocfilehash: 6ea9be8e7e67a8e52412e7011cfb1d33c9929191
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486776"
---
Som en del av kraven för den här snabb starten laddade du ned exempel koden till en mapp. Följ dessa steg om du vill undersöka och redigera exempel koden.

1. I Visual Studio Code går du till *src/Edge*. Du ser din *. kuvert* -fil och några mallar för distributionsmall.

    Distributions mal len refererar till distributions manifestet för gräns enheten, där variabler används för vissa egenskaper. *. Miljö* filen innehåller värdena för variablerna.
1. Gå till mappen *src/Cloud-to-Device-console-app* . Här ser du *appsettings.jspå* filen och några andra filer:
    * ***operations.jspå** _ – den lista över åtgärder som du vill att programmet ska köra.
    _ ***main.py** _ – exempel koden. Den här koden:

        _ Läser in appinställningar.
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
