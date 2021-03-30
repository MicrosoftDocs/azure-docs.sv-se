---
title: ta med fil
description: ta med fil
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/16/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bf107da82fb3f772a341e70ce472f08ea674a450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75692587"
---
### <a name="create-a-storage-account-for-event-processor-host"></a>Skapa ett lagringskonto för Event Processor Host
Event Processor Host är en smart agent som gör det enklare att ta emot händelser från Event Hubs genom hantering av permanenta kontrollpunkter och parallella mottaganden. För att kunna använda kontrollpunkter måste Event Processor Host ha ett lagringskonto. I följande exempel visas hur du skapar ett lagringskonto och hur du kommer åt dess åtkomstnycklar:

1. I menyn i Azure-portalen väljer du **Skapa en resurs**.

    ![Skapa ett resurs meny objekt Microsoft Azure-portalen](./media/event-hubs-create-storage/create-resource.png)

2. Välj **Lagring** > **Lagringskonto**.
   
    ![Välj lagrings konto Microsoft Azure-portalen](./media/event-hubs-create-storage/select-storage-account.png)

3. Gör följande på sidan **Skapa lagringskonto**: 

   1. Ange **namnet på lagrings kontot**.
   2. Välj en Azure- **prenumeration** som innehåller händelsehubben.
   3. Välj eller skapa **resurs gruppen** som har händelsehubben.
   4. Välj en **plats** där du vill skapa resursen. 
   5. Välj **Granska + skapa**.
   
        ![Granska + skapa, skapa lagrings konto Microsoft Azure-portalen](./media/event-hubs-create-storage/review-create.png)

4. På sidan **Granska + skapa** granskar du värdena och väljer **Skapa**. 

    ![Granska inställningarna för lagrings kontot och skapa Microsoft Azure-portalen](./media/event-hubs-create-storage/create-storage-account.png)
5. När du ser meddelandet **distributioner lyckades** i dina meddelanden väljer du **gå till resurs** för att öppna sidan lagrings konto. Du kan också expandera **distributions information** och sedan välja den nya resursen i resurs listan.  

    ![Gå till resurs, lagrings konto distribution, Microsoft Azure-portalen](./media/event-hubs-create-storage/go-to-resource.png) 
6. Välj **behållare**.

    ![Välj blobar container service, lagrings konton Microsoft Azure-portalen](./media/event-hubs-create-storage/select-blob-container-service.png)
7. Välj **+ behållare** högst upp, ange ett **namn** för behållaren och välj **OK**. 

    ![Skapa en ny BLOB-behållare, lagrings konton Microsoft Azure-portalen](./media/event-hubs-create-storage/create-new-blob-container.png)
8. Välj **åtkomst nycklar** på sidan **lagrings konto** och kopiera värdet för **KEY1**.

    Spara följande värden i anteckningar eller på någon annan tillfällig plats.
    - Namn på lagrings kontot
    - Åtkomst nyckel för lagrings kontot
    - Behållarens namn
