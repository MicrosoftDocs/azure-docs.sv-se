---
title: ta med fil
description: ta med fil
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2f3e4bf640b8da31a7fa4d818b94b0372d3026b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763437"
---
Exempel programmet innehåller två simulerade enheter och en IoT Edge Gateway. Följande självstudier visar två metoder för att experimentera med och förstå funktionerna i gatewayen:

* Skapa IoT Edge gateway i en virtuell Azure-dator och Anslut en simulerad kamera.
* Skapa IoT Edge gateway på en riktig enhet, till exempel en Intel-NUC och Anslut en riktig kamera.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Använd program mal len Azure IoT Central video analys för att skapa ett program för åter försäljnings lager
> * Anpassa program inställningarna
> * Skapa en enhets mall för en IoT Edge gateway-enhet
> * Lägga till en gateway-enhet i IoT Central-programmet

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här själv studie serien behöver du:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).
* Om du använder en riktig kamera måste du ha anslutning mellan IoT Edge-enheten och kameran och du behöver **real tids strömnings protokoll** kanal.

## <a name="initial-setup"></a>Första konfigurationen

I de här självstudierna uppdaterar och använder du flera konfigurationsfiler. De ursprungliga versionerna av dessa filer är tillgängliga i [lva-Gateway GitHub-](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) lagringsplatsen. Lagrings platsen innehåller en [Scratchpad](https://github.com/Azure/live-video-analytics/blob/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) -textfil där du kan hämta och använda för att registrera konfigurations värden från de tjänster som du distribuerar. Den här filen hjälper dig att slutföra senare steg i självstudierna.

Skapa en mapp med namnet *lva-Configuration* på den lokala datorn för att spara kopior av filerna. Högerklicka på var och en av följande länkar och välj **Spara som** för att spara filen i mappen *lva* :
