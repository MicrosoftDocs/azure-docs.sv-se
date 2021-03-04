---
title: Skapa ett Event Hubs dedikerat kluster med hjälp av Azure Portal
description: I den här snabb starten får du lära dig hur du skapar ett Azure Event Hubs-kluster med Azure Portal.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 6ff4ee1f098407ba8b3cd2727410bdfc842db89a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040098"
---
# <a name="quickstart-create-a-dedicated-event-hubs-cluster-using-azure-portal"></a>Snabb start: skapa ett dedikerat Event Hubs-kluster med Azure Portal 
Event Hubs kluster erbjuder distributioner för enskilda klienter för kunder med de mest krävande strömnings behoven. Det här erbjudandet har ett garanterat 99,99% SLA och är bara tillgängligt på vår dedikerade pris nivå. Ett [Event Hubs kluster](event-hubs-dedicated-overview.md) kan intränga miljon tals händelser per sekund med garanterad kapacitet och under sekund svars tid. Namn områden och händelse nav som skapats i ett kluster inkluderar alla funktioner i standard erbjudandet och mer, men utan några ingångs gränser. Det dedikerade erbjudandet omfattar även den populära [Event Hubs Capture](event-hubs-capture-overview.md) -funktionen utan extra kostnad, så att du automatiskt kan registrera och logga data strömmar till [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) eller [Azure Data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md).

Dedikerade kluster tillhandahålls och faktureras efter **kapacitets enheter (CUS)**, en förallokerad mängd processor-och minnes resurser. Du kan köpa 1, 2, 4, 8, 12, 16 eller 20 CUs för varje kluster. I den här snabb starten kommer vi att hjälpa dig att skapa en 1 CU Event Hubs-kluster via Azure Portal.

