---
title: Skapa och ta bort hanterade privata slut punkter i ett Azure Stream Analytics kluster
description: Lär dig hur du hanterar privata slut punkter i ett Azure Stream Analytics kluster.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 9939130782594c03a497d98ce6cd9b33b28eadec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718411"
---
# <a name="create-and-delete-managed-private-endpoints-in-an-azure-stream-analytics-cluster"></a>Skapa och ta bort hanterade privata slut punkter i ett Azure Stream Analytics kluster

Du kan ansluta Azure Stream Analytics-jobb som körs i ett kluster för att mata in och mata ut resurser som finns bakom en brandvägg eller ett virtuellt Azure-nätverk (VNet). Först skapar du en hanterad privat slut punkt för en resurs, till exempel Azure Event Hub eller Azure SQL Database, i Stream Analytics klustret. Godkänn sedan anslutningen till den privata slutpunkten från dina indata eller utdata.

När du har godkänt anslutningen kan alla jobb som körs i ditt Stream Analytics-kluster komma åt resursen via den privata slut punkten. Den här artikeln beskriver hur du skapar och tar bort privata slutpunkter i ett Stream Analytics-kluster. Du kan skapa privata slut punkter för Azure SQL Database, Azure Storage, Azure Data Lake Storage Gen2, Azure Event Hub och Azure Service Bus. Privata slut punkter för andra tjänster kommer snart att läggas till. 

## <a name="create-managed-private-endpoint-in-stream-analytics-cluster"></a>Skapa hanterad privat slut punkt i Stream Analytics kluster

I det här avsnittet får du lära dig hur du skapar en privat slut punkt i ett Stream Analytics kluster.

1. Leta upp och välj ditt Stream Analytics-kluster i Azure Portal.

1. Under **Inställningar** väljer du **hanterade privata slut punkter**.

1. Välj **ny** och ange följande information för att välja den resurs som du vill komma åt säkert via en privat slut punkt.

   |Inställning|Värde|
   |---|---|
   |Namn|Ange ett namn för din privata slut punkt. Om det här namnet tas skapar du ett unikt namn.|
   |Anslutningsmetod|Välj **Anslut till en Azure-resurs i min katalog**.<br><br>Du kan välja en av dina resurser för att på ett säkert sätt ansluta till med hjälp av den privata slut punkten, eller så kan du ansluta till någon annans resurs genom att använda ett resurs-ID eller alias som de har delat med dig.|
   |Prenumeration|Välj din prenumeration.|
   |Resurstyp|Välj den [resurs typ som mappar till din resurs](../private-link/private-endpoint-overview.md#private-link-resource).|
   |Resurs|Välj den resurs som du vill ansluta till med hjälp av privat slut punkt.|
   |Mål under resurs|Typ av under resurs för den resurs som väljs ovan som din privata slut punkt kommer att ha åtkomst till.|

   ![Skapa upplevelse för privat slut punkt](./media/private-endpoints/create-private-endpoint.png)

1. Godkänn anslutningen från mål resursen. Om du till exempel har skapat en privat slut punkt till en Azure SQL Database instans i föregående steg, bör du gå till den här SQL Database instansen och se en väntande anslutning som ska godkännas. Det kan ta några minuter innan anslutningsbegäran visas.

    ![Godkänn privat slut punkt](./media/private-endpoints/approve-private-endpoint.png)

1. Du kan gå tillbaka till ditt Stream Analytics-kluster för att se tillstånds ändringen från **väntande kund godkännande** till en **väntande DNS-installation** som **slutförs** på några minuter.

## <a name="delete-a-managed-private-endpoint-in-a-stream-analytics-cluster"></a>Ta bort en hanterad privat slut punkt i ett Stream Analytics kluster

1. Leta upp och välj ditt Stream Analytics-kluster i Azure Portal.

1. Under **Inställningar** väljer du **hanterade privata slut punkter**.

1. Välj den privata slut punkt som du vill ta bort och välj **ta bort**.

   ![ta bort privat slut punkt](./media/private-endpoints/delete-private-endpoint.png)

## <a name="next-steps"></a>Nästa steg

Nu har du en översikt över hur du hanterar privata slut punkter i ett Azure Stream Analytics kluster. Sedan kan du lära dig hur du skalar kluster och Kör jobb i klustret:

* [Skala ett Azure Stream Analytics kluster](scale-cluster.md)
* [Hantera Stream Analytics jobb i ett Stream Analytics kluster](manage-jobs-cluster.md)
