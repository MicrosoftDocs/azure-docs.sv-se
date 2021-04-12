---
title: Självstudie – definiera en ny Gateway-enhets typ i Azure IoT Central | Microsoft Docs
description: Den här självstudien visar dig som ett verktyg för att definiera en ny enhets typ för IoT gateway i ditt Azure IoT Central-program.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: cdace22072e4122150c878d43b99765cd6fd59e1
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490785"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Självstudie – definiera en ny enhets typ för IoT gateway i ditt Azure IoT Central-program

*Den här artikeln gäller lösnings byggare och enhets utvecklare.*

Den här självstudien visar dig som Solution Builder och hur du använder en gateway-enhet för att definiera en gateway-enhet i IoT Central-programmet. Sedan kan du konfigurera flera underordnade enheter som ansluter till ditt IoT Central-program via gateway-enheten. 

I den här självstudien skapar du en mall för **Smart Building** gateway-enhet. En enhet för **Smart skapande** Gateway har relationer med andra efterföljande enheter.

![Diagram över relationen mellan gateway-enheter och underordnade enheter](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Även om du gör det möjligt för underordnade enheter att kommunicera med ditt IoT Central-program kan en gateway-enhet också:

* Skicka sin egen telemetri, till exempel temperatur.
* Svara på skrivbara egenskaps uppdateringar gjorda av en operatör. En operatör kan exempelvis ändra sändnings intervallet för telemetri.
* Svara på kommandon, t. ex. omstart av enheten.

> [!div class="checklist"]
> Skapa mallar för underordnade enheter skapa en mall för en gateway-enhet publicera enhets mal len skapa de simulerade enheterna

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien måste du [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md).

## <a name="create-downstream-device-templates"></a>Skapa mallar för underordnade enheter

I den här självstudien används enhets mallar för en **S1-sensor** och en **RS40-sensor** för att generera simulerade underordnade enheter.

Så här skapar du en enhets mall för en **S1 sensor** enhet:

1. I det vänstra fönstret väljer du **enhets mallar**. Välj sedan **+** för att börja lägga till mallen.

1. Rulla nedåt tills du ser panelen för **S1-sensor** enheten. Välj panelen och välj sedan **Nästa: anpassa**.

1. På sidan **Granska** väljer du **skapa** för att lägga till enhets mal len i programmet. 

Så här skapar du en enhets mall för en * RS40 för användnings **sensor** :

1. I det vänstra fönstret väljer du **enhets mallar**. Välj sedan **+** för att börja lägga till mallen.

1. Rulla nedåt tills du ser panelen för ***RS40 besittnings sensor** enhet. Välj panelen och välj sedan **Nästa: anpassa**.

1. På sidan **Granska** väljer du **skapa** för att lägga till enhets mal len i programmet. 

Nu har du enhets mallar för de två underordnade enhets typerna:

![Enhets mallar för underordnade enheter](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Mall för att skapa en gateway-enhet

I den här självstudien skapar du en enhets mall för en gateway-enhet från grunden. Du kan använda den här mallen senare för att skapa en simulerad gateway-enhet i ditt program.

Så här lägger du till en ny Gateway-mall i programmet:

1. I det vänstra fönstret väljer du **enhets mallar**. Välj sedan **+** för att börja lägga till mallen.

1. På sidan **Välj Malltyp** väljer du panelen IoT- **enhet** och väljer sedan **Nästa: anpassa**.

1. På sidan **Anpassa enhet** markerar du kryss rutan **gateway-enhet** .

1. På sidan **Granska** väljer du **skapa**. 

1. Ange **Smart Building gateway-enhet** som mallens namn.

1. På sidan **skapa en kapacitets modell** väljer du den **anpassade** panelen.

1. Välj **+** om du vill lägga till ett gränssnitt.  Välj standard gränssnittet för **enhets information** .

### <a name="add-relationships"></a>Lägg till relationer

Härnäst lägger du till relationer till mallarna för de underordnade enhets mallarna:

1. I enhets mal len för **Smart skapande Gateway** väljer du **relationer**.

1. Välj **+ Lägg till relation**. Ange **miljö sensor** som visnings namn och välj S1- **sensor** som mål.

1. Välj **+ Lägg till relation** igen. Ange användnings **sensor** som visnings namn och välj **RS40 besittnings sensor** som mål.

1. Välj **Spara**.

![Enhets mal len för smart skapande Gateway, med relationer](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Lägga till molnegenskaper

En gateway-enhets mall kan innehålla moln egenskaper. Moln egenskaper finns bara i IoT Central programmet och skickas aldrig till eller tas emot från en enhet.

För att lägga till moln egenskaper till enhets mal len för **Smart skapande Gateway** .

1. I enhets mal len för **Smart skapande Gateway** väljer du **moln egenskaper**.

1.  Använd informationen i följande tabell för att lägga till två moln egenskaper till din gateway-enhets mall.

    | Visningsnamn      | Semantiktyp | Schema |
    | ----------------- | ------------- | ------ |
    | Senaste servicedatum | Inget          | Datum   |
    | Kundnamn     | Inget          | Sträng |

2. Välj **Spara**.

### <a name="create-views"></a>Skapa vyer

Som ett verktyg kan du anpassa programmet för att visa relevant information om miljö sensor enheten i en operatör. Dina anpassningar gör att operatören kan hantera miljö sensor enheter som är anslutna till programmet. Du kan skapa två typer av vyer för en operator som ska användas för att interagera med enheter:

* Formulär för att visa och redigera egenskaper för enhet och moln.
* Instrument paneler för att visualisera enheter.

Så här genererar du standardvyer för **enhets mal len för smart skapande Gateway** :

1. I enhets mal len för **Smart skapande Gateway** väljer du **vyer**.

1. Välj **generera panelen standardvyer** och se till att alla alternativ är markerade.

1. Välj **generera standard visnings instrument panel (er)**.

## <a name="publish-the-device-template"></a>Publicera enhetsmallen

Innan du kan skapa en simulerad gatewayenhet eller ansluta en riktig gateway-enhet måste du publicera din enhets mall.

Så här publicerar du en gateways enhets mall:

1. Välj enhets mal len för **Smart skapande Gateway** på sidan **enhets mallar** .

2. Välj **Publicera**.

3. I dialog rutan **publicera en enhets mall** väljer du **publicera**.

När en enhets mal len har publicerats visas den på sidan **enheter** och i operatorn. I en publicerad enhets mall kan du inte redigera en enhets modell utan att skapa en ny version. Du kan dock göra uppdateringar av moln egenskaper, anpassningar och vyer i en publicerad enhets mall. De här uppdateringarna innebär inte att en ny version skapas. När du har gjort några ändringar väljer du **publicera**  för att skicka ut ändringarna till din operatör.

## <a name="create-the-simulated-devices"></a>Skapa de simulerade enheterna

I den här självstudien används simulerade underordnade enheter och en simulerad gateway-enhet.

Så här skapar du en simulerad gateway-enhet:

1. På sidan **enheter** väljer du **Smart Building gateway-enhet** i listan med mallar för enheter.

1. Välj om **+** du vill börja lägga till en ny enhet.

1. Behåll det genererade **enhets-ID: t** och **enhets namnet**. Kontrol lera att den **simulerade** växeln är **på**. Välj **Skapa**.

Så här skapar du en simulerad underordnad enhet:

1. På sidan **enheter** väljer du **RS40 beläggnings sensor** i listan över enhetsspecifika.

1. Välj om **+** du vill börja lägga till en ny enhet.

1. Behåll det genererade **enhets-ID: t** och **enhets namnet**. Kontrol lera att den **simulerade** växeln är **på**. Välj **Skapa**.

1. På sidan **enheter** väljer du **S1-sensor** i listan med enhetsspecifika mallar.

1. Välj om **+** du vill börja lägga till en ny enhet.

1. Behåll det genererade **enhets-ID: t** och **enhets namnet**. Kontrol lera att den **simulerade** växeln är **på**. Välj **Skapa**.

![Simulerade enheter i ditt program](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Lägg till underordnade enhets relationer till en gateway-enhet

Nu när du har de simulerade enheterna i ditt program kan du skapa relationerna mellan de efterföljande enheterna och gateway-enheten:

1. På sidan **enheter** väljer du **S1-sensor** i listan med enhets mallar och väljer sedan den simulerade **S1-sensor** enheten.

1. Välj **Anslut till gateway**.

1. I dialog rutan **Anslut till en gateway** väljer du enhets mal len för **Smart Building Gateway** och väljer sedan den simulerade instans som du skapade tidigare.

1. Välj **Anslut**.

1. På sidan **enheter** väljer du **RS40 beläggnings sensor** i listan med enhetsspecifika enheter och väljer sedan den simulerade RS40 för **besittnings sensor** .

1. Välj **Anslut till gateway**.

1. I dialog rutan **Anslut till en gateway** väljer du enhets mal len för **Smart Building Gateway** och väljer sedan den simulerade instans som du skapade tidigare.

1. Välj **Anslut**.

Både de simulerade underordnade enheterna är nu anslutna till den simulerade gateway-enheten. Om du går till vyn **underordnade enheter** för din gateway-enhet kan du se de relaterade underordnade enheterna:

![Vy över underordnade enheter](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Välj en mall för gateway-enhet och gateway-enhet och välj **Anslut**.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

* Skapa en ny IoT-gateway som en enhets mall.
* Skapa moln egenskaper.
* Skapa anpassningar.
* Definiera en visualisering för enhetens telemetri.
* Lägg till relationer.
* Publicera din enhets mall.

Sedan kan du, som enhets utvecklare, lära dig att:

> [!div class="nextstepaction"]
> [Lägga till en Azure IoT Edge enhet till ditt Azure IoT Central-program](tutorial-add-edge-as-leaf-device.md)
