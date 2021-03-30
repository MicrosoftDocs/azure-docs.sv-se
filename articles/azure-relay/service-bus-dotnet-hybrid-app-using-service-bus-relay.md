---
title: Azure Windows Communication Foundation (WCF) Relay hybrid lokalt/moln program (.NET) | Microsoft Docs
description: Lär dig att göra en lokal WCF-tjänst tillgänglig för en webbapp i molnet med Azure Relay
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 22b582634b623b39545eca225c8df0130606c2bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983889"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Göra en lokal WCF-tjänst tillgänglig för en webbapp i molnet med Azure Relay

Den här artikeln visar hur du skapar ett hybridprogram i molnet med Microsoft Azure och Visual Studio. Du skapar ett program som använder flera Azure-resurser i molnet. Den här självstudien hjälper dig att lära dig:

* Hur du skapar eller anpassar en befintlig webbtjänst för att den ska kunna användas av en webblösning.
* Använda Azure Windows Communication Foundation (WCF) Relay-tjänsten för att dela data mellan ett Azure-program och en webb tjänst som är värd för någon annan stans.

Du utför följande uppgifter i den här självstudien:

> [!div class="checklist"]
>
> * Installera krav för den här självstudien.
> * Granska scenariot.
> * Skapa ett namnområde.
> * Skapa en lokal server.
> * Skapa ett ASP .NET-program.
> * Kör appen lokalt.
> * Distribuera webbappen till Azure.
> * Kör appen på Azure.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* [Visual Studio 2015 eller senare](https://www.visualstudio.com). I exemplen i den här självstudien använder du Visual Studio 2019.
* Azure SDK för .NET. Installera det från den [nedladdningssidan för SDK](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Så här hjälper Azure Relay dig med hybridlösningar

Affärs lösningar består vanligt vis av en kombination av anpassad kod och befintliga funktioner. Anpassad kod förvarar nya och unika affärs behov. Lösningar och system som redan finns på plats tillhandahåller befintliga funktioner.

Lösningsarkitekter har börjat använda molnet för enklare hantering av skalkrav och lägre driftskostnader. Då upptäcker de att befintliga tjänst till gångar som de vill använda som bygg stenar för deras lösningar finns i företags brand väggen och av enkel räckvidd av moln lösningen. Många interna tjänster är inte byggda eller värdbaserade på ett sätt som lätt kan exponeras i företags nätverkets gräns.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) tar befintliga WCF-webbtjänster och gör tjänsterna säkert tillgängliga för lösningar som ligger utanför företags omkretsen utan att kräva påträngande ändringar i företags nätverkets infrastruktur. Sådana relätjänster finns fortfarande i deras befintliga miljö, men de delegerar lyssnandet efter inkommande sessioner och förfrågningar till relätjänsten i molnet. Azure Relay skyddar även tjänsterna mot obehörig åtkomst med hjälp av [signatur för delad åtkomst (SAS)](../service-bus-messaging/service-bus-sas.md)-autentisering.

## <a name="review-the-scenario"></a>Granska scenariot

I den här självstudiekursen kommer du att skapa en ASP.NET-webbplats som gör att du kan se en lista över produkter på sidan för inventarieförteckningar.

![Scenario][0]

I självstudiekursen förutsätter vi att du har produktinformation i ett befintligt, lokalt system och använder Azure Relay för att få åtkomst till det systemet. En webb tjänst som körs i ett enkelt konsol program simulerar den här situationen. Den innehåller en mängd olika produkter i minnet. Du kan köra det här konsol programmet på din dator och distribuera webb rollen till Azure. Genom att göra det kan du se hur den webb roll som körs i Azure Data Center anropar din dator. Det här anropet sker även om datorn nästan verkligen ligger bakom minst en brand vägg och ett Network Address Translation-skikt (NAT).

## <a name="set-up-the-development-environment"></a>Konfigurera utvecklingsmiljön

Innan du kan börja utveckla Azure-program måste du hämta de verktyg som krävs och ställa in din utvecklingsmiljö:

1. Installera Azure SDK för .NET från [hämtningssidan](https://azure.microsoft.com/downloads/) för SDK.
1. I **.net** -kolumnen väljer du den version av [Visual Studio](https://www.visualstudio.com) som du använder. Den här självstudien använder Visual Studio 2019.
1. När du uppmanas att köra eller spara installations programmet väljer du **Kör**.
1. I dialog rutan **installations program för webb plattform** väljer du **Installera** och fortsätter med installationen.

När installationen är färdig har du allt som krävs för att börja utveckla appen. SDK inkluderar verktyg som låter dig utveckla Azure-program i Visual Studio på ett enkelt sätt.

## <a name="create-a-namespace"></a>Skapa ett namnområde

Det första steget är att skapa ett namnområde och hämta en [SAS-nyckel (signatur för delad åtkomst)](../service-bus-messaging/service-bus-sas.md). Ett namnområde ger en appgräns för varje app som exponeras via Relay-tjänsten. En SAS-nyckel genereras automatiskt av systemet när ett namn område för tjänsten skapas. Kombinationen av tjänstens namnområde och SAS-nyckeln ger referensen för Azure som används för att tillåta åtkomst till ett program.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Skapa en lokal server

Först skapar du ett simulerat lokalt produktkatalogsystem.  Det här projektet är ett konsolprogram för Visual Studio som använder [Azure Service Bus NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) för att inkludera Service Bus-bibliotek och -konfigurationsinställningar. <a name="create-the-project"></a>

1. Starta Microsoft Visual Studio som administratör. Det gör du genom att högerklicka på program ikonen för Visual Studio och välja **Kör som administratör**.
1. I Visual Studio väljer du **skapa ett nytt projekt**.
1. I **skapa ett nytt projekt** väljer du **konsol program (.NET Framework)** för C# och väljer **sedan nästa**.
1. Ge projektet namnet *ProductsServer* och välj **skapa**.

   ![Konfigurera ditt nya projekt][11]

1. I **Solution Explorer** högerklickar du på projektet **ProductsServer** och väljer sedan **Hantera NuGet-paket**.
1. Välj **Bläddra**, leta upp och välj **windowsazure. Service Bus**. Välj **Installera** och godkänn användnings villkoren.

   ![Välj NuGet-paket][13]

   Alla nödvändiga klientsammansättningar nu är refererade.

1. Lägg till en ny klass för ditt produktkontrakt. I **Solution Explorer** högerklickar du på projektet **ProductsServer** och väljer **Lägg till**  >  **klass**.
1. I **namn** anger du namnet *ProductsContract. cs* och väljer **Lägg till**.

Gör följande kod ändringar i din lösning:

1. I *ProductsContract.cs* ersätter du definitionen för namnområdet med följande kod. Den definierar kontraktet för tjänsten.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. I *program. cs* ersätter du namn områdets definition med följande kod, som lägger till profil tjänsten och värden för den.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. I **Solution Explorer** dubbelklickar du på **App.config** för att öppna filen i Visual Studio-redigeraren. `<system.ServiceModel>` `<system.ServiceModel>` Lägg till följande XML-kod längst ned i elementet, men fortfarande i. Se till att ersätta `yourServiceNamespace` med namnet på ditt namn område och `yourKey` med SAS-nyckeln som du hämtade tidigare från portalen:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > Felet som orsakas av `transportClientEndpointBehavior` är bara en varning och är inte ett spärrnings problem i det här exemplet.

1. Om du fortfarande *App.config*, i `<appSettings>` elementet, ersätter du värdet för anslutnings strängen med den anslutnings sträng som du tidigare hämtade från portalen.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Välj Ctrl + Skift + B eller Välj **bygge**  >  **build-lösning** för att bygga programmet och kontrol lera att ditt arbete hittills är korrekt.

## <a name="create-an-aspnet-application"></a>Skapa ett ASP.NET-program

I det här avsnittet skapar du ett enkelt ASP.NET-program som visar data som hämtats från din produkttjänst.

### <a name="create-the-project"></a>Skapa projektet

1. Se till att Visual Studio körs som administratör.
1. I Visual Studio väljer du **skapa ett nytt projekt**.
1. I **skapa ett nytt projekt** väljer du **ASP.NET-webbprogram (.NET Framework)** för C# och väljer **Nästa**.
1. Ge projektet namnet *ProductsPortal* och välj **skapa**.
1. I **skapa ett nytt ASP.NET-webbprogram** väljer du **MVC** och väljer **ändra** under **autentisering**.

   ![Välj ASP.NET-webbprogram][16]

1. I **ändra autentisering** väljer du **Ingen autentisering** och väljer sedan **OK**. I den här självstudien distribuerar du en app som inte behöver en användare för att logga in.

    ![Ange autentisering][18]

1. Tillbaka i **skapa ett nytt ASP.NET-webbprogram**, Välj **skapa** för att skapa MVC-appen.
1. Konfigurera Azure-resurser för en ny webbapp. Följ stegen i [publicera din](../app-service/quickstart-dotnet-framework.md#launch-the-publish-wizard)webbapp. Gå sedan tillbaka till den här självstudien och fortsätt till nästa steg.
1. I **Solution Explorer** högerklickar du på **modeller** och väljer sedan **Lägg till**  >  **klass**.
1. Namnge klassen *Product. cs* och välj sedan **Lägg till**.

    ![Skapa produktmodell][17]

### <a name="modify-the-web-application"></a>Göra ändringar i webbprogrammet

1. Ersätt den befintliga namn områdets definition med följande kod i filen *Product. cs* i Visual Studio:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. I **Solution Explorer** expanderar du **kontrollanter** och dubbelklickar sedan på **HomeController. cs** för att öppna filen i Visual Studio.
1. Ersätt den befintliga definitionen för namnområdet med följande kod i *HomeController.cs*:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. I **Solution Explorer** expanderar du  >  **delade** vyer och dubbelklickar sedan på **_Layout. cshtml** för att öppna filen i Visual Studio-redigeraren.
1. Ändra alla förekomster av `My ASP.NET Application` till *Northwind Traders-produkter*.
1. Ta bort `Home` `About` länkarna, och `Contact` . Ta bort den markerade koden i följande exempel.

    ![Ta bort de genererade listobjekten][41]

1. I **Solution Explorer**, expandera **vyer**  >  **Start** och dubbelklicka sedan på **index. cshtml** för att öppna filen i Visual Studio-redigeraren. Ersätt hela innehållet i filen med följande kod:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. För att kontrol lera att ditt arbete hittills är korrekt kan du välja CTRL + SHIFT + B för att bygga projektet.

### <a name="run-the-app-locally"></a>Köra appen lokalt

Kör programmet för att kontrollera att det fungerar.

1. Se till att **ProductsPortal** är det aktiva projektet. Högerklicka på projekt namnet i **Solution Explorer** och välj **Ange som start projekt**.
1. I Visual Studio väljer du F5.

Programmet bör visas och körs i en webbläsare.

![Skärm bild som visar ett exempel på ett program som körs i en webbläsare med den URL som är markerad.][21]

## <a name="put-the-pieces-together"></a>Knyta ihop säcken

Nästa steg är att koppla samman den lokala produktservern med ASP.NET-programmet.

1. Om det inte redan är öppet öppnar du det **ProductsPortal** -projekt som du skapade i avsnittet [skapa ett ASP.NET program](#create-an-aspnet-application) i Visual Studio.
1. Precis som steget i avsnittet [skapa en lokal server](#create-an-on-premises-server) lägger du till NuGet-paketet i projekt referenserna. I **Solution Explorer** högerklickar du på projektet **ProductsPortal** och väljer sedan **Hantera NuGet-paket**.
1. Sök efter *WindowsAzure.ServiceBus* och markera posten **WindowsAzure.ServiceBus**. Slutför sedan installationen och Stäng den här dialog rutan.
1. I **Solution Explorer** högerklickar du på projektet **ProductsPortal** och väljer sedan **Lägg till**  >  **befintligt objekt**.
1. Navigera till filen *ProductsContract.cs* från konsolprojektet **ProductsServer**. Markera *ProductsContract. cs*. Välj nedpilen bredvid **Lägg till** och välj sedan **Lägg till som länk**.

   ![Lägg till som en länk][24]

1. Öppna nu filen *HomeController.cs* i Visual Studio-redigeraren och ersätt definitionen för namnområde med följande kod. Se till att ersätta `yourServiceNamespace` med namnet på namn området för tjänsten och `yourKey` med din SAS-nyckel. Med den här koden kan klienten anropa den lokala tjänsten och returnera resultatet av anropet.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. I **Solution Explorer** högerklickar du på **ProductsPortal** -lösningen. Se till att högerklicka på lösningen, inte projektet. Välj **Lägg till**  >  **befintligt projekt**.
1. Navigera till projektet **ProductsServer** och dubbelklicka sedan på lösningsfilen *ProductsServer.csproj* för att lägga till den.
1. **ProductsServer** måste köras för att visa data på **ProductsPortal**. I **Solution Explorer** högerklickar du på lösningen **ProductsPortal** och väljer **Egenskaper** för att visa **egenskaps sidor**.
1. Välj **vanliga egenskaper**  >  **Start projekt** och välj **flera start projekt**. Se till att **ProductsServer** och **ProductsPortal** visas i den ordningen och att **åtgärden** för båda är **Start**.

      ![Flera startprojekt][25]

1. Välj **vanliga egenskaper**  >  **projekt beroenden** till vänster.
1. För **projekt** väljer du **ProductsPortal**. Se till att **ProductsServer** är markerat.

    ![Projektberoenden][26]

1. För **projekt** väljer du **ProductsServer**. Se till att **ProductsPortal** inte är markerat och välj sedan **OK** för att spara ändringarna.

## <a name="run-the-project-locally"></a>Köra projektet lokalt

Om du vill testa programmet lokalt i Visual Studio väljer du F5. Den lokala servern, **ProductsServer**, bör starta först och sedan bör **ProductsPortal** -programmet starta i ett webbläsarfönster. Den här gången ser du att data för produktinventarielistorna hämtats från det lokala systemet för produkttjänster.

![Webbprogram][10]

Välj **Uppdatera** på sidan **ProductsPortal** . Varje gång du uppdaterar sidan kommer du att se att appservern visar ett meddelande när `GetProducts()` från **ProductsServer** anropas.

Stäng båda programmen innan du fortsätter till nästa avsnitt.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Distribuera ProductsPortal-projektet till en Azure-webbapp

Nästa steg är att publicera om Azure Web App **ProductsPortal** -klient delen:

1. I **Solution Explorer** högerklickar du på projektet **ProductsPortal** och väljer **publicera**. På sidan **Publicera** väljer du **Publicera**.

   > [!NOTE]
   > Du kanske får upp ett felmeddelande i webbläsarfönstret när webbprojektet **ProductsPortal** startas automatiskt efter distributionen. Detta är normalt och beror på att **ProductsServer**-programmet ännu inte körs.
   >

1. Kopiera webb adressen till den distribuerade webbappen. Du behöver URL: en senare. Du kan också hämta denna URL från fönstret **Azure App Service aktivitet** i Visual Studio:

   ![Webbadressen för den distribuerade appen][9]

1. Stäng webbläsarfönstret för att stoppa programmet som körs.

<a name="set-productsportal-as-web-app"></a>Innan du kör programmet i molnet måste du se till att **ProductsPortal** startas inifrån Visual Studio som en webbapp.

1. I Visual Studio högerklickar du på projektet **ProductsPortal** och väljer **Egenskaper**.
1. Välj **webb**. Under **Start åtgärd** väljer du **start-URL**. Ange URL: en för din tidigare distribuerade webbapp, i det här exemplet `https://productsportal20190906122808.azurewebsites.net/` .

    ![Start-URL][27]

1. Välj **Arkiv**  >  **Spara alla**.
1. Välj **build**  >  **Rebuild-lösning**.

## <a name="run-the-application"></a>Kör programmet

Välj F5 för att skapa och köra programmet. Den lokala servern, som är **ProductsServer** -konsol programmet, bör starta först och sedan bör **ProductsPortal** -programmet starta i ett webbläsarfönster, som du ser här:

   ![Köra webbappen i Azure][1]

Produkt inventeringen visar data som hämtats från det lokala produkt tjänst systemet och visar dessa data i webbappen. Kontrollera URL:en för att se till att **ProductsPortal** körs i molnet, som en Azure-webbapp.

   > [!IMPORTANT]
   > Konsolprogrammet **ProductsServer** måste köras och det måste kunna skicka data till programmet **ProductsPortal**. Om webbläsaren visar ett fel, vänta några sekunder på att **ProductsServer** ska läsas in och visa följande meddelande, och uppdatera sedan webbläsaren.
   >

Uppdatera sidan **ProductsPortal** i webbläsaren. Varje gång du uppdaterar sidan kommer du att se att appservern visar ett meddelande när `GetProducts()` från **ProductsServer** anropas.

![Uppdaterade utdata][38]

## <a name="next-steps"></a>Nästa steg

Fortsätt till följande självstudie:

> [!div class="nextstepaction"]
>[Göra en lokal WCF-tjänst tillgänglig för en WCF-klient utanför nätverket](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
