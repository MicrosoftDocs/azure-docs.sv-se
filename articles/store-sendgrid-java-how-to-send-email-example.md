---
title: Store-SendGrid-Java-How-to-send-e-mail-example
description: Skicka e-post med SendGrid från java i en Azure-distribution. Det resulterande programmet kommer att uppmana användaren att ange e-postvärden.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.custom: devx-track-java
ms.openlocfilehash: 7a857b887b53ccb2288507742ca52d72d90ff548
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214362"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>Skicka e-post med SendGrid från java i en Azure-distribution
I följande exempel visas hur du kan använda SendGrid för att skicka e-postmeddelanden från en webb sida som finns i Azure. Det resulterande programmet kommer att uppmana användaren att ange e-postvärden, som visas i följande skärm bild.

![E-postformulär][emailform]

Det resulterande e-postmeddelandet ser ut ungefär som på följande skärm bild.

![E-postmeddelande][emailsent]

Du måste göra följande för att använda koden i det här avsnittet:

1. Hämta javax. e-jar v7, till exempel från <https://www.oracle.com/technetwork/java/javamail/index.html> .
2. Lägg till jar v7 i din Java-build-sökväg.
3. Om du använder Sol förmörkelse för att skapa Java-programmet kan du inkludera SendGrid-biblioteken i program distributions filen (WAR) med hjälp av funktionen för distributions sammansättning i Sol förmörkelse. Om du inte använder Sol förmörkelse för att skapa Java-programmet, se till att biblioteken ingår i samma Azure-roll som Java-programmet och läggs till i programmets klass Sök väg.

Du måste också ha ditt eget SendGrid-användarnamn och-lösen ord för att kunna skicka e-postmeddelandet. Information om hur du kommer igång med SendGrid finns i [så här skickar du e-post med SendGrid från Java](store-sendgrid-java-how-to-send-email.md).

Dessutom rekommenderar vi att du [skapar ett Hello World-program för Azure i Sol förmörkelse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)eller med andra tekniker som är värdar för Java-program i Azure om du inte använder Sol förmörkelse.

## <a name="create-a-web-form-for-sending-email"></a>Skapa ett webb formulär för att skicka e-post
Följande kod visar hur du skapar ett webb formulär för att hämta användar data för att skicka e-post. I syfte med det här innehållet heter JSP-filen **emailform.jsp**.

```html
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Email form</title>
</head>
<body>
  <p>Fill in all fields and click <b>Send this email</b>.</p>
  <br/>
  <form action="sendemail.jsp" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input type="text" size=50 name="emailTo">
        </td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input type="text" size=50 name="emailFrom">
        </td>
      </tr>
      <tr>
        <td>Subject:</td>
        <td><input type="text" size=100 name="emailSubject" value="My email subject">
        </td>
      </tr>
      <tr>
        <td>Text:</td>
        <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
        </td>
      </tr>
      <tr>
        <td>SendGrid user name:</td>
        <td><input type="text" name="sendGridUser">
        </td>
      </tr>
      <tr>
        <td>SendGrid password:</td>
        <td><input type="password" name="sendGridPassword">
        </td>
      </tr>
      <tr>
        <td colspan=2><input type="submit" value="Send this email">
        </td>
      </tr>
    </table>
  </form>
  <br/>
</body>
</html>
```

## <a name="create-the-code-to-send-the-email"></a>Skapa koden för att skicka e-postmeddelandet
Följande kod, som anropas när du fyller i formuläret i emailform.jsp, skapar e-postmeddelandet och skickar det. I syfte med det här innehållet heter JSP-filen **sendemail.jsp**.

```java
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Email processing happens here</title>
</head>
<body>
    <b>This is my send mail page.</b><p/>
  <%

  final String sendGridUser = request.getParameter("sendGridUser");
  final String sendGridPassword = request.getParameter("sendGridPassword");

  class SMTPAuthenticator extends Authenticator
  {
    public PasswordAuthentication getPasswordAuthentication()
    {
        String username = sendGridUser;
        String password = sendGridPassword;

        return new PasswordAuthentication(username, password);   
    }
  }
  try
  {

      // The SendGrid SMTP server.
      String SMTP_HOST_NAME = "smtp.sendgrid.net";

      Properties properties;

      properties = new Properties();

      // Specify SMTP values.
      properties.put("mail.transport.protocol", "smtp");
      properties.put("mail.smtp.host", SMTP_HOST_NAME);
      properties.put("mail.smtp.port", 587);
      properties.put("mail.smtp.auth", "true");

      // Display the email fields entered by the user. 
      out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
      out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
      out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
      out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

      // Create the authenticator object.
      Authenticator authenticator = new SMTPAuthenticator();

      // Create the mail session object.
      Session mailSession;
      mailSession = Session.getDefaultInstance(properties, authenticator);

      // Display debug information to stdout, useful when using the
      // compute emulator during development.
      mailSession.setDebug(true);

      // Create the message and message part objects.
      MimeMessage message;
      Multipart multipart;
      MimeBodyPart messagePart; 

      message = new MimeMessage(mailSession);

      multipart = new MimeMultipart("alternative");
      messagePart = new MimeBodyPart();
      messagePart.setContent(request.getParameter("emailText"), "text/html");
      multipart.addBodyPart(messagePart);            

      // Specify the email To, From, Subject and Content. 
      message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
      message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
      message.setSubject(request.getParameter("emailSubject")); 
      message.setContent(multipart);

      // Uncomment the following if you want to add a footer.
      // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

      // Uncomment the following if you want to enable click tracking.
      // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

      Transport transport;
      transport = mailSession.getTransport();
      // Connect the transport object.
      transport.connect();
      // Send the message.
      transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
      // Close the connection.
      transport.close();

    out.println("<p>Email processing completed.</p>");

  }
  catch (Exception e)
  {
      out.println("<p>Exception encountered: " + 
                        e.getMessage()     +
                        "</p>");   
  }
%>

</body>
</html>
```

Förutom att skicka e-postmeddelandet ger emailform.jsp ett resultat för användaren. ett exempel är följande skärm bild:

![Skicka e-post resultat][emailresult]

## <a name="next-steps"></a>Nästa steg
Distribuera ditt program till Compute-emulatorn och i en webbläsare kör emailform.jsp, ange värden i formuläret, klicka på **skicka det här e-postmeddelandet** och se resultatet i sendemail.jsp.

Den här koden angavs för att visa hur du använder SendGrid i Java på Azure. Innan du distribuerar till Azure i produktion kanske du vill lägga till mer fel hantering eller andra funktioner. Exempel: 

* Du kan använda Azure Storage-blobbar eller SQL Database för att lagra e-postadresser och e-postmeddelanden, i stället för att använda ett webb formulär. Information om hur du använder Azure Storage-blobar i Java finns i [så här använder du tjänsten Blob Storage från Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Information om hur du använder SQL Database i Java finns i [använda SQL Database i Java](./azure-sql/database/connect-query-java.md).
* Mer information om hur du använder SendGrid i Java finns i [så här skickar du e-post med SendGrid från Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg