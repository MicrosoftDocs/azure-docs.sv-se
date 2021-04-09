---
title: Snabb start – Lägg till en simulerad enhet i Azure IoT Central
description: Den här snabb starten visar hur du skapar en enhets mall och lägger till en simulerad enhet i IoT Central-programmet.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 488de7114d80e6a88cc619ba3b42f867c985ea11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833923"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Snabb start: lägga till en simulerad enhet till ditt IoT Central program

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

En enhets mall definierar funktionerna hos en enhet som ansluter till ditt IoT Central-program. Funktionerna omfattar telemetri som enheten skickar, enhets egenskaper och de kommandon som en enhet svarar på. Från en enhets mall kan ett verktyg eller en operatör lägga till både riktiga och simulerade enheter i ett program. Simulerade enheter är användbara för att testa beteendet för ditt IoT Central program innan du ansluter riktiga enheter.

I den här snabb starten lägger du till en enhets mall för en ESP32-Azure IoT kit-utvecklings tavla och skapar en simulerad enhet. För att slutföra den här snabb starten behöver du inte en riktig enhet, men du arbetar med en simulering av enheten. En ESP32-enhet:

* Skickar telemetri som temperatur.
* Rapporterar enhetsspecifika egenskaper, till exempel maximal temperatur sedan enheten startades om.
* Svarar på kommandon som till exempel omstart.
* Rapporterar allmänna enhets egenskaper, t. ex. version och serie nummer för inbyggd program vara.

## <a name="prerequisites"></a>Förutsättningar

Slutför snabb starten [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) för att skapa ett IoT Central program med hjälp av den **anpassade appen > anpassade program** .

## <a name="create-a-device-template"></a>Skapa en enhetsmall

Som ett verktyg kan du skapa och redigera mallar för enheter i IoT Central program. När du har publicerat en enhets mall kan du generera en simulerad enhet eller ansluta riktiga enheter från enhets mal len. Med simulerade enheter kan du testa appens beteende innan du ansluter en riktig enhet.

Om du vill lägga till en ny enhets mall i programmet väljer du fliken **enhets mallar** i den vänstra rutan.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Skärm bild som visar en tom lista över enhetsspecifika mallar":::

En enhets mall innehåller en enhets modell som definierar:

* Den telemetri som enheten skickar.
* Enhets egenskaper.
* De kommandon som enheten svarar på.

### <a name="add-a-device-template"></a>Lägga till en enhetsmall

Det finns flera alternativ för att lägga till en enhets modell till ditt IoT Central-program. Du kan skapa en modell från grunden, importera en modell från en fil eller välja en enhet från enhets katalogen. IoT Central också stöd för en *enhets-första* metod där programmet automatiskt importerar en modell från en lagrings plats när en riktig enhet ansluter för första gången.

I den här snabb starten väljer du en enhet från enhets katalogen för att skapa en enhets mall.

Följande steg visar hur du använder enhets katalogen för att importera modellen för en **ESP32** -enhet. De här enheterna skickar telemetri, till exempel temperatur, till ditt program:

1. Om du vill lägga till en ny enhets mall väljer du **+ ny** på sidan **mallar för enheter** .

1. På sidan **Välj typ** bläddrar du nedåt tills du hittar panelen **ESP32-Azure IoT kit** i avsnittet **Använd en förkonfigurerad enhets mall** .

1. Välj panelen **ESP32 – Azure IoT kit** och välj sedan **Nästa: granska**.

1. På sidan **Granska** väljer du **skapa**.

1. Efter några sekunder kan du se din nya enhets mall:

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="Skärm bild som visar enhets mal len för ESP32-enheten":::

    Namnet på mallen är **sensor styrenhet**. Modellen innehåller komponenter som **sensor styrenhet**, **SensorTemp** och **enhets informations gränssnitt**. Komponenter definierar funktionerna för en ESP32-enhet. Funktionerna omfattar telemetri, egenskaper och kommandon.

### <a name="add-cloud-properties"></a>Lägga till molnegenskaper

En enhets mall kan innehålla moln egenskaper. Moln egenskaper finns bara i IoT Central programmet och skickas aldrig till eller tas emot från en enhet. Så här lägger du till två moln egenskaper:

1. Välj **moln egenskaper** och sedan **+ Lägg till moln egenskap**. Använd informationen i följande tabell för att lägga till två moln egenskaper till din enhets mall:

    | Visningsnamn      | Semantisk typ | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Inget          | Datum   |
    | Kundnamn     | Inget          | Sträng |

1. Välj **Spara** för att spara ändringarna:

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Skärm bild som visar två moln egenskaper":::

## <a name="views"></a>Vyer

Som ett verktyg kan du anpassa programmet för att visa relevant information om enheten till en operatör. Anpassningarna gör att operatören kan hantera enheter som är anslutna till programmet. Du kan skapa två typer av vyer för en operator som ska användas för att interagera med enheter:

* Formulär för att visa och redigera egenskaper för enhet och moln.
* Instrument paneler för att visualisera enheter, inklusive telemetri som de skickar.

### <a name="default-views"></a>Standardvyer

