---
title: Använda Twilio för röst och SMS (PHP) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kod exempel skrivna i PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: bf1ab01b39d594002bc5e677ffe6c3049fbb91ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95521027"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Använda Twilio för röst-och SMS-funktioner i PHP
Den här guiden visar hur du utför vanliga programmerings åtgärder med Twilio API-tjänsten på Azure. Scenarierna som ingår är att ringa ett telefonsamtal och skicka ett SMS-meddelande (Short Message Service). Mer information om Twilio och hur du använder röst-och SMS i dina program finns i avsnittet [Nästa steg](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Vad är Twilio?
Twilio fungerar som en framtids framtid, vilket gör det möjligt för utvecklare att bädda in röst, VoIP och meddelande hantering i program. De virtualiserar all infrastruktur som behövs i en molnbaserad, global miljö och exponerar den via Twilio Communications API-plattformen. Programmen är enkla att bygga och skalbara. Njut av flexibilitet med priset betala per användning och dra nytta av molnets tillförlitlighet.

Med **Twilio Voice** kan dina program ringa och ta emot telefonsamtal. **TWILIO SMS** gör det möjligt för ditt program att skicka och ta emot SMS-meddelanden. Med **Twilio-klienten** kan du göra VoIP-samtal från valfri telefon, surfplatta eller webbläsare och har stöd för WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Priser och Special erbjudanden för Twilio
Azure-kunder får ett [Special erbjudande](https://www.twilio.com/azure): kostnads fri $10 Twilio kredit när du uppgraderar ditt Twilio-konto. Den här Twilio-krediten kan tillämpas på alla Twilio-användningar ($10 kredit som motsvarar att skicka så många som 1 000 SMS-meddelanden eller ta emot upp till 1000 inkommande röst minuter, beroende på platsen för ditt telefonnummer och ditt meddelande eller samtals mål). Lös in denna Twilio kredit och kom igång på: [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure) .

Twilio är en tjänst för att betala per användning. Det finns inga konfigurations avgifter och du kan när som helst stänga ditt konto. Du hittar mer information på [Twilio-priser][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Begrepp
Twilio-API: et är ett RESTful-API som tillhandahåller röst-och SMS-funktioner för program. Klient bibliotek är tillgängliga på flera språk. en lista finns i [TWILIO API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio-verb och Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-verb
API: et använder Twilio-verb. exempelvis instruerar verbet Twilio till audibly att leverera ett meddelande på ett samtal. **&lt; &gt;**

Följande är en lista över Twilio-verb. Lär dig mer om andra verb och funktioner via [Twilio Markup Language-dokumentation](https://www.twilio.com/docs/api/twiml).

* **&lt; Dial &gt;**: ansluter anroparen till en annan telefon.
* **&lt; Samla &gt;** in: samlar in numeriska siffror som anges på telefon tangent bordet.
* Koppla: avslutar ett anrop. **&lt; &gt;**
* **&lt; Play &gt;**: spelar upp en ljudfil.
* **&lt; Paus &gt;**: väntar i tyst läge under ett angivet antal sekunder.
* **&lt; Post &gt;**: registrerar anroparens röst och returnerar en URL för en fil som innehåller inspelningen.
* **&lt; Omdirigera &gt;**: överför kontroll av ett anrop eller SMS till TWIML på en annan URL.
* **&lt; Avvisa &gt;**: avvisar ett inkommande samtal till ditt Twilio-nummer utan fakturering
* **&lt; Säg &gt;**: konverterar text till tal som görs i ett samtal.
* **&lt; SMS &gt;**: skickar ett SMS-meddelande.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML är en uppsättning XML-baserade instruktioner som baseras på Twilio-verb som informerar Twilio om hur man bearbetar ett anrop eller SMS.

Följande TwiML skulle till exempel konvertera texten **Hello World** till tal.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

När programmet anropar Twilio-API: t är en av API-parametrarna den URL som returnerar TwiML-svaret. I utvecklings syfte kan du använda Twilio webb adresser för att tillhandahålla de TwiML-svar som används av dina program. Du kan också vara värd för dina egna URL: er för att skapa TwiML-svar och ett annat alternativ är att använda **TwiMLResponse** -objektet.

Mer information om Twilio-verb, deras attribut och TwiML finns i [TwiML][twiml]. Mer information om Twilio-API: et finns i [Twilio-API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto kan du registrera dig på [testa Twilio][try_twilio]. Du kan börja med ett kostnads fritt konto och uppgradera kontot senare.

När du registrerar dig för ett Twilio-konto får du ett konto-ID och en autentiseringstoken. Båda kommer att behövas för att göra Twilio-API-anrop. För att förhindra obehörig åtkomst till ditt konto bör du skydda din autentiseringstoken. Ditt konto-ID och autentiseringstoken visas på sidan för [Twilio-kontot][twilio_account]i fälten med namnet konto- **sid** respektive **autentiseringstoken**.

## <a name="create-a-php-application"></a><a id="create_app"></a>Skapa ett PHP-program
Ett PHP-program som använder Twilio-tjänsten och körs i Azure skiljer sig inte från andra PHP-program som använder tjänsten Twilio. Även om Twilio-tjänsterna är REST-baserade och kan anropas från PHP på flera sätt, kommer den här artikeln att fokusera på hur du använder Twilio-tjänster med [Twilio-bibliotek för php från GitHub][twilio_php]. Mer information om hur du använder Twilio-biblioteket för PHP finns i [https://www.twilio.com/docs/libraries/php][twilio_lib_docs] .

Detaljerade anvisningar för att skapa och distribuera ett Twilio-/PHP-program till Azure finns i [så här ringer du ett telefonsamtal med Twilio i ett PHP-program på Azure][howto_phonecall_php].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurera ditt program så att det använder Twilio-bibliotek
Du kan konfigurera ditt program så att det använder Twilio-biblioteket för PHP på två sätt:

1. Ladda ned Twilio-biblioteket för PHP från GitHub ( [https://github.com/twilio/twilio-php][twilio_php] ) och Lägg till katalogen **tjänster** i ditt program.
   
    ELLER
2. Installera Twilio-biblioteket för PHP som ett päron paket. Den kan installeras med följande kommandon:

    ```bash
    $ pear channel-discover twilio.github.com/pear
    $ pear install twilio/Services_Twilio
    ```

När du har installerat Twilio-biblioteket för PHP kan du lägga till ett **require_once** -uttryck högst upp i dina php-filer för att referera till biblioteket:

```php
require_once 'Services/Twilio.php';
```

Mer information finns i [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Gör så här: gör ett utgående samtal
Följande visar hur du gör ett utgående samtal med hjälp av **Services_Twilio** -klassen. Den här koden använder också en Twilio plats för att returnera TwiML-svaret (Twilio Markup Language). Ersätt värdena för **från** -och **till** -telefonnumret och se till att du verifierar **från** telefonnumret för ditt Twilio-konto innan du kör koden.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";

// The number of the phone initiating the call.
$from_number = "NNNNNNNNNNN";

// The number of the phone receiving call.
$to_number = "NNNNNNNNNNN";

// Use the Twilio-provided site for the TwiML response.
$url = "https://twimlets.com/message";

// The phone message text.
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

//Make the call.
try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

Som nämnts använder den här koden en Twilio plats för att returnera TwiML-svaret. Du kan i stället använda din egen webbplats för att tillhandahålla TwiML-svaret. Mer information finns i [så här ger du TwiML svar från din egen webbplats](#howto_provide_twiml_responses).

* **Obs**: för att felsöka verifierings fel för TLS/SSL-certifikat, se [https://www.twilio.com/docs/api/errors][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Gör så här: Skicka ett SMS-meddelande
Följande visar hur du skickar ett SMS-meddelande med hjälp av **Services_Twilio** -klassen. **Från** -numret tillhandahålls av Twilio för utvärderings konton för att skicka SMS-meddelanden. **Till** -numret måste kontrol leras för ditt Twilio-konto innan koden körs.

```php
// Include the Twilio PHP library.
require_once 'Services/Twilio.php';

// Library version.
$version = "2010-04-01";

// Set your account ID and authentication token.
$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";


$from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
$to_number = "NNNNNNNNNNN";
$message = "Hello world.";

// Create the call client.
$client = new Services_Twilio($sid, $token, $version);

// Send the SMS message.
try
{
    $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Gör så här: Tillhandahåll TwiML svar från din egen webbplats
När ditt program initierar ett anrop till Twilio-API: et skickar Twilio din begäran till en URL som förväntas returnera ett TwiML-svar. Exemplet ovan använder Twilio-URL: en [https://twimlets.com/message][twimlet_message_url] . (Även om TwiML har utformats för användning av Twilio kan du Visa den i webbläsaren. Klicka till exempel om [https://twimlets.com/message][twimlet_message_url] du vill visa ett tomt `<Response>` element. Klicka på ett annat exempel [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] om du vill se ett `<Response>` element som innehåller ett `<Say>` -element.)

I stället för att förlita dig på Twilio-URL: en kan du skapa en egen webbplats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar XML-svar. i det här avsnittet förutsätter vi att du använder PHP för att skapa TwiML.

Följande PHP-sida resulterar i ett TwiML-svar som säger **Hello World** på anropet.

```xml
<?php    
    header("content-type: text/xml");    
    echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
?>
<Response>    
    <Say>Hello world.</Say>
</Response>
```

Som du kan se i exemplet ovan är TwiML-svaret bara ett XML-dokument. Twilio-biblioteket för PHP innehåller klasser som ska generera TwiML åt dig. Exemplet nedan genererar motsvarande svar enligt ovan, men använder klassen **Services \_ Twilio \_ Twiml** i TWILIO-biblioteket för php:

```php
require_once('Services/Twilio.php');

$response = new Services_Twilio_Twiml();
$response->say("Hello world.");
print $response;
```

Mer information om TwiML finns i [https://www.twilio.com/docs/api/twiml][twiml_reference] . 

När du har konfigurerat PHP-sidan för att tillhandahålla TwiML svar, använder du URL: en för PHP-sidan som den URL som skickades till-  `Services_Twilio->account->calls->create`  metoden. Om du till exempel har ett webb program med namnet **MyTwiML** som har distribuerats till en Azure-värdbaserad tjänst och namnet på php-sidan är **MyTwiML. php**, kan URL: en skickas till  **Services_Twilio->konto->anrop->skapa**  som visas i följande exempel:

```php
require_once 'Services/Twilio.php';

$sid = "your_twilio_account_sid";
$token = "your_twilio_authentication_token";
$from_number = "NNNNNNNNNNN";
$to_number = "NNNNNNNNNNN";
$url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

// The phone message text.
$message = "Hello world.";

$client = new Services_Twilio($sid, $token, "2010-04-01");

try
{
    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        $url.'?Message='.urlencode($message)
    );
}
catch (Exception $e) 
{
    echo 'Error: ' . $e->getMessage();
}
```

Mer information om hur du använder Twilio i Azure med PHP finns i [så här ringer du upp ett telefonsamtal med Twilio i ett PHP-program på Azure][howto_phonecall_php].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Så här gör du: Använd ytterligare Twilio-tjänster
Förutom exemplen som visas här erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [TWILIO API-dokumentationen][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna i Twilio-tjänsten kan du följa dessa länkar för att lära dig mer:

* [Twilio säkerhets rikt linjer][twilio_security_guidelines]
* [Twilio-HowTo och exempel kod][twilio_howtos]
* [Twilio snabb starts guider][twilio_quickstarts] 
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio-support][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart