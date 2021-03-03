---
title: Skapa enhets grupp i enhets uppdatering för Azure IoT Hub | Microsoft Docs
description: Skapa en enhets grupp i enhets uppdatering för Azure IoT Hub
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680031"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>Skapa enhets grupper i enhets uppdatering för IoT Hub
Enhets uppdatering för IoT Hub gör det möjligt att distribuera en uppdatering till en grupp med IoT-enheter.

## <a name="prerequisites"></a>Förutsättningar

* [Åtkomst till en IoT Hub med enhets uppdatering för IoT Hub aktive rad](create-device-update-account.md). Vi rekommenderar att du använder en S1-nivå (standard) eller högre för IoT Hub. 
* En IoT-enhet (eller Simulator) etablerad för enhets uppdatering i IoT Hub.
* [Minst en uppdatering har importer ATS för den etablerade enheten.](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>Lägg till en tagg till dina enheter  

Enhets uppdatering för IoT Hub gör det möjligt att distribuera en uppdatering till en grupp med IoT-enheter. Det första steget är att lägga till en tagg till mål uppsättningen med enheter i IoT Hub för att skapa en grupp. Taggar kan bara läggas till på enheten när den har anslutits till enhets uppdateringen.

I dokumentationen nedan beskrivs hur du lägger till och uppdaterar en-tagg.

### <a name="programmatically-update-device-twin"></a>Uppdatera enhet program mässigt med dubbla

Du kan uppdatera enheten med en lämplig tagg med hjälp av RegistryManager när enheten har registrerats med enhets uppdatering. 
[Lär dig hur du lägger till taggar med en exempel-.NET-app.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[Lär dig mer om Taggegenskaper](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format).

#### <a name="device-update-tag-format"></a>Etikett format för enhets uppdatering

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>Använda jobb

Det går att schemalägga ett jobb på flera enheter för att lägga till eller uppdatera en enhets uppdaterings tagg enligt [dessa](../iot-hub/iot-hub-devguide-jobs.md) exempel. [Läs mer](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > Den här åtgärden går mot din aktuella kvot för IOT Hub-meddelanden och vi rekommenderar att du bara ändrar upp till 50 000 enhets dubbla Taggar i taget, annars kan du behöva köpa fler IoT Hub enheter om du överskrider din dagliga IoT Hub meddelande kvot. Information finns i [kvoter och begränsning](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling).

### <a name="direct-twin-updates"></a>Direkta dubbla uppdateringar

Taggar kan också läggas till eller uppdateras direkt i enheten.

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till din IoT Hub.

2. Från IoT-enheter eller IoT Edge i det vänstra navigerings fönstret hittar du din IoT-enhet och navigerar till enheten med dubbla.

3. Ta bort eventuella befintliga enhets uppdaterings märken i enhets numret genom att ställa in dem på null.

4. Lägg till ett nytt märkes värde för enhets uppdatering som visas nedan. [Exempel på enhets dubbla JSON-dokument med taggar.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>Begränsningar

* Du kan lägga till valfritt värde i taggen förutom "Okategoriserade", vilket är ett reserverat värde.
* Tagg-värdet får inte överstiga 255 tecken.
* Taggvärde får innehålla alfanumeriska tecken och följande specialtecken ".", "-", "_", "~".
* Tagg-och grupp namn är Skift läges känsliga.
* En enhet kan bara ha en tagg med namnet ADUGroup, eventuella efterföljande tillägg till en tagg med det namnet kommer att åsidosätta det befintliga värdet för taggens namn ADUGroup.
* En enhet kan endast tillhöra en grupp.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>Skapa en enhets grupp genom att välja en befintlig IoT Hub-tagg

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Välj IoT Hub som du tidigare anslöt till din enhets uppdaterings instans.

3. Välj alternativet enhets uppdateringar under automatisk enhets hantering från det vänstra navigerings fältet.

4. Välj fliken grupper överst på sidan. Du kommer att kunna se antalet enheter som är anslutna till enhets uppdatering som inte är grupperade än.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Skärm bild av uppdelade enheter." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Välj knappen Lägg till för att skapa en ny grupp.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Skärm bild av enhets grupp tillägg." lightbox="media/create-update-group/add-group.png":::

6. Välj en IoT Hub tagg i listan och välj sedan skapa uppdaterings grupp.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Skärm bild av val av tagg." lightbox="media/create-update-group/select-tag.png":::

7. När gruppen har skapats kommer du att se att listan uppdatera efterlevnad för diagrammet och gruppen har uppdaterats.  Uppdatera Compliance-diagrammet visar antalet enheter i olika tillstånd: vid senaste uppdatering, nya uppdateringar, uppdateringar pågår och enheter som ännu inte har grupper ATS. [Lär dig mer om att uppdatera efterlevnad.](device-update-compliance.md) 
    :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Skärm bild av vyn uppdatera efterlevnad." lightbox="media/create-update-group/updated-view.png":::

8. Du bör se den nyligen skapade gruppen och eventuella tillgängliga uppdateringar för enheterna i den nya gruppen. Du kan distribuera uppdateringen till den nya gruppen från den här vyn genom att klicka på uppdateringens namn. Se nästa steg: Distribuera uppdatering för mer information.

## <a name="view-device-details-for-the-group-you-created"></a>Visa enhets information för den grupp som du har skapat

1. Navigera till den nya gruppen och klicka på grupp namnet.

2. En lista över enheter som ingår i gruppen visas tillsammans med deras egenskaper för enhets uppdatering. I den här vyn kan du också se uppdaterad kompatibilitetsinformation för alla enheter som är medlemmar i gruppen. Uppdatera Compliance-diagrammet visar antalet enheter i olika tillstånd: vid senaste uppdatering är nya uppdateringar tillgängliga och uppdateringar pågår.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Skärm bild av vyn information om enhets grupp." lightbox="media/create-update-group/group-details.png":::

3. Du kan också klicka på varje enskild enhet i en grupp som ska omdirigeras till sidan med enhets information i IoT Hub.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Skärm bild av vyn enhets information." lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>Nästa steg 

[Distribuera uppdatering](deploy-update.md)

[Läs mer om enhets grupper](device-update-groups.md)

[Lär dig mer om att uppdatera efterlevnad.](device-update-compliance.md)
