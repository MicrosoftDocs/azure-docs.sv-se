---
title: Skapa ett enhets uppdaterings konto i enhets uppdatering för Azure IoT Hub | Microsoft Docs
description: Skapa ett enhets uppdaterings konto i enhets uppdatering för Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 5956b7b74d27a4f9a2b79ee3950c8ac765610c70
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558490"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Enhetsuppdatering för IoT Hub-resurshantering

För att komma igång med enhets uppdateringen måste du skapa ett konto för enhets uppdatering, instans och ange roller för åtkomst kontroll. 

## <a name="prerequisites"></a>Förutsättningar

* Åtkomst till en IoT Hub. Vi rekommenderar att du använder en S1-nivå (standard) eller högre. 
* Webbläsare som stöds:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Skapa ett konto för enhets uppdatering

1. Gå till [Azure Portal](https://portal.azure.com)

2. Klicka på skapa en resurs och Sök efter "enhets uppdatering för IoT Hub"

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Skärm bild av enhets uppdatering för IoT Hub resurs." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Klicka på Skapa > enhets uppdatering för IoT Hub

4. Ange Azure-prenumerationen som ska associeras med ditt enhets uppdaterings konto och resurs grupp

5. Ange ett namn och en plats för ditt enhets uppdaterings konto

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Skärm bild av konto information." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > Du kan gå till [sidan Azure products-by-region](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) för att identifiera de regioner där enhets uppdatering för IoT Hub är tillgänglig. Om enhets uppdateringen för IoT Hub inte är tillgänglig i din region kan du välja att skapa ett konto i en tillgänglig region som är närmast dig. 

6. Klicka på Nästa: granska + skapa>

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Skärm bild av konto information granska." lightbox="media/create-device-update-account/account-review.png":::

7. Granska informationen och välj sedan skapa. Du ser att distributionen pågår. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Skärm bild av konto distribution pågår." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. Du ser distributions status ändringen till slutfört om några minuter. Klicka på "gå till resurs"

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Skärm bild av konto distribution slutförd." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Skapa en enhets uppdaterings instans 

En instans av enhets uppdateringen är associerad med en enda IoT-hubb. Välj den IoT-hubb som ska användas med enhets uppdatering. Vi kommer att skapa en ny princip för delad åtkomst under det här steget för att säkerställa att enhets uppdateringen bara använder de behörigheter som krävs för att arbeta med IoT Hub (register skrivning och tjänst anslutning). Den här principen säkerställer att åtkomsten endast är begränsad till enhets uppdatering.

Skapa en enhets uppdaterings instans efter att ett konto har skapats.

1. När du är i den nyligen skapade konto resursen går du till bladet instans hantering "instanser"

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Skärm bild av instans hantering i konto." lightbox="media/create-device-update-account/instance-blade.png":::

2. Klicka på "skapa och ange ett instans namn och välj din IoT Hub

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Skärm bild av instans information." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > IoT Hub som du länkar till din enhets uppdaterings resurs måste inte finnas i samma region som ditt enhets uppdaterings konto. Men för bättre prestanda rekommenderar vi dock att IoT Hub finns i en region som eller nära den region där ditt enhets uppdaterings konto finns. 

3. Klicka på Skapa. Du kommer att se instansen i status "skapa". 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Skärm bild av instansen som skapar." lightbox="media/create-device-update-account/instance-creating.png":::

4. Tillåt 5-10 minuter att slutföra instansen av instans distributionen. Uppdatera statusen tills du ser att etablerings status är klar.

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="En skärm bild av instansen har skapats." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Konfigurera IoT Hub 

För att enhets uppdateringen ska kunna ta emot ändrings meddelanden från IoT Hub integreras enhets uppdateringen med den inbyggda Händelsehubben. Om du klickar på knappen "Konfigurera IoT Hub" konfigureras de nödvändiga meddelande vägarna och den åtkomst princip som krävs för att kommunicera med IoT-enheter. 

Konfigurera IoT Hub

1. När instansen "etablerings status" blir "lyckades" väljer du instansen på bladet instans hantering. Klicka på Konfigurera IoT Hub

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Skärm bild som visar hur du konfigurerar IoT Hub för en instans." lightbox="media/create-device-update-account/instance-configure.png":::

2. Välj "Jag accepterar att göra dessa ändringar"

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Skärm bild som visar hur man godkänner att konfigurera IoT Hub för en instans." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Klicka på uppdatera

[Lär dig mer om de meddelande vägar som har kon figurer ATS.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Konfigurera åtkomst kontroll roller

För att andra användare ska ha åtkomst till enhets uppdateringen måste användarna beviljas åtkomst till den här resursen. 

1. Gå till åtkomst kontroll (IAM) i enhets uppdaterings kontot

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Skärm bild av åtkomst kontroll i enhets uppdaterings konto." lightbox="media/create-device-update-account/account-access-control.png":::

2. Klicka på Lägg till roll tilldelningar

3. Under "Välj en roll" väljer du en enhets uppdaterings roll från de aktuella alternativen
     - Administratör för enhets uppdatering
     - Enhets uppdaterings läsare
     - Innehålls administratör för enhets uppdatering
     - Innehålls läsare för enhets uppdatering
     - Administratör för enhets uppdaterings distribution
     - Distributions läsare för enhets uppdatering
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Skärm bild av roll tilldelningar för åtkomst kontroll i enhets uppdaterings konto." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Lär dig mer om rollbaserad åtkomst kontroll i enhets uppdatering för IoT Hub](device-update-control-access.md) 
    
4. Tilldela åtkomst till en användare eller Azure AD-grupp
5. Klicka på Spara
6. Du är nu redo att använda enhets uppdaterings upplevelsen inifrån din IoT Hub

## <a name="next-steps"></a>Nästa steg

Försök att uppdatera en enhet med någon av följande snabb Självstudier:

 - [Enhets uppdatering på en simulator](device-update-simulator.md)
 - [Enhets uppdatering på Raspberry Pi](device-update-raspberry-pi.md)
 - [Enhets uppdatering på Ubuntu Server 18,04 x64 Package agent](device-update-ubuntu-agent.md)

[Lär dig mer om enhets uppdaterings konto och instans.](device-update-resources.md) 

[Lär dig mer om roller för åtkomst kontroll för enhets uppdatering. ](device-update-control-access.md) 

