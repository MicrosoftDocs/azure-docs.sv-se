---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d12df7197945a514ed8d3d0dca77271fb4bd0903
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96509250"
---
## <a name="prerequisites"></a>Förutsättningar
Om du inte har en [Azure-prenumeration](../articles/guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för Service Bus
Följ anvisningarna i den här självstudien: [snabb start: använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer på avsnittet](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) för att utföra följande uppgifter:

- Skapa ett **premium** Service Bus-namnområde. 
- Hämta anslutnings strängen. 
- Skapa ett Service Bus ämne.
- Skapa en prenumeration på ämnet. Du behöver bara en prenumeration i den här själv studie kursen, så du behöver inte skapa några prenumerationer S2 och S3. 

## <a name="send-messages-to-the-service-bus-topic"></a>Skicka meddelanden till Service Bus ämnet
I det här steget ska du använda ett exempel program för att skicka meddelanden till Service Bus avsnittet som du skapade i föregående steg. 

1. Klona [GitHub Azure-Service-Bus-lagringsplatsen](https://github.com/Azure/azure-service-bus/).
2. Gå till mappen *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration* i Visual Studio och öppna filen *SBEventGridIntegration. SLN* .
3. I fönstret Solution Explorer expanderar du projektet **MessageSender** och väljer **program. cs**.
4. Ersätt `<SERVICE BUS NAMESPACE - CONNECTION STRING>` med anslutnings strängen till Service Bus namn området och `<TOPIC NAME>` namnet på ämnet. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Skapa och kör programmet för att skicka fem test meddelanden ( `const int numberOfMessages = 5;` ) till Service Bus avsnittet. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Utdata för konsol program":::
