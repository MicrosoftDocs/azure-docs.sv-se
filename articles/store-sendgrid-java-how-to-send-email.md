---
title: Använda SendGrid e-posttjänst (Java) | Microsoft Docs
description: Lär dig hur du skickar e-post med e-posttjänsten SendGrid i Azure. Kod exempel som skrivits i Java.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.custom: devx-track-java
ms.openlocfilehash: 9ff006b74b6202b02a2767aee4d853b1206ce60d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015459"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>Skicka e-post med SendGrid från Java
Den här guiden visar hur du utför vanliga programmerings åtgärder med e-posttjänsten SendGrid i Azure. Exemplen är skrivna i Java. Scenarierna som beskrivs är att **Skapa e-post**, **skicka e-post**, **lägga till bilagor**, **använda filter** och **Uppdatera egenskaper**. Mer information om SendGrid och hur du skickar e-post finns i avsnittet [Nästa steg](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Vad är e-posttjänsten för SendGrid?
SendGrid är en [molnbaserad e-posttjänst] som tillhandahåller tillförlitlig [e-postleverans], skalbarhet och real tids analys tillsammans med flexibla API: er som underlättar anpassad integrering. Vanliga scenarier för SendGrid-användning är:

* Skicka kvitton automatiskt till kunder
* Administrera distributions listor för att skicka kunders månatliga e-Fliers och Special erbjudanden
* Samla in real tids mått för saker som blockerad e-post och kundens svars tid
* Generera rapporter för att hjälpa till att identifiera trender
* Vidarebefordra kund förfrågningar
* E-postmeddelanden från ditt program

Mer information finns i <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Gör så här: använda javax. mail-biblioteken
Hämta javax. mail-biblioteken, till exempel från <https://www.oracle.com/technetwork/java/javamail> och importera dem till din kod. På en hög nivå är processen för att använda javax. mail-biblioteket för att skicka e-post med SMTP att göra följande:

1. Ange SMTP-värden, inklusive SMTP-servern, som för SendGrid är smtp.sendgrid.net.

    ```java
    import java.util.Properties;
    import javax.mail.*;
    import javax.mail.internet.*;

    public class MyEmailer {
        private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
        private static final String SMTP_AUTH_USER = "your_sendgrid_username";
        private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

        public static void main(String[] args) throws Exception{
            new MyEmailer().SendMail();
        }

        public void SendMail() throws Exception
        {
            Properties properties = new Properties();
                properties.put("mail.transport.protocol", "smtp");
                properties.put("mail.smtp.host", SMTP_HOST_NAME);
                properties.put("mail.smtp.port", 587);
                properties.put("mail.smtp.auth", "true");
                // …
    ```

1. Utöka *javax. mail. Authenticator* -klassen och returnera SendGrid användar namn och lösen ord i din implementering av *getPasswordAuthentication* -metoden.  

    ```java
    private class SMTPAuthenticator extends javax.mail.Authenticator {
    public PasswordAuthentication getPasswordAuthentication() {
        String username = SMTP_AUTH_USER;
        String password = SMTP_AUTH_PWD;
        return new PasswordAuthentication(username, password);
    }
    ```
2. Skapa en autentiserad e-postsession via ett *javax. mail. session-* objekt.  

    ```java
    Authenticator auth = new SMTPAuthenticator();
    Session mailSession = Session.getDefaultInstance(properties, auth);
    ```
3. Skapa ett meddelande och tilldela **till**, **från**, **ämne** och innehålls värden. Detta visas i avsnittet så här gör [du för att: skapa ett e-postmeddelande](#how-to-create-an-email) .
4. Skicka meddelandet via ett *javax. mail. transport* -objekt. Detta visas i avsnittet [så här: Skicka ett e-postmeddelande] [#how-till-Send-a-email].

## <a name="how-to-create-an-email"></a>Gör så här: skapa ett e-postmeddelande
Följande visar hur du anger värden för ett e-postmeddelande.

```java
MimeMessage message = new MimeMessage(mailSession);
Multipart multipart = new MimeMultipart("alternative");
BodyPart part1 = new MimeBodyPart();
part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
BodyPart part2 = new MimeBodyPart();
part2.setContent(
    "<p>Hello,</p>
    <p>Your Contoso order has <b>shipped</b>.</p>
    <p>Thank you,<br>John</br></p>",
    "text/html");
multipart.addBodyPart(part1);
multipart.addBodyPart(part2);
message.setFrom(new InternetAddress("john@contoso.com"));
message.addRecipient(Message.RecipientType.TO,
    new InternetAddress("someone@example.com"));
message.setSubject("Your recent order");
message.setContent(multipart);
```

## <a name="how-to-send-an-email"></a>Gör så här: Skicka ett e-postmeddelande
Följande visar hur du skickar ett e-postmeddelande.

```java
Transport transport = mailSession.getTransport();
// Connect the transport object.
transport.connect();
// Send the message.
transport.sendMessage(message, message.getAllRecipients());
// Close the connection.
transport.close();
```

## <a name="how-to-add-an-attachment"></a>Gör så här: Lägg till en bilaga
Följande kod visar hur du lägger till en bifogad fil.

```java
// Local file name and path.
String attachmentName = "myfile.zip";
String attachmentPath = "c:\\myfiles\\";
MimeBodyPart attachmentPart = new MimeBodyPart();
// Specify the local file to attach.
DataSource source = new FileDataSource(attachmentPath + attachmentName);
attachmentPart.setDataHandler(new DataHandler(source));
// This example uses the local file name as the attachment name.
// They could be different if you prefer.
attachmentPart.setFileName(attachmentName);
multipart.addBodyPart(attachmentPart);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Gör så här: använda filter för att aktivera sid fötter, spårning och analys
SendGrid tillhandahåller ytterligare e-postfunktioner genom att använda *filter*. Detta är inställningar som kan läggas till i ett e-postmeddelande för att aktivera vissa funktioner som att aktivera Klicka på spårning, Google Analytics, prenumerations spårning och så vidare. En fullständig lista över filter finns i [filter inställningar][Filter Settings].

* Följande visar hur du infogar ett sid fots filter som resulterar i att HTML-text visas längst ned i e-postmeddelandet som skickas.

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"footer\":
        {\"settings\":
        {\"enable\":1,\"text/html\":
        \"<html><b>Thank you</b> for your business.</html>\"}}}}");
    ```
* Ett annat exempel på ett filter är att klicka på spåra. Anta att e-postmeddelandet innehåller en hyperlänk, till exempel följande och att du vill spåra klicknings takten:

    ```java
    messagePart.setContent(
        "Hello,
        <p>This is the body of the message. Visit
        <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
        Thank you.",
        "text/html");
    ```
* Använd följande kod för att aktivera Klicka på spårning:

    ```java
    message.addHeader("X-SMTPAPI",
        "{\"filters\":
        {\"clicktrack\":
        {\"settings\":
        {\"enable\":1}}}}");
    ```

## <a name="how-to-update-email-properties"></a>Så här gör du: uppdatera e-postegenskaper
Vissa e-postegenskaper kan skrivas över med **set-egenskapen** eller läggas till med hjälp av **egenskapen Add**.

Om du till exempel vill ange **ReplyTo** -adresser använder du följande:

```java
InternetAddress addresses[] =
    { new InternetAddress("john@contoso.com"),
        new InternetAddress("wendy@contoso.com") };

message.setReplyTo(addresses);
```

Om du vill lägga till en **CC** -mottagare använder du följande:

```java
message.addRecipient(Message.RecipientType.CC, new
InternetAddress("john@contoso.com"));
```

## <a name="how-to-use-additional-sendgrid-services"></a>Så här gör du: Använd ytterligare SendGrid-tjänster
SendGrid erbjuder webbaserade API: er som du kan använda för att utnyttja ytterligare SendGrid-funktioner från ditt Azure-program. Fullständig information finns i [SENDGRID API-dokumentationen][SendGrid API documentation].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i SendGrid-e-posttjänsten kan du följa dessa länkar för att lära dig mer.

* Exempel som visar hur du använder SendGrid i en Azure-distribution: [så här skickar du e-post med SendGrid från java i en Azure-distribution](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API-dokumentation: <https://sendgrid.com/docs/API_Reference/index.html>
* SendGrid Special erbjudande för Azure-kunder: <https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[molnbaserad e-posttjänst]: https://sendgrid.com/email-solutions
[transaktionell e-postleverans]: https://sendgrid.com/transactional-email
