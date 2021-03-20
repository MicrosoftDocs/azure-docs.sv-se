---
title: Använda Azure Service Bus köer med PHP
description: I den här självstudien får du lära dig hur du skapar PHP-program för att skicka meddelanden till och ta emot meddelanden från en Service Bus kö.
services: service-bus-messaging
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: a7e0d1fa321f1b7c1295b5a640fe78b46adf1c72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85341120"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Snabb start: så här använder du Service Bus köer med PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

I den här självstudien får du lära dig hur du skapar PHP-program för att skicka meddelanden till och ta emot meddelanden från en Service Bus kö. 

## <a name="prerequisites"></a>Förutsättningar
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Om du inte har en kö att arbeta med följer du stegen i artikeln [använd Azure Portal för att Service Bus skapa](service-bus-quickstart-portal.md) en kö.
    1. Läs snabb **översikten** över Service Bus **köer**. 
    2. Skapa ett Service Bus- **namnområde**. 
    3. Hämta **anslutnings strängen**. 

        > [!NOTE]
        > Du skapar en **kö** i Service Bus namn området genom att använda php i den här självstudien. 
3. [Azure SDK för PHP](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>Skapa ett PHP-program
Det enda kravet för att skapa ett PHP-program som kommer åt Azure Blob Service är referensen till klasser i [Azure SDK för php](https://github.com/Azure/azure-sdk-for-php) inifrån din kod. Du kan använda alla utvecklingsverktyg för att skapa ditt program eller anteckningar.

> [!NOTE]
> Din PHP-installation måste också ha [openssl-tillägget](https://php.net/openssl) installerat och aktiverat.

I den här guiden kommer du att använda tjänst funktioner som kan anropas från ett PHP-program lokalt eller i kod som körs i en Azure-webbroll, arbets roll eller webbplats.

## <a name="get-the-azure-client-libraries"></a>Hämta Azures klient bibliotek
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet så att det använder Service Bus
Gör så här om du vill använda API: erna för Service Bus queue:

1. Referera till den automatiska inläsnings filen med instruktionen [require_once][require_once] .
2. Referera till alla klasser som du kan använda.

I följande exempel visas hur du tar med den automatiska inläsnings filen och refererar till `ServicesBuilder` klassen.

> [!NOTE]
> Det här exemplet (och andra exempel i den här artikeln) förutsätter att du har installerat PHP-klientprogrammet för Azure via Composer. Om du har installerat biblioteken manuellt eller som ett päron paket måste du referera till filen **windowsazure. php** autoloader.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

I exemplen nedan `require_once` visas instruktionen alltid, men endast de klasser som krävs för att exemplet ska kunna köras refereras till.

## <a name="set-up-a-service-bus-connection"></a>Konfigurera en Service Bus anslutning
Om du vill instansiera en Service Bus-klient måste du först ha en giltig anslutnings sträng i det här formatet:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Där `Endpoint` är vanligt vis formatet `[yourNamespace].servicebus.windows.net` .

Du måste använda-klassen för att skapa en Azure-tjänst-klient `ServicesBuilder` . Du kan:

* Skicka anslutnings strängen direkt till den.
* Använd **CloudConfigurationManager (CCM)** om du vill kontrol lera flera externa källor för anslutnings strängen:
  * Som standard levereras den med stöd för en extern källa – miljövariabler
  * Du kan lägga till nya källor genom att utöka `ConnectionStringSource` klassen

I exemplen som beskrivs här anges anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Skapa en kö
Du kan utföra hanterings åtgärder för Service Bus köer via- `ServiceBusRestProxy` klassen. Ett `ServiceBusRestProxy` objekt konstrueras via `ServicesBuilder::createServiceBusService` fabriks metoden med en lämplig anslutnings sträng som kapslar in token-behörigheterna för att hantera det.

I följande exempel visas hur du instansierar ett `ServiceBusRestProxy` och anropar `ServiceBusRestProxy->createQueue` för att skapa en kö med namnet `myqueue` i ett `MySBNamespace` namn område för tjänsten:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Du kan använda `listQueues` metoden på `ServiceBusRestProxy` objekt för att kontrol lera om det redan finns en kö med ett angivet namn i ett namn område.
> 
> 

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Om du vill skicka ett meddelande till en Service Bus kö anropar programmet `ServiceBusRestProxy->sendQueueMessage` metoden. Följande kod visar hur du skickar ett meddelande till kön som `myqueue` tidigare skapats i `MySBNamespace` namn området för tjänsten.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Meddelanden som skickas till (och tas emot från) Service Bus köer är instanser av klassen [BrokeredMessage][BrokeredMessage] . [BrokeredMessage][BrokeredMessage] -objekt har en uppsättning standard metoder och egenskaper som används för att lagra anpassade programspecifika egenskaper, och en brödtext med godtyckliga program data.

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den övre gränsen för kös Tor lek är 5 GB.

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö

Det bästa sättet att ta emot meddelanden från en kö är att använda en `ServiceBusRestProxy->receiveQueueMessage` metod. Meddelanden kan tas emot i två olika lägen: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) och [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** är standard.

När du använder [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) -läge är ta emot en enda bild. det vill säga när Service Bus får en läsbegäran för ett meddelande i en kö, markerar det meddelandet som förbrukat och returnerar det till programmet. Läget [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus har markerat meddelandet som förbrukat, och när programmet startas om och börjar förbruka meddelanden igen, kommer det att ha missat meddelandet som förbrukades innan kraschen.

I standard läget för [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) blir det möjligt att ta emot ett meddelande en åtgärd i två steg, vilket gör det möjligt att stödja program som inte kan tolerera meddelanden som saknas. När Service Bus tar emot en begäran hittar den nästa meddelande som ska förbrukas, låser det för att hindra andra användare från att ta emot det och returnerar det sedan till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det tillförlitligt för framtida bearbetning) slutförs det andra steget i Receive-processen genom att skicka det mottagna meddelandet till `ServiceBusRestProxy->deleteMessage` . När Service Bus ser `deleteMessage` anropet markeras meddelandet som förbrukat och tas bort från kön.

I följande exempel visas hur du tar emot och bearbetar ett meddelande med [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) -läge (standard läget).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden

Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagar program inte kan bearbeta meddelandet av någon anledning, kan det anropa `unlockMessage` metoden i det mottagna meddelandet (i stället för `deleteMessage` metoden). Detta leder till att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användnings program eller ett annat användnings program.

Det finns också en tids gräns som är kopplad till ett meddelande som är låst i kön och om programmet inte kan bearbeta meddelandet innan tids gränsen för låsning går ut (till exempel om programmet kraschar), kommer Service Bus att låsa upp meddelandet automatiskt och göra det tillgängligt för att tas emot igen.

I händelse av att programmet kraschar när meddelandet har bearbetats men innan `deleteMessage` begäran utfärdas, kommer meddelandet att skickas vidare till programmet när det startas om. Detta kallas ofta *minst en gång* bearbetning. det vill säga att varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte kan tolerera dubbel bearbetning rekommenderas att du lägger till ytterligare logik till program för att hantera duplicerad meddelande leverans. Detta uppnås ofta med hjälp `getMessageId` av meddelandets metod, som är konstant över leverans försök.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Service Bus köer, se [köer, ämnen och prenumerationer][Queues, topics, and subscriptions] för mer information.

Mer information finns även i [php Developer Center](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


