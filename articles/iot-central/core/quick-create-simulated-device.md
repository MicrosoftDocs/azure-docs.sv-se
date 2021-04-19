---
title: Snabbstart – Lägga till en simulerad enhet i Azure IoT Central
description: Den här snabbstarten visar hur du skapar en enhetsmall och lägger till en simulerad enhet i IoT Central program.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 0396c028c8c0e1a18baf037fed4efc21aad944f1
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715108"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Snabbstart: Lägga till en simulerad enhet i ditt IoT Central program

En enhetsmall definierar funktionerna för en enhet som ansluter till IoT Central program. Funktionerna omfattar telemetri som enheten skickar, enhetsegenskaper och de kommandon som en enhet svarar på. Med hjälp av en enhetsmall kan du lägga till både verkliga och simulerade enheter i ett program. Simulerade enheter är användbara för att testa beteendet för ditt IoT Central innan du ansluter riktiga enheter.

I den här snabbstarten lägger du till en enhetsmall för en ESP32-Azure IoT Kit-utvecklingskort och skapar en simulerad enhet. För att slutföra den här snabbstarten behöver du ingen riktig enhet, du arbetar med en simulering av enheten. En ESP32-enhet:

* Skickar telemetri, till exempel temperatur.
* Rapporterar enhetsspecifika egenskaper, till exempel den högsta temperaturen sedan enheten startades om.
* Svarar på kommandon som omstart.
* Rapporterar allmänna enhetsegenskaper, till exempel version av inbyggd programvara och serienummer.

## <a name="prerequisites"></a>Förutsättningar

Slutför [snabbstarten Skapa Azure IoT Central program](./quick-deploy-iot-central.md) för att skapa ett IoT Central-program med hjälp av mallen **Anpassad app > anpassat** program.

## <a name="create-a-device-template"></a>Skapa en enhetsmall

Om du vill lägga till en ny enhetsmall i programmet väljer du **fliken Enhetsmallar** i den vänstra rutan.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Skärmbild som visar en tom lista över enhetsmallar":::

En enhetsmall innehåller en enhetsmodell som definierar:

* Telemetrin som enheten skickar.
* Enhetsegenskaper.
* De kommandon som enheten svarar på.

### <a name="add-a-device-template"></a>Lägga till en enhetsmall

Det finns flera alternativ för att lägga till en enhetsmodell i ditt IoT Central program. Du kan skapa en modell från grunden, importera en modell från en fil eller välja en enhet från enhetskatalogen. IoT Central stöder också *en enhet först-metod* där programmet automatiskt importerar en modell från en lagringsplats när en verklig enhet ansluter för första gången.

I den här snabbstarten väljer du en enhet från enhetskatalogen för att skapa en enhetsmall.

Följande steg visar hur du använder enhetskatalogen för att importera modellen för en **ESP32-enhet.** Dessa enheter skickar telemetri, till exempel temperatur, till ditt program:

1. Om du vill lägga till en ny enhetsmall **väljer du +** Ny på sidan **Enhetsmallar.**

1. På sidan **Välj typ** rullar du nedåt tills du hittar panelen **ESP32-Azure IoT Kit** i avsnittet Använda en förkonfigurerad **enhetsmall.**

1. Välj panelen **ESP32-Azure IoT Kit** och välj sedan **Nästa: Granska.**

1. På sidan **Granska** väljer du **Skapa**.

1. Efter några sekunder kan du se den nya enhetsmallen:

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="Skärmbild som visar enhetsmallen för ESP32-enhet":::

    Namnet på mallen är **Sensor Controller**. Modellen innehåller komponenter som Sensor **Controller,** **SensorTemp** och **Device Information.** Komponenter definierar funktionerna för en ESP32-enhet. Funktionerna omfattar telemetri, egenskaper och kommandon.

### <a name="add-cloud-properties"></a>Lägga till molnegenskaper

En enhetsmall kan innehålla molnegenskaper. Molnegenskaper finns bara i IoT Central och skickas aldrig till eller tas emot från en enhet. Så här lägger du till två molnegenskaper:

1. Välj **Molnegenskaper och** sedan **+ Lägg till molnegenskap**. Använd informationen i följande tabell för att lägga till två molnegenskaper i enhetsmallen:

    | Visningsnamn      | Semantisk typ | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Inget          | Datum   |
    | Kundnamn     | Inget          | Sträng |

1. Välj **Spara** för att spara ändringarna:

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Skärmbild som visar två molnegenskaper":::

