---
title: Azure snabb start – skapa en Event Hub med hjälp av Azure Portal
description: I den här snabb starten får du lära dig hur du skapar en Azure Event Hub med hjälp av Azure Portal.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 6bd9d247db6cd5a956ff4bce9b70f1f8a5a7499a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742049"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Snabbstart: Skapa en händelsehubb med hjälp av Azure Portal
Azure Event Hubs är en stor data strömnings plattform och händelse inmatnings tjänst som kan ta emot och bearbeta miljon tals händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri som producerats av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadapter. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här snabbstarten skapar du en händelsehubb med hjälp av [Azure-portalen](https://portal.azure.com).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du följande:

- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk samling Azure-resurser. Alla resurser distribueras och hanteras i en resursgrupp. Så här skapar du en resursgrupp:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **resurs grupper** i det vänstra navigerings fältet. Välj **Lägg till**.

   ![Resursgrupper – Lägg till, knapp](./media/event-hubs-quickstart-portal/resource-groups1.png)

1. För **Prenumeration** väljer du namnet på den Azure-prenumeration som du vill skapa resursgruppen i.
1. Ange ett unikt **namn på resursgruppen**. Systemet kontrollerar direkt om namnet är tillgängligt i den valda Azure-prenumerationen.
1. Välj en **region** för resursgruppen.
1. Välj **Granska + skapa**.

   ![Resursgrupp – skapa](./media/event-hubs-quickstart-portal/resource-groups2.png)
1. Välj **Granska** i fönstret **Granska + Skapa**. 

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Ett Event Hubs-namnområde innehåller en unik omfångs behållare där du skapar en eller flera händelse nav. Gör följande om du vill skapa ett namnområde i din resursgrupp med Portal:

1. I Azure-portalen väljer du **Skapa en resurs** högst upp till vänster på skärmen.
1. Välj **Alla tjänster** på den vänstra menyn och välj **stjärnan (`*`)** bredvid **Event Hubs** i kategorin **Analytics**. Bekräfta att **Event Hubs** läggs till i **FAVORITER** på den vänstra navigeringsmenyn. 
    
   ![Söka efter Event Hubs](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
1. Välj **Event Hubs** under **FAVORITER** på den vänstra navigeringsmenyn och välj **Lägg till** i verktygsfältet.

   ![Knappen Lägg till](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
1. Gör följande på sidan **Skapa namnområde**:  
   1. Välj den **prenumeration** som du vill skapa namnområdet i.  
   1. Välj den **resurs grupp** som du skapade i föregående steg.   
   1. Ange ett **namn** för namn området. Systemet kontrollerar omedelbart om namnet är tillgängligt.  
   1. Välj en **plats** för namnområdet.
   1. Välj **pris nivå** (Basic eller standard). Mer information om skillnaderna mellan nivåerna Basic och standard finns i [Event Hubs priser](https://azure.microsoft.com/pricing/details/event-hubs/), [skillnader mellan nivåer](event-hubs-faq.md#what-is-the-difference-between-event-hubs-basic-and-standard-tiers)och [kvoter och begränsningar](event-hubs-quotas.md). 
   1. Lämna inställningarna för **data flödes enheter** som de är. Genomflödesenheter är färdiga kapacitetsenheter. Läs mer om data flödes enheter i [Event Hubs skalbarhet](event-hubs-scalability.md#throughput-units).  
   1. Välj **Granska + skapa** längst ned på sidan.
      
      ![Skapa en namnrymd för en händelsehubb](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   1. På sidan **Granska + skapa** granskar du inställningarna och väljer **skapa**. Vänta tills distributionen har slutförts. 
      
      ![Granska + skapa sida](./media/event-hubs-quickstart-portal/review-create.png)
      
   1. På sidan **distribution** väljer du **gå till resurs** för att navigera till sidan för ditt namn område. 
      
      ![Distributionen är klar – gå till resurs](./media/event-hubs-quickstart-portal/deployment-complete.png)  
   1. Bekräfta att du ser sidan **Event Hubs namn område** som liknar följande exempel:   
      
      ![Startsida för namnområdet](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

      > [!NOTE]
      > Azure Event Hubs ger dig en Kafka-slutpunkt. Den här slut punkten gör det möjligt för Event Hubs namn området att förstå [Apache Kafka](https://kafka.apache.org/intro) meddelande protokoll och API: er. Med den här funktionen kan du kommunicera med dina Event Hub på samma sätt som med Kafka-ämnen utan att ändra protokoll klienter eller köra egna kluster. Event Hubs stöder [Apache Kafka version 1,0](https://kafka.apache.org/10/documentation.html) och senare. Mer information finns i [använda Event Hubs från Apache Kafka-program](event-hubs-for-kafka-ecosystem-overview.md).
    
## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Gör följande om du vill skapa en händelsehubb i namnområdet:

1. På sidan Event Hubs-namnområde väljer du **Event Hubs** på den vänstra menyn.
1. Längst upp i fönstret väljer du **+ Event Hub**.
   
    ![Lägg till Event Hub – knapp](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Ange ett namn för händelsehubben och välj sedan **skapa**.
   
    ![Skapa händelsehubb](./media/event-hubs-quickstart-portal/create-event-hub5.png)

    Med inställningen **antal partitioner** kan du parallellisera förbrukningen för många konsumenter. Mer information finns i [partitioner](event-hubs-scalability.md#partitions).

    Inställningen för **kvarhållning av meddelanden** anger hur länge Event Hubs tjänsten behåller data. Mer information finns i [Event kvarhållning](event-hubs-features.md#event-retention).
1. Du kan kontrollera status för skapandet av händelsehubben i aviseringarna. När händelsehubben har skapats visas den i listan över händelse nav.

    ![Händelsehubben har skapats](./media/event-hubs-quickstart-portal/event-hub-created.png)
    
## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en resursgrupp, en Event Hubs-namnrymd och en händelsehubb. Steg-för-steg-instruktioner för att skicka händelser till (eller) ta emot händelser från en händelsehubben finns i följande Självstudier: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Kör](event-hubs-go-get-started-send.md)
- [C (skickar endast)](event-hubs-c-getstarted-send.md)
- [Apache Storm (tar endast emot)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
