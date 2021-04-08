---
title: Så här ringer du ett telefonsamtal från Twilio (Java) | Microsoft Docs
description: Lär dig hur du ringer ett telefonsamtal från en webb sida med Twilio i ett Java-program på Azure.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: fdaa2eeef58599cbff8dbf7daa34f9a30f31b9cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548768"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Ringa ett telefonsamtal med Twilio i ett Java-program på Azure
I följande exempel visas hur du kan använda Twilio för att ringa ett samtal från en webb sida som finns i Azure. Det resulterande programmet kommer att uppmana användaren att ange Telefonsamtals värden, som du ser i följande skärm bild.

![Anrops formulär för Azure med Twilio och Java][twilio_java]

Du måste göra följande för att använda koden i det här avsnittet:

1. Hämta ett Twilio-konto och autentiseringstoken. Kom igång med Twilio genom att utvärdera priserna på [https://www.twilio.com/pricing][twilio_pricing] . Du kan registrera dig på [https://www.twilio.com/try-twilio][try_twilio] . Information om det API som tillhandahålls av Twilio finns i [https://www.twilio.com/api][twilio_api] .
2. Hämta Twilio-JAR. I [https://github.com/twilio/twilio-java][twilio_java_github] kan du ladda ned GitHub-källorna och skapa en egen jar-fil eller ladda ned en förbyggd jar-fil (med eller utan beroenden).
   Koden i det här avsnittet skrevs med hjälp av den färdiga TwilioJava-3.3.8.
3. Lägg till JAR i din Java-build-sökväg.
4. Om du använder Sol förmörkelse för att skapa Java-programmet inkluderar du Twilio JAR i program distributions filen (WAR) med hjälp av funktionen distributions sammansättning i Sol förmörkelse. Om du inte använder Sol förmörkelse för att skapa Java-programmet ser du till att Twilio JAR ingår i samma Azure-roll som Java-programmet och läggs till i programmets klass Sök väg.
5. Se till att ditt cacerts-Equifax innehåller certifikat utfärdarens säkra certifikat utfärdare med MD5 finger avtryck 67: CB: 9D: C0:13:24:8A: 82:9B: B2:17:1E: D1:1B: EG: D4 (serie numret är 35: DE: F4: CF och det SHA1-finger avtryck är D2:32:09: AD: 23: D3:14:23:21:74: E4:14.4:7F: 9D: 62:13:97:86:63:3A). Detta är certifikat utfärdarens certifikat (CA) för [https://api.twilio.com][twilio_api_service] tjänsten, som anropas när du använder Twilio-API: er.

Dessutom rekommenderar vi att du [skapar ett Hello World program med hjälp av Azure Toolkit for Eclipse][azure_java_eclipse_hello_world]eller med andra metoder för att vara värd för Java-program i Azure om du inte använder Sol förmörkelse.

## <a name="create-a-web-form-for-making-a-call"></a>Skapa ett webb formulär för att ringa ett samtal
Följande kod visar hur du skapar ett webb formulär för att hämta användar data för att ringa ett samtal. I det här exemplet skapades ett nytt dynamiskt webb projekt med namnet **TwilioCloud**, och **callform.jsp** lades till som en JSP-fil.

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Automated call form</title>
    </head>
    <body>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <br/>
        <form action="makecall.jsp" method="post">
            <table>
                <tr>
                    <td>To:</td>
                    <td><input type="text" size=50 name="callTo" value="" />
                    </td>
                </tr>
                <tr>
                    <td>From:</td>
                    <td><input type="text" size=50 name="callFrom" value="" />
                    </td>
                </tr>
                <tr>
                    <td>Call message:</td>
                    <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
                    </td>
                </tr>
                <tr>
                    <td colspan=2><input type="submit" value="Make this call" />
                    </td>
                </tr>
            </table>
        </form>
        <br/>
    </body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Skapa koden för att ringa
Följande kod, som anropas när användaren fyller i formuläret som visas av callform.jsp, skapar ett anrops meddelande och genererar anropet. I det här exemplet heter JSP-filen **makecall.jsp** och har lagts till i **TwilioCloud** -projektet. (Använd ditt Twilio-konto och autentiseringstoken i stället för plats hållarens värden som tilldelats **accountSID** och **authToken** i koden nedan.)

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
import="java.util.*"
import="com.twilio.*"
import="com.twilio.sdk.*"
import="com.twilio.sdk.resource.factory.*"
import="com.twilio.sdk.resource.instance.*"
pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
<%
try 
{
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Instantiate an instance of the Twilio client.     
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to retrieve the CallFactory.
    Account account = client.getAccount();

    // Display the client endpoint. 
    out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

    // Display the API version.
    String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
    out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

    // Retrieve the values entered by the user.
    String callTo = request.getParameter("callTo");  
    // The Outgoing Caller ID, used for the From parameter,
    // must have previously been verified with Twilio.
    String callFrom = request.getParameter("callFrom");
    String userText = request.getParameter("callText");

    // Replace spaces in the user's text with '%20', 
    // to make the text suitable for a URL.
    userText = userText.replace(" ", "%20");

    // Create a URL using the Twilio message and the user-entered text.
    String Url="https://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=" + userText;

    // Display the message URL.
    out.println("<p>");
    out.println("The URL is " + Url);
    out.println("</p>");

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", callFrom);
    params.put("To", callTo);
    params.put("Url", Url);

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);
    out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
} 
catch (TwilioRestException e) 
{
    out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
catch (Exception e) 
{
    out.println("<p>Exception encountered: " + e.getMessage() + "");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
%>
    </body>
</html>
```

Förutom att ringa upp visar makecall.jsp Twilio-slutpunkten, API-versionen och anrops statusen. Ett exempel är följande skärm bild:

![Azure-samtals svar med Twilio och Java][twilio_java_response]

## <a name="run-the-application"></a>Kör programmet
Följande är de övergripande stegen för att köra programmet. information om de här stegen finns i [skapa ett Hello World program med hjälp av Azure Toolkit for Eclipse][azure_java_eclipse_hello_world].

1. Exportera din TwilioCloud-krig till Azure **appens rot** -mappen. 
2. Ändra **Start. cmd** för att packa upp din TWILIOCLOUD-krig.
3. Kompilera ditt program för Compute-emulatorn.
4. Starta distributionen i Compute-emulatorn.
5. Öppna en webbläsare och kör `http://localhost:8080/TwilioCloud/callform.jsp` .
6. Ange värden i formuläret, klicka på **gör detta samtal** och se resultatet i makecall.jsp.

När du är redo att distribuera till Azure omkompilerar du för distribution till molnet, distribuerar till Azure och kör http://*your_hosted_name*. cloudapp.net/TwilioCloud/callform.jsp i webbläsaren (Ersätt värdet för *your_hosted_name*).

## <a name="next-steps"></a>Nästa steg
Den här koden angavs för att visa grundläggande funktioner med Twilio i Java på Azure. Innan du distribuerar till Azure i produktion kanske du vill lägga till mer fel hantering eller andra funktioner. Exempel:

* I stället för att använda ett webb formulär kan du använda Azure Storage-blobbar eller SQL Database för att lagra telefonnummer och samtals text. Information om hur du använder Azure Storage-blobar i Java finns i [så här använder du tjänsten Blob Storage från Java][howto_blob_storage_java]. 
* Du kan använda **RoleEnvironment. getConfigurationSettings** för att hämta Twilio-konto-ID och autentiseringstoken från distributionens konfigurations inställningar, i stället för att hårdkoda värdena i makecall.jsp. Information om klassen **RoleEnvironment** finns i [använda Azure Service runtime-biblioteket i JSP][azure_runtime_jsp].
* makecall.jsp-koden tilldelar en Twilio URL, [https://twimlets.com/message][twimlet_message_url] till **URL** -variabeln. Den här URL: en innehåller ett TwiML-svar (Twilio Markup Language) som informerar Twilio hur du går vidare med anropet. Den TwiML som returneras kan till exempel **&lt; &gt; innehålla verb som** resulterar i att text läses till samtals mottagaren. I stället för att använda Twilio-URL: en kan du bygga din egen tjänst för att svara på Twilio: s begäran. Mer information finns i [så här använder du Twilio för röst-och SMS-funktioner i Java][howto_twilio_voice_sms_java]. Mer information om TwiML finns i [https://www.twilio.com/docs/api/twiml][twiml] , och mer information om **&lt; Säg &gt;** och andra Twilio-verb finns på [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Läs rikt linjerna för Twilio-säkerhet på [https://www.twilio.com/docs/security][twilio_docs_security] .

Mer information om Twilio finns i [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a>Se även
* [Använda Twilio för röst-och SMS-funktioner i Java][howto_twilio_voice_sms_java]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[azure_java_eclipse_hello_world]: /java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: /previous-versions/azure/hh690948(v=azure.100)
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
