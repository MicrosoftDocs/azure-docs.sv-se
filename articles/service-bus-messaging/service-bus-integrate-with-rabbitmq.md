---
title: Så här integrerar du RabbitMQ med Azure Service Bus
description: Steg-för-steg-guide om hur du integrerar RabbitMQ med Azure Service Bus
services: Azure Service Bus
author: videlalvaro
ms.topic: how-to
ms.service: service-bus
ms.date: 07/02/2020
ms.author: alvidela
ms.openlocfilehash: 6366824b8dc7f63f99ebda2a542d95d3eb1c6146
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91301209"
---
# <a name="how-to-integrate-rabbitmq-with-azure-service-bus"></a>Så här integrerar du RabbitMQ med Azure Service Bus

I den här guiden ska vi lära dig hur du skickar meddelanden från RabbitMQ till Azure Service Bus.

Här är några scenarier där vi kan använda dessa funktioner:

- **Edge-inställningar**: vi har en Edge-inställning där vi skickar meddelanden till rabbitmq, men vi vill vidarebefordra dessa meddelanden till [Azure Service Bus](./service-bus-messaging-overview.md) för ytterligare bearbetning, så vi kan använda många av funktionerna i [Azure Big data](/azure/architecture/guide/architecture-styles/big-data).
- **Hybrid moln**: företaget förvärvade bara en tredje part som använder rabbitmq för sina meddelande behov. De finns i ett annat moln. Även om de övergår till Azure kan du redan börja dela data genom att överbrygga RabbitMQ med Azure Service Bus.
- **Integration från tredje part**: en tredje part använder rabbitmq som en Broker och vill skicka data till oss, men de är utanför organisationen. Vi kan ge dem SAS-nyckeln som ger dem till gång till en begränsad uppsättning Azure Service Bus köer där de kan vidarebefordra sina meddelanden till.

Listan går vidare, men vi kan lösa de flesta av dessa användnings fall genom att överbrygga RabbitMQ till Azure.

