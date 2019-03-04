---
title: Hur du använder e-posttjänsten SendGrid (.NET) | Microsoft Docs
description: Lär dig hur skicka e-postmeddelande med e-posttjänsten SendGrid på Azure. Kodexempel som skrivits i C# och använder .NET API.
services: ''
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
ms.openlocfilehash: 91d28802b4af23da5b8060fa7c8f9a7e843a7dab
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840273"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Hur du skickar e-post med SendGrid med Azure
## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga programmeringsspråk uppgifter med e-posttjänsten SendGrid på Azure. Exemplen är skrivna i C\# och har stöd för .NET Standard 1.3. Scenarier som omfattas är konstruera e-post, skicka e-post, lägga till bifogade filer, och aktiverar olika e-post och spåra inställningar. Mer information om SendGrid och skicka e-post finns i den [nästa steg] [ Next steps] avsnittet.

## <a name="what-is-the-sendgrid-email-service"></a>Vad är e-posttjänsten SendGrid?
SendGrid är en [molnbaserade e-posttjänsten] som ger tillförlitliga [transaktionsbaserad e-postleverans], skalbarhet och realtidsanalys tillsammans med flexibla API: er som gör anpassad integration enkel. Vanliga användningsområden för SendGrid är:

* Automatiskt skicka kvitton eller köp bekräftelser för kunder.
* Administrera distribution visas för att skicka kunder månatliga flygblad och kampanjer.
* Insamling av realtidsstatistisk för saker som blockerade e-post och Kundengagemang.
* Vidarebefordran av kundfrågor.
* Bearbetning av inkommande e-postmeddelanden.

