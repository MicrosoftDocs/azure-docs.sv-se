---
title: Så här använder du e-posttjänsten för SendGrid (.NET) | Microsoft Docs
description: 'Lär dig hur du skickar e-post med e-posttjänsten SendGrid i Azure. Kod exempel som skrivits i C# och använder .NET-API: et.'
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.custom: devx-track-dotnet
ms.openlocfilehash: ae816d2be592ab774500d1cfe8f2f6a7b7905b91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98196562"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Skicka e-post med hjälp av SendGrid med Azure
## <a name="overview"></a>Översikt
Den här guiden visar hur du utför vanliga programmerings åtgärder med e-posttjänsten SendGrid i Azure. Exemplen skrivs i C \# och stöder .net Standard 1,3. Scenarierna som beskrivs är att skapa e-post, skicka e-post, lägga till bilagor och aktivera olika inställningar för e-post och spårning. Mer information om SendGrid och hur du skickar e-post finns i avsnittet [Nästa steg][Next steps] .

## <a name="what-is-the-sendgrid-email-service"></a>Vad är e-posttjänsten för SendGrid?
SendGrid är en [molnbaserad e-posttjänst] som tillhandahåller tillförlitlig [e-postleverans], skalbarhet och real tids analys tillsammans med flexibla API: er som underlättar anpassad integrering. Vanliga SendGrid-användnings fall är:

* Skicka kvitton eller inköps bekräftelser automatiskt till kunder.
* Administrera distributions listor för att skicka kunders månatliga Fliers och kampanjer.
* Samla in real tids mått för saker som blockerad e-post och kund engagemang.
* Vidarebefordrar kund förfrågningar.
* Bearbetar inkommande e-post.

