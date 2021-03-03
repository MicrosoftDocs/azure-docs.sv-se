---
title: Uppdatera din Azure percept DK över luften
description: Lär dig att ta emot över Air-uppdateringar till din Azure percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: aa8f696b6a26e812256be4e93975844f2c721b6e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663855"
---
# <a name="update-your-azure-percept-dk-over-the-air"></a>Uppdatera din Azure percept DK över luften

Följ den här guiden för att lära dig hur du uppdaterar operatörs kortet för din Azure percept DK-trafik med enhets uppdatering för IoT Hub.

## <a name="import-your-update-file-and-manifest-file"></a>Importera din uppdaterings fil och MANIFEST fil.

> [!NOTE]
> Om du redan har importerat uppdateringen kan du hoppa över direkt för att **skapa en enhets** uppdaterings grupp.

1. Gå till Azure-IoT Hub som du använder för din Azure percept-enhet. I den vänstra panelen väljer du **enhets uppdateringar** under **Automatisk enhets hantering**.
 
1. Flera flikar visas överst på skärmen. Välj fliken **uppdateringar** .
 
1. Välj **+ Importera ny uppdatering** under rubriken **redo att distribuera** .
 
1. Klicka på rutorna under **Välj Importera manifest fil** och **Välj uppdatera filer** för att välja lämplig manifest fil (. JSON) och en uppdaterings fil (. SWU). Du hittar dessa uppdateringsfiler för din Azure percept-enhet [här](https://go.microsoft.com/fwlink/?linkid=2155625).
 
1. Välj mappikonen eller text rutan under **Välj en lagrings behållare** och välj sedan lämpligt lagrings konto.
 
1. Om du redan har skapat en lagrings behållare kan du använda den igen. Annars väljer du **+ container** för att skapa en ny lagrings behållare för OTA-uppdateringar. Välj den behållare som du vill använda och klicka på **Välj**.
 
    >[!Note]
    >Om du inte har någon behållare kommer du att uppmanas att skapa en.
 
1. Välj **Skicka** för att starta import processen. Sändnings processen tar cirka 4 minuter.
 
    >[!Note]
    >Du kan bli ombedd att lägga till en CORS-regel (Cross Origin Request) för åtkomst till den valda lagrings behållaren. Välj **Lägg till regel och försök igen** .
 
    >[!Note]
    >På grund av bild storleken kan du se att sidan **skickas...** I upp till 5 min innan du ser nästa steg.
    
1. Import processen börjar och du omdirigeras till fliken **import historik** på sidan **enhets uppdateringar** . Klicka på **Uppdatera** för att övervaka förloppet medan importen är slutförd. Beroende på Uppdateringens storlek kan det ta några minuter eller längre tid (vid hög belastnings tider, vänta till att import tjänsten tar upp till 1 tim).

1. När kolumnen status visar att importen har slutförts väljer du fliken **redo att distribuera** och klickar på **Uppdatera**. Nu bör du se den importerade uppdateringen i listan.
 
## <a name="create-a-device-update-group"></a>Skapa en enhets uppdaterings grupp
Med enhets uppdatering för IoT Hub kan du rikta en uppdatering till specifika grupper av Azure percept-DKs. Om du vill skapa en grupp måste du lägga till en tagg till din mål uppsättning enheter i Azure IoT Hub.

> [!NOTE]
> Om du redan har skapat en grupp kan du gå direkt till nästa steg.

Grupp märkes krav:
- Du kan lägga till valfritt värde i taggen, förutom för **Okategoriserade**, vilket är ett reserverat värde.
- Tagg-värdet får inte överstiga 255 tecken.
- Taggvärde får bara innehålla följande specialtecken: ".", "-", "_", "~".
- Tagg-och grupp namn är Skift läges känsliga.
- En enhet kan bara ha en tagg. Eventuella efterföljande taggar som läggs till på enheten åsidosätter den tidigare taggen.
- En enhet kan endast tillhöra en grupp.

1. Lägg till en tagg till din enhet (er).
    1. Gå till **IoT Edge** i det vänstra navigerings fönstret och leta upp din Azure percept dk och navigera till **enheten med dubbla**.
    1. Lägg till en ny **enhets uppdatering för IoT Hub** märkes värde enligt nedan (ändra ```<CustomTagValue>``` till ditt värde, t. ex. AzurePerceptGroup1). Lär dig mer om enhets dubbla [JSON-dokument Taggar](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins#device-twins).

    ```
    "tags": {
    "ADUGroup": "<CustomTagValue>"
    },
    ```

 
1. Klicka på **Spara** och Lös eventuella formaterings problem.
 
1. Skapa en grupp genom att välja en befintlig Azure IoT Hub-tagg.
    1. Gå tillbaka till din Azure IoT Hub-sida.
    1. Välj **enhets uppdateringar** under **Automatisk enhets hantering** på den vänstra meny panelen.
    1. Välj fliken **grupper** . På den här sidan visas antalet uppdelade enheter som är anslutna till enhets uppdatering.
    1. Välj **+ Lägg** till för att skapa en ny grupp.
    1. Välj en IoT Hub-tagg i listan och klicka på **Skicka**.
    1. När gruppen har skapats uppdateras listan uppdatera efterlevnad för diagrammet och gruppen. Diagrammet visar antalet enheter i olika stadier av kompatibilitet: **den senaste uppdateringen**, **nya uppdateringar som är tillgängliga**, **uppdateringar** pågår och **ännu inte grupper ATS**.
 

## <a name="deploy-an-update"></a>Distribuera en uppdatering
1. Du bör se den nyligen skapade gruppen med en ny uppdatering som visas under **tillgängliga uppdateringar** (du kan behöva uppdatera en gång). Välj uppdateringen.
 
1. Bekräfta att rätt enhets grupp har valts som mål enhets grupp. Välj ett **start datum** och **Start tid** för distributionen och klicka sedan på **skapa distribution**. 

    >[!CAUTION]
    >När du anger start tiden kommer det tidigare att utlösa distributionen omedelbart.
 
1. Kontrol lera diagrammet efterlevnad. Nu bör du se att uppdateringen pågår.
 
1. När uppdateringen har slutförts visar ditt Compliance-diagram din nya uppdaterings status.
 
1. Välj fliken **distributioner** högst upp på sidan **enhets uppdateringar** .
 
1. Välj din distribution för att Visa distributions informationen. Du kan behöva klicka på **Uppdatera** tills **statusen** har ändrats till **lyckades**.

## <a name="next-steps"></a>Nästa steg

Ditt dev-paket har nu uppdaterats. Du kan fortsätta utveckling och drift med din devkit.