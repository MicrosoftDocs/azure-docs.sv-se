---
title: 'Självstudie: REST-självstudie med Azure Relay'
description: 'Självstudie: Bygg ett Azure Relay värd program som visar ett REST-baserat gränssnitt.'
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 0620f55650d0e4da0cd7a616649df952f3017455
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88922335"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Självstudie: självstudie om Azure WCF Relay REST

I den här självstudien beskrivs hur du skapar ett Azure Relay värd program som exponerar ett REST-baserat gränssnitt. Med hjälp av REST kan du ge en webbklient, till exempel en webbläsare, åtkomst till API:er för Service Bus via HTTP-förfrågningar.

I självstudien används den Windows Communication Foundation (WCF) REST programmerings modellen för att skapa en REST-tjänst på Azure Relay. Mer information finns i [WCF rest programmerings modell](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) och [utformning och implementering av tjänster](/dotnet/framework/wcf/designing-and-implementing-services).

Du utför följande uppgifter i den här självstudien:

> [!div class="checklist"]
>
> * Installera krav för den här självstudien.
> * Skapa ett relä namn område.
> * Definiera ett REST-baserat WCF-tjänst kontrakt.
> * Implementera REST-baserade WCF-kontrakt.
> * Värd och kör REST-baserade WCF-tjänster.
> * Kör och testa tjänsten.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* [Visual Studio 2015 eller senare](https://www.visualstudio.com). I exemplen i den här självstudien använder du Visual Studio 2019.
* Azure SDK för .NET. Installera det från den [nedladdningssidan för SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Skapa ett relä namn område

För att komma igång med reläfunktionerna i Azure måste du först skapa ett namnområde för tjänsten. Ett namnområde tillhandahåller en omfångscontainer för adressering av Azure-resurser i ditt program. Följ [anvisningarna här](relay-create-namespace-portal.md) för att skapa ett Relay-namnområde.

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>Definiera ett REST-baserat WCF-tjänst kontrakt som ska användas med Azure Relay

När du skapar en tjänst för WCF REST-Style måste du definiera kontraktet. Kontraktet anger vilka åtgärder som värden stöder. En tjänst åtgärd liknar en webb tjänst metod. Definiera ett kontrakt med ett C++-, C#-eller Visual Basic-gränssnitt. Varje metod i gränssnittet motsvarar en viss tjänsteåtgärd. Använd attributet [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) för varje gränssnitt och Använd attributet [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) för varje åtgärd. 

> [!TIP]
> Om en metod i ett gränssnitt som har [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) inte har [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), så visas inte metoden. Koden som används för dessa aktiviteter visas i exemplet enligt proceduren.

Den främsta skillnaden mellan ett WCF-kontrakt och ett REST-format-kontrakt är att lägga till en egenskap till [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Den här egenskapen gör att du kan mappa en metod i gränssnittet till en metod på andra sidan av gränssnittet. I det här exemplet används attributet [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) för att länka en metod till `HTTP GET` . Den här metoden gör det möjligt för Service Bus att korrekt hämta och tolka kommandon som skickas till gränssnittet.

### <a name="to-create-a-contract-with-an-interface"></a>Så här skapar du ett kontrakt med ett gränssnitt

1. Starta Microsoft Visual Studio som administratör. Det gör du genom att högerklicka på program ikonen för Visual Studio och välja **Kör som administratör**.
1. I Visual Studio väljer du **skapa ett nytt projekt**.
1. I **skapa ett nytt projekt** väljer du **konsol program (.NET Framework)** för C# och väljer **sedan nästa**.
1. Ge projektet namnet *ImageListener*. Använd standard **platsen** och välj sedan **skapa**.

   För ett C#-projekt skapar Visual Studio en *program. cs* -fil. Den här klassen innehåller en tom `Main()`-metod, ett krav för att ett konsolapprojekt ska kunna skapas på rätt sätt.

1. I **Solution Explorer** högerklickar du på projektet **ImageListener** och väljer sedan **Hantera NuGet-paket**.
1. Välj **Bläddra**, leta upp och välj **windowsazure. Service Bus**. Välj **Installera** och godkänn användnings villkoren.

    Det här steget lägger till referenser till Service Bus och *System.ServiceModel.dll*. Det här paketet lägger automatiskt till referenser till Service Bus bibliotek och WCF `System.ServiceModel` .

1. Lägg uttryckligen till en referens till `System.ServiceModel.Web.dll` i projektet. I **Solution Explorer** högerklickar du på **referenser** under projektmappen och väljer **Lägg till referens**.
1. I **Lägg till referens** väljer du **ramverk** och anger *system. ServiceModel. Web* i **search**. Markera kryssrutan **System.ServiceModel.Web** och klicka sedan på **OK**.

Gör sedan följande kod ändringar till projektet:

1. Lägg till följande- `using` instruktioner överst i filen *program. cs* .

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) är det namnområde som genom programmering ger åtkomst till grundläggande funktioner i WCF. WCF Relay använder många av objekten och attributen för WCF för att definiera tjänste kontrakt. Du använder det här namn området i de flesta relä program.
    * Med [system. ServiceModel. Channels](/dotnet/api/system.servicemodel.channels) kan du definiera kanalen, som är det objekt som du kommunicerar med Azure Relay och klientens webbläsare.
    * [System. ServiceModel. Web](/dotnet/api/system.servicemodel.web) innehåller de typer som du kan använda för att skapa webbaserade program.

1. Byt namn på `ImageListener` namn området till `Microsoft.ServiceBus.Samples` .

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Direkt efter den inledande klammern i namn områdes deklarationen definierar du ett nytt gränssnitt med namnet `IImageContract` och tillämpar `ServiceContractAttribute` attributet på gränssnittet med värdet `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1` . 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    Namnområdesvärdet skiljer sig från det namnområde som du använder under hela intervallet för din kod. Namn områdets värde är en unik identifierare för det här kontraktet och måste ha versions information. Mer information finns i [Versionhantering för tjänster](/dotnet/framework/wcf/service-versioning). Genom att ange namnområdet uttryckligen förhindrar du att det förvalda namnområdesvärdet läggs till i kontraktnamnet.

1. I `IImageContract` gränssnittet deklarerar du en metod för den enskild åtgärd som `IImageContract` kontraktet visar i gränssnittet och tillämpar `OperationContract` attributet på den metod som du vill exponera som en del av det offentliga Service Bus kontraktet.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. I `OperationContract` attributet lägger du till `WebGet` värdet.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Genom att lägga till `WebGet` värdet kan relä tjänsten dirigera http get-begäranden till `GetImage` och översätta retur värden i `GetImage` ett `HTTP GETRESPONSE` svar. Senare i självstudien använder du en webbläsare för att få åtkomst till den här metoden och för att visa bilden i webbläsaren.

1. Direkt efter definitionen `IImageContract` deklarerar du en kanal som ärver från både `IImageContract`- och `IClientChannel`-gränssnittet.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   En kanal är det WCF-objekt via vilken tjänsten och klienten skickar information till varandra. Senare skapar du kanalen i värd programmet. Azure Relay använder sedan den här kanalen för att skicka HTTP GET-begäranden från webbläsaren till din `GetImage` implementering. Reläet använder också kanalen för att ta `GetImage` returvärdet och översätta den till en `HTTP GETRESPONSE` för klientens webbläsare.

1. Välj **bygge**  >  **build-lösning** för att bekräfta att ditt arbete hittills är korrekt.

### <a name="example-that-defines-a-wcf-relay-contract"></a>Exempel som definierar ett WCF Relay kontrakt

Följande kod visar ett grundläggande gränssnitt som definierar ett WCF Relay kontrakt.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>Implementera REST-baserade WCF Service-kontrakt

Om du vill skapa en REST-typ WCF Relay tjänst skapar du först kontraktet med ett gränssnitt. Nästa steg är att implementera gränssnittet. Den här proceduren förutsätter att du skapar en klass `ImageService` som heter och som implementerar det användardefinierade `IImageContract` gränssnittet. När du har implementerat kontraktet konfigurerar du gränssnittet med hjälp av en *App.config* -fil. Konfigurations filen innehåller nödvändig information för programmet. Den här informationen innehåller namnet på tjänsten, namnet på kontraktet och typen av protokoll som används för att kommunicera med relä tjänsten. Koden som används för dessa aktiviteter visas i exemplet enligt proceduren.

Precis som i föregående steg är det mycket skillnad mellan att implementera ett REST-format-kontrakt och ett WCF Relay kontrakt.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Implementera ett Service Bus-kontrakt i REST-format

1. Skapa en ny klass med namnet `ImageService` direkt efter definitionen av gränssnittet `IImageContract`. Klassen `ImageService` implementerar gränssnittet `IImageContract`.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Precis som med andra gränssnittsimplementeringar kan du implementera definitionen i en annan fil. Men i den här självstudiekursen visas implementeringen i samma fil som gränssnittsdefinitionen och `Main()`-metoden.

1. Använd attributet [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) för `IImageService` klassen för att ange att klassen är en implementering av ett WCF-kontrakt.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Som tidigare nämnts är det här namn området inte ett traditionellt namn område. Den ingår i WCF-arkitekturen som identifierar kontraktet. Mer information finns i [data kontrakts namn](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Lägg till en *. jpg* -bild i projektet. Den här filen är en bild som tjänsten visar i den mottagande webbläsaren.

   1. Högerklicka på projektet och välj **Lägg till**.
   1. Välj sedan **befintligt objekt**.
   1. Använd **Lägg till befintligt objekt** för att bläddra till en lämplig. jpg och välj sedan **Lägg till**. När du lägger till filen väljer du **alla filer** i list rutan bredvid **fil namn**.

   Resten av den här självstudien förutsätter att namnet på avbildningen är *image.jpg*. Om du har en annan fil måste du byta namn på avbildningen eller ändra din kod för att kompensera.

1. För att se till att den aktiva tjänsten kan hitta avbildnings filen i **Solution Explorer** högerklickar du på avbildnings filen och väljer sedan **Egenskaper**. I **Egenskaper**, anger du **Kopiera till utdatakatalogen** för att **Kopiera om nyare**.

1. Använd proceduren i [om du vill skapa ett kontrakt med ett gränssnitt](#to-create-a-contract-with-an-interface) för att lägga till en referens till *System.Drawing.dll* Assembly i projektet.

1. Lägg till följande associerade `using` instruktioner:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. I `ImageService` -klassen lägger du till följande konstruktor som läser in bitmappen och förbereder den för att skicka den till klientens webbläsare:

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. Direkt efter föregående kod lägger du till följande `GetImage` metod i `ImageService` -klassen för att returnera ett http-meddelande som innehåller avbildningen.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Den här implementeringen använder `MemoryStream` för att hämta avbildningen och förbereda den för strömning till webbläsaren. Den startar ström positionen vid noll, deklarerar Stream-innehållet som en *. jpg* och strömmar informationen.

1. Välj **bygge**  >  **build-lösning**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Definiera konfigurationen för att köra webbtjänsten på Service Bus

1. I **Solution Explorer** dubbelklickar du på **App.config** för att öppna filen i Visual Studio-redigeraren.

    *App.config* -filen innehåller tjänst namnet, slut punkten och bindningen. Slut punkten är platsen Azure Relay visar för klienter och värdar för att kommunicera med varandra. Bindningen är den typ av protokoll som används för att kommunicera. Den största skillnaden här är att den konfigurerade tjänst slut punkten refererar till en [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) -bindning.

1. XML-elementet `<system.serviceModel>` är ett WCF-element som definierar en eller flera tjänster. Här används den för att definiera namn och slut punkt för tjänsten. Längst ned i `<system.serviceModel>` elementet, men fortfarande i `<system.serviceModel>` , lägger du till ett `<bindings>` element som har följande innehåll:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Det här innehållet definierar de bindningar som används i programmet. Du kan definiera flera bindningar, men i den här självstudien har du bara definierat en.

    Föregående kod definierar en WCF Relay [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) -bindning med `relayClientAuthenticationType` värdet `None` . Den här inställningen anger att en slut punkt som använder denna bindning inte kräver klientens autentiseringsuppgifter.

1. Lägg till ett `<services>`-element efter elementet `<bindings>`. På ett liknande sätt som med bindningarna kan du definiera flera tjänster i en enda konfigurationsfil. I den här självstudiekursen kommer du dock bara att definiera en.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Det här innehållet konfigurerar en tjänst som använder den tidigare definierade standarden `webHttpRelayBinding` . Den använder också standard `sbTokenProvider` , som definieras i nästa steg.

1. Efter `<services>` elementet skapar du ett `<behaviors>` -element med följande innehåll och ersätter `SAS_KEY` det med nyckeln signatur för delad åtkomst (SAS). Information om hur du hämtar en SAS-nyckel från [Azure Portal][Azure portal]finns i [Hämta autentiseringsuppgifter för hantering](service-bus-relay-tutorial.md#get-management-credentials).

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. Om du fortfarande *App.config*, i- `<appSettings>` elementet, ersätter du hela värdet för anslutnings strängen med den anslutnings sträng som du tidigare hämtade från portalen.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. Välj **build**  >  **build-lösning** för att bygga hela lösningen.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Exempel som implementerar REST-baserade WCF Service-kontrakt

Följande kod visar kontrakt-och tjänst implementering för en REST-baserad tjänst som körs på Service Bus med hjälp av `WebHttpRelayBinding` bindningen.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

I följande exempel visas den *App.config* -fil som är associerad med tjänsten.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>Värd för den REST-baserade WCF-tjänsten att använda Azure Relay

I det här avsnittet beskrivs hur du kör en webb tjänst med hjälp av ett konsol program med WCF Relay. En fullständig lista över koden som skrivs i det här avsnittet visas i exemplet enligt proceduren.

### <a name="to-create-a-base-address-for-the-service"></a>Så här skapar du en basadress för tjänsten

1. I `Main()` funktions deklarationen skapar du en variabel för att lagra projektets namnrymd. Ersätt `yourNamespace` med namnet på det relä namn område som du skapade tidigare.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    Service Bus använder namnet på ditt namnområde för att skapa ett unikt URI.

1. Skapa en `Uri`-instans för tjänstens basadress som baseras på namnområdet.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Så här skapar och konfigurerar du webbtjänstevärden

I `Main()` skapar du webb tjänst värden med hjälp av den URI-adress som skapades tidigare i det här avsnittet.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

Tjänstevärden är det WCF-objekt som instantierar värdprogrammet. I det här exemplet överförs den typ av värd som du vill skapa, som är en `ImageService` och även den adress där du vill exponera värd programmet.

### <a name="to-run-the-web-service-host"></a>Så här kör du webbtjänstevärden

1. I `Main()` lägger du till följande rad för att öppna tjänsten.

    ```csharp
    host.Open();
    ```

    Tjänsten körs nu.

1. Visa ett meddelande som anger att tjänsten körs och hur du stoppar tjänsten.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Stäng tjänstevärden när du är klar.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>Exempel på tjänst kontrakt och implementering

Följande exempel innehåller tjänstekontraktet och implementeringen från föregående steg i självstudiekursen och fungerar som värd för tjänsten i ett konsolprogram. Kompilera följande kod till en körbar fil med namnet *ImageListener.exe*.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>Kör och testa tjänsten

Gör följande för att köra appen när du har skapat lösningen:

1. Välj F5 eller bläddra till platsen för den körbara filen *ImageListener\bin\Debug\ImageListener.exe* för att köra tjänsten. Låt appen köras, eftersom det krävs för nästa steg.
1. Kopiera och klistra in adressen från kommandotolken i en webbläsare för att se bilden.
1. När du är klar väljer du retur i kommando tolks fönstret för att stänga appen.

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat ett program som använder tjänsten Azure Relay kan du läsa mer i följande artiklar:

* [Vad är Azure Relay?](relay-what-is-it.md)
* [Exponera en lokal WCF REST-tjänst till extern klient med hjälp av Azure WCF Relay](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