Mer information finns på [https://sendgrid.com](https://sendgrid.com) eller SendGrid [C# library][sendgrid-csharp] GitHub lagrings platsen.

## <a name="create-a-sendgrid-account"></a>Skapa ett SendGrid-konto
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Referera till SendGrid .NET-klass biblioteket
[SendGrid NuGet-paketet](https://www.nuget.org/packages/Sendgrid) är det enklaste sättet att hämta SendGrid-API: et och att konfigurera ditt program med alla beroenden. NuGet är ett Visual Studio-tillägg som ingår i Microsoft Visual Studio 2015 och senare och som gör det enkelt att installera och uppdatera bibliotek och verktyg.

> [!NOTE]
> Om du vill installera NuGet om du kör en tidigare version av Visual Studio än Visual Studio 2015 går du till [https://www.nuget.org](https://www.nuget.org) och klickar på knappen **Installera NuGet** .
>
>

Så här installerar du SendGrid NuGet-paketet i ditt program:

1. Klicka på **nytt projekt** och välj en **mall**.

   ![Skapa ett nytt projekt][create-new-project]
2. Högerklicka på **referenser** i **Solution Explorer** och klicka sedan på **Hantera NuGet-paket**.

   ![SendGrid NuGet-paket][SendGrid-NuGet-package]
3. Sök efter **SendGrid** och välj **SendGrid** -objektet i resultat listan.
4. Välj den senaste stabila versionen av NuGet-paketet i list rutan version för att kunna arbeta med objekt modellen och API: er som visas i den här artikeln.

   ![SendGrid-paket][sendgrid-package]
5. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialog rutan.

SendGrids .NET-klass bibliotek kallas **SendGrid**. Den innehåller följande namn rymder:

* **SendGrid** för att kommunicera med SendGrid-API: et.
* **SendGrid. helpers. mail** för hjälp metoder för att enkelt skapa SendGridMessage-objekt som anger hur e-postmeddelanden ska skickas.

Lägg till följande kod namns deklarationer högst upp i C#-filen där du vill få åtkomst till SendGrid e-posttjänsten.

```csharp
using SendGrid;
using SendGrid.Helpers.Mail;
```

## <a name="how-to-create-an-email"></a>Gör så här: skapa ett e-postmeddelande
Använd **SendGridMessage** -objektet för att skapa ett e-postmeddelande. När meddelande-objektet har skapats kan du ange egenskaper och metoder, inklusive e-postsändare, e-postmottagare och ämne och brödtext i e-postmeddelandet.

Följande exempel visar hur du skapar ett fullständigt ifyllt e-postobjekt:

```csharp
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
```

Mer information om alla egenskaper och metoder som stöds av **SendGrid** -typen finns i [SendGrid-csharp][sendgrid-csharp] på GitHub.

## <a name="how-to-send-an-email"></a>Gör så här: Skicka ett e-postmeddelande
När du har skapat ett e-postmeddelande kan du skicka det med SendGrid-API: et. Alternativt kan du använda [. NÄTVERKets inbyggda bibliotek][NET-library].

Att skicka e-post kräver att du anger din SendGrid API-nyckel. Om du behöver information om hur du konfigurerar API-nycklar kan du gå till SendGrid API Keys- [dokumentation][documentation].

Du kan lagra dessa autentiseringsuppgifter via din Azure Portal genom att klicka på program inställningar och lägga till nyckel/värde-par under appinställningar.

![Inställningar för Azure-App][azure_app_settings]

Sedan kan du komma åt dem på följande sätt:

```csharp
var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
var client = new SendGridClient(apiKey);
```

I följande exempel visas hur du skickar ett e-postmeddelande med hjälp av webb-API: t för SendGrid med ett konsol program.

```csharp
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
```

## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Så här gör du: skicka e-post från ASP .NET Core API med hjälp av mails-klassen

Exemplet nedan kan användas för att skicka ett enda e-postmeddelande till flera personer från ASP .NET Core API med `MailHelper` klassen för `SendGrid.Helpers.Mail` namn område. I det här exemplet använder vi ASP .NET core 1,0. 

I det här exemplet har API-nyckeln lagrats i `appsettings.json` filen som kan åsidosättas från Azure Portal som visas i exemplen ovan.

Innehållet i `appsettings.json` filen bör se ut ungefär så här:

```json
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
```

Först måste vi lägga till koden nedan i `Startup.cs` filen för .net Core API-projektet. Detta krävs för att vi ska kunna komma åt `SENDGRID_API_KEY` filen från `appsettings.json` filen genom att använda beroende inmatning i API-styrenheten. `IConfiguration`Gränssnittet kan matas in i konstruktorn för kontrollanten efter att det har lagts till i `ConfigureServices` metoden nedan. `Startup.cs`Filens innehåll ser ut ungefär så här när du har lagt till den kod som krävs:

```csharp
    public IConfigurationRoot Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        // Add mvc here
        services.AddMvc();
        services.AddSingleton<IConfiguration>(Configuration);
    }
```

När gränssnittet har matats in på styrenheten `IConfiguration` kan vi använda `CreateSingleEmailToMultipleRecipients` metoden i `MailHelper` klassen för att skicka ett enda e-postmeddelande till flera mottagare. Metoden accepterar en ytterligare boolesk parameter med namnet `showAllRecipients` . Den här parametern kan användas för att styra om e-postmottagare ska kunna se varje annan e-postadress i avsnittet till i e-posthuvudet. Exempel koden för Controller bör vara som följer nedan 

```csharp
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
          var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
          var response = await client.SendEmailAsync(msg);
      }
   }
}
```

## <a name="how-to-add-an-attachment"></a>Gör så här: Lägg till en bilaga
Bilagor kan läggas till i ett meddelande genom att anropa metoden **AddAttachment** och minimalt ange fil namnet och base64-kodat innehåll som du vill bifoga. Du kan inkludera flera bilagor genom att anropa den här metoden en gång för varje fil som du vill bifoga eller med hjälp av metoden **AddAttachments** . I följande exempel visas hur du lägger till en bifogad fil i ett meddelande:

```csharp
var banner2 = new Attachment()
{
    Content = Convert.ToBase64String(raw_content),
    Type = "image/png",
    Filename = "banner2.png",
    Disposition = "inline",
    ContentId = "Banner 2"
};
msg.AddAttachment(banner2);
```

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Gör så här: använda e-postinställningar för att aktivera sid fötter, spårning och analys
SendGrid tillhandahåller ytterligare e-postfunktioner genom att använda e-postinställningar och spårnings inställningar. De här inställningarna kan läggas till i ett e-postmeddelande för att aktivera vissa funktioner, till exempel att klicka på spårning, Google Analytics, prenumerations spårning och så vidare. En fullständig lista över appar finns i dokumentation om [Inställningar][settings-documentation].

Appar kan användas för **SendGrid** e-postmeddelanden med metoder som implementerats som en del av **SendGridMessage** -klassen. Följande exempel visar sidfoten och klickar på spårnings filter:

Följande exempel visar sidfoten och klickar på spårnings filter:

### <a name="footer-settings"></a>Sid fots inställningar

```csharp
    msg.SetFooterSetting(
        true,
        "Some Footer HTML",
        "<strong>Some Footer Text</strong>");
```

### <a name="click-tracking"></a>Klicka på spåra

```csharp
msg.SetClickTracking(true);
```

## <a name="how-to-use-additional-sendgrid-services"></a>Så här gör du: Använd ytterligare SendGrid-tjänster
SendGrid erbjuder flera API: er och Webhooks som du kan använda för att utnyttja ytterligare funktioner i ditt Azure-program. Mer information finns i [SENDGRID API-referensen][SendGrid API documentation].

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i SendGrid-e-posttjänsten kan du följa dessa länkar för att lära dig mer.

* SendGrid C \# -bibliotek lagrings platsen: [SendGrid-csharp][sendgrid-csharp]
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
[SendGrid API documentation]: https://sendgrid.com/docs/api-reference/
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[molnbaserad e-posttjänst]: https://sendgrid.com/solutions
[transaktionell e-postleverans]: https://sendgrid.com/use-cases/transactional-email