Först måste du skapa ett kostnads fritt Azure-konto genom att registrera dig [här](https://azure.microsoft.com/free/)

När du har loggat in på ditt konto går du till [Azure Portal](https://portal.azure.com/) och skapar ett nytt Azure Service Bus- [namnområde](./service-bus-create-namespace-portal.md). Namn områden är de omfångs behållare där våra meddelande komponenter är aktiva, t. ex. köer och ämnen.

## <a name="adding-a-new-azure-service-bus-namespace"></a>Lägga till ett nytt Azure Service Bus-namnområde

I Azure Portal klickar du på knappen stora plus för att lägga till en ny resurs

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-resource.png" alt-text="Skapa resurs":::

Välj sedan integrering och klicka på Azure Service Bus för att skapa ett meddelande namn område:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/integration.png" alt-text="Välj Azure Service Bus":::

Du uppmanas att ange namn områdets information. Ange den prenumeration som du vill använda. Om du inte har en [resurs grupp](../azure-resource-manager/management/manage-resource-groups-portal.md)kan du skapa en ny.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace.png" alt-text="Skapa namnområde":::

Används `rabbitmq` för `Namespace name` , men det kan vara vad du vill. Ange sedan `East US` för platsen. Välj `Basic` som pris nivå.

Om alla har gått bra bör du se följande bekräftelse skärm:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-namespace-confirm.png" alt-text="Bekräfta skapande av namnrymd":::

Sedan går du tillbaka till Azure Portal du ser det nya `rabbitmq` namn området som visas där. Klicka på den för att få åtkomst till resursen så att du kan lägga till en kö i den.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/resource-view-with-namespace.png" alt-text="Resurs lista med nytt namn område":::

## <a name="creating-our-azure-service-bus-queue"></a>Skapa vår Azure Service Bus-kö

Nu när du har Azure Service Bus namn området klickar du på `Queues` knappen till vänster under `Entities` , så att du kan lägga till en ny kö:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-queue.png" alt-text="Skapa kö":::

Namnet på kön är `from-rabbitmq` bara en påminnelse till var meddelanden kommer från. Du kan lämna alla andra alternativ som standard, men du kan ändra dem så att de passar appens behov.

## <a name="enabling-the-rabbitmq-shovel-plugin"></a>Aktivera plugin-programmet RabbitMQ Shovel

För att skicka meddelanden från RabbitMQ till Azure Service Bus ska vi använda [Shovel-plugin-programmet](https://www.rabbitmq.com/shovel.html) som levereras med rabbitmq. Du kan aktivera plugin-programmet och dess visuella gränssnitt med det här kommandot:

```bash
rabbitmq-plugins enable rabbitmq_shovel_management
```

>Du kan behöva köra det kommandot som rot.

Nu är det dags att hämta de autentiseringsuppgifter som krävs för att ansluta RabbitMQ till Azure.

## <a name="connecting-rabbitmq-to-azure-service-bus"></a>Ansluter RabbitMQ till Azure Service Bus

Du måste skapa en princip för [delad åtkomst](../storage/common/storage-sas-overview.md) (SAS) för din kö så att rabbitmq kan publicera meddelanden till den. Med en SAS-princip kan du ange vilken extern part som får göra med din resurs. Idén är att RabbitMQ kan skicka meddelanden, men inte lyssna eller hantera kön.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/create-sas-policy.png" alt-text="Lägg till SAS-princip":::

Kryssa i `Send` rutan och klicka sedan `Create` för att ha vår SAS-princip på plats.

När principen har skapats klickar du på den för att se den **primära anslutnings strängen**. Vi ska använda det för att RabbitMQ prata med Azure Service Bus:

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/sas-policy-key.png" alt-text="Hämta SAS-princip":::

Innan du kan använda den anslutnings strängen måste du konvertera den till RabbitMQ AMQP-anslutningsfel. Gå till verktyget för [konvertering av anslutnings sträng](https://red-mushroom-0f7446a0f.azurestaticapps.net/) och klistra in anslutnings strängen i formuläret genom att klicka på konvertera. Du får en anslutnings sträng som är RabbitMQ klar. (Den webbplatsen kör allting lokalt i webbläsaren så att dina data inte skickas via kabeln). Du kan komma åt käll koden på [GitHub](https://github.com/videlalvaro/connstring_to_amqp).

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/converter.png" alt-text="Konvertera anslutnings sträng":::

Öppna RabbitMQ Management-plugin-programmet i webbläsaren `http://localhost:15672/#/dynamic-shovels` och gå till `Admin -> Shovel Management` , där du kan lägga till nya Shovel som tar hand om att skicka meddelanden från en rabbitmq-kö till din Azure Service Bus-kö.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/add-shovel.png" alt-text="Lägg till RabbitMQ Shovel":::

Här anropar du Shovel `azure` och väljer `AMQP 0.9.1` som käll protokoll. I skärm bilden har vi `amqp://` , vilket är standard-URI: n som ansluter oss till en lokal rabbitmq-server. Se till att anpassa till den aktuella distributionen.

På kö-sidan av saker kan du använda `azure` som namn på din kö. Om kön inte finns skapar RabbitMQ den åt dig. Du kan också välja namnet på en kö som redan finns. Du kan lämna de andra alternativen som standard.

`destination`Välj sedan som protokoll på sidan av saker `AMQP 1.0` . I `URI` fältet anger du den anslutnings sträng som du fick från föregående steg, konverterade du Azure-anslutningssträngen till rabbitmq-formatet. Den bör se ut så här:

```
amqps://rabbitmq-shovel:StringOfRandomChars@rabbitmq.servicebus.windows.net:5671/?sasl=plain
```

I `Address` fältet anger du namnet på din **Azure Service Bus-kö**, i det här fallet anropades den `from-rabbitmq` . Klicka på så är `Add Shovel` installationen redo att börja ta emot meddelanden.

## <a name="publishing-messages-from-rabbitmq-to-azure-service-bus"></a>Publicera meddelanden från RabbitMQ till Azure Service Bus

I hanterings gränssnittet för RabbitMQ kan vi gå till `Queues` , välja `azure` kön och söka efter `Publish message` panelen. Ett formulär visas där du kan publicera meddelanden direkt i kön. I vårt exempel ska vi bara lägga till `fist message` som- `Payload` och-träff `Publish Message` :

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/first-message.png" alt-text="Publicera första meddelandet":::

Gå tillbaka till Azure och kontrol lera din kö. Klicka på `Service Bus Explorer` i den vänstra panelen och klicka sedan på knappen _Granska_ . Om alla går bra kan du se din kö nu har ett meddelande. Yay, bevarar!

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/service-bus-queue.png" alt-text="Azure Service Bus kö":::

Men vi ser till att meddelandet är det som du skickade från RabbitMQ. Välj `Peek` fliken och klicka på `Peek` knappen för att hämta de sista meddelandena i kön. Klicka på meddelandet för att kontrol lera innehållet. Du bör se något som liknar bilden nedan där `first message` visas.

:::image type="content" source="./media/service-bus-integrate-with-rabbitmq/peek.png" alt-text="Queue Peek":::

## <a name="lets-recap"></a>Låt oss Sammanfattning

Grattis! Du har uppnått ett parti! Du har hanterat för att hämta dina meddelanden från RabbitMQ till Azure Service Bus, så vi Sammanfattning stegen:

1. Skapa ett Azure Service Bus-namnområde
2. Lägg till en kö i namn området
3. Lägg till en SAS-princip i kön
4. Hämta köns anslutnings sträng
5. Aktivera RabbitMQ Shovel-plugin-programmet & hanterings gränssnittet
6. Konvertera Azure Service Bus-anslutningssträngen till RabbitMQ AMQP-format
7. Lägg till en ny Shovel i RabbitMQ & Anslut den till Azure Service Bus
8. Publicera meddelanden

Genom att följa föregående steg, integrerade delar av organisationen som var utanför Azure. Shovel-plugin-programmet har gett dig möjlighet att leverera meddelanden från RabbitMQ till Azure Service Bus. Detta har enorma fördelar eftersom du nu kan låta betrodda tredje parter ansluta sina appar till Azure-distributionen.

I slutet är meddelande tjänsten om aktivering av anslutningar och med den här tekniken öppnade vi bara en ny.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Service Bus](./service-bus-messaging-overview.md)
- Läs mer om [stöd för AMQP 1,0 i Service Bus](./service-bus-amqp-overview.md)
