---
title: Använda Notification Hubs med python
description: Lär dig hur du använder Azure Notification Hubs från ett python-program.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-python
ms.openlocfilehash: f81614005a1b0374dc249187c4ff3c920b7c97e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92424850"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Använda Notification Hubs från python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Du kan komma åt alla Notification Hubs-funktioner från en Java/PHP/python/ruby-slutpunkt med hjälp av REST-gränssnittet Notification Hub enligt beskrivningen i MSDN-artikeln [Notification HUBS REST-API: er](/previous-versions/azure/reference/dn223264(v=azure.100)).

> [!NOTE]
> Det här är en exempel referens implementering för att implementera meddelandet som skickas i python och inte är det som är det som inte är det som stöds av python-SDK. Exemplet skapades med python 3,4.

Den här artikeln visar hur du:

- Bygg en REST-klient för Notification Hubs funktioner i python.
- Skicka meddelanden med hjälp av python-gränssnittet till REST-API: erna för Notification Hub.
- Få en dumpning av HTTP REST-begäran/-svaret för fel sökning/utbildnings syfte.

Du kan följa [självstudien kom igång](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) för den mobila plattform som du väljer, implementera backend-delen i python.

> [!NOTE]
> Omfånget för exemplet är bara begränsat för att skicka meddelanden och ingen registrerings hantering.

## <a name="client-interface"></a>Klient gränssnitt

Huvud klient gränssnittet kan ge samma metoder som är tillgängliga i [.net Notification HUBS SDK](https://msdn.microsoft.com/library/jj933431.aspx). Med det här gränssnittet kan du direkt översätta alla självstudier och exempel som är tillgängliga på den här webbplatsen och som tillhandahålls av communityn på Internet.

Du hittar all kod som är tillgänglig i [exempel omslutningen python].

Till exempel för att skapa en-klient:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Skicka ett popup-meddelande i Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementering

Om du inte redan har gjort det följer du [självstudien kom igång] fram till det sista avsnittet där du måste implementera Server delen.

All information om hur du implementerar ett komplett REST-omslag finns på [MSDN](/previous-versions/azure/reference/dn530746(v=azure.100)). I det här avsnittet beskrivs python-implementering av de viktigaste stegen som krävs för att få åtkomst till Notification Hubs REST-slutpunkter och skicka meddelanden

1. Parsa anslutningssträngen
2. Generera autentiseringstoken
3. Skicka ett meddelande med HTTP REST API

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen

Här är huvud klassen som implementerar klienten, vars konstruktor tolkar anslutnings strängen:

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>Skapa säkerhetstoken

Information om hur säkerhetstoken skapas finns [här](/previous-versions/azure/reference/dn495627(v=azure.100)).
Lägg till följande metoder i `NotificationHub` klassen för att skapa token baserat på URI: n för den aktuella begäran och de autentiseringsuppgifter som extraheras från anslutnings strängen.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))


@staticmethod
def encode_base64(data):
    return base64.b64encode(data)


def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest


def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>Skicka ett meddelande med HTTP REST API

Låt oss först använda definiera en klass som representerar ett meddelande.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Den här klassen är en behållare för en intern meddelande text eller en uppsättning egenskaper för en mall, en uppsättning huvuden, som innehåller format (ursprunglig plattform eller mall) och plattformsspecifika egenskaper (till exempel Apples förfallo egenskap och WNS-rubriker).

Läs [Notification HUBS REST API-dokumentationen](/previous-versions/azure/reference/dn495827(v=azure.100)) och de speciella meddelande plattforms formaten för alla alternativ som är tillgängliga.

Nu med den här klassen skriver du sändnings meddelande metoderna i `NotificationHub` klassen.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()


def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)


def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)


def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)


def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)


def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)


def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

    self.send_notification(nh, tags)


def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)


def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

Dessa metoder skickar en HTTP POST-begäran till/Messages-slutpunkten för Notification Hub, med rätt brödtext och rubriker för att skicka meddelandet.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Aktivera detaljerad loggning med hjälp av egenskapen debug

