---
title: Använd Azure Portal för att skapa Service Bus ämnen och prenumerationer
description: 'Snabb start: i den här snabb starten får du lära dig hur du skapar ett Service Bus ämne och prenumerationer på det avsnittet med hjälp av Azure Portal.'
author: spelluru
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 749ba3d2947dfd7defdf2549305f2a61c16ba713
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95803288"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Använda Azure-portalen för att skapa ett Service Bus-ämne och prenumerationer på ämnet
I den här snabb starten använder du Azure Portal för att skapa ett Service Bus ämne och sedan skapa prenumerationer på det avsnittet. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Vad är Service Bus-ämnen och -prenumerationer?
Service Bus-ämnen och -prenumerationer stöder en *publicera/prenumerera*-modell för meddelandekommunikation. När du använder ämnen och prenumerationer så kommunicerar inte komponenterna i ett distribuerat program direkt med varandra. Istället så utbyter de meddelanden via ett ämne, som agerar mellanhand.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

I motsats till Service Bus köer, där varje meddelande bearbetas av en enskild konsument, innehåller ämnen och prenumerationer en en-till-många-form av kommunikation med ett mönster för publicering/prenumeration. Det är möjligt att registrera flera prenumerationer för ett ämne. När ett meddelande skickas till ett ämne så görs det tillgängligt för varje prenumeration för oberoende hantering/bearbetning. En prenumeration på ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som har skickats till ämnet. Du kan välja att registrera filter regler för ett ämne per prenumeration, vilket gör att du kan filtrera eller begränsa vilka meddelanden till ett ämne som tas emot av vilka ämnes prenumerationer.

Med hjälp av Service Bus ämnen och prenumerationer kan du skala för att bearbeta ett stort antal meddelanden över ett stort antal användare och program.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en Service Bus namnrymd, ett ämne i namn området och tre prenumerationer på ämnet. Information om hur du publicerar meddelanden till ämnet och prenumererar på meddelanden från en prenumeration finns i följande snabb starter i avsnittet **publicera och prenumerera på meddelanden** . 

- [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- [Java](service-bus-java-how-to-use-topics-subscriptions.md)
- [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
- [Python](service-bus-python-how-to-use-topics-subscriptions.md)
- [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
- [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)