## <a name="views"></a>Vyer

Du kan anpassa programmet för att visa relevant information om enheten. Med anpassningar kan andra hantera de enheter som är anslutna till programmet. Du kan skapa två typer av vyer för att interagera med enheter:

* Formulär för att visa och redigera enhets- och molnegenskaper.
* Instrumentpaneler för att visualisera enheter, inklusive telemetri som de skickar.

### <a name="default-views"></a>Standardvyer

Standardvyer är ett snabbt sätt att komma igång med att visualisera viktig enhetsinformation. Du kan ha upp till tre standardvyer som genereras för enhetsmallen:

* I **vyn** Kommandon kan du skicka kommandon till enheten.
* I **översiktsvyn** används diagram och mått för att visa enhettelemetri.
* Vyn **Om** visar enhetsegenskaper.

Välj **noden** Vyer i enhetsmallen. Du kan se att IoT Central genererat **en översikt,** en **Om-vy** och en **rådatavy** åt dig när du lade till mallen.

Så här lägger du till ett nytt formulär för att hantera enheten:

1. Välj **noden Vyer** och välj sedan panelen Redigera **enhet och molndata för** att lägga till en ny vy.

1. Ändra formulärnamnet till **Hantera enhet**.

1. Välj **molnegenskaperna** **Kundnamn och Senaste** servicedatum och **egenskapen Måltemperatur.** Välj sedan **Lägg till avsnitt:**

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Skärmbild som visar ett nytt formulär som lagts till i enhetsmallen":::

1. Välj **Spara** för att spara det nya formuläret.

## <a name="publish-device-template"></a>Publicera enhetsmall

Innan du kan skapa en simulerad enhet eller ansluta en riktig enhet måste du publicera enhetsmallen. Även IoT Central publicerade mallen när du först skapade den, måste du publicera den uppdaterade versionen.

Så här publicerar du en enhetsmall:

1. Gå till **enhetsmallen sensorstyrenhet** från **sidan Enhetsmallar.**

1. Välj **Publicera** i kommandofältet överst på sidan.

1. I dialogrutan som visas väljer du **Publicera**.

När du har publicerat en enhetsmall visas den på **sidan** Enheter. I en publicerad enhetsmall kan du inte redigera en enhetsmodell utan att skapa en ny version. Du kan dock ändra molnegenskaper, anpassningar och vyer i en publicerad enhetsmall utan versionshantering. När du har gjort några ändringar väljer **du Publicera**  för att skicka ändringarna så att verkliga och simulerade enheter kan användas.

## <a name="add-a-simulated-device"></a>Lägga till en simulerad enhet

Om du vill lägga till en simulerad enhet i programmet använder du **esp32-enhetsmallen** som du skapade.

1. Om du vill lägga till en ny **enhet väljer du** Enheter i den vänstra rutan. Fliken **Enheter** visar Alla **enheter och** **enhetsmallen Sensor Controller** för ESP32-enheten. Välj **Sensor Controller**.

1. Om du vill lägga till en simulerad DevKit-enhet väljer **du + Ny**. Använd det föreslagna **enhets-ID:t** eller ange ditt eget. Ett enhets-ID kan innehålla bokstäver, siffror och `-` tecken. Du kan också ange ett namn för den nya enheten. Kontrollera att Simulera **den här enheten** är inställd på **Ja** och välj sedan **Skapa.**

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="Skärmbild som visar den simulerade Sensor Controller-enheten":::

Nu kan du interagera med vyerna som skapades tidigare med hjälp av simulerade data:

1. Välj den simulerade enheten på **sidan** Enheter

    * Vyn **Översikt** visar ett diagram över den simulerade telemetrin:

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="Skärmbild som visar översiktssidan för simulerad enhet":::

    * Vyn **Om** visar egenskapsvärden.

    * I **vyn** Kommandon kan du köra kommandon, till exempel **starta** om enheten.

    * Vyn **Hantera enheter** är det formulär som du skapade för att hantera enheten.

    * I **vyn Rådata** kan du visa råtelemetri och egenskapsvärden som skickas av enheten. Den här vyn är användbar för felsökning av enheter.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har  du lärt dig hur du skapar en sensorkontrollantenhetsmall för en ESP32-enhet och lägger till en simulerad enhet i programmet.

Om du vill veta mer om övervakning av enheter som är anslutna till ditt program fortsätter du till snabbstarten:

> [!div class="nextstepaction"]
> [Konfigurera regler och åtgärder](./quick-configure-rules.md)
