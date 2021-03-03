---
title: Exponera en lokal WCF REST-tjänst för klienter som använder Azure Relay
description: I den här självstudien beskrivs hur du exponerar en lokal WCF REST-tjänst för en extern klient med hjälp av Azure WCF Relay.
ms.topic: tutorial
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 7669bc07ad91933cd31bd2ccd10eaf830d98de7c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710795"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Självstudie: exponera en lokal WCF REST-tjänst till extern klient med hjälp av Azure WCF Relay

I den här självstudien beskrivs hur du skapar ett WCF Relay klient program och-tjänster med hjälp av Azure Relay. En liknande självstudie som använder [Service Bus meddelanden](../service-bus-messaging/service-bus-messaging-overview.md)finns i [kom igång med Service Bus köer](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Genom att arbeta med den här självstudien får du en förståelse för hur du skapar en WCF Relay-klient och ett tjänst program. Precis som deras ursprungliga WCF-motsvarigheter är en tjänst en konstruktion som exponerar en eller flera slut punkter. Varje slut punkt visar en eller flera tjänst åtgärder. Slutpunkten för en tjänst anger en adress där tjänsten ligger, en bindning som innehåller den information som en klient måste använda för att kommunicera med tjänsten och ett kontrakt som definierar de funktioner som tjänsten levererar till sina klienter. Den största skillnaden mellan WCF och WCF Relay är att slut punkten exponeras i molnet istället för lokalt på datorn.

När du arbetar genom ordningen på avsnitten i den här självstudien har du en igång-tjänst. Du har också en-klient som kan anropa driften av tjänsten. 

Du utför följande uppgifter i den här självstudien:

> [!div class="checklist"]
>
> * Installera krav för den här självstudien.
> * Skapa ett relä namn område.
> * Skapa ett WCF-tjänst kontrakt.
> * Implementera WCF-kontraktet.
> * Värd och kör WCF-tjänsten för att registrera dig hos relä tjänsten.
> * Skapa en WCF-klient för tjänste kontraktet.
> * Konfigurera WCF-klienten.
> * Implementera WCF-klienten.
> * Kör programmen.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

* En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* [Visual Studio 2015 eller senare](https://www.visualstudio.com). I exemplen i den här självstudien använder du Visual Studio 2019.
* Azure SDK för .NET. Installera det från den [nedladdningssidan för SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Skapa ett relä namn område

Det första steget är att skapa ett namnområde och hämta en [SAS-nyckel (signatur för delad åtkomst)](../service-bus-messaging/service-bus-sas.md). Ett namnområde ger en appgräns för varje app som exponeras via Relay-tjänsten. En SAS-nyckel genereras automatiskt av systemet när ett namn område för tjänsten skapas. Kombinationen av tjänstens namnområde och SAS-nyckeln ger referensen för Azure som används för att tillåta åtkomst till ett program.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definiera ett kontrakt för WCF-tjänsten

Tjänste kontraktet anger vilka åtgärder tjänsten stöder. Åtgärder är webb tjänst metoder eller-funktioner. Kontrakt skapas genom att definiera ett gränssnitt för C++, C# eller Visual Basic. Varje metod i gränssnittet motsvarar en viss tjänsteåtgärd. Attributet [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) måste tillämpas på varje gränssnitt och attributet [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) måste tillämpas på varje åtgärd. Om en metod i ett gränssnitt som har attributet [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) inte har attributet [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) , visas inte metoden. Koden för dessa arbetsuppgifter visas in exemplet som följer efter proceduren. En större diskussion om kontrakt och tjänster finns i [utforma och implementera tjänster](/dotnet/framework/wcf/designing-and-implementing-services).

### <a name="create-a-relay-contract-with-an-interface"></a>Skapa ett relä kontrakt med ett gränssnitt

1. Starta Microsoft Visual Studio som administratör. Det gör du genom att högerklicka på program ikonen för Visual Studio och välja **Kör som administratör**.
1. I Visual Studio väljer du **skapa ett nytt projekt**.
1. I **skapa ett nytt projekt** väljer du **konsol program (.NET Framework)** för C# och väljer **sedan nästa**.
1. Ge projektet namnet *EchoService* och välj **skapa**.

   ![Skapa en konsolapp][2]

1. I **Solution Explorer** högerklickar du på projektet och väljer **Hantera NuGet-paket**. I **NuGet Package Manager** väljer du **Bläddra** och söker efter och väljer **windowsazure. Service Bus**. Välj **Installera** och godkänn användnings villkoren.

    ![Service Bus-paket][3]

   Det här paketet lägger automatiskt till referenser till Service Bus bibliotek och WCF `System.ServiceModel` . [System.ServiceModel](/dotnet/api/system.servicemodel) är det namnområde som ger dig programmatisk åtkomst till de grundläggande funktionerna i WCF. Service Bus använder många av WFC:s objekt och attribut för att definiera tjänstekontrakt.

1. Lägg till följande- `using` instruktioner överst i *program.cs*:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Ändra namnet på namnområdet från standardnamnet `EchoService` till `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > I den här självstudien används C#-namn området `Microsoft.ServiceBus.Samples` som är namn området för den kontraktbaserade hanterade typen som används i konfigurations filen i avsnittet [Konfigurera WCF-klienten](#configure-the-wcf-client) . Du kan ange ett namn område som du vill ha när du skapar det här exemplet. Själv studie kursen kommer dock inte att fungera om du inte ändrar namn områdena för kontraktet och tjänsten i enlighet med detta i program konfigurations filen. Det namn område som anges i *App.config* -filen måste vara samma som det namn område som anges i C#-filerna.
   >

1. Direkt efter `Microsoft.ServiceBus.Samples` namn områdes deklarationen, men inom namn området, definierar du ett nytt gränssnitt med namnet `IEchoContract` och tillämpar `ServiceContractAttribute` attributet på gränssnittet med ett namn områdes värde på `https://samples.microsoft.com/ServiceModel/Relay/` . Klistra in följande kod efter namn områdes deklarationen:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    Namnområdesvärdet skiljer sig från det namnområde som du använder under hela intervallet för din kod. Istället används namnområdesvärdet som en unik identifierare för det här kontraktet. Genom att ange namnområdet uttryckligen förhindrar du att det förvalda namnområdesvärdet läggs till i kontraktnamnet.

   > [!NOTE]
   > Namnområdet för tjänstekontraktet innehåller vanligtvis ett namngivningsschema som inkluderar information om versionen. Om du tar med versionsinformation i namnområdet för tjänstekontraktet kan tjänsterna isolera större ändringar genom att definiera ett nytt tjänstkontrakt med ett nytt namnområde och sedan exponera det på en ny slutpunkt. På så sätt kan klienterna fortsätta att använda det gamla service kontraktet utan att behöva uppdateras. Versionsinformation kan bestå av ett datum eller ett build-nummer. Mer information finns i [Versionhantering för tjänster](/dotnet/framework/wcf/service-versioning). I den här självstudien innehåller namngivnings planen för namn området för tjänst kontraktet inte versions information.
   >

1. I `IEchoContract` gränssnittet deklarerar du en metod för den enskild åtgärd som `IEchoContract` kontraktet visar i gränssnittet och tillämpar `OperationContractAttribute` attributet på den metod som du vill ska visas som en del av det offentliga WCF Relay kontraktet enligt följande:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Direkt efter gränssnittsdefinitionen `IEchoContract` deklarerar du en kanal som ärver egenskaper från båda `IEchoContract` och även från gränssnittet `IClientChannel`, som visas här:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    En kanal är det WCF-objekt via vilken värden och klienten skickar information till varandra. Senare ska du skriva kod mot kanalen för att få eko på information mellan de två programmen.

1. Välj **bygge**  >  **build-lösning** eller Välj CTRL + SHIFT + B för att bekräfta att ditt arbete hittills är korrekt.

### <a name="example-of-a-wcf-contract"></a>Exempel på ett WCF-kontrakt

Följande kod visar ett grundläggande gränssnitt som definierar ett WCF Relay kontrakt.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Nu när gränssnittet har skapats kan du implementera det.

## <a name="implement-the-wcf-contract"></a>Implementera WCF-kontraktet

Om du skapar ett Azure-relä måste du först skapa kontraktet med ett gränssnitt. Mer information om hur du skapar gränssnittet finns i föregående avsnitt. Nästa procedur implementerar gränssnittet. Den här uppgiften innebär att skapa en klass med namnet `EchoService` som implementerar det användardefinierade `IEchoContract` gränssnittet. När du har implementerat gränssnittet konfigurerar du gränssnittet med hjälp av en *App.config* konfigurations fil. Konfigurations filen innehåller nödvändig information för programmet. Den här informationen innehåller namnet på tjänsten, namnet på kontraktet och typen av protokoll som används för att kommunicera med relä tjänsten. Den kod som används för dessa uppgifter finns i exemplet som följer efter proceduren. En mer allmän diskussion om hur du implementerar ett tjänst kontrakt finns i [implementera Service kontrakt](/dotnet/framework/wcf/implementing-service-contracts).

1. Skapa en ny klass med namnet `EchoService` direkt efter definitionen av gränssnittet `IEchoContract`. Klassen `EchoService` implementerar gränssnittet `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Precis som med andra gränssnittsimplementeringar kan du implementera definitionen i en annan fil. Men i den här självstudiekursen ligger implementeringen i samma fil som gränssnittsdefinitionen och `Main()`-metoden.

1. Tillämpa attributet [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) på gränssnittet `IEchoContract`. Attributet anger namnet på tjänsten och på namnområdet. När du har gjort det, visas klassen `EchoService` på följande sätt:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementera metoden `Echo` som definierats i gränssnittet `IEchoContract` i klassen `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Välj **bygge**  >  **build-lösning** eller Välj CTRL + SHIFT + B.

### <a name="define-the-configuration-for-the-service-host"></a>Definiera konfigurationen för tjänst värden

Konfigurations filen liknar en WCF-konfigurationsfil. Den innehåller tjänst namnet, slut punkten och bindningen. Slut punkten är platsen Azure Relay visar för klienter och värdar för att kommunicera med varandra. Bindningen är den typ av protokoll som används för att kommunicera. Den största skillnaden är att den här konfigurerade tjänst slut punkten refererar till en [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) -bindning, som inte är en del av .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) är en av de bindningar som definierats av tjänsten.

1. I **Solution Explorer** dubbelklickar du på **App.config** för att öppna filen i Visual Studio-redigeraren.
1. I elementet `<appSettings>` ersätter du platshållarna med namnet på ditt namnområde för tjänsten och den SAS-nyckel som du kopierade i ett av de föregående stegen.
1. Lägg till ett `<services>`-element inom taggarna `<system.serviceModel>`. Du kan definiera flera Relay-program i en enda konfigurations fil. I den här självstudiekursen definieras dock bara en.

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Inne i elementet `<services>` element lägger du till ett `<service>`-element för att definiera namnet på tjänsten.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Definiera platsen för slutpunktskontraktet inne i elementet `<service>` och även typen av bindning för slutpunkten.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Slutpunkten definierar var klienten ska söka efter värdprogrammet. Senare använder självstudien det här steget för att skapa en URI som helt exponerar värden via Azure Relay. Bindningen deklarerar att vi använder TCP som protokoll för att kommunicera med relä tjänsten.

1. Välj **bygge**  >  **build-lösning** eller Välj CTRL + SHIFT + B för att bekräfta att ditt arbete hittills är korrekt.

### <a name="example-of-implementation-of-a-service-contract"></a>Exempel på implementering av ett tjänst kontrakt

Följande kod visar implementeringen av tjänstekontraktet.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Följande kod visar det grundläggande formatet för den *App.config* -fil som är associerad med tjänst värden.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Värd och kör WCF-tjänsten för att registrera dig hos relä tjänsten

I det här steget beskrivs hur du kör en Azure Relay-tjänst.

### <a name="create-the-relay-credentials"></a>Skapa autentiseringsuppgifter för vidarebefordran

1. Skapa två variabler i `Main()` där du ska lagra namnområdet och SAS-nyckeln som läses från konsolfönstret.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    SAS-nyckeln kommer att användas senare för att komma åt ditt projekt. Namnområdet skickas som en parameter till `CreateServiceUri` för att skapa en URI för tjänsten.

1. Med hjälp av ett [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) -objekt måste du deklarera att du ska använda en SAS-nyckel som autentiseringstyp. Lägg till följande kod direkt efter den som lades till i det senaste steget.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Skapa en bas adress för tjänsten

När du har lagt till den kod som du lade till i föregående avsnitt skapar du en `Uri` instans för tjänstens bas adress. Den här URI anger Service Bus-schemat, namnområdet och sökvägen för tjänstegränssnittet.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

Värdet "SB" är en förkortning för Service Bus schema. Det indikerar att vi använder TCP som protokoll. Detta schema angavs också tidigare i konfigurations filen när [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) angavs som bindning.

URI är `sb://putServiceNamespaceHere.windows.net/EchoService` för den här självstudiekursen.

### <a name="create-and-configure-the-service-host"></a>Skapa och konfigurera tjänst värden

1. Arbeta fortfarande i `Main()` , Ställ in anslutnings läget på `AutoDetect` .

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Anslutnings läget beskriver det protokoll som tjänsten använder för att kommunicera med relä tjänsten. antingen HTTP eller TCP. Med hjälp av standardinställningen `AutoDetect` försöker tjänsten ansluta till Azure Relay via TCP om den är tillgänglig och http om TCP inte är tillgängligt. Det här resultatet skiljer sig från protokollet som tjänsten anger för klient kommunikation. Det här protokollet bestäms av den bindning som används. En tjänst kan till exempel använda [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) -bindningen, som anger att dess slut punkt kommunicerar med klienter via http. Samma tjänst kan anges `ConnectivityMode.AutoDetect` så att tjänsten kommunicerar med Azure Relay via TCP.

1. Skapa tjänstevärden med hjälp av den URI som du skapade tidigare i det här avsnittet.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Tjänstevärden är det WCF-objekt som instantierar tjänsten. Här skickar du den typ av tjänst som du vill skapa, en `EchoService` typ och även till den adress som du vill exponera tjänsten för.

1. Lägg till referenser till [system. ServiceModel. Description](/dotnet/api/system.servicemodel.description) och [Microsoft. Service Bus. description](/dotnet/api/microsoft.servicebus.description)överst i *program.cs* -filen.

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. När du är tillbaka i `Main()`, konfigurerar du slutpunkten för att ge allmän åtkomst till den.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Det här steget informerar tjänsten Relay som ditt program kan hittas offentligt genom att undersöka Atom-flödet för ditt projekt. Om du anger `DiscoveryType` till kan `private` en klient fortfarande komma åt tjänsten. Tjänsten visas dock inte när den söker i `Relay` namn området. Klienten måste då i stället känna till sökvägen för slutpunkten på förhand.

1. Tillämpa autentiseringsuppgifterna för tjänsten på de tjänst slut punkter som definierats i *App.config* -filen:

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Som tidigare nämnts kan du ha deklarerat flera tjänster och slut punkter i konfigurations filen. Om du hade gjort detta skulle den här koden ha bläddrat igenom hela konfigurationsfilen och ha sökt efter varje slutpunkt som den skulle ha tillämpat på dina autentiseringsuppgifter. I den här självstudien har konfigurations filen bara en slut punkt.

### <a name="open-the-service-host"></a>Öppna tjänst värden

1. I `Main()` lägger du till följande rad för att öppna tjänsten.

    ```csharp
    host.Open();
    ```

1. Informera användaren om att tjänsten körs och förklara hur tjänsten stängs av.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Stäng tjänstevärden när du är klar.

    ```csharp
    host.Close();
    ```

1. Välj Ctrl + Skift + B för att bygga projektet.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Exempel som är värd för en tjänst i ett konsol program

Den färdiga Service koden bör visas på följande sätt. Koden innehåller service kontraktet och implementeringen från föregående steg i självstudien och är värd för tjänsten i ett konsol program.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Skapa en WCF-klient för tjänstekontraktet

Nästa uppgift är att skapa ett klient program och definiera tjänst kontraktet som du implementerar senare. De här stegen liknar de steg som används för att skapa en tjänst: definiera ett kontrakt, redigera en *App.config* fil med autentiseringsuppgifter för att ansluta till relä tjänsten och så vidare. Den kod som används för dessa arbetsuppgifter visas in exemplet som följer efter proceduren.

1. Skapa ett nytt projekt i den aktuella Visual Studio-lösningen för klienten:

   1. I **Solution Explorer** högerklickar du på den aktuella lösningen (inte projektet) och väljer **Lägg till**  >  **nytt projekt**.
   1. I **Lägg till ett nytt projekt** väljer du **konsol program (.NET Framework)** för C# och väljer **sedan nästa**.
   1. Namnge Project *EchoClient* och välj **skapa**.

1. I **Solution Explorer**, i **EchoClient** -projektet, dubbelklickar du på **program.cs** för att öppna filen i redigeraren, om den inte redan är öppen.
1. Ändra namnet på namnområdet från standardnamnet `EchoClient` till `Microsoft.ServiceBus.Samples`.
1. Installera [Service Bus NuGet-paketet](https://www.nuget.org/packages/WindowsAzure.ServiceBus):

   1. I **Solution Explorer** högerklickar du på **EchoClient** och väljer sedan **Hantera NuGet-paket**.
   1. Välj **Bläddra** och Sök efter och välj **windowsazure. Service Bus**. Välj **Installera** och godkänn användnings villkoren.

      ![Installera Service Bus-paket][4]

1. Lägg till en `using` instruktion för [system. ServiceModel](/dotnet/api/system.servicemodel) -namnrymden i *program.cs* -filen.

    ```csharp
    using System.ServiceModel;
    ```

1. Lägga till en definition för tjänstekontraktet i namnområdet, som visas i följande exempel. Den här definitionen är identisk med den definition som används i **tjänste** projektet. Lägg till den här koden överst i `Microsoft.ServiceBus.Samples` namn området.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Välj Ctrl + Skift + B för att bygga klienten.

### <a name="example-of-the-echoclient-project"></a>Exempel på EchoClient-projektet

Följande kod visar den aktuella statusen för *program.cs* -filen i **EchoClient** -projektet.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Konfigurera WCF-klienten

I det här steget skapar du en *App.config* -fil för ett grundläggande klient program som ansluter till den tjänst som du skapade tidigare i den här självstudien. I den här *App.config* -filen definieras kontrakt, bindning och namn på slut punkten. Den kod som används för dessa arbetsuppgifter visas in exemplet som följer efter proceduren.

1. I **Solution Explorer**, i **EchoClient** -projektet, dubbelklickar du på **App.config** för att öppna filen i Visual Studio-redigeraren.
1. I elementet `<appSettings>` ersätter du platshållarna med namnet på ditt namnområde för tjänsten och den SAS-nyckel som du kopierade i ett av de föregående stegen.
1. I `system.serviceModel` elementet lägger du till ett- `<client>` element.

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Den här koden förklarar att du definierar ett klient program för WCF-typ.

1. Definiera namnet, kontraktet och bindningstypen för slutpunkten inom elementet `client`.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Den här koden definierar slut punktens namn. Den definierar också det avtal som definierats i tjänsten och det faktum att klient programmet använder TCP för att kommunicera med Azure Relay. Namnet på slutpunkten används i nästa steg för att länka samman denna slutpunktskonfiguration med URI:n för tjänsten.

1. Välj **Arkiv**  >  **Spara alla**.

### <a name="example-of-the-appconfig-file"></a>Exempel på App.config-filen

Följande kod visar *App.config* -filen för eko klienten.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Implementera WCF-klienten

I det här avsnittet implementerar du ett grundläggande klient program som ansluter till den tjänst som du skapade tidigare i den här självstudien. På samma sätt som tjänsten gör klienten många av samma åtgärder för att få åtkomst till Azure Relay:

* Ställer in anslutningsläget.
* Skapar den URI som lokaliserar värdtjänsten.
* Definierar säkerhetsautentiseringen.
* Tillämpar autentiseringsuppgifterna på anslutningen.
* Öppnar anslutningen.
* Utför de programspecifika uppgifterna.
* Stänger ned anslutningen.

En av de viktigaste skillnaderna är dock att klient programmet använder en kanal för att ansluta till relä tjänsten. Tjänsten använder ett anrop för **ServiceHost**. Den kod som används för dessa arbetsuppgifter visas in exemplet som följer efter proceduren.

### <a name="implement-a-client-application"></a>Implementera ett klient program

1. Ställ in anslutningsläget på `AutoDetect`. Lägg till följande kod inne i `Main()`-metoden för **EchoClient**-programmet.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Definiera variabler för att lagra värdena för tjänstens namnområde och den SAS-nyckel som läses från konsolen.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Skapa den URI som definierar platsen för värden i ditt relä projekt.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Skapa autentiseringsobjektet för slutpunkten för tjänstens namnområde.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Skapa den kanal fabrik som läser in konfigurationen som beskrivs i *App.config* -filen.

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    En kanalfabriken är ett WCF-objekt som skapar en kanal via vilken tjänsten och klientprogrammet kan kommunicera.

1. Tillämpa autentiseringsuppgifterna.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Skapa och öppna kanalen till tjänsten.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Skriv det grundläggande gränssnittet och funktionerna för ekot.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Koden använder instansen av kanal objekt som proxy för tjänsten.

1. Stäng kanalen och stäng fabriken.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Exempel kod för den här självstudien

Den färdiga koden bör visas på följande sätt. Den här koden visar hur du skapar ett klient program, hur du anropar driften av tjänsten och hur du stänger klienten när åtgärds anropet har slutförts.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Köra programmen

1. Välj Ctrl + Skift + B för att bygga lösningen. Den här åtgärden skapar både klient projektet och det tjänst projekt som du skapade i föregående steg.
1. Du måste se till att tjänsteprogrammet körs innan du startar klientprogrammet. I **Solution Explorer** högerklickar du på lösningen **EchoService** och väljer sedan **Egenskaper**.
1. På **egenskaps sidor**, **vanliga egenskaper**  >  **Start projekt** och välj sedan **flera start projekt**. Kontrollera att **EchoService** visas först i listan.
1. Ställ in rutan **Åtgärd** för både **EchoService**- och **EchoClient**-projektet på **Starta**.

    ![Egenskaps sidor för projekt][5]

1. Välj **projekt beroenden**. I **Projects** väljer du **EchoClient**. För **är beroende av** kontrollerar du att **EchoService** har valts.

    ![Projektberoenden][6]

1. Välj **OK** för att stänga **egenskaps sidor**.
1. Välj F5 för att köra båda projekten.
1. Båda konsolfönstren öppnas och uppmanar dig att ange namnet på namnområdet. Tjänsten måste köras först, så i **EchoService** -konsol fönstret anger du namn området och väljer sedan Retur.
1. Sedan uppmanas du att ange din SAS-nyckel i konsolen. Ange SAS-nyckeln och välj RETUR.

    Här är exempel på vad som kan matas ut från konsolfönstret. Värdena här är bara exempel.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    Tjänsteprogrammet skriver till konsolfönstret, till adressen som den lyssnar på, som visas i följande exempel.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. Ange samma information som du angav tidigare för tjänsteprogrammet i konsolfönstret för **EchoClient**. Ange samma namn område och SAS-nyckel värden för klient programmet.
1. När du har angett dessa värden, öppnar klienten en kanal till tjänsten och du uppmanas att ange lite text. Följ anvisningarna i följande utmatningsexempel för konsolen.

    `Enter text to echo (or [Enter] to exit):`

    Ange text som ska skickas till tjänst programmet och välj RETUR. Texten skickas till tjänsten via tjänsteåtgärden Echo och visas i tjänstekonsolfönstret, som i följande exempelutmatning.

    `Echoing: My sample text`

    Klientprogrammet får returvärdet för `Echo`-åtgärden, som är den ursprungliga texten, och skriver ut denna i sitt konsolfönster. Följande text är exempel på utdata från klient konsol fönstret.

    `Server echoed: My sample text`

1. Du kan fortsätta att skicka textmeddelanden från klienten till tjänsten på detta sätt. När du är klar väljer du ange i klient-och tjänst konsolens fönster för att avsluta båda programmen.

## <a name="next-steps"></a>Nästa steg

Fortsätt till följande självstudie:

> [!div class="nextstepaction"]
>[Göra en lokal WCF REST-tjänst tillgänglig för en klient utanför nätverket](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
