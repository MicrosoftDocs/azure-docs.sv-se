---
title: ta med fil
description: ta med fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 4e2abda6e0e3ef3d638952c05c31a50d91d24e88
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901226"
---
Använda Azure-portalen för att skapa en IoT-hubb:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På Start sidan för Azure väljer du **skapa en resurs** och anger sedan *IoT Hub* i **Sök på Marketplace**.

1. Välj **IoT Hub** från Sök resultaten och välj sedan **skapa**.

1. På fliken **grundläggande** inställningar fyller du i fälten på följande sätt:

   - **Prenumeration**: Välj den prenumeration som ska användas för hubben.

   - **Resurs grupp**: Välj en resurs grupp eller skapa en ny. Om du vill skapa en ny väljer du **Skapa ny** och fyller i det namn som du vill använda. Om du vill använda en befintlig resurs grupp väljer du den resurs gruppen. Mer information finns i [Hantera Azure Resource Manager-resursgrupper](../articles/azure-resource-manager/management/manage-resource-groups-portal.md). I den här självstudiekursen används namnet **tutorial-iot-hub-rg**.

   - **Region**: Välj den region där du vill att hubben ska placeras. Välj den plats som är närmast dig. Vissa funktioner, till exempel [IoT Hub enhets strömmar](../articles/iot-hub/iot-hub-device-streams-overview.md), är bara tillgängliga i vissa regioner. För dessa begränsade funktioner måste du välja en av de regioner som stöds. I den här självstudien används regionen **USA, västra** .

   - **IoT Hub namn**: Ange ett namn för hubben. Det här namnet måste vara globalt unikt. I den här självstudien används **självstudier – IoT-Hub**. Du måste välja ett eget unikt namn när du skapar din hubb.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Skapa ett nav i Azure Portal](media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png)

1. Välj **Nästa: nätverk** för att fortsätta skapa hubben.

   Välj de slut punkter som kan ansluta till din IoT Hub. Du kan välja den **offentliga slut punkten för standardinställning (alla nätverk)** eller välja **offentlig slut punkt (valda IP-intervall)** eller **privat slut punkt**. Godkänn standardinställningen för den här självstudien.

   ![Välj de slut punkter som kan ansluta](media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png)

1. Välj **Nästa: hantering** för att fortsätta skapa hubben.

    ![Ange storlek och skala för en ny hubb med hjälp av Azure Portal](media/iot-hub-tutorials-create-free-hub/hub-definition-management.png)

    Du kan acceptera standardinställningarna här. Om du vill kan du ändra något av följande fält:

    - **Pris-och skalnings nivå**: den valda nivån. Välj den kostnads fria nivån. Den kostnadsfria nivån är avsedd för testning och utvärdering. Det gör att 500-enheter kan anslutas till hubben och upp till 8 000 meddelanden per dag. Varje Azure-prenumeration kan skapa en IoT-hubb på den kostnads fria nivån.

    - **IoT Hub-enheter**: Antalet meddelanden som tillåts per enhet per dag beror på hubbens prisnivå. Om du till exempel vill att hubben ska stödja ingångar av 700 000-meddelanden väljer du två enheter i S1-nivån.
    Varje Azure-prenumeration kan skapa en IoT-hubb på den kostnads fria nivån. Mer information om de andra alternativen för nivån finns i avsnittet om att [välja rätt nivå för IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

    - **Defender för IoT**: aktivera det här för att lägga till ett extra lager med hot skydd i IoT och dina enheter. Det här alternativet är inte tillgängligt för hubbar på den kostnads fria nivån. Mer information om den här funktionen finns [Azure Security Center for IoT](/azure/asc-for-iot/).

    - **Avancerade inställningar**  >  **Partitioner från enhet till moln**: den här egenskapen relaterar meddelanden från enhet till moln till antalet samtidiga läsare av meddelanden. De flesta hubbar behöver bara fyra partitioner. En nav på den kostnads fria nivån är begränsad till två partitioner.

1.  Välj **Nästa: Taggar** för att fortsätta till nästa skärm.

    Taggar är namn/värde-par. Du kan tilldela samma tagg till flera resurser och resurs grupper för att kategorisera resurser och konsolidera fakturering. Mer information finns i [använda taggar för att ordna dina Azure-resurser](../articles/azure-resource-manager/management/tag-resources.md).

    ![Tilldela taggar för hubben med hjälp av Azure Portal](media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png)

1.  Välj **Nästa: granska + skapa** för att granska dina val. Du ser något som liknar den här skärmen, men med de värden som du valde när du skapade hubben.

    ![Granska informationen för att skapa den nya hubben](media/iot-hub-tutorials-create-free-hub/hub-definition-create.png)

1. Anteckna IoT-hubbnamnet som du har valt. Du använder det här värdet senare i kursen.