Standardvyer är ett snabbt sätt att komma igång med att visualisera viktig enhets information. Du kan ha upp till tre standardvyer som genereras för din enhets mall:

* I vyn **kommandon** kan operatören skicka kommandon till din enhet.
* I vyn **Översikt** används diagram och mått för att Visa telemetri för enheter.
* I vyn **om** visas enhets egenskaper.

Välj noden **vyer** i enhets mal len. Du kan se att IoT Central genererade en **Översikt** och en **om** -vy åt dig när du lade till mallen.

För att lägga till ett nytt **hanterat enhets** formulär som en operatör kan använda för att hantera enheten:

1. Välj noden **vyer** och välj sedan panelen **Redigera enhet och moln data** för att lägga till en ny vy.

1. Ändra formulär namnet för att **hantera enheten**.

1. Välj **kundens namn** och **senaste service datum** för moln egenskaper och egenskapen **mål temperatur** . Välj sedan **Lägg till avsnitt**:

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Skärm bild som visar nytt formulär tillagt i enhets mal len":::

1. Spara ditt nya formulär genom att välja **Spara** .

## <a name="publish-device-template"></a>Publicera enhets mal len

Innan du kan skapa en simulerad enhet eller ansluta en riktig enhet måste du publicera din enhets mall. Även om IoT Central har publicerat mallen när du först skapade den, måste du publicera den uppdaterade versionen.

Så här publicerar du en enhets mal len:

1. Gå till enhets mal len för **sensor styrenheten** från sidan **mallar för enheter** .

1. Välj **publicera**:

    :::image type="content" source="media/quick-create-simulated-device/published-model.png" alt-text="Skärm bild som visar platsen för publicerings ikonen":::

1. I den **publicera den här enhets mal len i program** dialog rutan väljer du **publicera**.

När du har publicerat en enhets mall visas den på sidan **enheter** . I en publicerad enhets mall kan du inte redigera en enhets modell utan att skapa en ny version. Du kan dock ändra moln egenskaper, anpassningar och vyer i en publicerad enhets mall utan versions hantering. När du har gjort några ändringar väljer du **publicera**  för att skicka ut ändringarna till din operatör.

## <a name="add-a-simulated-device"></a>Lägga till en simulerad enhet

Om du vill lägga till en simulerad enhet i programmet använder du enhets mal len **ESP32** som du skapade.

1. Om du vill lägga till en ny enhet som en operatör väljer du **enheter** i den vänstra rutan. Fliken **enheter** visar **alla enheter** och enhets mal len för **sensor styrenheten** för ESP32-enheten. Välj **sensor styrenhet**.

1. Om du vill lägga till en simulerad DevKit-enhet väljer du **+ ny**. Använd det föreslagna **enhets-ID: t** eller ange ditt eget. Ett enhets-ID kan innehålla bokstäver, siffror och `-` tecken. Du kan också ange ett namn på den nya enheten. Se till att **simulera att enheten** är inställd på **Ja** och välj sedan **skapa**.

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="Skärm bild som visar den simulerade sensor styrenhets enheten":::

Nu kan du interagera med de vyer som har skapats av verktyget för enhets mal len med simulerade data:

1. Välj den simulerade enheten på sidan **enheter**

    * I vyn **Översikt** visas ett observations område för den simulerade Telemetrin:

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="Skärm bild som visar översikts sidan för simulerad enhet":::

    * Vyn **About** visar egenskaps värden.

    * I vyn **kommandon** kan du köra kommandon, t. ex. **omstart** på enheten.

    * Vyn **Hantera enheter** är det formulär som du skapade för operatören för att hantera enheten.

    * I vyn **rå data** kan du Visa de obehandlade telemetri-och egenskaps värden som skickas av enheten. Den här vyn är användbar för fel sökning av enheter.

## <a name="use-a-simulated-device-to-improve-views"></a>Använd en simulerad enhet för att förbättra vyer

När du har skapat en ny simulerad enhet kan verktyget använda den här enheten för att fortsätta att förbättra och bygga på vyerna för enhets mal len.

1. Välj **enhets mallar** i den vänstra rutan och välj mallen för **sensor styrenheten** .

1. Välj någon av de vyer som du vill redigera, till exempel **Översikt**, eller skapa en ny vy. Välj **Konfigurera för hands versions enhet** och **Välj sedan från en enhet som körs**. Här kan du välja att inte ha någon förhands gransknings enhet, en riktig enhet som kon figurer ATS för testning eller en befintlig enhet som du har lagt till i IoT Central.

1. Välj den simulerade enheten i listan. Välj sedan **Använd**. Nu kan du se samma simulerade enhet i din enhets mall för att skapa upplevelser. Den här vyn är användbar för diagram och andra visualiseringar.

    :::image type="content" source="media/quick-create-simulated-device/configure-preview.png" alt-text="Skärm bild som visar en konfigurerad förhands gransknings enhet":::

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en enhets mall för **sensor styrenhet** för en ESP32-enhet och lägger till en simulerad enhet i programmet.

Om du vill veta mer om övervakning av enheter som är anslutna till ditt program fortsätter du till snabb starten:

> [!div class="nextstepaction"]
> [Konfigurera regler och åtgärder](./quick-configure-rules.md)