För mer information, besök [ https://sendgrid.com ](https://sendgrid.com) eller Sendgrids [C#-biblioteket] [ sendgrid-csharp] GitHub-lagringsplatsen.

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referens för SendGrid .NET-klassbiblioteket
Den [SendGrid NuGet-paketet](https://www.nuget.org/packages/Sendgrid) är det enklaste sättet att få SendGrid-API och konfigurera ditt program med alla beroenden. NuGet är en Visual Studio-tillägg som ingår i Microsoft Visual Studio 2015 och senare som gör det enkelt att installera och uppdatera bibliotek och verktyg.

> [!NOTE]
> Om du vill installera NuGet om du kör en version av Visual Studio som är tidigare än Visual Studio 2015, besök [ https://www.nuget.org ](https://www.nuget.org), och klicka på den **installera NuGet** knappen.
>
>

Om du vill installera SendGrid NuGet-paketet i ditt program, gör du följande:

1. Klicka på **nytt projekt** och välj en **mallen**.

   ![Skapa ett nytt projekt][create-new-project]
2. I **Solution Explorer**, högerklicka på **referenser**, klicka sedan på **hantera NuGet-paket**.

   ![SendGrid-NuGet-paketet][SendGrid-NuGet-package]
3. Sök efter **SendGrid** och välj den **SendGrid** objekt i resultatlistan.
4. Välj den senaste stabila versionen av Nuget-paketet i listrutan version för att kunna arbeta med object model och API: er som visas i den här artikeln.

   ![SendGrid-paket][sendgrid-package]
5. Klicka på **installera** att slutföra installationen och stäng sedan dialogrutan.

SendGrid-.NET-klassbiblioteket kallas **SendGrid**. Det innehåller följande namnrymder:

* **SendGrid** för att kommunicera med SendGrid-API.
* **SendGrid.Helpers.Mail** helper-metoder att enkelt skapa SendGridMessage-objekt som anger hur du skickar e-postmeddelanden.

Lägg till följande kod namnrymdsdeklarationer överst till en C#-fil som du vill komma åt e-posttjänsten SendGrid via programmering.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Så här: skapa ett e-postmeddelande
Använd den **SendGridMessage** objektet för att skapa ett e-postmeddelande. När meddelandeobjektet har skapats kan ange du egenskaper och metoder, bland annat e-postavsändaren, e-postmottagare, ämne och brödtext.

I följande exempel visar hur du skapar ett fullständigt fylls i automatiskt e-post-objekt:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

Mer information om alla egenskaper och metoder som stöds av den **SendGrid** skriver, se [sendgrid-csharp] [ sendgrid-csharp] på GitHub.

## <a name="how-to-send-an-email"></a>Så här: skicka ett e-postmeddelande
När du har skapat ett e-postmeddelande, kan du skicka den med hjälp av SendGrid-API. Du kan också använda [. NETS inbyggda biblioteket][NET-library].

Skicka e-post måste du ange din SendGrid API-nyckel. Om du behöver information om hur du konfigurerar API-nycklar kan du besöka SendGrid-API-nycklar [dokumentation][documentation].

Du kan lagra dessa autentiseringsuppgifter via din Azure-portalen genom att klicka på inställningar för program och lägga till nyckel/värde-par under appinställningar.

 ![Azure app-inställningar][azure_app_settings]

 Sedan kan du kan komma åt dem på följande sätt:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

I följande exempel visas hur du skickar ett e-postmeddelande med SendGrid webb-API med ett konsolprogram.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Så här: skicka e-post från ASP .NET Core API med hjälp av MailHelper-klass

I exemplet nedan kan användas för att skicka ett enda e-postmeddelande till flera personer från ASP .NET Core API med hjälp av den `MailHelper` klass av `SendGrid.Helpers.Mail` namnområde. I det här exemplet använder vi ASP .NET Core 1.0. 

I det här exemplet, API-nyckeln har lagrats i den `appsettings.json` -fil som kan åsidosättas från Azure-portalen som visas i ovanstående exempel.

Innehållet i `appsettings.json` fil bör se ut som:

    {
       "Logging": {
       "IncludeScopes": false,
       "LogLevel": {
       "Default": "Debug",
       "System": "Information",
       "Microsoft": "Information"
         }
       },
     "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }

Först måste vi lägga till den nedan koden i den `Startup.cs` i .NET Core API-projektet. Detta krävs så att vi kan komma åt den `SENDGRID_API_KEY` från den `appsettings.json` filen med hjälp av beroendeinmatning i API-kontroll. Den `IConfiguration` gränssnitt kan införas i konstruktorn för kontrollanten har lagt till i den `ConfigureServices` metod nedan. Innehållet i `Startup.cs` filen ser ut som följande när du lägger till kod som krävs:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

På domänkontrollanten efter att infoga den `IConfiguration` gränssnitt, som vi kan använda den `CreateSingleEmailToMultipleRecipients` -metoden för den `MailHelper` klassen för att skicka ett enda e-postmeddelande till flera mottagare. Metoden godkänner en ytterligare boolesk parameter som heter `showAllRecipients`. Den här parametern kan användas för att kontrollera om e-postmottagare kommer att kunna se varandras e-postadress i avsnittet till i e-postrubrik. Exempelkod för domänkontrollanter måste som ha färre än 

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using SendGrid;
    using SendGrid.Helpers.Mail;
    using Microsoft.Extensions.Configuration;

    namespace SendgridMailApp.Controllers
    {
        [Route("api/[controller]")]
        public class NotificationController : Controller
        {
           private readonly IConfiguration _configuration;

           public NotificationController(IConfiguration configuration)
           {
             _configuration = configuration;
           }      
        
           [Route("SendNotification")]
           public async Task PostMessage()
           {
              var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
              var client = new SendGridClient(apiKey);
              var from = new EmailAddress("test1@example.com", "Example User 1");
              List<EmailAddress> tos = new List<EmailAddress>
              {
                  new EmailAddress("test2@example.com", "Example User 2"),
                  new EmailAddress("test3@example.com", "Example User 3"),
                  new EmailAddress("test4@example.com","Example User 4")
              };
            
              var subject = "Hello world email from Sendgrid ";
              var htmlContent = "<strong>Hello world with HTML content</strong>";
              var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
              var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, displayRecipients);
              var response = await client.SendEmailAsync(msg);
          }
       }
    }
    
## <a name="how-to-add-an-attachment"></a>Så här: Lägg till en bifogad fil
Bifogade filer som kan läggas till ett meddelande genom att anropa den **AddAttachment** metod och minimalt ange filnamnet och Base64-kodat innehåll du vill bifoga. Du kan innehålla flera bifogade filer genom att anropa den här metoden när du vill bifoga för varje fil eller med hjälp av den **AddAttachments** metod. I följande exempel visar att lägga till en bifogad fil till ett meddelande:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Så här: använda e-postinställningar för att aktivera sidfötter, spårning och analys
SendGrid ger ytterligare e-funktioner genom att använda postinställningar för e-och spårning. De här inställningarna kan läggas till e-postmeddelandet att aktivera vissa funktioner, till exempel klickspårning, Google analytics, prenumeration, spårnings- och så vidare. En fullständig lista över appar, finns det [inställningar dokumentation][settings-documentation].

Appar kan tillämpas på **SendGrid** e-postmeddelanden med hjälp av metoder som införts som en del av den **SendGridMessage** klass. I följande exempel visar sidfoten och klickar på Spåra filter:

I följande exempel visar sidfoten och klickar på Spåra filter:

### <a name="footer-settings"></a>Inställningar för sidfot
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Klickspårning
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Så här: använda ytterligare SendGrid-tjänster
SendGrid erbjuder flera API: er och webhooks som du kan använda för att utnyttja ytterligare funktioner i ditt Azure-program. Mer information finns i den [SendGrid API-referens][SendGrid API documentation].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna för den e-posttjänsten SendGrid kan du följa dessa länkar om du vill veta mer.

* SendGrid C\# biblioteket lagringsplats: [sendgrid-csharp][sendgrid-csharp]
* SendGrid API-dokumentation: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[molnbaserade e-posttjänsten]: https://sendgrid.com/solutions
[transaktionsbaserad e-postleverans]: https://sendgrid.com/use-cases/transactional-email

