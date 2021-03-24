---
title: 'Självstudie: distribuera video analys – identifiering av objekt och rörelse i Azure IoT Central program mal len'
description: Självstudie – i den här guiden sammanfattas stegen för att distribuera ett Azure IoT Central-program med hjälp av program mal len video analys – objekt och rörelse identifiering.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: c578da7e83a39f84e72b550038bd87dde3c61d28
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "101727472"
---
# <a name="tutorial-how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>Självstudie: Distribuera ett IoT Central program med hjälp av program mal len video analys – objekt och rörelse identifiering

En översikt över program komponenterna för nyckel *video analys – objekt och motion-identifiering* finns i [program arkitektur för objekt-och motion-identifiering](architecture-video-analytics.md).

Följande videoklipp ger en genom gång av hur du använder _program mal len video analys – objekt och rörelse identifiering_ för att distribuera en IoT Central-lösning:

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

I den här uppsättningen med självstudier får du lära dig att:

> [!div class="checklist"]
> * Distribuera programmet
> * Distribuera en IoT Edge-instans som ansluter till programmet
> * Övervaka och hantera programmet

## <a name="prerequisites"></a>Förutsättningar

En Azure-prenumeration rekommenderas. Du kan också använda en kostnads fri 7-dagars utvärderings version. Om du inte har någon Azure-prenumeration kan du skapa en på [sidan för Azure-registrering](https://aka.ms/createazuresubscription).

## <a name="deploy-the-application"></a>Distribuera programmet

Utför följande steg för att distribuera ett IoT Central program med hjälp av program mal len video analys:

1. Slutför antingen guiden [skapa ett video analys program i azure IoT Central (Yolo v3)](tutorial-video-analytics-create-app-yolo-v3.md) eller guiden [skapa en video analys i Azure IoT Central (Open &trade; )](tutorial-video-analytics-create-app-openvino.md) för att:
    - Skapa ett Azure Media Services-konto.
    - Skapa IoT Central-programmet från program mal len video analys – objekt och rörelse identifiering.
    - Konfigurera en gateway-enhet i IoT Central-programmet. Gatewayen gör det möjligt för kamera enheter att ansluta till programmet.

1. Slutför antingen NUC- [instansen skapa en IoT Edge för video analys (Linux VM)](tutorial-video-analytics-iot-edge-vm.md) eller [självstudien: skapa en IoT Edge instans för video analys (Intel)-](tutorial-video-analytics-iot-edge-nuc.md) självstudier för att:
    - Skapa en virtuell Azure-dator med Azure IoT Edge Runtime installerat. – Förbered IoT Edge-installationen som värd för video analys modulen.
    - Anslut IoT Edge-enheten till ditt IoT Central-program.

1. Slutför guiden [övervaka och hantera en video analys program](tutorial-video-analytics-manage.md) för att:
    - Lägg till kameror för objekt-och rörelse avkänning till gatewayen i ditt IoT Central-program.
    - Starta kamera bearbetningen.
    - Installera en lokal medie spelare för att visa fångade videor i AMS.
    - Visa fångade videor som visar identifierade objekt.
    - Städa upp.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med programmet kan du ta bort alla resurser som du har skapat på följande sätt:

1. I IoT Central programmet navigerar du till sidan **program** i avsnittet **Administration** . Välj sedan **Ta bort**.
1. Ta bort resurs gruppen **lva-RG** i Azure Portal.
1. På den lokala datorn stoppar du Docker-behållaren för **amp-visningsprogrammet** .

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över stegen för att distribuera och använda program mal len video analys, se

> [!div class="nextstepaction"]
> [Skapa ett video analys program i Azure IoT Central (Yolo v3)](tutorial-video-analytics-create-app-yolo-v3.md) eller

> [!div class="nextstepaction"]
> Kom igång genom att [skapa en video analys i Azure &trade; IoT Central (](tutorial-video-analytics-create-app-openvino.md) openman).