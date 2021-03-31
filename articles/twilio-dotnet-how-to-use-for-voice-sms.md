---
title: Använda Twilio för röst-och SMS (.NET) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal och skicka ett SMS-meddelande med Twilio-API-tjänsten på Azure. Kod exempel som skrivits i .NET.
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.custom: devx-track-dotnet
ms.openlocfilehash: 104f969f5e27ef36ad43eb10e19176a4bcfd6648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003775"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Använda Twilio för röst-och SMS-funktioner från Azure
Den här guiden visar hur du utför vanliga programmerings åtgärder med Twilio API-tjänsten på Azure. Scenarierna som ingår är att ringa ett telefonsamtal och skicka ett SMS-meddelande (Short Message Service). Mer information om Twilio och hur du använder röst-och SMS i dina program finns i avsnittet [Nästa steg](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Vad är Twilio?
Twilio fungerar som en framtids framtid, vilket gör det möjligt för utvecklare att bädda in röst, VoIP och meddelande hantering i program. De virtualiserar all infrastruktur som behövs i en molnbaserad, global miljö och exponerar den via Twilio Communications API-plattformen. Programmen är enkla att bygga och skalbara. Njut av flexibilitet med priset för betala per användning och dra nytta av molnets tillförlitlighet.

Med **Twilio Voice** kan dina program ringa och ta emot telefonsamtal. **TWILIO SMS** gör det möjligt för dina program att skicka och ta emot SMS-meddelanden. Med **Twilio-klienten** kan du göra VoIP-samtal från valfri telefon, surfplatta eller webbläsare och har stöd för WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Priser och Special erbjudanden för Twilio
Azure-kunder får ett [Special erbjudande](https://www.twilio.com/azure): kostnads fri $10 Twilio kredit när du uppgraderar ditt Twilio-konto. Den här Twilio-krediten kan tillämpas på alla Twilio-användningar ($10 kredit som motsvarar att skicka så många som 1 000 SMS-meddelanden eller ta emot upp till 1000 inkommande röst minuter, beroende på platsen för ditt telefonnummer och ditt meddelande eller samtals mål). Lös in denna Twilio kredit och kom igång på [Twilio.com/Azure](https://twilio.com/azure).

Twilio är en tjänst för att betala per användning. Det finns inga konfigurations avgifter, och du kan när som helst stänga ditt konto. Du hittar mer information på [Twilio-priser](https://www.twilio.com/voice/pricing).

## <a name="concepts"></a><a id="Concepts"></a>Begrepp
Twilio-API: et är ett RESTful-API som tillhandahåller röst-och SMS-funktioner för program. Klient bibliotek är tillgängliga på flera språk. en lista finns i [TWILIO API-bibliotek][twilio_libraries].

Viktiga aspekter av Twilio-API: et är Twilio-verb och Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-verb
API: et använder Twilio-verb. exempelvis instruerar verbet Twilio till audibly att leverera ett meddelande på ett samtal. **&lt; &gt;**

Följande är en lista över Twilio-verb.  Lär dig mer om andra verb och funktioner via [Twilio Markup Language-dokumentation](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Ansluter anroparen till en annan telefon.
* `<Gather>`: Samlar in numeriska siffror som anges på telefon tangent bordet.
* `<Hangup>`: Avslutar ett anrop.
* `<Play>`: Spelar upp en ljudfil.
* `<Pause>`: Väntar i tyst läge under ett angivet antal sekunder.
* `<Record>`: Registrerar anroparens röst och returnerar en URL för en fil som innehåller inspelningen.
* `<Redirect>`: Överför kontrollen av ett anrop eller SMS till TwiML på en annan URL.
* `<Reject>`: Avvisar ett inkommande samtal till ditt Twilio-nummer utan fakturering
* `<Say>`: Konverterar text till tal som görs i ett anrop.
* `<Sms>`: Skickar ett SMS-meddelande.

### <a name="twiml"></a>TwiML
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

## <a name="create-an-azure-application"></a><a id="create_app"></a>Skapa en Azure Application
Ett Azure-program som är värd för ett Twilio-aktiverat program skiljer sig inte från något annat Azure-program. Du lägger till Twilio .NET-biblioteket och konfigurerar rollen för att använda Twilio .NET-bibliotek.
Information om hur du skapar ett första Azure-projekt finns i [skapa ett Azure-projekt med Visual Studio][vs_project].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Konfigurera ditt program så att det använder Twilio-bibliotek
Twilio innehåller en uppsättning .NET Helper-bibliotek som omsluter olika aspekter av Twilio för att ge enkla och enkla sätt att interagera med Twilio-REST API och Twilio-klienten för att generera TwiML svar.

Twilio tillhandahåller fem bibliotek för .NET-utvecklare:

| Bibliotek | Beskrivning |
| --- | --- |
| Twilio. API | Kärn Twilio-biblioteket som omsluter Twilio-REST API i ett eget .NET-bibliotek. Det här biblioteket är tillgängligt för .NET, Silverlight och Windows Phone 7. |
| Twilio. TwiML | Är ett eget .NET-sätt att generera TwiML markering. |
| Twilio. MVC | För utvecklare som använder ASP.NET MVC innehåller det här biblioteket en TwilioController, TwiML ActionResult och ett verifierings attribut för begäran. |
| Twilio. WebMatrix | För utvecklare som använder Microsofts kostnads fria verktyg för WebMatrix-utveckling innehåller det här biblioteket kniv-syntax för olika Twilio-åtgärder. |
| Twilio. client. Capability | Innehåller Capabilitity-generatorn för användning med Twilio-klientens JavaScript SDK. |

> [!Important]
> Alla bibliotek kräver .NET 3,5, Silverlight 4 eller Windows Phone 7 eller senare.

I de exempel som anges i den här guiden används biblioteket Twilio. API.

Biblioteken kan [installeras med NuGet Package Manager-tillägget](https://www.twilio.com/docs/csharp/install) tillgängligt för Visual Studio 2010 upp till 2015.  Käll koden finns på [GitHub][twilio_github_repo], som innehåller en wiki som innehåller fullständig dokumentation för att använda biblioteken.

Som standard installerar Microsoft Visual Studio 2010 version 1,2 av NuGet. Att installera Twilio-biblioteken kräver version 1,6 av NuGet eller senare. Information om hur du installerar eller uppdaterar NuGet finns i [https://nuget.org/][nuget] .

> [!NOTE]
> Om du vill installera den senaste versionen av NuGet måste du först avinstallera den inlästa versionen med hjälp av Visual Studio Extension Manager. För att göra det måste du köra Visual Studio som administratör. Annars är knappen Avinstallera inaktive rad.
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>Så här lägger du till Twilio-biblioteken i Visual Studio-projektet:
1. Öppna därefter lösningen i Visual Studio.
2. Högerklicka på **referenser**.
3. Klicka på **Hantera NuGet-paket...**
4. Klicka på **online**.
5. Skriv *Twilio* i rutan Sök online.
6. Klicka på **Installera** på Twilio-paketet.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Gör så här: gör ett utgående samtal
Följande visar hur du gör ett utgående samtal med klassen **CallResource** . Den här koden använder också en Twilio plats för att returnera TwiML-svaret (Twilio Markup Language). Ersätt värdena för **till** och **från** telefonnummer och se till att du verifierar **från** telefonnumret för ditt Twilio-konto innan du kör koden.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

Mer information om de parametrar som skickas till metoden **CallResource. Create** finns i [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] .

Som nämnts använder den här koden en Twilio plats för att returnera TwiML-svaret. Du kan i stället använda din egen webbplats för att tillhandahålla TwiML-svaret. Mer information finns i [How to: tillhandahålla TwiML-svar från din egen webbplats](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Gör så här: Skicka ett SMS-meddelande
Följande skärm bild visar hur du skickar ett SMS-meddelande med klassen **MessageResource**  . **Från** -numret tillhandahålls av Twilio för utvärderings konton för att skicka SMS-meddelanden. **Till** -numret måste kontrol leras för ditt Twilio-konto innan du kör koden.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

try
{
    // Send an SMS message.
    var message = MessageResource.Create(
        to: new PhoneNumber("+12069419717"),
        from: new PhoneNumber("+14155992671"),
        body: "This is my SMS message.");
}
catch (TwilioException ex)
{
    // An exception occurred making the REST call
    Console.WriteLine(ex.Message);
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Gör så här: Tillhandahåll TwiML svar från din egen webbplats
När ditt program initierar ett anrop till Twilio-API: t till exempel via **CallResource. Create** -metoden-Twilio skickar din begäran till en URL som förväntas returnera ett TwiML-svar. Exemplet i [How to: gör ett utgående anrop](#howto_make_call) använder den Twilio URL: en [https://twimlets.com/message][twimlet_message_url] för att returnera svaret.

> [!NOTE]
> Även om TwiML har utformats för användning av webb tjänster kan du Visa TwiML i webbläsaren. Klicka till exempel om [https://twimlets.com/message][twimlet_message_url] du vill visa ett tomt `<Response>` element. Klicka på ett annat exempel [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) om du vill se ett `<Response>` element som innehåller ett &lt; Säg- &gt; element.
>

I stället för att förlita dig på Twilio-URL: en kan du skapa en egen URL-plats som returnerar HTTP-svar. Du kan skapa webbplatsen på valfritt språk som returnerar HTTP-svar. Det här avsnittet förutsätter att du kommer att vara värd för URL: en från en generisk ASP.NET-hanterare.

Följande ASP.NET-hanterare hantverk ett TwiML-svar som säger **Hello World** på anropet.

```csharp
using System.Text;
using System.Web;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {
        public void ProcessRequest(HttpContext context)
        {
            const string twiMLResponse =
                "<Response><Say>Hello World.</Say></Response>";

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.ContentEncoding = Encoding.UTF8;
            context.Response.Write(twiMLResponse);
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```
    
Som du kan se i exemplet ovan är TwiML-svaret bara ett XML-dokument. Biblioteket Twilio. TwiML innehåller klasser som genererar TwiML åt dig. Exemplet nedan ger motsvarande svar enligt ovan, men använder klassen **VoiceResponse** .

```csharp
using System.Web;
using Twilio.TwiML;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {

        public void ProcessRequest(HttpContext context)
        {
            var twiml = new VoiceResponse();
            twiml.Say("Hello World.");

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.Write(twiml.ToString());
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

Mer information om TwiML finns i [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml) .

När du har konfigurerat ett sätt att tillhandahålla TwiML svar kan du skicka den URL: en till metoden **CallResource. Create** . Om du till exempel har ett webb program som heter MyTwiML distribuerat till en moln tjänst i Azure och namnet på ASP.NET-hanteraren är MyTwiML. ashx, kan URL: en skickas till **CallResource. Create** som visas i följande kod exempel:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Mer information om hur du använder Twilio i Azure med ASP.NET finns i [så här gör du ett telefonsamtal med Twilio i en webbroll på Azure][howto_phonecall_dotnet].

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
