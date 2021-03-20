---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d2c309340155bc626d4da94d74aee9be51bde510
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90606418"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Skapa ett namnområde i Azure Portal
För att komma igång med Service Bus-meddelandeentiteter i Azure måste du först skapa ett namnområde med ett namn som är unikt i Azure. Ett namnområde innehåller en omfattningscontainer för adressering av Service Bus-resurser i ditt program.

Så här skapar du ett namnområde:

1. Logga in på [Azure-portalen](https://portal.azure.com)
2. I det vänstra navigeringsfältet i portalen väljer du **+ Skapa en resurs** följt av **Integration** och sedan **Service Bus**.

    ![Skapa en resurs -> Integration -> Service Bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. I dialogrutan **Skapa namnrymd** utför du följande steg: 
    1. Ange ett **namn för namnrymden**. Systemet kontrollerar omedelbart om namnet är tillgängligt. En lista över regler för namngivning av namn områden finns i [skapa namn område REST API](/rest/api/servicebus/create-namespace).
    2. Välj prisnivå (Basic, Standard eller Premium) för namnrymden. Om du vill använda [ämnen och prenumerationer](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) väljer du antingen Standard eller Premium. Ämnen/prenumerationer stöds inte på Basic-prisnivån.
    3. Om du valde prisnivån **Premium** utför du följande steg: 
        1. Ange antalet **meddelandefunktionsenheter**. Premium-nivån ger resursisolering på processor- och minnesnivå så att varje arbetsbelastning körs självständigt. Den här resurscontainern kallas för en meddelandefunktionsenhet. Ett Premium-namnområde har minst en meddelande enhet. Du kan välja 1, 2 eller 4 meddelandefunktionsenheter för varje Service Bus Premium-namnrymd. Mer information finns i [Service Bus Premium-meddelanden](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Ange huruvida du vill göra namnrymden **zonredundant**. Zonredundans ger förbättrad tillgänglighet genom att sprida repliker över tillgänglighetszoner inom en region utan extra kostnad. Mer information finns i [Tillgänglighetszoner i Azure](../articles/availability-zones/az-overview.md).
    4. För **Prenumeration** väljer du en Azure-prenumeration där du ska skapa namnrymden.
    5. För **Resursgrupp** väljer du en befintlig resursgrupp där namnrymden ska finnas, eller så skapar du en ny.      
    6. För **Plats** väljer du land eller region där namnrymden ska finnas.
    7. Välj **Skapa**. Systemet skapar namnområdet och aktiverar det. Du kan behöva vänta några minuter medan systemet tilldelar resurser till ditt konto.
   
        ![Skapa namnområde](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Bekräfta att Service Bus-namnrymden har distribuerats. För att visa meddelandena väljer du **klockikonen (aviseringar)** i verktygsfältet. Välj **namnet på resursgruppen** i meddelandet såsom det visas i bilden. Du ser då den resursgrupp som innehåller Service Bus-namnrymden.

    ![Distributionsavisering](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. På sidan **Resursgrupp** för resursgruppen väljer du din **Service Bus-namnrymd**. 

    ![Sidan Resursgrupp – välja Service Bus-namnrymd](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Startsidan för din Service Bus-namnrymd visas. 

    ![Startsidan för din Service Bus-namnrymd](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>Hämta anslutningssträngen 
Om du skapar ett nytt namnområde så genereras en automatiskt en ursprunglig regel för signatur för delad åtkomst (SAS) med ett kopplat par med primära och sekundära nycklar som ger fullständig kontroll över alla namnområdets aspekter. Se [Service Bus – autentisering och auktorisering](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) för mer information om hur du skapar regler med mer begränsade rättigheter för regelbundna sändare och mottagare. Kopiera primärnycklar och sekundärnycklar för namnrymden genom att utföra följande steg: 

1. Klicka på **Alla resurser**, sedan klickar du på den nyligen skapade namnrymden.
2. I namnområdesfönstret, klickar du på **Principer för delad åtkomst**.
3. I fönstret **Principer för delad åtkomst**, klickar du på **RootManageSharedAccessKey**.
   
    ![Skärm bild som visar fönstret principer för delad åtkomst med en princip markerad.](./media/service-bus-create-namespace-portal/connection-info.png)
4. I fönstret **Princip: RootManageSharedAccessKey** klickar du på knappen Kopiera bredvid **Primär anslutningssträng** för att kopiera anslutningssträngen till dina urklipp för senare användning. Klistra in det här värdet i Anteckningar eller på en tillfällig plats.
   
    ![Skärm bilden visar en S-princip som kallas RootManageSharedAccessKey, som innehåller nycklar och anslutnings strängar.](./media/service-bus-create-namespace-portal/connection-string.png)
5. Upprepa föregående steg, kopiera och klistra in värdet för **primärnyckeln** till en tillfällig plats för senare användning.

<!--Image references-->

