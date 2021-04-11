---
title: 'Snabb start: Skapa Azure Storage köer i portalen'
description: Använd Azure Portal för att skapa en kö. Använd sedan Azure Portal för att lägga till ett meddelande, Visa meddelande egenskaperna och ta bort kön för meddelandet.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 08/13/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 146a6809baebde406fb94f0d36973f95c971170b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275829"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Snabb start: skapa en kö och Lägg till ett meddelande med Azure Portal

I den här snabbstarten får du lära dig hur du använder [Azure-portalen](https://portal.azure.com/) till att skapa en kö i Azure Storage samt lägga till meddelanden i och ta dem ur kön.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Skapa en kö

Skapa en kö i Azure-portalen med hjälp av följande steg:

1. Navigera till ditt nya lagringskonto i Azure Portal.
2. I den vänstra menyn för lagrings kontot, bläddrar du till avsnittet **Queue Storage** och väljer sedan **köer**.
3. Välj knappen **+ Kö**.
4. Ange ett namn för den nya kön. Könamnet får bara innehålla gemener, måste börja med en bokstav eller siffra och får bara innehålla bokstäver, siffror och bindestreck (-).
6. Välj **OK** för att skapa kön.

    ![Skärmbild som visar hur du skapar en kön i Azure-portalen](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Lägg till ett meddelande

Lägg sedan till ett meddelande i den nya kön. Ett meddelande kan vara upp till 64 KB stort.

1. Välj den nya kön i listan över köer i lagringskontot.
1. Välj knappen **+ Lägg till meddelande** för att lägga till ett meddelande i kön. Ange ett meddelande i fältet **Meddelandetext**.
1. Ange när meddelandet ska upphöra. Giltiga värden som kan anges i fältet **upphör att gälla** är mellan 1 sekund och 7 dagar. Välj **meddelande upphör aldrig att gälla** för att ange ett meddelande som ska finnas kvar i kön tills det tas bort explicit.
1. Ange huruvida meddelandet ska kodas som Base64. Det rekommenderas att binära data kodas.
1. Välj knappen **OK** för att lägga till meddelandet.

    ![Skärmbild som visar hur du lägger till ett meddelande i en kö](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Visa meddelandeegenskaper

När du har lagt till ett meddelande visar Azure-portalen en lista över alla meddelanden i kön. Du kan visa meddelande-ID, innehållet i meddelandet, infogningstiden för meddelandet samt meddelandets upphörandetid. Du kan även se hur många gånger meddelandet har tagits ur kön.

![Skärmbild som visar meddelandeegenskaper](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Ta bort ett meddelande från kön

Du kan ett meddelande ur första plats i kön från Azure-portalen. När du tar ett meddelande ur kön tas det bort.

Borttagning från kö tar alltid bort det äldsta meddelandet i kön.

![Skärmbild som visar hur du tar ett meddelande ur kön från portalen](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig skapa en kö, lägga till ett meddelande, visa meddelandeegenskaper och ta ett meddelande ur kön i Azure-portalen.

> [!div class="nextstepaction"]
> [Vad är Azure Queue Storage?](storage-queues-introduction.md)
