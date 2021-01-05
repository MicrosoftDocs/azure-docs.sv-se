---
title: 'Självstudie: skapa en övervaknings-app på en sol panel med Azure IoT Central'
description: 'Självstudie: Lär dig hur du skapar ett sol panel program med hjälp av Azure IoT Central programmallar.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9ea1db982a6944bd12b458624545b3888881508f
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881927"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>Självstudie: skapa och utforska en mall för övervakning av solpanels program 

I den här självstudien får du stegvisa instruktioner för hur du skapar ett övervaknings program för en sol panel, som innehåller en exempel enhets modell med simulerade data. I den här självstudien får du lära dig att:


> [!div class="checklist"]
> * Skapa en testpanel-app utan kostnad
> * Gå igenom programmet
> * Rensa resurser


Om du inte har någon prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Förutsättningar

Det finns inga krav för att slutföra den här självstudien. En prenumeration på Azure rekommenderas, men krävs inte.


## <a name="create-a-solar-panel-monitoring-app"></a>Skapa en app för solpanelsövervakning 

Du kan skapa det här programmet i tre enkla steg:

1. Gå till [Azure IoT Central](https://apps.azureiotcentral.com). Välj **skapa** om du vill skapa ett nytt program. 

1. Välj fliken **energi** . Under **övervakning av sol panelen** väljer du **skapa app**. 

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av Azure IoT Central build-alternativ.](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. Fyll i den begärda informationen i dialog rutan **nytt program** och välj sedan **skapa**:
    * **Program namn**: Välj ett namn för ditt Azure IoT Central-program. 
    * **URL**: Välj en Azure IoT Central-URL. Plattformen verifierar dess unikhet.
    * **Pris plan**: om du redan har en Azure-prenumeration rekommenderas standardinställningen. Om du inte har någon Azure-prenumeration börjar du med den kostnads fria utvärderings versionen.
    * **Fakturerings information**: själva programmet är kostnads fritt. Katalogen, Azure-prenumerationen och regions informationen krävs för att etablera resurserna för din app.
        ![Skärm bild av nytt program.](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Skärm bild av fakturerings information.](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Verifiera programmet och simulerade data

Du kan när som helst ändra din nya-app. Kontrol lera nu att appen distribueras och fungerar som förväntat innan du ändrar den.

Om du vill kontrol lera att appen skapas och data simuleringen går du till **instrument panelen**. Om du kan se panelerna med vissa data, lyckades din app-distribution. Det kan ta några minuter att generera data simuleringen. 

## <a name="application-walk-through"></a>Program genom gång
När du har distribuerat app-mallen vill du utforska appen lite mer. Observera att den innehåller exempel på Smart meters enhet, enhets modell och instrument panel.

Adatum är ett fiktivt energi företag som övervakar och hanterar sol-paneler. På instrument panelen för övervakning av sol panelen ser du egenskaper för sol panel, data och exempel kommandon. På den här instrument panelen kan du eller ditt support team utföra följande aktiviteter proaktivt innan eventuella problem kräver ytterligare support:
* Granska den senaste panel informationen och dess installations plats på kartan.
* Kontrol lera panel status och anslutnings status.
* Gå igenom utvecklings-och temperatur trender för att fånga eventuella avvikande mönster.
* Spåra den totala energi generationen för planering och fakturering.
* Aktivera en panel och uppdatera versionen av den inbyggda program varan, om det behövs. I mallen visar kommando knapparna de möjliga funktionerna och skickar inte riktiga kommandon.

> [!div class="mx-imgBorder"]
> ![Skärm bild av instrument panelen för övervaknings mal len i sol panelen.](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Enheter
Appen levereras med ett exempel på en sol-panels enhet. Om du vill se enhets information väljer du **enheter**.

> [!div class="mx-imgBorder"]
> ![Skärm bild av Enhetarna för övervakning av solpanelen.](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

Välj exempel enheten **SP0123456789**. På fliken **Uppdatera egenskaper** kan du uppdatera enhetens skrivbara egenskaper och se ett visuellt objekt av de uppdaterade värdena på instrument panelen. 

> [!div class="mx-imgBorder"]
> ![Skärm bild av egenskaps mal len uppdatera egenskaper på Sol panelen.](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Enhets mall
Om du vill se enhets modellen för sol panelen väljer du fliken **enhets mallar** . Modellen har fördefinierade gränssnitt för data, egenskaper, kommandon och vyer.

> [!div class="mx-imgBorder"]
> ![Skärm bild av mall för övervakning av solpanelen enhets mallar.](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Rensa resurser
Om du väljer att inte fortsätta använda programmet tar du bort programmet med följande steg:

1. Välj **Administration** i det vänstra fönstret.
1. Välj **program inställningar**  >  **ta bort**. 

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av hanterings mal len för sol panelens övervakning.](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Nästa steg
 
> [!div class="nextstepaction"]
> [App-arkitektur för Azure IoT Central-solpanel](./concept-iot-central-solar-panel-app.md)
* [Skapa programmallar för sol panelen kostnads fritt](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* [Översikt över Azure IoT Central](../index.yml)