Om du aktiverar fel söknings egenskapen vid initiering av Notification Hub skrivs detaljerad loggnings information om HTTP-begäran och svars dumpning samt detaljerade aviserings meddelanden.
[Egenskapen Notification Hubs TestSend](/previous-versions/azure/reference/dn495827(v=azure.100)) returnerar detaljerad information om resultatet av meddelandet skicka.
Använd följande kod om du vill använda initiering av den:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Aviserings-HTTP-URL: en för Notification Hub läggs till med en "test"-frågesträng som ett resultat.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Slutför självstudien

Nu kan du slutföra självstudien kom igång genom att skicka meddelandet från en python-backend.

Initiera din Notification Hubs-klient (Ersätt anslutnings strängen och hubbens namn enligt anvisningarna i [Kom igång-självstudien]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Lägg sedan till sändnings koden beroende på din mål-mobila plattform. Det här exemplet lägger också till metoder på högre nivå för att aktivera sändning av meddelanden baserat på plattformen, till exempel send_windows_notification för Windows. send_apple_notification (för Apple) osv.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store och Windows Phone 8,1 (ej Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8,0 och 8,1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle-brand

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Om du kör python-koden bör du skapa ett meddelande som visas på mål enheten.

## <a name="examples"></a>Exempel

### <a name="enabling-the-debug-property"></a>Aktivera `debug` egenskapen

När du aktiverar fel söknings flaggan vid initieringen av NotificationHub visas detaljerad HTTP-begäran och svars dumpning samt NotificationOutcome som följande där du kan förstå vilka HTTP-huvuden som skickas i begäran och vilket HTTP-svar som har tagits emot från Notification Hub:

![Skärm bild av en-konsol med information om H T T P t P-begäran och svars dumpning och meddelanden om meddelande resultat som beskrivs i rött.][1]

Du ser ett detaljerat resultat för aviserings hubben.

- När meddelandet har skickats till Push Notification Service.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Om det inte fanns några mål för några push-meddelanden kommer du troligen att se följande utdata som svar (vilket tyder på att det inte fanns några registreringar som kunde leverera meddelandet, förmodligen eftersom registreringarna hade vissa felmatchade taggar)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Sänd popup-meddelande till Windows

Lägg märke till huvuden som skickas ut när du skickar ett popup-meddelande för sändning till Windows-klienten.

```python
hub.send_windows_notification(wns_payload)
```

![Skärm bild av en konsol med information om H T T P-begäran och Service Bus meddelande format och X W N S typ värden som beskrivs i rött.][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Skicka meddelande som anger en tagg (eller ett tagg uttryck)

Observera taggarna HTTP-huvud, som läggs till i HTTP-begäran (i exemplet nedan skickas meddelandet endast till registreringar med "idrotts"-nyttolasten)

```python
hub.send_windows_notification(wns_payload, "sports")
```

![Skärm bild av en konsol med information om H T T P-begäran och Service Bus meddelande format, en Service Bus meddelande tag gen och X W N S typ värden som anges i rött.][3]

### <a name="send-notification-specifying-multiple-tags"></a>Skicka meddelande som anger flera taggar

Observera hur taggarna HTTP-huvud ändras när flera taggar skickas.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![Skärm bild av en konsol med information om H T T P-begäran och Service Bus meddelande format, flera Service Bus Notification-Taggar och X W N S typ värden som anges i rött.][4]

### <a name="templated-notification"></a>Meddelande om mall

Observera att formatet HTTP-huvud ändras och nytto lasten skickas som en del av texten i HTTP-begäran:

**-Registrerad mall på klient sidan:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Server sidan – skickar nytto lasten:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![Skärm bild av en-konsol med information om H T T P P-begäran och innehålls typ och Service Buss format värden som anges i rött.][5]

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du skapar en python REST-klient för Notification Hubs. Här kan göra du följande:

- Hämta det fullständiga [python-exemplet för python-rest], som innehåller all kod i den här artikeln.
- Fortsätt lära dig mer om Notification Hubs taggnings funktionen i [själv studie kursen om att dela nyheter]
- Fortsätt lära dig mer om Notification Hubs mallar i [självstudien om nyheter]

<!-- URLs -->
[Exempel på python REST]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Självstudier för att komma igång]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Själv studie kurs om att dela nyheter]: ./notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Själv studie kurs om att lokalisera nyheter]: ./notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
