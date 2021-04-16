---
title: Använd Azure Portal för att skapa Service Bus ämnen och prenumerationer
description: 'Snabbstart: I den här snabbstarten lär du dig hur du skapar ett Service Bus ämne och prenumerationer på det ämnet med hjälp av Azure Portal.'
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 25b0579f05c1f7669a8dfc1df02e9a1f575ea066
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537146"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Använda Azure-portalen för att skapa ett Service Bus-ämne och prenumerationer på ämnet
I den här snabbstarten använder du Azure Portal för att skapa ett Service Bus ämne och sedan skapa prenumerationer på det ämnet. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Vad är Service Bus-ämnen och -prenumerationer?
Service Bus-ämnen och -prenumerationer stöder en *publicera/prenumerera*-modell för meddelandekommunikation. När du använder ämnen och prenumerationer så kommunicerar inte komponenterna i ett distribuerat program direkt med varandra. Istället så utbyter de meddelanden via ett ämne, som agerar mellanhand.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Till skillnad från Service Bus köer, där varje meddelande bearbetas av en enskild konsument, tillhandahåller ämnen och prenumerationer en en-till-många-kommunikation med hjälp av ett publicera/prenumerera-mönster. Det är möjligt att registrera flera prenumerationer för ett ämne. När ett meddelande skickas till ett ämne så görs det tillgängligt för varje prenumeration för oberoende hantering/bearbetning. En prenumeration på ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som har skickats till ämnet. Om du vill kan du registrera filterregler för ett ämne per prenumeration, vilket gör att du kan filtrera eller begränsa vilka meddelanden till ett ämne som tas emot av vilka ämnesprenumerationer.

Service Bus ämnen och prenumerationer gör att du kan skala för att bearbeta ett stort antal meddelanden över ett stort antal användare och program.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Du kan hantera Service Bus resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Med Service Bus Explorer kan användarna ansluta till en Service Bus och administrera meddelandeentiteter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjligheten att testa ämnen, köer, prenumerationer, vidarebefordrande tjänster, meddelandehubbbar och händelsehubbbar. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du Service Bus ett namnområde, ett ämne i namnområdet och tre prenumerationer på ämnet. Information om hur du publicerar meddelanden till ämnet och prenumererar på meddelanden från en prenumeration finns i någon av följande snabbstarter i avsnittet Publicera och **prenumerera på** meddelanden. 

- [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- [Java](service-bus-java-how-to-use-topics-subscriptions.md)
- [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
- [Python](service-bus-python-how-to-use-topics-subscriptions.md)
- [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
- [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)
