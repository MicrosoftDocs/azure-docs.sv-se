---
title: Använda Queue Storage från PHP-Azure Storage
description: Lär dig hur du använder Azure Queue Storage-tjänsten för att skapa och ta bort köer och infoga, hämta och ta bort meddelanden. Exempel skrivs i PHP.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 01/11/2018
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 69369d81892a10c390aa31a2c46f79fdfa41206d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592032"
---
# <a name="how-to-use-queue-storage-from-php"></a>Använda Queue Storage från PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Den här guiden visar hur du utför vanliga scenarier med hjälp av Azure Queue Storage-tjänsten. Exemplen skrivs via klasser från [Azure Storage klient bibliotek för php](https://github.com/Azure/azure-storage-php). De scenarier som beskrivs är att infoga, granska, hämta och ta bort Kömeddelanden, samt skapa och ta bort köer.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Skapa ett PHP-program

Det enda kravet för att skapa ett PHP-program som använder Azure Queue Storage är referensen till klasser i [Azure Storage klient bibliotek för php](https://github.com/Azure/azure-storage-php) inifrån din kod. Du kan använda valfritt utvecklingsverktyg för att skapa programmet, inklusive Anteckningar.

I den här hand boken använder du Queue Storage tjänst funktioner som kan anropas i ett PHP-program lokalt eller i kod som körs i ett webb program i Azure.

## <a name="get-the-azure-client-libraries"></a>Hämta Azures klient bibliotek

### <a name="install-via-composer"></a>Installera via Composer

1. Skapa en fil med namnet `composer.json` i roten för projektet och Lägg till följande kod i den:

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. Ladda ned [`composer.phar`](https://getcomposer.org/composer.phar) i projekt roten.

3. Öppna en kommando tolk och kör följande kommando i projekt roten:

    ```console
    php composer.phar install
    ```

Du kan också gå till [Azure Storage php-klientprogrammet](https://github.com/Azure/azure-storage-php) på GitHub för att klona käll koden.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurera ditt program till att få åtkomst till Queue Storage

Om du vill använda API: erna för Azure Queue Storage måste du:

1. Referera till den automatiska inläsnings filen med hjälp av [`require_once`](https://www.php.net/manual/en/function.require-once.php) instruktionen.
2. Referera till alla klasser som du kan använda.

I följande exempel visas hur du tar med den automatiska inläsnings filen och refererar till `QueueRestProxy` klassen.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

I följande exempel `require_once` visas instruktionen alltid, men endast de klasser som krävs för att köra exemplet refereras.

## <a name="set-up-an-azure-storage-connection"></a>Konfigurera en Azure Storage anslutning

Om du vill instansiera en Azure Queue Storage-klient måste du först ha en giltig anslutnings sträng. Formatet för Queue Storage anslutnings strängen är följande.

För åtkomst till en Live-tjänst:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

För åtkomst till emulator-lagring:

```php
UseDevelopmentStorage=true
```

Om du vill skapa en Azure Queue Storage-klient måste du använda- `QueueRestProxy` klassen. Du kan använda någon av följande metoder:

- Skicka anslutnings strängen direkt till den.
- Använd miljövariabler i din webbapp för att lagra anslutnings strängen. Se dokumentet [konfigurations inställningar i Azure Web App](../../app-service/configure-common.md) för att konfigurera anslutnings strängar.

I exemplen som beskrivs här anges anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Skapa en kö

`QueueRestProxy`Med ett objekt kan du skapa en kö med hjälp av- `CreateQueue` metoden. När du skapar en kö kan du ange alternativ för kön, men detta krävs inte. Det här exemplet visar hur du ställer in metadata i en kö.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Du bör inte förlita dig på SKIFT läges känslighet för metadata-nycklar. Alla nycklar läses från tjänsten med gemener.

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande i en kö

Använd om du vill lägga till ett meddelande i en kö `QueueRestProxy->createMessage` . Metoden tar köns namn, meddelande texten och meddelande alternativen (som är valfria).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello, World");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>En titt på nästa meddelande

Du kan titta på ett eller flera meddelanden överst i en kö utan att ta bort dem från kön genom att anropa `QueueRestProxy->peekMessages` . Som standard `peekMessage` returnerar metoden ett enda meddelande, men du kan ändra värdet med hjälp av `PeekMessagesOptions->setNumberOfMessages` metoden.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Ta bort nästa meddelande från kön

Koden tar bort ett meddelande från en kö i två steg. Först anropar du `QueueRestProxy->listMessages` , som gör meddelandet osynligt för all annan kod som läser från kön. Som standard är det här meddelandet osynligt i 30 sekunder. (Om meddelandet inte tas bort under den här tids perioden blir det synligt i kön igen.) Om du vill slutföra borttagningen av meddelandet från kön måste du anropa `QueueRestProxy->deleteMessage` . Den här två stegs processen för att ta bort ett meddelande säkerställer att när din kod inte kan bearbeta ett meddelande på grund av maskin-eller program varu fel, kan en annan instans av koden Hämta samma meddelande och försöka igen. Dina kod anrop `deleteMessage` direkt efter att meddelandet har bearbetats.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Ändra innehållet i ett meddelande i kön

Du kan ändra innehållet i ett meddelande på plats i kön genom att anropa `QueueRestProxy->updateMessage` . Om meddelandet representerar en arbetsuppgift kan du använda den här funktionen för att uppdatera arbetsuppgiftens status. Följande kod uppdaterar meddelandet i kön med nytt innehåll, och anger tids gränsen för synlighet så att den utökar ytterligare 60 sekunder. Detta sparar det arbete som är associerat med meddelandet och ger klienten en ytterligare minut att fortsätta arbeta med meddelandet. Du kan använda den här metoden för att spåra arbets flöden i flera steg för att köa meddelanden, utan att behöva börja om från början om ett bearbetnings steg Miss lyckas på grund av maskin-eller program varu fel. Normalt räknar du även antalet omförsök och tar bort meddelandet om fler än *n* försök misslyckas. Detta skyddar mot meddelanden som utlöser ett programfel varje gång de bearbetas.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Ytterligare alternativ för demsmq-meddelanden

Det finns två sätt som du kan anpassa meddelande hämtningen från en kö. För det första kan du hämta en grupp med meddelanden (upp till 32). För det andra kan du ange en längre eller kortare tids gräns för synlighet, så att koden får mer eller mindre tid att bearbeta varje meddelande fullständigt. I följande kod exempel används `getMessages` metoden för att hämta 16 meddelanden i ett anrop. Sedan bearbetar den varje meddelande med hjälp av en `for` loop. Koden ställer också in tidsgränsen för osynlighet till fem minuter för varje meddelande.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Hämta Kölängd

Du kan hämta en uppskattning av antalet meddelanden i en kö. `QueueRestProxy->getQueueMetadata`Metoden hämtar metadata om kön. Anrop av `getApproximateMessageCount` metoden för det returnerade objektet innehåller ett antal meddelanden i en kö. Antalet är bara ungefärlig eftersom meddelanden kan läggas till eller tas bort när Queue Storage svarar på din begäran.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Ta bort en kö

Om du vill ta bort en kö och alla meddelanden i den anropar du- `QueueRestProxy->deleteQueue` metoden.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i Azure Queue Storage kan du följa dessa länkar för att lära dig mer om komplexa lagrings uppgifter:

- Besök [API-referensen för klient biblioteket för Azure Storage php](https://azure.github.io/azure-storage-php/)
- Se [exempel på avancerad kö](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php).

Mer information finns i [php Developer Center](https://azure.microsoft.com/develop/php/).
