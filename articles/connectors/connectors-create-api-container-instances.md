---
title: Distribuera & Hantera Azure Container Instances
description: Automatisera aktiviteter och arbets flöden som skapar och hanterar behållar distributioner i Azure Container Instances genom att använda Azure Logic Apps
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76046297"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Distribuera och hantera Azure Container Instances med Azure Logic Apps

Med Azure Logic Apps och Azure Container instance Connector kan du konfigurera automatiserade uppgifter och arbets flöden som distribuerar och hanterar [behållar grupper](../container-instances/container-instances-container-groups.md). Container instance Connector stöder följande åtgärder:

* Skapa eller ta bort en behållar grupp
* Hämta egenskaperna för en behållar grupp
* Hämta en lista över behållar grupper
* Hämta loggarna för en behållar instans

Använd de här åtgärderna i Logi Kap par för uppgifter som att köra en behållar arbets belastning som svar på en Logic Apps-utlösare. Du kan också använda andra åtgärder för att använda utdata från behållar instansen. 

Den här anslutnings tjänsten tillhandahåller endast åtgärder, så för att starta din Logi Kap par, använder du en separat utlösare som en **upprepnings** utlösare för att köra en behållar arbets belastning enligt ett regelbundet schema Eller så kanske du måste utlösa en distribution av container grupper efter en händelse, till exempel när ett Outlook-e-postmeddelande har anlänt. 

Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md) och [hur du skapar och hanterar behållar instanser](../container-instances/container-instances-quickstart.md)

* Den Logic app där du vill komma åt dina behållar instanser. Om du vill använda en åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="add-a-container-instance-action"></a>Lägg till en åtgärd för behållar instans

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Välj en sökväg: 

   * Under det sista steget där du vill lägga till en åtgärd väljer du **nytt steg**. 

     \- eller -

   * Mellan stegen där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
   Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger du "container instance" som filter. Under listan åtgärder väljer du den Azure Container instance Connector-åtgärd som du vill använda.

1. Ange ett namn på anslutningen. 

1. Ange nödvändig information för den valda åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

  Välj till exempel **skapa container grupp** och ange egenskaperna för en behållar grupp och en eller flera behållar instanser i gruppen, som visas i följande bild (del detaljer):

  ![Skapa containergrupp](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/aci/) eller behållar grupp [yaml referens](../container-instances/container-instances-reference-yaml.md).

## <a name="next-steps"></a>Nästa steg

* Se en [exempel](https://github.com/Azure-Samples/aci-logicapps-integration) på en Logic-app som kör en behållare i Azure Container instances för att analysera sentiment för e-post eller Twitter-text

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)