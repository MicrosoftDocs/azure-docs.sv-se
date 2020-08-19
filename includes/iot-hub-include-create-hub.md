---
title: inkludera fil
description: inkludera fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d7ac494edfb9b1deeceafcac523ac58bccde96b3
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "85365213"
---
I det här avsnittet beskrivs hur du använder [Azure-portalen](https://portal.azure.com) för att skapa en IoT-hubb.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På Start sidan för Azure väljer du knappen **+ skapa en resurs** och anger sedan *IoT Hub* i fältet **Sök i Marketplace** .

1. Välj **IoT Hub** från Sök resultaten och välj sedan **skapa**.

1. På fliken **grundläggande** inställningar fyller du i fälten på följande sätt:

   - **Prenumeration**: Välj den prenumeration som ska användas för hubben.

   - **Resurs grupp**: Välj en resurs grupp eller skapa en ny. Om du vill skapa en ny väljer du **Skapa ny** och fyller i det namn som du vill använda. Om du vill använda en befintlig resurs grupp väljer du den resurs gruppen. Mer information finns i [Hantera Azure Resource Manager-resursgrupper](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Region**: Välj den region där du vill att hubben ska placeras. Välj den plats som är närmast dig. Vissa funktioner, till exempel [IoT Hub enhets strömmar](../articles/iot-hub/iot-hub-device-streams-overview.md), är bara tillgängliga i vissa regioner. För dessa begränsade funktioner måste du välja en av de regioner som stöds.

   - **IoT Hub namn**: Ange ett namn för hubben. Det här namnet måste vara globalt unikt. Om namnet som du anger är tillgängligt visas en grön bockmarkering.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Skapa ett nav i Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. Välj **Nästa: storlek och skala** för att fortsätta skapa hubben.

   ![Ange storlek och skala för en ny hubb med hjälp av Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   Du kan acceptera standardinställningarna här. Om du vill kan du ändra något av följande fält: 

    - **Pris-och skalnings nivå**: den valda nivån. Du kan välja mellan flera nivåer, beroende på hur många funktioner du vill och hur många meddelanden du skickar genom din lösning per dag. Den kostnadsfria nivån är avsedd för testning och utvärdering. Det gör att 500-enheter kan anslutas till hubben och upp till 8 000 meddelanden per dag. Varje Azure-prenumeration kan skapa en IoT-hubb på den kostnads fria nivån. 

      Om du arbetar med en snabb start för IoT Hub enhets strömmar väljer du den kostnads fria nivån.

    - **IoT Hub-enheter**: Antalet meddelanden som tillåts per enhet per dag beror på hubbens prisnivå. Om du till exempel vill att hubben ska stödja ingångar av 700 000-meddelanden väljer du två enheter i S1-nivån.
    Mer information om de andra alternativen för nivån finns i avsnittet om att [välja rätt nivå för IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

    - **Azure Security Center**: aktivera det här för att lägga till ett extra lager med hot skydd i IoT och dina enheter. Det här alternativet är inte tillgängligt för hubbar på den kostnads fria nivån. Mer information om den här funktionen finns [Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/).

    - **Avancerade inställningar**  >  **Partitioner från enhet till moln**: den här egenskapen relaterar meddelanden från enhet till moln till antalet samtidiga läsare av meddelanden. De flesta hubbar behöver bara fyra partitioner.

1.  Välj **Nästa: Taggar** för att fortsätta till nästa skärm.

    Taggar är namn/värde-par. Du kan tilldela samma tagg till flera resurser och resurs grupper för att kategorisera resurser och konsolidera fakturering. Mer information finns i [använda taggar för att ordna dina Azure-resurser](../articles/azure-resource-manager/management/tag-resources.md).

    ![Tilldela taggar för hubben med hjälp av Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

1.  Välj **Nästa: granska + skapa** för att granska dina val. Du ser något som liknar den här skärmen, men med de värden som du valde när du skapade hubben. 

    ![Granska informationen för att skapa den nya hubben](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1.  Välj **skapa** för att skapa din nya hubb. Det tar några minuter att skapa hubben.
