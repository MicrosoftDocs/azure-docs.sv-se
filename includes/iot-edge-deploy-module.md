---
title: ta med fil
description: ta med fil
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 6b9ec2017ffa5d4e4148b441aa23ed2eca6ee8b8
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628915"
---
En av de viktigaste funktionerna i Azure IoT Edge är att distribuera kod till dina IoT Edge-enheter från molnet. *IoT Edge moduler* är körbara paket som implementeras som behållare. I det här avsnittet ska du distribuera en fördefinierad modul från [avsnittet IoT Edge moduler i Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) direkt från Azure IoT Hub.

Den modul som du distribuerar i det här avsnittet simulerar en sensor och skickar genererade data. Modulen är användbar kod när du kör igång med IoT Edge eftersom du kan använda simulerade data för utveckling och testning. Om du vill se exakt vad den här modulen gör kan du visa [källkoden för den simulerade temperatursensorn](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Följ de här stegen för att distribuera din första modul från Azure Marketplace.

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din IoT-hubb.

1. Välj **IoT Edge** under **Automatisk enhets hantering** på menyn till vänster.

1. Välj enhets-ID för mål enheten i listan med enheter.

1. I den övre stapeln väljer du **Ange moduler**.

   ![Skärm bild som visar att du väljer Ange moduler.](./media/iot-edge-deploy-module/select-set-modules.png)

1. Öppna den nedrullningsbara menyn **Lägg till** under **IoT Edge moduler** och välj sedan **Marketplace-modul**.

   ![Skärm bild som visar den nedrullningsbara menyn Lägg till.](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. I **IoT Edge module Marketplace** söker du efter och väljer `Simulated Temperature Sensor` modulen.

   Modulen läggs till i avsnittet IoT Edge moduler med önskad **körnings** status.

1. Välj **Nästa: vägar** för att fortsätta till nästa steg i guiden.

   ![Skärm bild som visar fortsätter till nästa steg när modulen har lagts till.](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. På fliken **vägar** tar du bort standard vägen, **dirigerar** och väljer sedan **Nästa: granska + skapa** för att fortsätta till nästa steg i guiden.

   >[!Note]
   >Vägar konstrueras med hjälp av namn-och värdepar. Du bör se två vägar på den här sidan. Standard vägen, **Route**, skickar alla meddelanden till IoT Hub (som kallas `$upstream` ). En andra väg, **SimulatedTemperatureSensorToIoTHub**, skapades automatiskt när du lade till modulen från Azure Marketplace. Den här vägen skickar alla meddelanden från modulen simulerad temperatur till IoT Hub. Du kan ta bort standard vägen eftersom den är redundant i det här fallet.

   ![Skärm bild som visar borttagning av standard väg och flyttas sedan till nästa steg.](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. Granska JSON-filen och välj sedan **skapa**. JSON-filen definierar alla moduler som du distribuerar till din IoT Edge-enhet. Du ser **SimulatedTemperatureSensor** -modulen och de två modulerna för körning, **edgeAgent** och **edgeHub**.

   >[!Note]
   >När du skickar en ny distribution till en IoT Edge-enhet distribueras ingenting till enheten. I stället frågar enheten regelbundet IoT Hub efter nya instruktioner. Om enheten hittar ett uppdaterat distributionsmanifest använder den informationen om den nya distributionen för att hämta modulavbildningarna från molnet och börjar sedan köra modulerna lokalt. Den här processen kan ta några minuter.

1. När du har skapat modulens distributions information återgår guiden till enhets informations sidan. Visa distributions status på fliken **moduler** .

   Du bör se tre moduler: **$edgeAgent**, **$edgeHub** och **SimulatedTemperatureSensor**. Om en eller flera av modulerna har värdet **Ja** under **anges i distributionen** , men inte under **rapporteras av enheten**, startar IoT Edge-enheten fortfarande. Vänta några minuter och uppdatera sedan sidan.

   ![Skärm bild som visar simulerad temperatur sensor i listan över distribuerade moduler.](./media/iot-edge-deploy-module/view-deployed-modules.png)
