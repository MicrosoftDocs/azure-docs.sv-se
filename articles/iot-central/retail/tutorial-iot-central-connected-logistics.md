---
title: Självstudie över IoT Connectd logistik | Microsoft Docs
description: En själv studie kurs om den anslutna logistik program mal len för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 7c6c50b6fd8200726aa11f120d9748cc1b40aa22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831628"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Självstudie: Distribuera och gå igenom en ansluten logistik-Programmall

I den här självstudien lär du dig hur du kommer igång med den IoT Central *anslutna logistik* -programmallen. Du lär dig hur du distribuerar och använder mallen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett anslutet logistik program.
> * Använd viktiga funktioner i programmet.
> * Använd instrument panelen för att visa den kritiska logistik enhetens åtgärds aktivitet.
> * Använd enhets mal len
> * Följ regler
> * Använda jobb

## <a name="prerequisites"></a>Förutsättningar

* Det finns inga särskilda krav som krävs för att distribuera den här appen.
* Du kan använda den kostnads fria pris planen eller använda en Azure-prenumeration.

## <a name="create-connected-logistics-application"></a>Skapa ett anslutet logistik program

Skapa programmet med följande steg:

1. Gå till bygg webbplatsen för [Azure IoT Central](https://aka.ms/iotcentral) . Logga sedan in med ett Microsoft personal-, arbets-eller skol konto. Välj **Bygg** i det vänstra navigerings fältet och välj sedan fliken **åter försäljning** :

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png" alt-text="Ansluten logistik mall":::

1. Välj **skapa app** under **Connected logistik Application**.

1. **Skapa app** öppnar formuläret **ny** app. Ange följande information:


    * **Program namn**: du kan använda det föreslagna standard namnet eller ange ett eget program namn.
    * **URL**: du kan använda den föreslagna standard-URL: en eller ange en egen, unik minnes adress. Sedan rekommenderas standardinställningen om du redan har en Azure-prenumeration. Du kan börja med en kostnads fri utvärderings prenumerations plan på 7 dagar och välja att konvertera till en standard pris plan när som helst innan det kostnads fria slutet går ut.
    * **Fakturerings information**: katalog, Azure-prenumeration och regions information krävs för att etablera resurserna.
    * **Skapa**: Välj Skapa längst ned på sidan för att distribuera programmet.

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png" alt-text="App-mall för ansluten logistik":::

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png" alt-text="Fakturerings information för ansluten logistik":::

## <a name="walk-through-the-application"></a>Gå igenom programmet

Nedan visas en skärm bild som visar hur du väljer den anslutna logistik program mal len.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar hur du väljer program mal len ansluten logistik](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

I följande avsnitt får du stegvisa anvisningar genom programmets viktiga funktioner.

### <a name="dashboard"></a>Instrumentpanel

När du har distribuerat program mal len är standard instrument panelen en riktad Portal operatör med fokus på portalen. Northwind-näringsidkare är en fiktiv logistik leverantör som hanterar en Last flotta till sjöss och på marken. På den här instrument panelen visas två olika gateways som tillhandahåller telemetri från försändelser, tillsammans med tillhör ande kommandon, jobb och åtgärder.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar hur du skapar en app från program mal len ansluten logistik](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar fakturerings alternativen när du skapar programmet](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

Den här instrument panelen är förkonfigurerad för att visa den kritiska logistik enhetens Operations-aktivitet.

Instrument panelen möjliggör två olika hanterings åtgärder för gateway-enheter:

* Visa logistik vägar för Last bils leveranser och plats information om havs sändningar.
* Visa Gateway-status och annan relevant information.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png" alt-text="Ansluten logistik instrument panel":::

* Du kan spåra det totala antalet gatewayer, aktiva och okända taggar.
* Du kan utföra enhets hanterings åtgärder, till exempel: Uppdatera inbyggd program vara, inaktivera och aktivera sensorer, uppdatera ett sensor tröskelvärde, uppdatera telemetri intervall och uppdatera enhets tjänst kontrakt.
* Visa enhetens batteri förbrukning.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png" alt-text="Status för ansluten logistik instrument panel":::

#### <a name="device-template"></a>Enhets mall

Välj **enhets mallar** för att se kapacitets modellen för gateway. En kapacitets modell är strukturerad runt **Gateway-telemetri & egenskaps** -och **Gateway-kommando** gränssnitt.

**& egenskap för gateway-telemetri** – det här gränssnittet definierar all telemetri som rör sensorer, plats och enhets information. Gränssnittet definierar också enhetens dubbla egenskaps funktioner, till exempel sensor trösklar och uppdaterings intervall.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png" alt-text="Telemetri-och egenskaps gränssnitt":::

**Gateway-kommandon** – det här gränssnittet organiserar alla gateway-kommando funktioner:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png" alt-text="Gränssnitt för gateway-kommandon":::

### <a name="rules"></a>Regler

Välj fliken **regler** för reglerna i den här program mal len. Dessa regler är konfigurerade för e-postaviseringar till operatörerna för ytterligare undersökningar:

**Avisering om Gateway-stöld**: den här regeln utlöses när det finns oväntad ljus identifiering av sensorer under resan. Operatörer måste meddelas omedelbart för att undersöka potentiell stöld.

**Svarar** inte på Gateway: den här regeln utlöses om gatewayen inte rapporterar till molnet under en längre period. Det gick inte att svara på gatewayen på grund av låg batteri nivå, förlust av anslutning eller enhets skada.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png" alt-text="Regel definitioner":::

### <a name="jobs"></a>Jobb

Välj fliken **jobb** för att se jobben i det här programmet:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png" alt-text="Jobb som ska köras":::

Du kan använda jobb för att utföra åtgärder i hela programmet. Jobben i det här programmet använder enhets kommandon och dubbla funktioner för att utföra åtgärder, till exempel inaktivera specifika sensorer för alla gatewayer eller ändra sensor tröskeln beroende på leverans läge och väg:

* Det är en standard åtgärd för att inaktivera stötande sensorer under skeppning av havet för att spara batteri eller lägre temperatur tröskel under transport av kall kedja.

* Med jobb kan du utföra systemomfattande åtgärder som att uppdatera den inbyggda program varan på gatewayerna eller uppdatera service kontraktet för att hålla dig uppdaterad om underhålls aktiviteter.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort program mal len genom att gå till **administrations**  >  **programmets inställningar** och väljer **ta bort**.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png" alt-text="Rensa mall":::

## <a name="next-steps"></a>Nästa steg

Läs mer om:

> [!div class="nextstepaction"]
> [Anslutna logistik koncept](./architecture-connected-logistics.md)