> [!NOTE]
> Den här självbetjänings upplevelsen är för närvarande tillgänglig i för hands versionen på [Azure Portal](https://aka.ms/eventhubsclusterquickstart). Om du har frågor om det dedikerade erbjudandet kan du kontakta [Event Hubs-teamet](mailto:askeventhubs@microsoft.com).


## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här snabbstarten behöver du följande:

- Ett Azure-konto. Om du inte har ett konto kan du [köpa ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar. Den här funktionen stöds inte med ett kostnads fritt Azure-konto. 
- [Visual Studio](https://visualstudio.microsoft.com/vs/) 2017 Update 3 (version 15,3, 26730,01) eller senare.
- [SDK för .NET Standard](https://dotnet.microsoft.com/download) version 2.0 eller senare.
- [En resurs grupp har skapats](../event-hubs/event-hubs-create.md#create-a-resource-group).

## <a name="create-an-event-hubs-dedicated-cluster"></a>Skapa ett Event Hubs Dedicated-kluster
Ett Event Hubs kluster tillhandahåller en unik behållare där du kan skapa ett eller flera namn områden. I den här förhands gransknings fasen av portalens självbetjänings upplevelse kan du skapa 1 CU kluster i utvalda regioner. Om du behöver ett kluster som är större än 1 CU kan du skicka en support förfrågan för Azure för att skala upp klustret när det har skapats.

> [!IMPORTANT]
> Du kommer inte att kunna ta bort klustret i minst fyra timmar efter att du har skapat det. Därför kommer du att debiteras för minst 4 timmar med användningen av klustret. Mer information om priser finns i [Event Hubs-prissättning](https://azure.microsoft.com/pricing/details/event-hubs/). 

Utför följande steg för att skapa ett kluster i resurs gruppen med hjälp av Azure Portal:

1. Följ [den här länken](https://aka.ms/eventhubsclusterquickstart) om du vill skapa ett kluster på Azure Portal. Omvänt, Välj **alla tjänster** i det vänstra navigerings fönstret och skriv "Event Hubs kluster" i Sök fältet och välj "Event Hubs kluster" i listan över resultat.
2. Konfigurera följande inställningar på sidan **skapa kluster** :
    1. Ange ett **namn för klustret**. Systemet kontrollerar omedelbart om namnet är tillgängligt.
    2. Välj den **prenumeration** som du vill skapa klustret i.
    3. Välj den **resurs grupp** som du vill skapa klustret i.
    4. Välj en **plats** för klustret. Om din önskade region är nedtonad är det tillfälligt slut på kapacitet och du kan skicka in en [supportbegäran](#submit-a-support-request) till Event Hubss teamet.
    5. Välj knappen **Nästa: Taggar** längst ned på sidan. Du kan behöva vänta några minuter på att systemet blir klar med att etablera resurserna.

        ![Sidan skapa Event Hubs kluster – grunder](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-basics-page.png)
3. Konfigurera följande på sidan **taggar** :
    1. Ange ett **namn** och ett **värde** för taggen som du vill lägga till. Det här steget är **valfritt**.  
    2. Välj knappen **Granska + skapa** .

        ![Skapa Event Hubs-kluster Page-Tags sida](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-tags-page.png)
4. På sidan **Granska + skapa** granskar du informationen och väljer **skapa**. 

    ![Skapa Event Hubs kluster sida – granska + skapa sida](./media/event-hubs-dedicated-cluster-create-portal/create-event-hubs-clusters-review-create-page.png)

## <a name="create-a-namespace-and-event-hub-within-a-cluster"></a>Skapa ett namn område och en händelsehubben i ett kluster

1. Om du vill skapa ett namn område i ett kluster väljer du **+ namn område** på sidan **Event Hubs kluster** för klustret på den översta menyn.

    ![Sidan kluster hantering – knappen Lägg till namn område](./media/event-hubs-dedicated-cluster-create-portal/cluster-management-page-add-namespace-button.png)
2. Utför följande steg på sidan **skapa ett namn område** :
    1. Ange ett **namn för namnrymden**.  Systemet kontrollerar om namnet är tillgängligt.
    2. Namn området ärver följande egenskaper:
        1. Prenumerations-ID:t
        2. Resursgrupp
        3. Location
        4. Klusternamn
    3. Välj **skapa** för att skapa namn området. Nu kan du hantera klustret.  

        ![Skapa namnrymd på kluster Sidan](./media/event-hubs-dedicated-cluster-create-portal/create-namespace-cluster-page.png)
3. När ditt namn område har skapats kan du [skapa en händelsehubben](event-hubs-create.md#create-an-event-hub) som du normalt skapar ett i ett namn område. 


## <a name="submit-a-support-request"></a>Skicka en support förfrågan

Om du vill ändra storleken på klustret efter att du har skapat eller om din önskade region inte är tillgänglig, kan du skicka en supportbegäran genom att följa dessa steg:

1. I [Azure Portal](https://portal.azure.com)väljer du **Hjälp + Support** på den vänstra menyn.
2. Välj **+ ny support förfrågan** på support-menyn.
3. Följ de här stegen på sidan support:
    1. För **typ av problem** väljer du **teknisk** i list rutan.
    2. I fältet **Prenumeration** väljer du din prenumeration.
    3. För **tjänst** väljer du **Mina tjänster** och väljer sedan **Event Hubs**.
    4. För **resurs** väljer du klustret om det redan finns, annars väljer du **allmän fråga/resurs inte tillgänglig**.
    5. Välj **kvot** för **typ av problem**.
    6. Under **typ av problem** väljer du något av följande värden i list rutan:
        1. Välj **begäran om dedikerad SKU** för att begära att funktionen stöds i din region.
        2. Välj begäran om du vill skala **upp eller ned ett dedikerat kluster** om du vill skala upp eller ned ett dedikerat kluster. 
    7. Beskriv problemet för **ämne**.

        ![Sidan Support ärende](./media/event-hubs-dedicated-cluster-create-portal/support-ticket.png)

 ## <a name="delete-a-dedicated-cluster"></a>Ta bort ett dedikerat kluster
 
1. Om du vill ta bort klustret väljer du **ta bort** på den översta menyn. 

    > [!IMPORTANT]
    > Du kommer inte att kunna ta bort klustret i minst fyra timmar efter att du har skapat det. Därför kommer du att debiteras för minst 4 timmar med användningen av klustret. Mer information om priser finns i [Event Hubs-prissättning](https://azure.microsoft.com/pricing/details/event-hubs/).     
1. Ett meddelande visas som bekräftar att du vill ta bort klustret.
1. Skriv **namnet på klustret** och välj **ta bort** för att ta bort klustret.

    ![Ta bort kluster sida](./media/event-hubs-dedicated-cluster-create-portal/delete-cluster-page.png)


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat ett Event Hubs-kluster. Stegvisa instruktioner för att skicka och ta emot händelser från en händelsehubben och att avbilda händelser till en Azure Storage eller Azure Data Lake Store finns i följande själv studie kurser:

- Skicka och ta emot händelser 
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
- [Använd Azure Portal för att aktivera Event Hubs avbildning](event-hubs-capture-enable-through-portal.md)
- [Använd Azure Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
