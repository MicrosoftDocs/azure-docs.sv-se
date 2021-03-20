---
title: Snabb start – konfigurera IoT Hub Device Provisioning Service i Azure Portal
description: Snabb start – konfigurera Azure-IoT Hub Device Provisioning Service (DPS) i Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: e1bd521e9798b09f7930b43ab95c7cd7ef9e693d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737927"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Snabb start: Konfigurera IoT Hub Device Provisioning Service med Azure Portal

IoT Hub Device Provisioning Service kan användas med IoT Hub för att möjliggöra Zero-Touch-etablering, just-in-Time-etablering till önskad IoT-hubb utan mänsklig inblandning, så att kunderna kan etablera miljon tals IoT-enheter på ett säkert och skalbart sätt. Azure IoT Hub Device Provisioning Service stöder IoT-enheter med autentisering med TPM, symmetrisk nyckel och X. 509-certifikat. Mer information finns i [IoT Hub Device Provisioning service översikt](./about-iot-dps.md)

I den här snabb starten får du lära dig hur du ställer in IoT Hub Device Provisioning Service i Azure Portal för att konfigurera dina enheter med följande steg:

* Använd Azure Portal för att skapa en IoT Hub
* Använd Azure-portalen för att skapa en IoT Hub Device Provisioning-tjänst och hämta ID-omfånget
* Länka IoT-hubben till Device Provisioning-tjänsten

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration för att börja med den här artikeln. Du kan skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte redan har gjort det.


## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-iot-hub-device-provisioning-service"></a>Skapa en ny IoT Hub Device Provisioning Service

1. Välj knappen **+ skapa en resurs** igen.

2. *Sök i Marketplace* efter **enhets etablerings tjänsten**. Välj **IoT Hub Device Provisioning service** och tryck på knappen **skapa** . 

3. Ange följande information för din nya enhets etablerings tjänst instans och tryck på **skapa**.

    * **Namn:** Ange ett unikt namn för din nya enhets etablerings tjänst instans. Om namnet som du anger är tillgängligt visas en grön bockmarkering.
    * **Prenumeration:** Välj den prenumeration som du vill använda för att skapa den här instansen av enhets etablerings tjänsten.
    * **Resursgrupp:** I det här fältet kan du skapa en ny resursgrupp eller välja en befintlig som ska innehålla den nya instansen. Välj resursgruppen som innehåller den IoT-hubb du skapade, till exempel **TestResources**. Genom att lägga till alla relaterade resurser i en grupp kan du hantera dem tillsammans. Till exempel tas alla resurser som ingår i gruppen bort om resursgruppen tas bort. Mer information finns i [Hantera Azure Resource Manager-resursgrupper](../azure-resource-manager/management/manage-resource-groups-portal.md).
    * **Plats:** Välj den plats som är närmast till dina enheter.

      ![Ange grundläggande information om din enhets etablerings tjänst instans på Portal bladet](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Välj knappen meddelande för att övervaka skapandet av resurs instansen. När tjänsten har distribuerats väljer du **Fäst på instrument panelen** och **går sedan till resurs**.

    ![Övervaka distributionsmeddelandet](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Länka IoT-hubben och enhets etablerings tjänsten

I det här avsnittet ska du lägga till en konfiguration i enhets etablerings tjänst instansen. Den här konfigurationen anger den IoT-hubb för vilken enheter tillhandahålls.

1. Välj knappen **alla resurser** på den vänstra menyn i Azure Portal. Välj Device Provisioning-tjänstinstansen som du skapade i det föregående avsnittet. 

    Om din meny har kon figurer ATS med **utfällning** i stället för **dockat** läge i Portal inställningarna måste du klicka på de tre raderna längst upp till vänster för att öppna Portal menyn till vänster.  

2. Från enhets Provisioning-tjänstens meny väljer du **länkade IoT-hubbar**. Tryck på knappen **+ Lägg till** som visas överst. 

3. På sidan **Lägg till länk till IoT Hub** anger du följande information för att länka din nya enhets etablerings tjänst instans till en IoT-hubb. Tryck sedan på **Spara**. 

    * **Prenumeration:** Välj den prenumeration som innehåller IoT-hubben som du vill länka till din nya enhets etablerings tjänst instans.
    * **IoT-hubb:** Välj IoT Hub för att länka till din nya enhets etablerings tjänst instans.
    * **Åtkomstprincip:** Välj **iothubowner** som autentiseringsuppgifter när du upprättar länken till IoT-hubben.  

      ![Länka hubbens namn för att länka till Device Provisioning service-instansen på Portal bladet](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Nu bör den valda hubben visas under bladet **Linked IoT hubs** (Länkade IoT-hubbar). Du kan behöva klicka på **Uppdatera** för att visa.


## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure-portalen.

1. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan enhets etablerings tjänsten. Välj **ta bort** överst i enhets informations fönstret.  
2. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan din IoT Hub. Välj **ta bort** överst i informations fönstret hubb.  

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat en IoT-hubb och en enhets etablerings tjänst instans och länkat de två resurserna. Om du vill lära dig hur du använder den här installationen för att etablera en simulerad enhet fortsätter du till snabb starten för att skapa en simulerad enhet.

> [!div class="nextstepaction"]
> [Snabb start för att skapa en simulerad enhet](./quick-create-simulated-device-symm-key.md)
