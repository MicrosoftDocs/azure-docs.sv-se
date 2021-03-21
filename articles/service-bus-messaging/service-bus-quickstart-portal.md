---
title: Använd Azure Portal för att skapa en Service Bus kö
description: I den här snabb starten får du lära dig hur du skapar ett Service Bus-namnområde och en kö i namn området med hjälp av Azure Portal.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 79dd751c43443790aafc494d89ad45e3b6705a64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95799215"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Använd Azure Portal för att skapa ett Service Bus-namnområde och en kö
Den här snabb starten visar hur du skapar ett Service Bus-namnområde och en kö med hjälp av [Azure Portal][Azure portal]. Det visar också hur du får autentiseringsuppgifter för auktorisering som ett klient program kan använda för att skicka/ta emot meddelanden till/från kön. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du har en Azure-prenumeration för att slutföra den här snabb starten. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto][] innan du börjar.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en Service Bus namnrymd och en kö i namn området. Information om hur du skickar och tar emot meddelanden till/från kön finns i följande snabb starter i avsnittet **skicka och ta emot meddelanden** . 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[kostnads fritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
