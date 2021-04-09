---
title: Självstudie över hantering av Smart inventering i IoT | Microsoft Docs
description: En själv studie kurs om program mal len för smart inventerings hantering för IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 3dbb6ca64451cb60d5a8ec67ecdc528865a4438c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719091"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Självstudie: Distribuera och gå igenom en mall för hantering av Smart inventerings program

Den här självstudien visar hur du kommer igång genom att distribuera en IoT Central Mall för **Smart inventerings hantering** . Du får lära dig hur du distribuerar mallen, vad som ingår i rutan och vad du kanske vill göra härnäst.

I den här självstudien får du lära dig att

> [!div class="checklist"]
> * Skapa program för smart inventerings hantering 
> * gå igenom programmet 

## <a name="prerequisites"></a>Förutsättningar

* Inga särskilda krav krävs för att distribuera den här appen
* Vi rekommenderar att du har en Azure-prenumeration, men du kan även prova utan den

## <a name="create-smart-inventory-management-application-template"></a>Skapa program mal len för smart inventerings hantering

Du kan skapa program med följande steg

1. Gå till webbplatsen för Azure IoT Central Application Manager. Välj **Bygg** i det vänstra navigerings fältet och klicka sedan på fliken **detalj handel** .

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png" alt-text="Skärm bild som visar hur du väljer program mal len Smart inventerings hantering":::

2. Välj fliken **butik** och välj **skapa app** under **Smart Inventory Management**

3. **Skapa app** öppnar nytt program formulär och fyller i den begärda informationen som visas nedan.
    **Program namn**: du kan använda det föreslagna standard namnet eller ange ett eget program namn.
    **URL**: du kan använda den föreslagna standard-URL: en eller ange en egen, unik minnes adress. Sedan rekommenderas standardinställningen om du redan har en Azure-prenumeration. Du kan börja med en kostnads fri utvärderings prenumerations plan på 7 dagar och välja att konvertera till en standard pris plan när som helst innan det kostnads fria slutet går ut.
    **Fakturerings information**: katalog, Azure-prenumeration och regions information krävs för att etablera resurserna.
    **Skapa**: Välj Skapa längst ned på sidan för att distribuera programmet.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png" alt-text="Skärm bild som visar hur du skapar en app från program mal len Smart inventerings hantering":::

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png" alt-text="Skärm bild som visar fakturerings alternativen när du skapar programmet":::

## <a name="walk-through-the-application"></a>Gå igenom programmet 

### <a name="dashboard"></a>Instrumentpanel 

När du har distribuerat app-mallen är standard instrument panelen en fokuserad Portal för smart inventerings hantering. Northwind-näringsidkare är en fiktiv Smart Inventory Provider för hantering av smarta lager med Bluetooth låg energi (Bell) och Retail Store med radio frekvens identifiering (RFID). På den här instrument panelen kan du se två olika gateways som tillhandahåller telemetri om inventering tillsammans med tillhör ande kommandon, jobb och åtgärder som du kan utföra. Den här instrument panelen är förkonfigurerad för att visa den kritiska aktiviteten för enhets åtgärder för smart inventerings hantering.
Instrument panelen delas logiskt mellan två olika hanterings åtgärder för gateway-enheter, 
   * Lagret distribueras med en fast Bell-Gateway & Bell-taggar på lastpallar för att spåra & spårnings inventering vid en större anläggning
   * Butiks lager implementeras med en fast RFID-Gateway & RFID-taggar på enskilda objekt nivåer för att spåra och spåra lagret i ett lager uttag
   * Visa Gateway-platsen, status & relaterad information 

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar den övre halvan av Smart Inventory managementdashboard](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Du kan enkelt spåra det totala antalet gatewayer, aktiva och okända taggar.
   * Du kan utföra enhets hanterings åtgärder som uppdaterings-firmware, inaktivera sensor, aktivera sensor, uppdatera sensor tröskel, uppdatera telemetri & uppdatera enhets tjänst kontrakt
   * Gateway-enheter kan utföra lager hantering på begäran med en fullständig eller stegvis genomsökning.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar den nedre halvan av Smart Inventory-managementdashboard](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Enhets mall
Klicka på fliken Mallar på enheten så visas kapacitets modellen för gateway. En kapacitets modell är strukturerad runt två olika gränssnitts- **Gateway-telemetri & egenskaper** och **Gateway-kommandon**

**& egenskap för gateway-telemetri** – det här gränssnittet representerar all telemetri som rör sensorer, plats, enhets information och enhets dubbla egenskaps funktioner, till exempel tröskelvärden för gateway och uppdaterings intervall.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar enhets mal len för inventerings gateway i programmet](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Gateway-kommandon** – det här gränssnittet organiserar alla gatewayens kommando funktioner

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar gränssnittet för gateway-kommandon i enhets mal len för inventering av Gateway](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Regler
Välj fliken regler för att se två olika regler som finns i den här program mal len. Dessa regler är konfigurerade för e-postaviseringar till operatörerna för ytterligare undersökningar.

**Gatewayen är offline**: den här regeln utlöses om gatewayen inte rapporterar till molnet under en längre period. Det gick inte att svara på gatewayen på grund av låg energi läge, anslutningens hälso status och enhetens hälso tillstånd.

**Okända Taggar**: det är viktigt att spåra alla RFID-& Bell-taggar som är kopplade till en till gång. Om det finns för många okända Taggar i gatewayen är det en indikation på utmaningarna för synkronisering med tag-käll program.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar listan över regler i managementapplication för smart inventering](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Jobb
Välj fliken jobb för att se fem olika jobb som finns som en del av den här program mal len: du kan använda jobb funktionen om du vill utföra åtgärder i hela lösningen. Dessa lager hanterings jobb använder enhets kommandon och dubbla funktioner för att utföra uppgifter som,
   * Inaktivera läsare över hela gatewayen
   * ändrar tröskelvärdet för telemetri mellan 
   * utför lager genomsökning på begäran i hela lösningen.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar en lista över jobb i Smart Inventory-managementapplication](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort program mal len genom att gå till **administrations**  >  **program inställningar** och klicka på **ta bort**.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar hur du tar bort programmet när du är klar med det](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om hantering av smarta lager:

> [!div class="nextstepaction"]
> [Koncept för smart inventerings hantering](./architecture-smart-inventory-management.md)
