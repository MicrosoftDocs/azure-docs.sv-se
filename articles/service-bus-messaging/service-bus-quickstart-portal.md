---
title: Använd Azure Portal för att skapa en Service Bus kö
description: I den här snabbstarten får du lära dig hur du skapar Service Bus en namnrymd och en kö i namnområdet med hjälp av Azure Portal.
author: spelluru
ms.author: spelluru
ms.date: 08/12/2020
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: e38d32c93453737060f654add58f09902b05ee45
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537201"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Använd Azure Portal för att skapa Service Bus en namnrymd och en kö
Den här snabbstarten visar hur du skapar ett Service Bus namnområde och en kö med hjälp av [Azure Portal][Azure portal]. Den visar också hur du hämtar autentiseringsuppgifter för auktorisering som ett klientprogram kan använda för att skicka/ta emot meddelanden till/från kön. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har en Azure-prenumeration för att slutföra den här snabbstarten. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du Service Bus en namnrymd och en kö i namnområdet. Information om hur du skickar/tar emot meddelanden till/från kön finns i någon av följande snabbstarter i **avsnittet Skicka och ta emot** meddelanden. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
