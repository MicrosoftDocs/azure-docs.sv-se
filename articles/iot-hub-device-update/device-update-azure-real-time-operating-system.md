---
title: Enhets uppdatering för Azure real tids drifts system | Microsoft Docs
description: Kom igång med enhets uppdatering för Azure real tids operativ system
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 66da860a5cdae1f5c7c18e4136b1f2d960492ca8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629061"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Enhets uppdatering för Azure IoT Hub-självstudie med Azure Real Time Opera ting system (återställnings tider)

Den här självstudien går igenom hur du skapar enhets uppdateringen för IoT Hub agent i Azure återställnings tider NetX Duo. Den innehåller också enkla API: er för utvecklare som vill integrera enhets uppdaterings funktionen i sina program. Utforska [exempel](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) på utvärderings kort för nyckel halvledare som omfattar kom igång-guider för att lära dig att konfigurera, bygga och distribuera OTA-uppdateringar (över-Air) till enheterna.

I den här självstudien får du lära dig hur man
> [!div class="checklist"]
> * Kom igång
> * Tagga din enhet
> * Skapa en enhetsgrupp.
> * Distribuera en avbildnings uppdatering
> * Övervaka uppdaterings distributionen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
* Åtkomst till en IoT Hub. Vi rekommenderar att du använder en S1-nivå (standard) eller högre.
* En instans av enhets uppdatering och ett konto som är länkat till IoT Hub. Följ guiden för att [skapa och länka](http://create-device-update-account.md/) ett enhets uppdaterings konto om du inte har gjort det tidigare.

## <a name="get-started"></a>Kom igång

Varje fordonsbaserad Azure återställnings tider-projekt innehåller kod och dokumentation om hur du använder enhets uppdatering för IoT Hub på den. 
1. Hämta de Board-/regionsspecifika exempelfilerna från [Azure-återställnings tider och enhets uppdaterings exempel](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU).
2. Hitta mappen dokument från det hämtade exemplet.
3. I dokumenten följer du stegen för att förbereda Azure-resurser, konto och registrera IoT-enheter på den.
5. Följ sedan dokumenten för att bygga en ny avbildning av inbyggd program vara och importera manifestet för din tavla.
6. Nästa publicera inbyggd program vara och manifestet till enhets uppdatering för IoT Hub.
7. Hämta och kör slutligen projektet på enheten.

Läs mer om [Azure återställnings tider](https://docs.microsoft.com/azure/rtos/).  

## <a name="tag-your-device"></a>Tagga din enhet

1. Behåll enhets programmet som körs från föregående steg.
2. Logga in på [Azure Portal](https://portal.azure.com) och navigera till IoT Hub.
3. Gå till IoT-enheter i det vänstra navigerings fönstret och leta upp din IoT-enhet och navigera till enheten med dubbla.
4. Ta bort eventuella befintliga enhets uppdaterings märken i enhets numret genom att ställa in dem på null.
5. Lägg till ett nytt märkes värde för enhets uppdatering som visas nedan.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>Skapa uppdaterings grupp

1. Gå till den IoT Hub du tidigare anslöt till din enhets uppdaterings instans.
2. Välj alternativet enhets uppdateringar under automatisk enhets hantering från det vänstra navigerings fältet.
3. Välj fliken grupper överst på sidan. 
4. Välj knappen Lägg till för att skapa en ny grupp.
5. Välj den IoT Hub-tagg som du skapade i föregående steg från listan. Välj Skapa uppdaterings grupp.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Skärm bild som visar val av tagg." lightbox="media/create-update-group/select-tag.PNG":::

[Lär dig mer](create-update-group.md) om att lägga till taggar och skapa uppdaterings grupper

## <a name="deploy-new-firmware"></a>Distribuera ny inbyggd program vara

1. När gruppen har skapats bör du se en ny uppdatering som är tillgänglig för enhets gruppen, med en länk till uppdateringen under väntande uppdateringar. Du kan behöva uppdatera en gång. 
2. Klicka på den tillgängliga uppdateringen.
3. Bekräfta att rätt grupp har valts som mål grupp. Schemalägg distributionen och välj sedan distribuera uppdatering.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Välj uppdatera" lightbox="media/deploy-update/select-update.png":::

4. Visa diagrammet efterlevnad. Nu bör du se att uppdateringen pågår. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Uppdatering pågår" lightbox="media/deploy-update/update-in-progress.png":::

5. När enheten har uppdaterats bör du se att ditt Compliance-diagram och distributions information uppdateras för att avspegla samma. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Uppdateringen är klar" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Övervaka en uppdaterings distribution

1. Välj fliken distributioner högst upp på sidan.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Fliken distributioner" lightbox="media/deploy-update/deployments-tab.png":::

2. Välj den distribution du skapade för att Visa distributions informationen.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Distributionsinformation" lightbox="media/deploy-update/deployment-details.png":::

3. Välj Uppdatera för att visa den senaste statusinformationen. Fortsätt med den här processen tills statusen har ändrats till slutfört.

Du har nu slutfört en lyckad avbildnings uppdatering från slut punkt till slut punkt med enhets uppdatering för IoT Hub på en Raspberry Pi 3 B +-enhet. 

## <a name="cleanup-resources"></a>Rensa resurser

När du inte längre behöver rensa ditt enhets uppdaterings konto, instans, IoT Hub och IoT-enhet. 

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure återställnings tider och hur det fungerar med Azure IoT kan du läsa mer i Azure IoT https://azure.com/rtos .
