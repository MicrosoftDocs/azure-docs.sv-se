---
title: Så här ringer du ett telefonsamtal från Twilio (.NET) | Microsoft Docs
description: Lär dig att ringa ett telefonsamtal med Twilio-API-tjänsten på Azure. Kod exempel som skrivits i .NET.
services: ''
documentationcenter: .net
author: mimckitt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: mimckitt
ms.custom: devx-track-dotnet
ms.openlocfilehash: 22e0a19c0e2f5b29b35ebf43a3f250959c9b636d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95521129"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Ringa ett telefonsamtal med Twilio i en webbroll på Azure
Den här guiden visar hur du använder Twilio för att ringa ett samtal från en webb sida som finns i Azure. Det resulterande programmet uppmanas användaren att ringa ett samtal med det angivna antalet och meddelandet, som visas i följande skärm bild.

![Anrops formulär för Azure med hjälp av Twilio och ASP.NET][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Förutsättningar
Du måste göra följande för att använda koden i det här avsnittet:

1. Hämta ett Twilio-konto och autentiseringstoken från [Twilio-konsolen][twilio_console]. Kom igång med Twilio genom att registrera dig på [https://www.twilio.com/try-twilio][try_twilio] . Du kan utvärdera priser på [https://www.twilio.com/pricing][twilio_pricing] . Information om det API som tillhandahålls av Twilio finns i [https://www.twilio.com/voice/api][twilio_api] .
2. Lägg till *Twilio .net-biblioteket* i din webbroll. Mer information finns i så här lägger du till **Twilio-biblioteken i ditt webb roll projekt**, senare i det här avsnittet.

Du bör vara bekant med att skapa en grundläggande [webb roll på Azure][azure_webroles_get_started].

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Gör så här: skapa ett webb formulär för att ringa ett samtal
<a id="use_nuget"></a>Så här lägger du till Twilio-biblioteken i ditt webb roll projekt:

1. Öppna därefter lösningen i Visual Studio.
2. Högerklicka på **referenser**.
3. Klicka på **Hantera NuGet-paket**.
4. Klicka på **online**.
5. Skriv *Twilio* i rutan Sök online.
6. Klicka på **Installera** på Twilio-paketet.

Följande kod visar hur du skapar ett webb formulär för att hämta användar data för att ringa ett samtal. I det här exemplet skapas en ASP.NET-webbroll med namnet **TwilioCloud** .

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Gör så här: skapa koden för att ringa
Följande kod, som anropas när användaren fyller i formuläret, skapar ett anrops meddelande och genererar anropet. I det här exemplet körs koden i händelse hanteraren onclick för knappen i formuläret. (Använd ditt Twilio-konto och autentiseringstoken i stället för plats hållarens värden som är tilldelade till `accountSID` och `authToken` i koden nedan.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

Anropet görs och Twilio-slutpunkten, API-versionen och anrops status visas. Följande skärm bild visar utdata från en exempel körning.

![Azure-samtals svar med Twilio och ASP.NET][twilio_dotnet_basic_form_output]

Mer information om TwiML finns på [https://www.twilio.com/docs/api/twiml][twiml] . Mer information om &lt; Säg &gt; och andra Twilio-verb finns på [https://www.twilio.com/docs/api/twiml/say][twilio_say] .

## <a name="next-steps"></a><a id="nextsteps"></a>Nästa steg
Den här koden angavs för att visa grundläggande funktioner med Twilio i en ASP.NET-webbroll på Azure. Innan du distribuerar till Azure i produktion kanske du vill lägga till mer fel hantering eller andra funktioner. Exempel:

* I stället för att använda ett webb formulär kan du använda Azure Blob Storage eller en Azure SQL Database instans för att lagra telefonnummer och samtals text. Information om hur du använder blobbar i Azure finns i [så här använder du Azure Blob Storage-tjänsten i .net][howto_blob_storage_dotnet]. Information om hur du använder SQL Database finns i [så här använder du Azure SQL Database i .NET-program][howto_sql_azure_dotnet].
* Du kan använda `RoleEnvironment.getConfigurationSettings` för att hämta Twilio-konto-ID och autentiseringstoken från distributionens konfigurations inställningar, i stället för att hårdkoda värdena i formuläret. Information om `RoleEnvironment` klassen finns i [namn området Microsoft. windowsazure. ServiceRuntime][azure_runtime_ref_dotnet].
* Läs rikt linjerna för Twilio-säkerhet på [https://www.twilio.com/docs/security][twilio_docs_security] .
* Läs mer om Twilio på [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a><a name="seealso"></a>Se även
* [Använda Twilio för röst-och SMS-funktioner från Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: /previous-versions/azure/reference/ee741722(v=azure.100)
[azure_webroles_get_started]: ./cloud-services/cloud-services-dotnet-get-started.md