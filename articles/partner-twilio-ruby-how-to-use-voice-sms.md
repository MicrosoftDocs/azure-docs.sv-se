---
title: Använda Twilio för röst och SMS (ruby) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kod exempel som skrivits i Ruby.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 49203195bf7746d0bff1b9543d1641f69ab23359
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95542685"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Använda Twilio för röst-och SMS-funktioner i ruby
Den här guiden visar hur du utför vanliga programmerings åtgärder med Twilio API-tjänsten på Azure. Scenarierna som ingår är att ringa ett telefonsamtal och skicka ett SMS-meddelande (Short Message Service). Mer information om Twilio och hur du använder röst-och SMS i dina program finns i avsnittet [Nästa steg](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Vad är Twilio?
Twilio är ett webb tjänst-API för telefoni som gör att du kan använda dina befintliga webb språk och-kunskaper för att bygga röst-och SMS-program. Twilio är en tjänst från tredje part (inte en Azure-funktion och inte en Microsoft-produkt).

Med **Twilio Voice** kan dina program ringa och ta emot telefonsamtal. Med **TWILIO SMS** kan dina program skapa och ta emot SMS-meddelanden. **Twilio-klienten** gör att dina program kan aktivera röst kommunikation med befintliga Internet anslutningar, inklusive mobila anslutningar.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Priser och Special erbjudanden för Twilio
Information om priser för Twilio finns på [Twilio-priser][twilio_pricing]. Azure-kunder får ett [Special erbjudande][special_offer]: en kostnads fri kredit på 1000 texter eller 1000 inkommande minuter. Om du vill registrera dig för det här erbjudandet eller få mer information kan du besöka [https://ahoy.twilio.com/azure][special_offer] .  

## <a name="concepts"></a><a id="Concepts"></a>Begrepp
Twilio-API: et är ett RESTful-API som tillhandahåller röst-och SMS-funktioner för program. Klient bibliotek är tillgängliga på flera språk. en lista finns i [TWILIO API-bibliotek][twilio_libraries].

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML är en uppsättning XML-baserade instruktioner som informerar Twilio om hur man bearbetar ett anrop eller SMS.

Följande TwiML skulle till exempel konvertera texten **Hello World** till tal.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
    <Say>Hello World</Say>
</Response>
```

Alla TwiML-dokument har `<Response>` samma rot element. Därifrån använder du Twilio-verb för att definiera appens beteende.

### <a name="twiml-verbs"></a><a id="Verbs"></a>TwiML-verb
Twilio-verb är XML-taggar som talar om för Twilio vad som ska **utföras**. Exempelvis instruerar verbet Twilio till audibly att leverera ett meddelande på ett samtal. **&lt; &gt;** 

Följande är en lista över Twilio-verb.

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

Mer information om Twilio-verb, deras attribut och TwiML finns i [TwiML][twiml]. Mer information om Twilio-API: et finns i [Twilio-API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto kan du registrera dig på [testa Twilio][try_twilio]. Du kan börja med ett kostnads fritt konto och uppgradera kontot senare.

När du registrerar dig för ett Twilio-konto får du ett kostnads fritt telefonnummer för ditt program. Du får också ett konto-SID och en auth-token. Båda kommer att behövas för att göra Twilio-API-anrop. För att förhindra obehörig åtkomst till ditt konto bör du skydda din autentiseringstoken. Ditt konto-SID och auth-token visas på [sidan för Twilio-kontot][twilio_account]i fälten med namnet konto- **sid** respektive **autentiseringstoken**.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Verifiera telefonnummer
Förutom det antal som du får av Twilio kan du också kontrol lera de tal som du styr (d.v.s. din mobiltelefon nummer eller ditt hem telefonnummer) för användning i dina program. 

Information om hur du verifierar ett telefonnummer finns i [Hantera tal][verify_phone].

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Skapa ett ruby-program
Ett ruby-program som använder Twilio-tjänsten och körs i Azure skiljer sig från andra ruby-program som använder tjänsten Twilio. Även om Twilio Services är RESTful och kan anropas från ruby på flera sätt, kommer den här artikeln att fokusera på hur du använder Twilio Services med [Twilio Helper Library för ruby][twilio_ruby].

Börja med att [skapa en ny virtuell Azure Linux-dator][azure_vm_setup] för att fungera som värd för ditt nya ruby-webbprogram. Ignorera stegen som beskriver hur du skapar en räler-app och konfigurera den virtuella datorn. Se till att du skapar en slut punkt med en extern port på 80 och en intern port på 5000.

I exemplen nedan kommer vi att använda [Sinatra][sinatra], ett mycket enkelt webb ramverk för ruby. Men du kan verkligen använda Twilio Helper Library för ruby med andra webb ramverk, inklusive Ruby on-räler.

Använda SSH i din nya virtuella dator och skapa en katalog för din nya app. I katalogen skapar du en fil med namnet Gemfile och kopierar följande kod till den:

```bash
source 'https://rubygems.org'
gem 'sinatra'
gem 'thin'
```

Kör på kommando raden `bundle install` . Detta kommer att installera beroendena ovan. Skapa sedan en fil med namnet `web.rb` . Det här är den plats där koden för din webbapp bor. Klistra in följande kod i den:

```ruby
require 'sinatra'

get '/' do
    "Hello Monkey!"
end
```

Nu bör du kunna köra kommandot `ruby web.rb -p 5000` . Detta skapar en liten webb server på port 5000. Du bör kunna bläddra till den här appen i webbläsaren genom att gå till den URL som du har ställt in för din virtuella Azure-dator. När du har nått din webbapp i webbläsaren är du redo att börja skapa en Twilio-app.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Konfigurera ditt program så att det använder Twilio
Du kan konfigurera webbappen så att den använder Twilio-biblioteket genom att uppdatera din `Gemfile` att inkludera den här raden:

```bash
gem 'twilio-ruby'
```

Kör på kommando raden `bundle install` . Öppna `web.rb` och inkludera den här raden överst:

```ruby
require 'twilio-ruby'
```

Nu har du angett att du vill använda Twilio Helper-biblioteket för ruby i din webbapp.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Gör så här: gör ett utgående samtal
Följande visar hur du gör ett utgående samtal. Viktiga begrepp är bland annat att använda Twilio Helper-biblioteket för ruby för att göra REST API samtal och återge TwiML. Ersätt värdena för **från** -och **till** -telefonnumret och se till att du verifierar **från** telefonnumret för ditt Twilio-konto innan du kör koden.

Lägg till den här funktionen i `web.md` :

```ruby
# Set your account ID and authentication token.
sid = "your_twilio_account_sid";
token = "your_twilio_authentication_token";

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from = "NNNNNNNNNNN";

# The number of the phone receiving call.
to = "NNNNNNNNNNN";

# Use the Twilio-provided site for the TwiML response.
url = "http://yourdomain.cloudapp.net/voice_url";

get '/make_call' do
    # Create the call client.
    client = Twilio::REST::Client.new(sid, token);

    # Make the call
    client.account.calls.create(to: to, from: from, url: url)
end

post '/voice_url' do
    "<Response>
        <Say>Hello Monkey!</Say>
    </Response>"
end
```

Om du öppnar `http://yourdomain.cloudapp.net/make_call` i en webbläsare kommer det att utlösa anropet till Twilio-API: et för att ringa telefonsamtalet. De två första parametrarna i `client.account.calls.create` är ganska själv för klar ande: antalet anrop är `from` och numret som anropet är `to` . 

Den tredje parametern ( `url` ) är den URL som Twilio begär för att få instruktioner om vad som ska utföras när anropet är anslutet. I det här fallet ställer vi in en URL ( `http://yourdomain.cloudapp.net` ) som returnerar ett enkelt TwiML-dokument och använder `<Say>` verbet för att göra vissa text till tal och säger "Hello apa" till den person som tar emot samtalet.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Så här gör du: ta emot ett SMS-meddelande
I det tidigare exemplet initierade vi ett **utgående** telefonsamtal. Den här gången ska vi använda telefonnumret som Twilio gav oss under registreringen för att bearbeta ett **inkommande** SMS-meddelande.

Logga först in på din Twilio- [instrumentpanel][twilio_account]. Klicka på "siffror" i det övre navigerings fältet och klicka sedan på Twilio-talet som du angav. Du ser två URL: er som du kan konfigurera. En URL till en röst förfrågan och en URL för SMS-begäran. Detta är de URL: er som Twilio anropar varje gång ett telefonsamtal görs eller om ett SMS skickas till ditt nummer. URL-adresserna kallas även "Webhooks".

Vi vill bearbeta inkommande SMS-meddelanden, så vi uppdaterar URL: en till `http://yourdomain.cloudapp.net/sms_url` . Gå vidare och klicka på Spara ändringar längst ned på sidan. Nu `web.rb` ska vi gå tillbaka till vårt program för att hantera detta:

```ruby
post '/sms_url' do
    "<Response>
        <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
    </Response>"
end
```

När du har gjort ändringen ska du se till att starta om din webbapp. Ta nu en titt på din telefon och skicka ett SMS till din Twilio-nummer. Du bör uppmanas att få ett SMS-svar som säger "Hej, tack för ping! Twilio och Azure Rock! ".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Så här gör du: Använd ytterligare Twilio-tjänster
Förutom exemplen som visas här erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [TWILIO API-dokumentationen][twilio_api_documentation].

### <a name="next-steps"></a><a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna i Twilio-tjänsten kan du följa dessa länkar för att lära dig mer:

* [Twilio säkerhets rikt linjer][twilio_security_guidelines]
* [Twilio HowTos och exempel kod][twilio_howtos]
* [Twilio snabb starts guider][twilio_quickstarts] 
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio-support][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





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
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: /previous-versions/azure/virtual-machines/linux/classic/ruby-rails-web-app