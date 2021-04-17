---
title: Så här använder du Notification Hubs med Python
description: Lär dig hur du använder Azure Notification Hubs från ett Python-program.
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
ms.openlocfilehash: f964957b916c6841da097f93173b0306bb65c8a4
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576031"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Så här använder du Notification Hubs från Python

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Du kan komma åt Notification Hubs funktioner från en Java/PHP/Python/Ruby-backend med hjälp av Notification Hub REST-gränssnittet enligt beskrivningen i MSDN-artikeln [Notification Hubs REST-API:er](/previous-versions/azure/reference/dn223264(v=azure.100)).

> [!NOTE]
> Det här är ett exempel på en referensimplementering för implementering av meddelandet som skickas i Python och är inte den notification hub-SDK som stöds officiellt. Exemplet skapades med Python 3.4.

Den här artikeln visar hur du:

- Skapa en REST-klient för Notification Hubs funktioner i Python.
- Skicka meddelanden med Hjälp av Python-gränssnittet till Notification Hub REST API:er.
- Hämta en dump av HTTP REST-begäran/-svaret för felsökning/utbildning.

Du kan följa [självstudien Kom](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) igång för valfri mobilplattform och implementera backend-delen i Python.

> [!NOTE]
> Omfånget för exemplet är bara begränsat till att skicka meddelanden och det gör inte någon registreringshantering.

## <a name="client-interface"></a>Klientgränssnitt

Huvudklientgränssnittet kan tillhandahålla samma metoder som är tillgängliga i [.NET Notification Hubs SDK.](https://msdn.microsoft.com/library/jj933431.aspx) Med det här gränssnittet kan du direkt översätta alla självstudier och exempel som för närvarande är tillgängliga på den här webbplatsen och bidra från communityn på Internet.

Du hittar all kod som är tillgänglig i [Python REST wrapper sample].

Om du till exempel vill skapa en klient:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Så här skickar du ett popup-meddelande i Windows:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementering

Om du inte redan har gjort det följer du [självstudiekursen] Kom igång fram till det sista avsnittet där du måste implementera backend-delen.

All information om hur du implementerar en fullständig REST-wrapper finns på [MSDN](/previous-versions/azure/reference/dn530746(v=azure.100)). I det här avsnittet beskrivs Python-implementeringen av de viktigaste stegen som krävs för att komma Notification Hubs REST-slutpunkter och skicka meddelanden

1. Parsa anslutningssträngen
2. Generera auktoriseringstoken
3. Skicka ett meddelande med HTTP-REST API

### <a name="parse-the-connection-string"></a>Parsa anslutningssträngen

Här är huvudklassen som implementerar klienten, vars konstruktor parsar anslutningssträngen:

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

Information om hur du skapar säkerhetstoken finns [här.](/previous-versions/azure/reference/dn495627(v=azure.100))
Lägg till följande metoder i klassen för att skapa token baserat på URI:en för den aktuella begäran och de autentiseringsuppgifter `NotificationHub` som extraheras från anslutningssträngen.

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

### <a name="send-a-notification-using-http-rest-api"></a>Skicka ett meddelande med HTTP-REST API

Börja med att definiera en klass som representerar ett meddelande.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'gcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Den här klassen är en container för en intern meddelandetext eller en uppsättning egenskaper för ett mallmeddelande, en uppsättning huvuden som innehåller format (ursprunglig plattform eller mall) och plattformsspecifika egenskaper (som Apples förfalloegenskap och WNS-huvuden).

Se dokumentationen [Notification Hubs FÖR REST-API:er](/previous-versions/azure/reference/dn495827(v=azure.100)) och de specifika meddelandeplattformarna för alla tillgängliga alternativ.

Nu med den här klassen skriver du metoderna för att skicka meddelanden i `NotificationHub` klassen .

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

    json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

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


def send_google_notification(self, payload, tags=""):
    nh = Notification("gcm", payload)
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

Dessa metoder skickar en HTTP POST-begäran till slutpunkten /messages i meddelandehubben, med rätt brödtext och huvuden för att skicka meddelandet.

### <a name="using-debug-property-to-enable-detailed-logging"></a>Använda felsökningsegenskapen för att aktivera detaljerad loggning

Om du aktiverar felsökningsegenskapen vid initiering av Notification Hub skriver du ut detaljerad loggningsinformation om HTTP-begäran och svarsdumpen samt det detaljerade aviseringsmeddelandeutfallet.
Egenskapen [Notification Hubs TestSend returnerar](/previous-versions/azure/reference/dn495827(v=azure.100)) detaljerad information om resultatet av aviseringen.
För att använda den initierar du med hjälp av följande kod:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Notification Hub Send-begärans HTTP-URL läggs till med en "test"-frågesträng som resultat.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>Slutför självstudien

Nu kan du slutföra självstudien Kom igång genom att skicka meddelandet från en Python-backend-

Initiera din Notification Hubs (ersätt anslutningssträngen och hubbnamnet enligt anvisningarna i [självstudien Komma igång]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Lägg sedan till skicka-koden beroende på din mobila målplattform. Det här exemplet lägger också till metoder på högre nivå för att möjliggöra sändning av meddelanden baserat på plattformen, till exempel send_windows_notification för Windows; send_apple_notification (för Apple) osv.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store och Windows Phone 8.1 (inte Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 och 8.1 Silverlight

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
gcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_google_notification(gcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

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

Om du kör Python-koden bör du få ett meddelande som visas på målenheten.

## <a name="examples"></a>Exempel

### <a name="enabling-the-debug-property"></a>Aktivera `debug` egenskapen

När du aktiverar felsökningsflaggan när du initierar NotificationHub visas detaljerad HTTP-begäran och svarsdump samt NotificationOutcome som följande där du kan förstå vilka HTTP-huvuden som skickas i begäran och vilket HTTP-svar som togs emot från Notification Hub:

![Skärmbild av en konsol med information om H T T P-begäran och svarsdump samt meddelanden om meddelanderesultat i rött.][1]

Du kan till exempel se ett detaljerat Meddelandehubbresultat.

- när meddelandet har skickats till push-meddelandetjänsten.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Om inga mål hittades för push-meddelanden kommer du förmodligen att se följande utdata som svar (vilket indikerar att inga registreringar hittades för att leverera meddelandet, förmodligen eftersom registreringarna hade några felmatchade taggar)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Popup-meddelande för broadcast till Windows

Observera de rubriker som skickas ut när du skickar ett popup-meddelande till Windows-klienten.

```python
hub.send_windows_notification(wns_payload)
```

![Skärmbild av en konsol med information om H T T P-begäran och Service Bus-aviseringsformat och X W N S-typvärden som beskrivs i rött.][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Skicka ett meddelande som anger en tagg (eller tagguttryck)

Lägg märke till HTTP-rubriken Taggar, som läggs till i HTTP-begäran (i exemplet nedan skickas meddelandet endast till registreringar med nyttolasten "sport")

```python
hub.send_windows_notification(wns_payload, "sports")
```

![Skärmbild av en konsol med information om H T T P-begäran och Service Bus-meddelandeformat, en Service Bus-meddelandetagg och X W N S-typvärden som beskrivs i rött.][3]

### <a name="send-notification-specifying-multiple-tags"></a>Skicka meddelande om att ange flera taggar

Observera hur HTTP-huvudet Taggar ändras när flera taggar skickas.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![Skärmbild av en konsol med information om H T T P-begäran och Service Bus-meddelandeformatet, flera Service Bus-meddelandetaggar och X W N S-typvärden som beskrivs i rött.][4]

### <a name="templated-notification"></a>Mallmeddelande

Observera att FORMATET HTTP-huvud ändras och nyttolastens brödtext skickas som en del av HTTP-begärandetexten:

**Klientsidan – registrerad mall:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Serversidan – skicka nyttolasten:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![Skärmbild av en konsol med information om H T T P-begäran, innehållstypen och Service Bus-meddelandeformatvärdena i rött.][5]

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du skapar en Python REST-klient för Notification Hubs. Här kan göra du följande:

- Ladda ned det fullständiga [Python REST-omslutningsexempel]som innehåller all kod i den här artikeln.
- Fortsätt lära dig Notification Hubs taggningsfunktionen i [självstudien Om de senaste nyheterna]
- Fortsätt lära dig Notification Hubs om funktionen Mallar i [självstudien Lokalisera nyheter]

<!-- URLs -->
[Exempel på Python REST-omsamplade]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Självstudier för att komma igång]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Självstudie om de senaste nyheterna]: ./notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Självstudie om att lokalisera nyheter]: ./notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
