---
title: Använda Azure Service Bus ämnen med PHP
description: I den här självstudien får du lära dig hur du använder Azure Service Bus ämnen och prenumerationer från ett PHP-program.
ms.devlang: PHP
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 706f523fdfb3c710bb16b048cfc68ce98875adb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88066210"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Snabb start: använda Service Bus ämnen och prenumerationer med PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Den här artikeln visar hur du använder Service Bus ämnen och prenumerationer. Exemplen är skrivna i PHP och använder [Azure SDK för php](https://github.com/Azure/azure-sdk-for-php). Scenarierna som omfattas är:

- Skapa ämnen och prenumerationer 
- Skapa prenumerations filter 
- Skicka meddelanden till ett ämne 
- Ta emot meddelanden från en prenumeration
- Ta bort ämnen och prenumerationer

## <a name="prerequisites"></a>Förutsättningar
1. En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för Visual Studio eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Följ stegen i [snabb starten: använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer på avsnittet](service-bus-quickstart-topics-subscriptions-portal.md) för att skapa ett Service Bus- **namnområde** och hämta **anslutnings strängen**.

    > [!NOTE]
    > Du kommer att skapa ett **ämne** och en **prenumeration** på avsnittet med hjälp av **php** i den här snabb starten. 

## <a name="create-a-php-application"></a>Skapa ett PHP-program
Det enda kravet för att skapa ett PHP-program som har åtkomst till Azure Blob Service är att referera till klasser i [Azure SDK för php](https://github.com/Azure/azure-sdk-for-php) inifrån din kod. Du kan använda alla utvecklingsverktyg för att skapa ditt program eller anteckningar.

> [!NOTE]
> Din PHP-installation måste också ha [openssl-tillägget](https://php.net/openssl) installerat och aktiverat.
> 
> 

Den här artikeln beskriver hur du använder tjänst funktioner som kan anropas i ett PHP-program lokalt eller i kod som körs i en Azure-webbroll, arbets roll eller webbplats.

## <a name="get-the-azure-client-libraries"></a>Hämta Azures klient bibliotek

### <a name="install-via-composer"></a>Installera via Composer
1. Skapa en fil med namnet **composer.js** i roten i projektet och Lägg till följande kod i den:
   
    ```json
    {
      "require": {
        "microsoft/windowsazure": "*"
      }
    }
    ```
2. Hämta **[Composer. Phar] [Composer-Phar]** i projekt roten.
3. Öppna en kommando tolk och kör följande kommando i projekt roten
   
    ```
    php composer.phar install
    ```

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet så att det använder Service Bus
Så här använder du Service Bus API: er:

1. Referera till den automatiska inläsnings filen med instruktionen [require_once][require-once] .
2. Referera till alla klasser som du kan använda.

I följande exempel visas hur du tar med den automatiska inläsnings filen och refererar till klassen **ServiceBusService** .

> [!NOTE]
> Det här exemplet (och andra exempel i den här artikeln) förutsätter att du har installerat PHP-klientprogrammet för Azure via Composer. Om du har installerat biblioteken manuellt eller som ett päron paket måste du referera till filen **windowsazure. php** autoloader.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

I följande exempel `require_once` visas instruktionen alltid, men endast de klasser som krävs för att exemplet ska kunna köras refereras till.

## <a name="set-up-a-service-bus-connection"></a>Konfigurera en Service Bus anslutning
Om du vill instansiera en Service Bus-klient måste du först ha en giltig anslutnings sträng i det här formatet:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Där `Endpoint` är vanligt vis formatet `https://[yourNamespace].servicebus.windows.net` .

Du måste använda-klassen för att skapa en Azure-tjänst-klient `ServicesBuilder` . Du kan:

* Skicka anslutnings strängen direkt till den.
* Använd **CloudConfigurationManager (CCM)** om du vill kontrol lera flera externa källor för anslutnings strängen:
  * Som standard levereras den med stöd för en extern källa – miljövariabler.
  * Du kan lägga till nya källor genom att utöka `ConnectionStringSource`-klassen.

I exemplen som beskrivs här anges anslutningssträngen direkt.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Skapa ett ämne
Du kan utföra hanterings åtgärder för Service Bus ämnen via- `ServiceBusRestProxy` klassen. Ett `ServiceBusRestProxy` objekt konstrueras via `ServicesBuilder::createServiceBusService` fabriks metoden med en lämplig anslutnings sträng som kapslar in token-behörigheterna för att hantera det.

I följande exempel visas hur du instansierar ett `ServiceBusRestProxy` och anropar `ServiceBusRestProxy->createTopic` för att skapa ett ämne som heter `mytopic` i ett `MySBNamespace` namn område:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
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
> Du kan använda `listTopics` -metoden för `ServiceBusRestProxy` objekt för att kontrol lera om det redan finns ett ämne med ett angivet namn i ett namn område för tjänsten.
> 
> 

## <a name="create-a-subscription"></a>Skapa en prenumeration
Ämnes prenumerationer skapas också med- `ServiceBusRestProxy->createSubscription` metoden. Prenumerationer är namngivna och kan ha ett valfritt filter som begränsar den uppsättning av meddelanden som skickas till prenumerationens virtuella kö.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Skapa en prenumeration med standardfiltret (MatchAll)
Om inget filter anges när en ny prenumeration skapas, används **MatchAll** -filtret (standard). När **MatchAll** -filtret används placeras alla meddelanden som publiceras till ämnet i prenumerationens virtuella kö. I följande exempel skapas en prenumeration med namnet `mysubscription` och standard filtret används i **MatchAll** .

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
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

### <a name="create-subscriptions-with-filters"></a>Skapa prenumerationer med filter
Du kan även ställa in filter som gör att du kan ange vilka meddelanden som skickas till ett ämne som ska visas inom en viss ämnesprenumeration. Den mest flexibla typen av filter som stöds av prenumerationer är [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), som implementerar en delmängd av SQL92. SQL-filter tillämpas på egenskaperna i de meddelanden som publiceras till ämnet. Mer information om SqlFilters finns i [SqlFilter. SqlExpression-egenskapen][sqlfilter].

> [!NOTE]
> Varje regel i en prenumeration bearbetar inkommande meddelanden oberoende och lägger till sina resultat meddelanden i prenumerationen. Dessutom har varje ny prenumeration ett standard **regel** objekt med ett filter som lägger till alla meddelanden från ämnet i prenumerationen. Om du bara vill ta emot meddelanden som matchar ditt filter måste du ta bort standard regeln. Du kan ta bort standard regeln med hjälp av- `ServiceBusRestProxy->deleteRule` metoden.
> 
> 

I följande exempel skapas en prenumeration `HighMessages` med namnet med en **SqlFilter** som endast väljer meddelanden som har en anpassad egenskap som är `MessageNumber` större än 3. Information om hur du lägger till anpassade egenskaper i meddelanden finns i [skicka meddelanden till ett ämne](#send-messages-to-a-topic) .

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Den här koden kräver att ytterligare ett namn område används: `WindowsAzure\ServiceBus\Models\SubscriptionInfo` .

På samma sätt skapar följande exempel en prenumeration med namnet `LowMessages` med en `SqlFilter` som endast väljer meddelanden som har en `MessageNumber` egenskap som är mindre än eller lika med 3.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

När ett meddelande skickas till `mytopic` ämnet, levereras det alltid till mottagare som prenumererar på `mysubscription` prenumerationen och är selektivt levererade till mottagare som prenumererar på `HighMessages` `LowMessages` -och-prenumerationerna (beroende på meddelandets innehåll).

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
Om du vill skicka ett meddelande till ett Service Bus ämne anropar programmet `ServiceBusRestProxy->sendTopicMessage` metoden. Följande kod visar hur du skickar ett meddelande till avsnittet som `mytopic` tidigare har skapats i `MySBNamespace` namn området för tjänsten.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
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

Meddelanden som skickas till Service Bus-ämnen är instanser av klassen [BrokeredMessage][BrokeredMessage]. [BrokeredMessage][BrokeredMessage] -objekt har en uppsättning standard egenskaper och metoder, samt egenskaper som kan användas för att lagra anpassade programspecifika egenskaper. I följande exempel visas hur du skickar fem test meddelanden till `mytopic` avsnittet som skapats tidigare. `setProperty`Metoden används för att lägga till en anpassad egenskap ( `MessageNumber` ) i varje meddelande. `MessageNumber`Egenskap svärdet varierar för varje meddelande (du kan använda det här värdet för att avgöra vilka prenumerationer som får det, som du ser i avsnittet [skapa en prenumeration](#create-a-subscription) ):

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Service Bus-ämnena stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i ett ämne men det finns ett tak för den totala storleken för de meddelanden som ligger i ett ämne. Den övre gränsen för ämnes storleken är 5 GB. Mer information om kvoter finns i [Service Bus kvoter][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
Det bästa sättet att ta emot meddelanden från en prenumeration är att använda en `ServiceBusRestProxy->receiveSubscriptionMessage` metod. Meddelanden kan tas emot i två olika lägen: [ *ReceiveAndDelete* och *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** är standard.

När du använder läget [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) är inleveransen en engångsåtgärd, det vill säga, när Service Bus tar emot en läsbegäran för ett meddelande i en prenumeration så markerar den meddelandet som förbrukat och skickar tillbaka det till programmet. [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) *-läge är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera bearbetning av ett meddelande när ett fel uppstår. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus har markerat meddelandet som förbrukat, när programmet startas om och börjar förbruka meddelanden igen, har det fått meddelandet som förbrukades innan kraschen.

I standard läget för [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) blir det möjligt att ta emot ett meddelande en åtgärd i två steg, vilket gör det möjligt att stödja program som inte kan tolerera meddelanden som saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört bearbetningen av meddelandet (eller lagrar det tillförlitligt för framtida bearbetning) slutförs det andra steget i Receive-processen genom att skicka det mottagna meddelandet till `ServiceBusRestProxy->deleteMessage` . När Service Bus ser `deleteMessage` anropet markeras meddelandet som förbrukat och tas bort från kön.

I följande exempel visas hur du tar emot och bearbetar ett meddelande med [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) -läge (standard läget). 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
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

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Så här gör du: hantera program krascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagar program inte kan bearbeta meddelandet av någon anledning, kan det anropa `unlockMessage` metoden i det mottagna meddelandet (i stället för `deleteMessage` metoden). Det gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användnings program eller ett annat användnings program.

Det finns också en tids gräns som är kopplad till ett meddelande som är låst i kön och om programmet inte kan bearbeta meddelandet innan tids gränsen för låsning går ut (till exempel om programmet kraschar) låser Service Bus upp meddelandet automatiskt och gör det tillgängligt för att tas emot igen.

I händelse av att programmet kraschar när meddelandet har bearbetats men innan `deleteMessage` begäran utfärdas omlevereras meddelandet till programmet när det startas om. Den här typen av bearbetning kallas ofta *minst en gång* . det vill säga att varje meddelande bearbetas minst en gång, men i vissa situationer kan samma meddelande levereras igen. Om scenariot inte kan tolerera dubbel bearbetning bör programutvecklare lägga till ytterligare logik till program för att hantera duplicerad meddelande leverans. Den uppnås ofta med hjälp `getMessageId` av meddelandets metod, som är konstant över leverans försök.

## <a name="delete-topics-and-subscriptions"></a>Ta bort ämnen och prenumerationer
Om du vill ta bort ett ämne eller en prenumeration använder du respektive `ServiceBusRestProxy->deleteTopic` `ServiceBusRestProxy->deleteSubscripton` metod. Om du tar bort ett ämne så tar du även bort alla prenumerationer som är registrerade på det ämnet.

I följande exempel visas hur du tar bort ett ämne med namnet `mytopic` och dess registrerade prenumerationer.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
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

Genom att använda `deleteSubscription` -metoden kan du ta bort en prenumeration oberoende av varandra:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer gör det möjligt för användare att ansluta till en Service Bus namnrymd och administrera meddelande enheter på ett enkelt sätt. Verktyget innehåller avancerade funktioner som import/export-funktioner eller möjlighet att testa ämnen, köer, prenumerationer, relä tjänster, Notification Hub och Event Hub. 

## <a name="next-steps"></a>Nästa steg
Mer information finns i [köer, ämnen och prenumerationer][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md