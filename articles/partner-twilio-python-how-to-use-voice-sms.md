---
title: Använda Twilio för röst och SMS (python) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kod exempel som skrivits i python.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.custom: devx-track-python
ms.openlocfilehash: b4b9cd0db2a3a99aca80f42b6d69485a542bbadb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580965"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Använda Twilio för röst-och SMS-funktioner i python
Den här guiden visar hur du utför vanliga programmerings åtgärder med Twilio API-tjänsten på Azure. Scenarierna som ingår är att ringa ett telefonsamtal och skicka ett SMS-meddelande (Short Message Service). Mer information om Twilio och hur du använder röst-och SMS i dina program finns i avsnittet [Nästa steg](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Vad är Twilio?
Twilio fungerar som en framtids framtid, vilket gör det möjligt för utvecklare att bädda in röst, VoIP och meddelande hantering i program. De virtualiserar all infrastruktur som behövs i en molnbaserad, global miljö och exponerar den via Twilio Communications API-plattformen. Programmen är enkla att bygga och skalbara. Njut av flexibilitet med priset betala per användning och dra nytta av molnets tillförlitlighet.

Med **Twilio Voice** kan dina program ringa och ta emot telefonsamtal.
**TWILIO SMS** gör det möjligt för ditt program att skicka och ta emot SMS-meddelanden.
Med **Twilio-klienten** kan du göra VoIP-samtal från valfri telefon, surfplatta eller webbläsare och har stöd för WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Priser och Special erbjudanden för Twilio
Azure-kunder får ett [Special erbjudande][special_offer] på $10 Twilio kredit när du uppgraderar ditt Twilio-konto. Den här Twilio-krediten kan tillämpas på alla Twilio-användningar ($10 kredit som motsvarar att skicka så många som 1 000 SMS-meddelanden eller ta emot upp till 1000 inkommande röst minuter, beroende på platsen för ditt telefonnummer och ditt meddelande eller samtals mål). Lös in denna [Twilio kredit][special_offer] och kom igång.

Twilio är en tjänst för att betala per användning. Det finns inga konfigurations avgifter och du kan när som helst stänga ditt konto. Du hittar mer information på [Twilio-priser][twilio_pricing].

## <a name="concepts"></a><a id="Concepts"></a>Begrepp
Twilio-API: et är ett RESTful-API som tillhandahåller röst-och SMS-funktioner för program. Klient bibliotek är tillgängliga på flera språk. en lista finns i [TWILIO API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio-verb och Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-verb
API: et använder Twilio-verb. exempelvis instruerar verbet Twilio till audibly att leverera ett meddelande på ett samtal. **&lt; &gt;**

Följande är en lista över Twilio-verb. Lär dig mer om andra verb och funktioner via [Twilio Markup Language-dokumentation][twiml].

* **&lt; Dial &gt;**: ansluter anroparen till en annan telefon.
* **&lt; Samla &gt;** in: samlar in numeriska siffror som anges på telefon tangent bordet.
* Koppla: avslutar ett anrop. **&lt; &gt;**
* **&lt; Paus &gt;**: väntar i tyst läge under ett angivet antal sekunder.
* **&lt; Play &gt;**: spelar upp en ljudfil.
* **&lt; Kö &gt;**: Lägg till i en kö med anropare.
* **&lt; Post &gt;**: registrerar rösten för anroparen och returnerar en URL för en fil som innehåller inspelningen.
* **&lt; Omdirigera &gt;**: överför kontroll av ett anrop eller SMS till TWIML på en annan URL.
* **&lt; Avvisa &gt;**: avvisar ett inkommande samtal till ditt Twilio-nummer utan fakturering.
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

När programmet anropar Twilio-API: t är en av API-parametrarna den URL som returnerar TwiML-svaret. I utvecklings syfte kan du använda Twilio webb adresser för att tillhandahålla de TwiML-svar som används av dina program. Du kan också vara värd för dina egna URL: er för att skapa TwiML-svar och ett annat alternativ är att använda `TwiMLResponse` objektet.

Mer information om Twilio-verb, deras attribut och TwiML finns i [TwiML][twiml]. Mer information om Twilio-API: et finns i [Twilio-API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto kan du registrera dig på [testa Twilio][try_twilio]. Du kan börja med ett kostnads fritt konto och uppgradera kontot senare.

När du registrerar dig för ett Twilio-konto får du ett konto-SID och en autentiseringstoken. Båda kommer att behövas för att göra Twilio-API-anrop. För att förhindra obehörig åtkomst till ditt konto bör du skydda din autentiseringstoken. Ditt konto-SID och autentiseringstoken visas i Twilio- [konsolen][twilio_console]i fälten med namnet **konto-sid** respektive **autentiseringstoken**.

## <a name="create-a-python-application"></a><a id="create_app"></a>Skapa ett python-program
Ett python-program som använder Twilio-tjänsten och körs i Azure skiljer sig något från andra python-program som använder Twilio-tjänsten. Även om Twilio-tjänsterna är REST-baserade och kan anropas från python på flera sätt, kommer den här artikeln att fokusera på hur du använder Twilio-tjänster med [Twilio-bibliotek för python från GitHub][twilio_python]. Mer information om hur du använder Twilio-biblioteket för python finns i [https://www.twilio.com/docs/libraries/python][twilio_lib_docs] .

Börja med att konfigurera en ny virtuell Azure Linux-dator] [azure_vm_setup] att fungera som värd för ditt nya python-webbprogram. När den virtuella datorn är igång måste du exponera programmet på en offentlig port enligt beskrivningen nedan.

### <a name="add-an-incoming-rule"></a>Lägg till en inkommande regel
  1. Gå till sidan [nätverks säkerhets grupp] [azure_nsg].
  2. Välj den nätverks säkerhets grupp som motsvarar den virtuella datorn.
  3. Lägg till en **utgående regel** för **port 80**. Se till att tillåta inkommande från vilken adress som helst.

### <a name="set-the-dns-name-label"></a>Ange DNS-namnets etikett
  1. Gå till sidan [offentliga IP-adresser] [azure_ips].
  2. Välj den offentliga IP-adress som motsvarar den virtuella datorn.
  3. Ange **etiketten DNS-namn** i **konfigurations** avsnittet. I det här exemplet ser det ut ungefär så här: *domän etiketten*. centralus.cloudapp.Azure.com

När du har möjlighet att ansluta via SSH till den virtuella datorn kan du installera det valda webb ramverket (de två mest välkända i python som [kolv](http://flask.pocoo.org/) -och [django](https://www.djangoproject.com)). Du kan installera någon av dem direkt genom att köra `pip install` kommandot.

Tänk på att vi har konfigurerat den virtuella datorn så att den endast tillåter trafik på port 80. Så se till att konfigurera programmet till att använda den här porten.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurera ditt program så att det använder Twilio-bibliotek
Du kan konfigurera ditt program så att det använder Twilio-biblioteket för python på två sätt:

* Installera Twilio-biblioteket för python som ett pip-paket. Den kan installeras med följande kommandon:
   
  `$ pip install twilio`

    ELLER

* Ladda ned Twilio-biblioteket för python från GitHub ( [https://github.com/twilio/twilio-python][twilio_python] ) och installera det så här:

  `$ python setup.py install`

När du har installerat Twilio-biblioteket för python kan du sedan använda `import` det i python-filerna:

  `import twilio`

Mer information finns i [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Gör så här: gör ett utgående samtal
Följande visar hur du gör ett utgående samtal. Den här koden använder också en Twilio plats för att returnera TwiML-svaret (Twilio Markup Language). Ersätt värdena för **from_number** och **to_number** telefonnummer och kontrol lera att du har verifierat **From_number** telefonnumret för ditt Twilio-konto innan du kör koden.

```python
from urllib.parse import urlencode

# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

# The number of the phone initiating the call.
# This should either be a Twilio number or a number that you've verified
from_number = "NNNNNNNNNNN"

# The number of the phone receiving call.
to_number = "NNNNNNNNNNN"

# Use the Twilio-provided site for the TwiML response.
url = "https://twimlets.com/message?"

# The phone message text.
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url + urlencode({'Message': message}))
print(call.sid)
```

> [!IMPORTANT]
> Telefonnummer måste formateras med en +-och en landskod. Till exempel + 16175551212 (E. 164-format). Twilio kommer också att acceptera oformaterade amerikanska tal. Till exempel (415) 555-1212 eller 415-555-1212.

Som nämnts använder den här koden en Twilio plats för att returnera TwiML-svaret. Du kan i stället använda din egen webbplats för att tillhandahålla TwiML-svaret. Mer information finns i [så här ger du TwiML svar från din egen webbplats](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Gör så här: Skicka ett SMS-meddelande
Följande visar hur du skickar ett SMS-meddelande med hjälp av- `TwilioRestClient` klassen. **From_numbers** numret tillhandahålls av Twilio för utvärderings konton för att skicka SMS-meddelanden. Du måste verifiera **to_numbers** numret för ditt Twilio-konto innan du kör koden.

```python
# Import the Twilio Python Client.
from twilio.rest import TwilioRestClient

# Set your account ID and authentication token.
account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"

from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
to_number = "NNNNNNNNNNN"
message = "Hello world."

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Send the SMS message.
message = client.messages.create(to=to_number,
                                    from_=from_number,
                                    body=message)
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Gör så här: Tillhandahåll TwiML svar från din egen webbplats
När ditt program initierar ett anrop till Twilio-API: et skickar Twilio din begäran till en URL som förväntas returnera ett TwiML-svar. Exemplet ovan använder Twilio-URL: en [https://twimlets.com/message][twimlet_message_url] . (Även om TwiML har utformats för användning av Twilio kan du Visa den i webbläsaren. Klicka till exempel om [https://twimlets.com/message][twimlet_message_url] du vill visa ett tomt `<Response>` element. Klicka på ett annat exempel [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] om du vill se ett `<Response>` element som innehåller ett `<Say>` -element.)

I stället för att förlita dig på Twilio-URL: en kan du skapa en egen webbplats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar XML-svar. i det här avsnittet förutsätter vi att du använder python för att skapa TwiML.

I följande exempel visas ett TwiML-svar som säger **Hello World** på anropet.

Med en kolv:

```python
from flask import Response
@app.route("/")
def hello():
    xml = '<Response><Say>Hello world.</Say></Response>'
    return Response(xml, mimetype='text/xml')
```

Med django:

```python
from django.http import HttpResponse
def hello(request):
    xml = '<Response><Say>Hello world.</Say></Response>'
    return HttpResponse(xml, content_type='text/xml')
```

Som du kan se i exemplet ovan är TwiML-svaret bara ett XML-dokument. Twilio-biblioteket för python innehåller klasser som ska generera TwiML åt dig. Exemplet nedan ger motsvarande svar enligt ovan, men använder `twiml` modulen i Twilio-biblioteket för python:

```python
from twilio import twiml

response = twiml.Response()
response.say("Hello world.")
print(str(response))
```

Mer information om TwiML finns i [https://www.twilio.com/docs/api/twiml][twiml_reference] .

När du har konfigurerat python-programmet för att tillhandahålla TwiML-svar använder du URL: en för programmet som den URL som skickades till- `client.calls.create`  metoden. Om du till exempel har ett webb program med namnet **MyTwiML** som har distribuerats till en Azure-värdbaserad tjänst kan du använda dess URL som webhook, som du ser i följande exempel:

```python
from twilio.rest import TwilioRestClient

account_sid = "your_twilio_account_sid"
auth_token = "your_twilio_authentication_token"
from_number = "NNNNNNNNNNN"
to_number = "NNNNNNNNNNN"
url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

# Initialize the Twilio client.
client = TwilioRestClient(account_sid, auth_token)

# Make the call.
call = client.calls.create(to=to_number,
                           from_=from_number,
                           url=url)
print(call.sid)
```

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Så här gör du: Använd ytterligare Twilio-tjänster
Förutom exemplen som visas här erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [TWILIO API-dokumentationen][twilio_api].

## <a name="next-steps"></a><a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna om Twilio-tjänsten kan du följa dessa länkar för att lära dig mer:

* [Twilio säkerhets rikt linjer][twilio_security_guidelines]
* [Twilio HowTo-handböcker och exempel kod][twilio_howtos]
* [Twilio snabb starts guider][twilio_quickstarts]
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio-support][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
