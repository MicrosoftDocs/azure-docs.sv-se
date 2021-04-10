---
title: Självstudie – Skapa och hantera regler i ditt Azure IoT Central-program
description: I den här självstudien får du IoT Central lära dig hur du kan övervaka dina enheter i nära real tid och automatiskt anropa åtgärder, som att skicka ett e-postmeddelande när regeln utlöses.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b0b5aafd85fe6d992afa9d879f73ef0ec43e00d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99834382"
---
# <a name="tutorial-create-a-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Självstudier: Skapa en regel och konfigurera aviseringar i ditt Azure IoT Central-program

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

Du kan använda Azure IoT Central för att fjärrövervaka dina anslutna enheter. Med Azure IoT Central-regler kan du övervaka dina enheter i nära real tid och automatiskt anropa åtgärder, till exempel skicka ett e-postmeddelande. Den här artikeln förklarar hur du skapar regler för att övervaka de telemetri som dina enheter skickar.

Enheter använder telemetri för att skicka numeriska data från enheten. En regel utlöses när den valda Telemetrin korsar ett angivet tröskelvärde.

I den här självstudien skapar du en regel för att skicka ett e-postmeddelande när temperaturen i en simulerad sensor enhet överskrider 70 &deg; F.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en regel
> * Lägg till en e-poståtgärd

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar fyller du i [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md) och [lägger till en simulerad enhet i IoT Central](./quick-create-simulated-device.md) snabb starter för att skapa **sensor styrenhetens** enhets mall att fungera med.

## <a name="create-a-rule"></a>Skapa en regel

För att skapa en telemetri-regel måste enhets mal len innehålla minst ett telemetri-värde. I den här självstudien används en simulerad **sensor för sensor styrenhet** som skickar telemetri för temperatur och fuktighet. Du har lagt till den här enhets mal len och skapat en simulerad enhet i guiden [Lägg till en simulerad enhet i snabb starten för IoT Central programmet](./quick-create-simulated-device.md) . Regeln övervakar temperaturen som rapporteras av enheten och skickar ett e-postmeddelande när det hamnar ovanför 70 grader.

> [!NOTE]
> Det finns en gräns på 50 regler per program.

1. I det vänstra fönstret väljer du **regler**.

1. Om du inte har skapat några regler än visas följande skärm:

    :::image type="content" source="media/tutorial-create-telemetry-rules/rules-landing-page.png" alt-text="Skärm bild som visar den tomma listan med regler":::

1. Välj **+ ny** för att lägga till en ny regel.

1. Ange namnet _temperatur övervakare_ för att identifiera regeln och tryck på RETUR.

1. Välj enhets mal len **sensor styrenhet** . Regeln gäller som standard automatiskt för alla enheter som är associerade med enhets mal len. Om du vill filtrera efter en delmängd av enheterna väljer du **+ filtrera** och använder enhets egenskaper för att identifiera enheterna. Om du vill inaktivera regeln växlar du till den **aktiverade/inaktiverade** knappen:

    :::image type="content" source="media/tutorial-create-telemetry-rules/device-filters.png" alt-text="Skärm bild som visar valet av enhets mal len i regel definitionen":::

### <a name="configure-the-rule-conditions"></a>Konfigurera regel villkoren

Villkor definierar de kriterier som regeln övervakar. I den här självstudien konfigurerar du regeln för att utlösa när temperaturen överskrider 70 &deg; F.

1. Välj **temperatur** i list rutan för **telemetri** .

1. Sedan väljer du **är större än** som **Operator** och anger _70_ som **värde**.

    :::image type="content" source="media/tutorial-create-telemetry-rules/condition-filled-out.png" alt-text="Skärm bild som visar temperatur villkoret för regeln":::

1. Du kan också ange en **tids agg regering**. När du väljer en tids agg regering måste du också välja en agg regerings typ, till exempel Average eller sum, från List rutan agg regering.

    * Utan agg regering utlöser regeln för varje telemetri-datapunkt som uppfyller villkoret. Om du till exempel konfigurerar regeln så att den utlöses när temperaturen är över 70, utlöses regeln nästan omedelbart när enhetens temperatur överskrider detta värde.
    * Med agg regering utlöser regeln om det sammanlagda värdet för telemetri-datapunkten i tids perioden uppfyller villkoret. Om du till exempel konfigurerar regeln så att den utlöses när temperaturen är över 70 och med en genomsnittlig tids mängd på 10 minuter utlöses regeln när enheten rapporterar en genomsnitts temperatur som är större än 70, beräknad under ett 10-minuters intervall.

    :::image type="content" source="media/tutorial-create-telemetry-rules/aggregate-condition-filled-out.png" alt-text="Skärm bild som visar det sammanställda villkoret":::

Du kan lägga till flera villkor i en regel genom att välja **+ villkor**. När flera villkor har angetts måste alla villkor vara uppfyllda för att regeln ska kunna utlösas. Varje villkor är anslutet av en implicit `AND` sats. Om du använder tids agg regering med flera villkor måste alla värden för telemetri aggregeras.

### <a name="configure-actions"></a>Konfigurera åtgärder

När du har definierat villkoret ställer du in de åtgärder som ska vidtas när regeln utlöses. Åtgärder anropas när alla villkor som anges i regeln utvärderas till sant.

1. Välj **+ e-post** i avsnittet **åtgärder** .

1. Ange _temperatur varning_ som visnings namn för åtgärden, din e-postadress i fältet **till** och _kontrol lera enheten!_ som anteckning som ska visas i bröd texten i e-postmeddelandet.

    > [!NOTE]
    > E-postmeddelanden skickas endast till användare som har lagts till i programmet och har loggat in minst en gång. Lär dig mer om [användar hantering](howto-administer.md) i Azure IoT Central.

    :::image type="content" source="media/tutorial-create-telemetry-rules/configure-action.png" alt-text="Skärm bild som visar e-poståtgärden för regeln":::

1. Välj **Slutför** för att spara åtgärden. Du kan lägga till flera åtgärder i en regel.

1. Spara regeln genom att välja **Spara**. Regeln går inom några minuter och börjar övervaka telemetri som skickas till ditt program. När villkoret som anges i regeln uppfylls utlöser regeln den konfigurerade e-poståtgärden.

Efter en stund får du ett e-postmeddelande när regeln utlöses:

:::image type="content" source="media/tutorial-create-telemetry-rules/email.png" alt-text="Skärm bild som visar e-postavisering":::

## <a name="delete-a-rule"></a>Ta bort en regel

Om du inte längre behöver en regel tar du bort den genom att öppna regeln och välja **ta bort**.

## <a name="enable-or-disable-a-rule"></a>Aktivera eller inaktivera en regel

Välj den regel som du vill aktivera eller inaktivera. Aktivera eller inaktivera regeln för alla enheter som omfattas av regeln genom att växla den **aktiverade/inaktiverade** knappen i regeln.

## <a name="enable-or-disable-a-rule-for-specific-devices"></a>Aktivera eller inaktivera en regel för vissa enheter

Välj den regel som du vill anpassa. Använd ett eller flera filter i avsnittet **mål enheter** för att begränsa regelns omfång till de enheter som du vill övervaka.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en telemetribaserad regel
* Lägga till en åtgärd

Nu när du har definierat en tröskel-baserad regel är det föreslagna nästa steg att lära dig att:

> [!div class="nextstepaction"]
> [Skapa Webhooks på regler](./howto-create-webhooks.md).
