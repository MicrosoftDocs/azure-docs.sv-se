---
title: Själv studie kurs om IoT Digital Distribution Center | Microsoft Docs
description: En själv studie kurs om program mal len för digital distributions Center för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 59b688803260220866fc331506429a5990c8760a
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346205"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Självstudie: Distribuera och gå igenom en app-mall för digital distributions Center



Den här självstudien visar hur du kommer igång genom att distribuera en IoT Central **digital distributions Center** -Programmall. Du får lära dig hur du distribuerar mallen, vad som ingår i rutan och vad du kanske vill göra härnäst.

I den här självstudien får du lära dig att 
* Skapa ett program för digital distributions Center 
* Gå igenom programmet 

## <a name="prerequisites"></a>Förutsättningar
* Inga särskilda krav krävs för att distribuera den här appen
* Vi rekommenderar att du har en Azure-prenumeration, men du kan även prova utan den

## <a name="create-digital-distribution-center-application-template"></a>Skapa app-mall för digital distributions Center

Du kan skapa program med följande steg

1. Gå till webbplatsen för Azure IoT Central Application Manager. Välj **Bygg** i det vänstra navigerings fältet och klicka sedan på fliken **detalj handel** .

    :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-homepage.png" alt-text="App-mall för digital distributions Center":::
1. Välj fliken **butik** och välj **skapa app** under appen **digital distribution Center**

1. **Skapa app** öppnar nytt program formulär och fyller i den begärda informationen som visas nedan.
   **Program namn**: du kan använda det föreslagna standard namnet eller ange ett eget program namn.
   **URL**: du kan använda den föreslagna standard-URL: en eller ange en egen, unik minnes adress. Sedan rekommenderas standardinställningen om du redan har en Azure-prenumeration. Du kan börja med en kostnads fri utvärderings prenumerations plan på 7 dagar och välja att konvertera till en standard pris plan när som helst innan det kostnads fria slutet går ut.
   **Fakturerings information**: katalog, Azure-prenumeration och regions information krävs för att etablera resurserna.
   **Skapa**: Välj Skapa längst ned på sidan för att distribuera programmet.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create.png" alt-text="Skärm bild som visar hur du skapar en app från program mal len Digital distributions Center":::

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create-billinginfo.png" alt-text="Skärm bild som visar fakturerings alternativen när du skapar programmet":::

## <a name="walk-through-the-application-dashboard"></a>Gå igenom program instrument panelen 

När du har distribuerat app-mallen är standard instrument panelen en distributions Center operatör med fokus på portalen. Northwind-näringsidkare är en fiktiv distributions Center lösnings leverantör som hanterar transport band system. 

På den här instrument panelen visas en gateway och en kamera som fungerar som en IoT-enhet. Gateway tillhandahåller telemetri om paket som giltig, ogiltig, oidentifierad och storlek tillsammans med tillhör ande enhets dubbla egenskaper. Alla underordnade kommandon körs på IoT-enheter, till exempel en kamera. Den här instrument panelen är förkonfigurerad för att demonstrera den kritiska distributions Center-aktiviteten för enhets åtgärder.

Instrument panelen är logiskt strukturerad för att Visa enhets hanterings funktionerna i Azure IoT gateway och IoT-enheten.  
   * Du kan utföra Gateway-kommandot & kontroll uppgifter
   * Hantera alla kameror som ingår i lösningen. 

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar instrument panelen för digital distributions Center](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Enhets mall

Klicka på fliken Mallar på enheten så visas kapacitets modellen för gateway. En kapacitets modell är strukturerad runt två olika gränssnitts **kamera** och **Gateway för digital distribution**

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar enhets mal len för digital distribution gateway i programmet](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Kamera** – det här gränssnittet ordnar alla Camera-/regionsspecifika kommando funktioner 

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar kamera gränssnittet i enhets mal len för digital distribution Gateway](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Gateway för digital distribution** – det här gränssnittet representerar all telemetri från kamera, Cloud Defined Device, med dubbla egenskaper och gateway-information.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar gränssnittet för digital distribution gateway i enhets mal len för digital distribution Gateway](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Gateway-kommandon
Det här gränssnittet ordnar alla funktioner i Gateway-kommandot

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar gränssnittet för gateway-kommandon i enhets mal len för digital distribution Gateway](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Regler
Välj fliken regler för att se två olika regler som finns i den här program mal len. Dessa regler är konfigurerade för e-postaviseringar till operatörerna för ytterligare undersökningar.

 **Avisering om för många ogiltiga paket** – den här regeln utlöses när kameran identifierar ett stort antal ogiltiga paket som flödar genom transport systemet.
 
**Stort paket** – den här regeln utlöses om kameran identifierar enorma paket som inte kan kontrol leras för kvaliteten. 

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar listan över regler i appen Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Jobb
Välj fliken jobb för att se fem olika jobb som finns som en del av den här program mal len: du kan utnyttja jobb funktionen för att utföra åtgärder i hela lösningen. Här är digitala distributions Center jobb använder enhets kommandona & dubbla funktioner för att utföra uppgifter som,
   * Kalibrera kameran innan du påbörjar paket identifieringen 
   * regelbundet uppdatera inbyggd kamera
   * ändra intervallet för telemetri för att hantera data uppladdning

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar listan över jobb i appen Digital Distribution Center](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte kommer att fortsätta att använda det här programmet tar du bort program mal len genom att gå till **administrations**  >  **program inställningar** och klicka på **ta bort**.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar hur du tar bort programmet när du är klar med det](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Nästa steg
* Läs mer om lösnings arkitekturen för digital distributions Center 
> [!div class="nextstepaction"]
> [koncept för digital distributions Center](./architecture-digital-distribution-center.md)
* Läs mer om andra [IoT Central mallar för butik](./overview-iot-central-retail.md)
* Läs mer om IoT Central referera till [IoT Central översikt](../core/overview-iot-central.md)
