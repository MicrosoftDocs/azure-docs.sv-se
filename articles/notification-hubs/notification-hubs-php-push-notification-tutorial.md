---
title: Använda Azure Notification Hubs med PHP
description: Lär dig hur du använder Azure Notification Hubs från en PHP-Server.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: daebf7c6a5fc9056e16b77a40ee9f90db598749f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87076632"
---
# <a name="how-to-use-notification-hubs-from-php"></a>Använda Notification Hubs från PHP

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Du kan komma åt alla Notification Hubs-funktioner från en Java/PHP/Ruby-server med hjälp av REST-gränssnittet Notification Hub, som beskrivs i MSDN-avsnittet [Notification HUBS REST-API: er](/previous-versions/azure/reference/dn223264(v=azure.100)).

I det här avsnittet visar vi hur du:

* Bygg en REST-klient för Notification Hubs funktioner i PHP.
* Följ avsnittet [skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs](ios-sdk-get-started.md) för din mobila plattform och implementera Server delen i php.

## <a name="client-interface"></a>Klient gränssnitt

Huvud klient gränssnittet kan ge samma metoder som är tillgängliga i [.net Notification HUBS SDK](https://msdn.microsoft.com/library/jj933431.aspx), vilket gör att du direkt kan översätta alla självstudier och exempel som är tillgängliga på den här webbplatsen och som tillhandahålls av communityn på Internet.

Du hittar all kod som är tillgänglig i [exemplet på php-rest].

Till exempel för att skapa en-klient:

```php
$hub = new NotificationHub("connection string", "hubname");
```

Skicka ett internt iOS-meddelande:

```php
$notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
$hub->sendNotification($notification, null);
```

## <a name="implementation"></a>Implementering

Om du inte redan har gjort det följer du kursen [kom igång] fram till det sista avsnittet där du måste implementera Server delen.
Om du vill kan du även använda koden från [exemplet på php-rest] och gå direkt till avsnittet [självstudier](#complete-tutorial) .

All information om hur du implementerar ett komplett REST-omslag finns på [MSDN](/previous-versions/azure/reference/dn530746(v=azure.100)). I det här avsnittet beskriver vi PHP-implementeringen av de viktigaste stegen som krävs för att få åtkomst till Notification Hubs REST-slutpunkter:

1. Parsa anslutningssträngen
2. Generera autentiseringstoken
3. Utför HTTP-anropet

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen

Här är huvud klassen som implementerar klienten, vars konstruktor tolkar anslutnings strängen:

```php
class NotificationHub {
    const API_VERSION = "?api-version=2013-10";

    private $endpoint;
    private $hubPath;
    private $sasKeyName;
    private $sasKeyValue;

    function __construct($connectionString, $hubPath) {
        $this->hubPath = $hubPath;

        $this->parseConnectionString($connectionString);
    }

    private function parseConnectionString($connectionString) {
        $parts = explode(";", $connectionString);
        if (sizeof($parts) != 3) {
            throw new Exception("Error parsing connection string: " . $connectionString);
        }

        foreach ($parts as $part) {
            if (strpos($part, "Endpoint") === 0) {
                $this->endpoint = "https" . substr($part, 11);
            } else if (strpos($part, "SharedAccessKeyName") === 0) {
                $this->sasKeyName = substr($part, 20);
            } else if (strpos($part, "SharedAccessKey") === 0) {
                $this->sasKeyValue = substr($part, 16);
            }
        }
    }
}
```

### <a name="create-a-security-token"></a>Skapa en säkerhetstoken

I Azure-dokumentationen hittar du information om hur du [skapar en SAS](/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token)-säkerhetstoken.

Lägg till- `generateSasToken` metoden i `NotificationHub` klassen för att skapa token baserat på URI: n för den aktuella begäran och de autentiseringsuppgifter som extraheras från anslutnings strängen.

```php
private function generateSasToken($uri) {
    $targetUri = strtolower(rawurlencode(strtolower($uri)));

    $expires = time();
    $expiresInMins = 60;
    $expires = $expires + $expiresInMins * 60;
    $toSign = $targetUri . "\n" . $expires;

    $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

    $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

    return $token;
}
```

### <a name="send-a-notification"></a>Skicka ett meddelande

Först ska vi definiera en klass som representerar ett meddelande.

```php
class Notification {
    public $format;
    public $payload;

    # array with keynames for headers
    # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
    # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
    public $headers;

    function __construct($format, $payload) {
        if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
            throw new Exception('Invalid format: ' . $format);
        }

        $this->format = $format;
        $this->payload = $payload;
    }
}
```

Den här klassen är en behållare för en intern meddelande text, eller en uppsättning egenskaper om det rör sig om en mall och en uppsättning huvuden, som innehåller format (ursprunglig plattform eller mall) och plattformsspecifika egenskaper (t. ex. Apples förfallo egenskap och WNS-rubriker).

Läs [Notification HUBS REST API-dokumentationen](/previous-versions/azure/reference/dn495827(v=azure.100)) och de speciella meddelande plattforms formaten för alla alternativ som är tillgängliga.

Med den här klassen kan vi nu skriva sändnings meddelande metoderna i `NotificationHub` klassen:

```php
public function sendNotification($notification, $tagsOrTagExpression="") {
    if (is_array($tagsOrTagExpression)) {
        $tagExpression = implode(" || ", $tagsOrTagExpression);
    } else {
        $tagExpression = $tagsOrTagExpression;
    }

    # build uri
    $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
    $ch = curl_init($uri);

    if (in_array($notification->format, ["template", "apple", "fcm"])) {
        $contentType = "application/json";
    } else {
        $contentType = "application/xml";
    }

    $token = $this->generateSasToken($uri);

    $headers = [
        'Authorization: '.$token,
        'Content-Type: '.$contentType,
        'ServiceBusNotification-Format: '.$notification->format
    ];

    if ("" !== $tagExpression) {
        $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
    }

    # add headers for other platforms
    if (is_array($notification->headers)) {
        $headers = array_merge($headers, $notification->headers);
    }

    curl_setopt_array($ch, array(
        CURLOPT_POST => TRUE,
        CURLOPT_RETURNTRANSFER => TRUE,
        CURLOPT_SSL_VERIFYPEER => FALSE,
        CURLOPT_HTTPHEADER => $headers,
        CURLOPT_POSTFIELDS => $notification->payload
    ));

    // Send the request
    $response = curl_exec($ch);

    // Check for errors
    if($response === FALSE){
        throw new Exception(curl_error($ch));
    }

    $info = curl_getinfo($ch);

    if ($info['http_code'] <> 201) {
        throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
    }
} 
```

Metoderna ovan skickar en HTTP POST-begäran till `/messages` slut punkten för Notification Hub, med rätt brödtext och rubriker för att skicka meddelandet.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Slutför självstudien

Nu kan du slutföra självstudien kom igång genom att skicka meddelandet från en PHP-Server.

Initiera din Notification Hubs-klient (Ersätt anslutnings strängen och hubbens namn enligt anvisningarna i guiden för att komma igång med [kom igång]):

```php
$hub = new NotificationHub("connection string", "hubname");
```

Lägg sedan till sändnings koden beroende på din mål-mobila plattform.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store och Windows Phone 8,1 (ej Silverlight)

```php
$toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
$notification = new Notification("windows", $toast);
$notification->headers[] = 'X-WNS-Type: wns/toast';
$hub->sendNotification($notification, null);
```

### <a name="ios"></a>iOS

```php
$alert = '{"aps":{"alert":"Hello from PHP!"}}';
$notification = new Notification("apple", $alert);
$hub->sendNotification($notification, null);
```

### <a name="android"></a>Android

```php
$message = '{"data":{"msg":"Hello from PHP!"}}';
$notification = new Notification("fcm", $message);
$hub->sendNotification($notification, null);
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8,0 och 8,1 Silverlight

```php
$toast = '<?xml version="1.0" encoding="utf-8"?>' .
            '<wp:Notification xmlns:wp="WPNotification">' .
               '<wp:Toast>' .
                    '<wp:Text1>Hello from PHP!</wp:Text1>' .
               '</wp:Toast> ' .
            '</wp:Notification>';
$notification = new Notification("windowsphone", $toast);
$notification->headers[] = 'X-WindowsPhone-Target : toast';
$notification->headers[] = 'X-NotificationClass : 2';
$hub->sendNotification($notification, null);
```

### <a name="kindle-fire"></a>Kindle-brand

```php
$message = '{"data":{"msg":"Hello from PHP!"}}';
$notification = new Notification("adm", $message);
$hub->sendNotification($notification, null);
```

Om du kör din PHP-kod bör du nu skapa ett meddelande som visas på mål enheten.

## <a name="next-steps"></a>Nästa steg

I det här avsnittet visade vi hur du skapar en enkel Java REST-klient för Notification Hubs. Här kan göra du följande:

* Hämta det kompletta [exemplet för php-rest], som innehåller all kod ovan.
* Fortsätt lära dig mer Notification Hubs taggnings funktionen i själv studie kursen [viktig Nyheter]
* Lär dig mer om att skicka meddelanden till enskilda användare i [själv studie kursen om att meddela användare]

Mer information finns även i [php Developer Center](https://azure.microsoft.com/develop/php/).

[Exempel på PHP-REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Skicka push-meddelanden till iOS-appar med hjälp av Azure Notification Hubs](ios-sdk-get-started.md))
