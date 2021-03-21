---
title: Använda Twilio för röst-och SMS (Java) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kod exempel som skrivits i Java.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: e7ff925534a9cab6523097f84a8ae199e9cb83eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548734"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Använda Twilio för röst-och SMS-funktioner i Java
Den här guiden visar hur du utför vanliga programmerings åtgärder med Twilio API-tjänsten på Azure. Scenarierna som ingår är att ringa ett telefonsamtal och skicka ett SMS-meddelande (Short Message Service). Mer information om Twilio och hur du använder röst-och SMS i dina program finns i avsnittet [Nästa steg](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Vad är Twilio?
Twilio är ett webb tjänst-API för telefoni som gör att du kan använda dina befintliga webb språk och-kunskaper för att bygga röst-och SMS-program. Twilio är en tjänst från tredje part (inte en Azure-funktion och inte en Microsoft-produkt).

Med **Twilio Voice** kan dina program ringa och ta emot telefonsamtal. Med **TWILIO SMS** kan dina program skapa och ta emot SMS-meddelanden. **Twilio-klienten** gör att dina program kan aktivera röst kommunikation med befintliga Internet anslutningar, inklusive mobila anslutningar.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Priser och Special erbjudanden för Twilio
Information om priser för Twilio finns på [Twilio-priser][twilio_pricing]. Azure-kunder får ett [Special erbjudande][special_offer]: en kostnads fri kredit på 1000 texter eller 1000 inkommande minuter. Om du vill registrera dig för det här erbjudandet eller få mer information kan du besöka [https://ahoy.twilio.com/azure][special_offer] .

## <a name="concepts"></a><a id="Concepts"></a>Begrepp
Twilio-API: et är ett RESTful-API som tillhandahåller röst-och SMS-funktioner för program. Klient bibliotek är tillgängliga på flera språk. en lista finns i [TWILIO API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio-verb och Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-verb
API: et använder Twilio-verb. exempelvis instruerar verbet Twilio till audibly att leverera ett meddelande på ett samtal. **&lt; &gt;**

Följande är en lista över Twilio-verb.

* **&lt; Dial &gt;**: ansluter anroparen till en annan telefon.
* **&lt; Samla &gt;** in: samlar in numeriska siffror som anges på telefon tangent bordet.
* Koppla: avslutar ett anrop. **&lt; &gt;**
* **&lt; Play &gt;**: spelar upp en ljudfil.
* **&lt; Kö &gt;**: Lägg till i en kö med anropare.
* **&lt; Paus &gt;**: väntar i tyst läge under ett angivet antal sekunder.
* **&lt; Post &gt;**: registrerar anroparens röst och returnerar en URL för en fil som innehåller inspelningen.
* **&lt; Omdirigera &gt;**: överför kontroll av ett anrop eller SMS till TWIML på en annan URL.
* **&lt; Avvisa &gt;**: avvisar ett inkommande samtal till ditt Twilio-nummer utan fakturering.
* **&lt; Säg &gt;**: konverterar text till tal som görs i ett samtal.
* **&lt; SMS &gt;**: skickar ett SMS-meddelande.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML är en uppsättning XML-baserade instruktioner som baseras på Twilio-verb som informerar Twilio om hur man bearbetar ett anrop eller SMS.

Följande TwiML skulle till exempel konvertera texten **Hello World!** till tal.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

När programmet anropar Twilio-API: t är en av API-parametrarna den URL som returnerar TwiML-svaret. I utvecklings syfte kan du använda Twilio webb adresser för att tillhandahålla de TwiML-svar som används av dina program. Du kan också vara värd för dina egna URL: er för att skapa TwiML-svar och ett annat alternativ är att använda **TwiMLResponse** -objektet.

Mer information om Twilio-verb, deras attribut och TwiML finns i [TwiML][twiml]. Mer information om Twilio-API: et finns i [Twilio-API][twilio_api].

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Skapa ett Twilio-konto
När du är redo att skaffa ett Twilio-konto kan du registrera dig på [testa Twilio][try_twilio]. Du kan börja med ett kostnads fritt konto och uppgradera kontot senare.

När du registrerar dig för ett Twilio-konto får du ett konto-ID och en autentiseringstoken. Båda kommer att behövas för att göra Twilio-API-anrop. För att förhindra obehörig åtkomst till ditt konto bör du skydda din autentiseringstoken. Ditt konto-ID och autentiseringstoken visas i Twilio- [konsolen][twilio_console]i fälten med namnet **konto-sid** respektive **autentiseringstoken**.

## <a name="create-a-java-application"></a><a id="create_app"></a>Skapa ett Java-program
1. Hämta Twilio-JAR och Lägg till den i din Java-build-sökväg och din WAR-distributions sammansättning. I [https://github.com/twilio/twilio-java][twilio_java] kan du ladda ned GitHub-källorna och skapa en egen jar-fil eller ladda ned en förbyggd jar-fil (med eller utan beroenden).
2. Se till att ditt JDK- **cacerts** innehåller Equifax säkra certifikat utfärdarens certifikat med MD5 finger avtryck 67: CB: 9d: C0:13:24:8a: 82:9b: B2:17:1e: D1:1b: EG: D4 (serie numret är 35: de: F4: CF och det SHA1-finger avtryck är D2:32:09: AD: 23: D3:14:23:21:74: E4:14.4:7f: 9d: 62:13:97:86:63:3a). Detta är certifikat utfärdarens certifikat (CA) för [https://api.twilio.com][twilio_api_service] tjänsten, som anropas när du använder Twilio-API: er.

Detaljerade instruktioner för att använda Twilio-klient biblioteket för Java finns i [så här ringer du upp ett telefonsamtal med Twilio i ett Java-program på Azure][howto_phonecall_java].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurera ditt program så att det använder Twilio-bibliotek
I din kod kan du lägga till **import** -instruktioner överst i källfilerna för de Twilio-paket eller klasser som du vill använda i ditt program.

För java-källfiler:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

För källfiler för Java Server Page (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
```

Beroende på vilka Twilio-paket eller klasser du vill använda kan dina **import** -instruktioner vara olika.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Gör så här: gör ett utgående samtal
Följande visar hur du gör ett utgående samtal med hjälp av **anrops** klassen. Den här koden använder också en Twilio plats för att returnera TwiML-svaret (Twilio Markup Language). Ersätt värdena för **från** -och **till** -telefonnumret och se till att du verifierar **från** telefonnumret för ditt Twilio-konto innan du kör koden.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Mer information om de parametrar som skickas till metoden **Call. Creator** finns i [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] .

Som nämnts använder den här koden en Twilio plats för att returnera TwiML-svaret. Du kan i stället använda din egen webbplats för att tillhandahålla TwiML-svaret. Mer information finns i [så här ger du TwiML svar i ett Java-program på Azure](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Gör så här: Skicka ett SMS-meddelande
Följande visar hur du skickar ett SMS-meddelande med hjälp av **meddelande** klassen. **From** Number **4155992671** tillhandahålls av Twilio för utvärderings konton för att skicka SMS-meddelanden. **Till** -numret måste kontrol leras för ditt Twilio-konto innan koden körs.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Mer information om de parametrar som skickas till metoden **Message. Creator** finns i [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms] .

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Gör så här: Tillhandahåll TwiML svar från din egen webbplats
När ditt program initierar ett anrop till Twilio-API: t, till exempel via metoden **CallCreator. Create** , skickar Twilio din begäran till en URL som förväntas returnera ett TwiML-svar. Exemplet ovan använder Twilio-URL: en [https://twimlets.com/message][twimlet_message_url] . (Även om TwiML har utformats för användning av webb tjänster kan du Visa TwiML i webbläsaren. Klicka till exempel på [https://twimlets.com/message][twimlet_message_url] om du vill se ett tomt **&lt; &gt; svars** element, som ett annat exempel: klicka [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] om du vill se ett **&lt; svars &gt;** element som innehåller ett **&lt; Säg &gt;** -element.)

I stället för att förlita dig på Twilio-URL: en kan du skapa en egen URL-plats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar HTTP-svar. Det här avsnittet förutsätter att du är värd för URL: en på en JSP-sida.

Följande JSP-sida resulterar i ett TwiML-svar som säger **Hello World!** på anropet.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

Följande JSP-sida resulterar i ett TwiML-svar som säger att det finns text, har flera pauser och innehåller information om Twilio API-versionen och Azure Role-namnet.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

Parametern **API version** är tillgänglig i Twilio Voice-begäranden (inte SMS-begäranden). Se respektive för att se de tillgängliga parametrarna för begäran för Twilio röst-och SMS-begäranden <https://www.twilio.com/docs/api/twiml/twilio_request> <https://www.twilio.com/docs/api/twiml/sms/twilio_request> . **Rolename** Environment-variabeln är tillgänglig som en del av en Azure-distribution. (Om du vill lägga till anpassade miljövariabler så att de kan hämtas från **system. getenv**, se avsnittet miljövariabler i [Diverse roll konfigurations inställningar][misc_role_config_settings].)

När du har konfigurerat JSP-sidan för att tillhandahålla TwiML svar, använder du webb adressen till JSP-sidan som den URL som skickades till metoden **Call. Creator** . Om du till exempel har ett webb program med namnet MyTwiML distribuerat till en värd tjänst i Azure och namnet på JSP-sidan är mytwiml.jsp, kan URL: en skickas till **anropa. Creator** som visas i följande avsnitt:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Ett annat alternativ för att svara med TwiML är via **VoiceResponse** -klassen, som finns i paketet **com. Twilio. TwiML** .

Mer information om hur du använder Twilio i Azure med Java finns i [så här ringer du upp ett telefonsamtal med Twilio i ett Java-program på Azure][howto_phonecall_java].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Så här gör du: Använd ytterligare Twilio-tjänster
Förutom exemplen som visas här erbjuder Twilio webbaserade API: er som du kan använda för att utnyttja ytterligare Twilio-funktioner från ditt Azure-program. Fullständig information finns i [TWILIO API-dokumentationen][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Nästa steg
Nu när du har lärt dig grunderna i Twilio-tjänsten kan du följa dessa länkar för att lära dig mer:

* [Twilio säkerhets rikt linjer][twilio_security_guidelines]
* [Twilio-HowTo och exempel kod][twilio_howtos]
* [Twilio snabb starts guider][twilio_quickstarts]
* [Twilio på GitHub][twilio_on_github]
* [Prata med Twilio-support][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
