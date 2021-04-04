---
title: Använda SendGrid email service (PHP) | Microsoft Docs
description: Lär dig hur du skickar e-post med e-posttjänsten SendGrid i Azure. Kod exempel skrivna i PHP.
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: b3a9fee09d1eac6fb4d716af83c348cb2c21f7a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023796"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>Så här använder du e-posttjänsten SendGrid från PHP

Den här guiden visar hur du utför vanliga programmerings åtgärder med e-posttjänsten SendGrid i Azure. Exemplen skrivs i PHP.
Scenarierna som beskrivs är att **Skapa e-post**, **skicka e-post** och **lägga till bifogade filer**. Mer information om SendGrid och hur du skickar e-post finns i avsnittet [Nästa steg](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Vad är e-posttjänsten för SendGrid?
SendGrid är en [molnbaserad e-posttjänst] som tillhandahåller tillförlitlig [e-postleverans], skalbarhet och real tids analys tillsammans med flexibla API: er som underlättar anpassad integrering. Vanliga scenarier för SendGrid-användning är:

* Skicka kvitton automatiskt till kunder
* Administrera distributions listor för att skicka kunders månatliga e-Fliers och Special erbjudanden
* Samla in real tids mått för saker som blockerad e-post och kundens svars tid
* Generera rapporter för att hjälpa till att identifiera trender
* Vidarebefordra kund förfrågningar
* E-postmeddelanden från ditt program

Mer information finns i [https://sendgrid.com][https://sendgrid.com].

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>Använda SendGrid från ditt PHP-program

Användning av SendGrid i ett Azure PHP-program kräver ingen särskild konfiguration eller kodning. Eftersom SendGrid är en tjänst kan den nås på exakt samma sätt från ett moln program som kan från ett lokalt program.

## <a name="how-to-send-an-email"></a>Gör så här: Skicka ett e-postmeddelande

Du kan skicka e-post med antingen SMTP eller webb-API: t från SendGrid.

### <a name="smtp-api"></a>SMTP-API

Om du vill skicka e-post med SendGrid SMTP API använder du *Swift Mailer*, ett komponentbaserade bibliotek för att skicka e-post från php-program. Du kan ladda ned [Swift Mailer Library](https://swiftmailer.symfony.com/) v 5.3.0 (Använd [Composer] för att installera Swift-Mailer). Om du skickar e-post med biblioteket måste du skapa instanser av `Swift\_SmtpTransport` `Swift\_Mailer` `Swift\_Message` klasserna, och, ange lämpliga egenskaper och anropa- `Swift\_Mailer::send` metoden.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
 $html = "<html>
       <head></head>
       <body>
           <p>Hi!<br>
               How are you?<br>
           </p>
       </body>
       </html>";
 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');
 // Email recipients
 $to = array(
       'john@contoso.com'=>'Destination 1 Name',
       'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
     // This will let us know how many users received this message
     echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
     echo "Something went wrong - ";
     print_r($failures);
 }
```

### <a name="web-api"></a>Webb-API
Använd PHP- [funktionen vändning][curl function] för att skicka e-post med hjälp av SendGrid-webb-API.

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $params = array(
      'api_user' => $user,
      'api_key' => $pass,
      'to' => 'john@contoso.com',
      'subject' => 'testing from curl',
      'html' => 'testing body',
      'text' => 'testing body',
      'from' => 'anna@contoso.com',
   );

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

SendGrid för webb-API: et liknar ett REST API, men det är inte ett RESTful-API, eftersom både GET-och POST-verb kan användas interstort i de flesta anrop.

## <a name="how-to-add-an-attachment"></a>Gör så här: Lägg till en bilaga

### <a name="smtp-api"></a>SMTP-API

Sändning av en bifogad fil med SMTP-API: et omfattar en extra kodrad till exempel skriptet för att skicka ett e-postmeddelande med Swift-e-post.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
  $html = "<html>
      <head></head>
      <body>
         <p>Hi!<br>
            How are you?<br>
         </p>
      </body>
      </html>";

 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');

 // Email recipients
 $to = array(
      'john@contoso.com'=>'Destination 1 Name',
      'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
      // This will let us know how many users received this message
      echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
      echo "Something went wrong - ";
      print_r($failures);
 }
```

Den extra kodraden är följande:

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

Den här kodraden anropar metoden Attach på `Swift\_Message` objektet och använder statisk metod `fromPath` i `Swift\_Attachment` klassen för att hämta och bifoga en fil i ett meddelande.

### <a name="web-api"></a>Webb-API

Att skicka en bifogad fil med webb-API: et liknar att skicka ett e-postmeddelande med webb-API: et. Observera dock att i exemplet nedan måste parameter mat ris innehålla följande element:

```php
    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
```

#### <a name="example"></a>Exempel

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $fileName = 'myfile';
 $filePath = dirname(__FILE__);

 $params = array(
     'api_user' => $user,
     'api_key' => $pass,
     'to' =>'john@contoso.com',
     'subject' => 'test of file sends',
     'html' => '<p> the HTML </p>',
     'text' => 'the plain text',
     'from' => 'anna@contoso.com',
     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
 );

 print_r($params);

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Gör så här: använda filter för att aktivera sid fötter, spårning och analys

SendGrid tillhandahåller ytterligare e-postfunktioner genom att använda *filter*. Detta är inställningar som kan läggas till i ett e-postmeddelande för att aktivera vissa funktioner som att aktivera Klicka på spårning, Google Analytics, prenumerations spårning och så vidare.

Filter kan tillämpas på ett meddelande med hjälp av filter egenskapen. Varje filter anges av en hash som innehåller filter specifika inställningar. Följande exempel aktiverar sid fots filtret och anger ett textmeddelande som ska läggas till längst ned i e-postmeddelandet. I det här exemplet ska vi använda [SendGrid-php-bibliotek].

Använd [Composer] för att installera bibliotek:

```bash
php composer.phar require sendgrid/sendgrid 2.1.1
```

### <a name="example"></a>Exempel  

```php
<?php
 /*
  * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
  */
 include "vendor/autoload.php";

 $email = new SendGrid\Email();
 // The list of addresses this message will be sent to
 // [This list is used for sending multiple emails using just ONE request to SendGrid]
 $toList = array('john@contoso.com', 'anna@contoso.com');

 // Specify the names of the recipients
 $nameList = array('Name 1', 'Name 2');

 // Used as an example of variable substitution
 $timeList = array('4 PM', '5 PM');

 // Set all of the above variables
 $email->setTos($toList);
 $email->addSubstitution('-name-', $nameList);
 $email->addSubstitution('-time-', $timeList);

 // Specify that this is an initial contact message
 $email->addCategory("initial");

 // You can optionally setup individual filters here, in this example, we have
 // enabled the footer filter
 $email->addFilter('footer', 'enable', 1);
 $email->addFilter('footer', "text/plain", "Thank you for your business");
 $email->addFilter('footer', "text/html", "Thank you for your business");

 // The subject of your email
 $subject = 'Example SendGrid Email';

 // Where is this message coming from. For example, this message can be from
 // support@yourcompany.com, info@yourcompany.com
 $from = 'someone@example.com';

 // If you do not specify a sender list above, you can specify the user here. If
 // a sender list IS specified above, this email address becomes irrelevant.
 $to = 'john@contoso.com';

 # Create the body of the message (a plain-text and an HTML version).
 # text is your plain-text email
 # html is your html version of the email
 # if the receiver is able to view html emails then only the html
 # email will be displayed

 /*
  * Note the variable substitution here =)
  */
 $text = "
 Hello -name-,
 Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
 Regards,
 Fred";

 $html = "
 <html>
 <head></head>
 <body>
 <p>Hello -name-,<br>
 Thank you for your interest in our products. We have set up an appointment
 to call you at -time- EST to discuss your needs in more detail.

 Regards,

 Fred<br>
 </p>
 </body>
 </html>";

 // set subject
 $email->setSubject($subject);

 // attach the body of the email
 $email->setFrom($from);
 $email->setHtml($html);
 $email->addTo($to);
 $email->setText($text);

 // Your SendGrid account credentials
 $username = 'sendgridusername@yourdomain.com';
 $password = 'example';

 // Create SendGrid object
 $sendgrid = new SendGrid($username, $password);

 // send message
 $response = $sendgrid->send($email);

 print_r($response);
 ```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig grunderna i SendGrid-e-posttjänsten kan du följa dessa länkar för att lära dig mer.

* SendGrid-dokumentation: <https://sendgrid.com/docs>
* SendGrid PHP-bibliotek: <https://github.com/sendgrid/sendgrid-php>
* SendGrid Special erbjudande för Azure-kunder: <https://sendgrid.com/windowsazure.html>

Mer information finns även i [php Developer Center](https://azure.microsoft.com/develop/php/).

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[molnbaserad e-posttjänst]: https://sendgrid.com/email-solutions
[transaktionell e-postleverans]: https://sendgrid.com/transactional-email
[SendGrid – php-bibliotek]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Composer]: https://getcomposer.org/download/
