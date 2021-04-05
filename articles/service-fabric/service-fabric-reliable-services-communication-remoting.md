---
title: Fjärrtjänst för fjärr kommunikation med C# i Service Fabric
description: Med Service Fabric fjärr kommunikation kan klienter och tjänster kommunicera med C#-tjänster med hjälp av ett fjärran rop.
ms.topic: conceptual
ms.date: 09/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: a0486a27d76c978a65c4a3cfd81df52a12e4ea1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791585"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Tjänst-Remoting i C# med Reliable Services

> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java i Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

För tjänster som inte är kopplade till ett visst kommunikations protokoll eller en viss stack, t. ex. ett webb-API, Windows Communication Foundation eller andra, är Reliable Services Framework en fjärran sluten mekanism för att snabbt och enkelt konfigurera fjärran rop för tjänster. Den här artikeln beskriver hur du konfigurerar fjärran rop för tjänster som skrivits med C#.

## <a name="set-up-remoting-on-a-service"></a>Konfigurera fjärr kommunikation på en tjänst

Du kan konfigurera fjärr kommunikation för en tjänst i två enkla steg:

1. Skapa ett gränssnitt för tjänsten som ska implementeras. Det här gränssnittet definierar de metoder som är tillgängliga för ett fjärran rop på din tjänst. Metoderna måste vara en uppgift som returnerar asynkrona metoder. Gränssnittet måste implementera `Microsoft.ServiceFabric.Services.Remoting.IService` för att signalera att tjänsten har ett Remoting-gränssnitt.
2. Använd en Remoting-lyssnare i din tjänst. En Remoting-lyssnare är en `ICommunicationListener` implementering som tillhandahåller funktioner för fjärr kommunikation. `Microsoft.ServiceFabric.Services.Remoting.Runtime`Namn området innehåller tilläggs metoden `CreateServiceRemotingInstanceListeners` för både tillstånds lösa och tillstånds känsliga tjänster som kan användas för att skapa en lyssnare för fjärr kommunikation med hjälp av standard transport protokollet för fjärr kommunikation.

>[!NOTE]
>`Remoting`Namn området är tillgängligt som ett separat NuGet-paket med namnet `Microsoft.ServiceFabric.Services.Remoting` .

Till exempel visar följande tillstånds lösa tjänst en enda metod för att få "Hello World" över ett fjärran rop.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> Argumenten och retur typerna i tjänst gränssnittet kan vara alla enkla, komplexa eller anpassade typer, men de måste kunna serialiseras av .NET [DataContractSerializer](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer).
>
>

## <a name="call-remote-service-methods"></a>Anropa fjärrtjänstens metoder

Anrops metoder för en tjänst med hjälp av fjärran vändning görs med hjälp av en lokal proxy till tjänsten via `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klassen. `ServiceProxy`Metoden skapar en lokal proxy med samma gränssnitt som tjänsten implementerar. Med den proxyservern kan du anropa metoder i gränssnittet via fjärr anslutning.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Fjärrramverket för fjärr kommunikation sprider undantag som orsakas av tjänsten till klienten. Det innebär att när `ServiceProxy` används är klienten ansvarig för hantering av undantag som har utlösts av tjänsten.

## <a name="service-proxy-lifetime"></a>Livstid för Tjänstproxy

Skapande av Tjänstproxy är en förenklad åtgärd, så att du kan skapa så många som du behöver. Service proxy-instanser kan återanvändas så länge de behövs. Om ett RPC-anrop genererar ett undantag kan du fortfarande återanvända samma proxy-instans. Varje Tjänstproxy innehåller en kommunikations klient som används för att skicka meddelanden via kabeln. Vid RPC-anrop utförs interna kontroller för att avgöra om kommunikations klienten är giltig. Baserat på resultatet av dessa kontroller återskapas kommunikations klienten om det behövs. Om ett undantag uppstår, behöver du därför inte återskapa `ServiceProxy` .

### <a name="service-proxy-factory-lifetime"></a>Service proxyns fabriks livstid

[ServiceProxyFactory](/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) är en fabrik som skapar proxy-instanser för olika fjärr kommunikations gränssnitt. Om du använder API: et `ServiceProxyFactory.CreateServiceProxy` för att skapa en proxyserver skapar ramverket en proxy för singleton-tjänsten.
Det är praktiskt att skapa en manuellt när du behöver åsidosätta [IServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) -egenskaper.
Fabriks skapandet är en dyr åtgärd. En Tjänstproxy hanterar ett internt cacheminne för kommunikations klienten.
Vi rekommenderar att du cachelagrar tjänstens proxy-fabrik så länge som möjligt.

## <a name="remoting-exception-handling"></a>Undantags hantering för fjärr kommunikation

Alla fjärrundantag som har utlösts av tjänst-API: et skickas tillbaka till klienten som AggregateException. Fjärrundantag bör kunna serialiseras av DataContract. Om de inte gör det genererar proxy-API: et [ServiceException](/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) med fel meddelandet.

Tjänstproxyn hanterar alla undantags fel för den tjänstmall som den skapas för. Den löser in slut punkterna igen om det finns undantags fel (icke-tillfälliga undantag) och försöker anropa anropet på nytt med rätt slut punkt. Antalet återförsök för fel i redundans är obegränsade.
Om tillfälliga undantag uppstår, försöker proxyservern att ringa upp igen.

Standard parametrarna för återförsök tillhandahålls av [OperationRetrySettings](/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

En användare kan konfigurera dessa värden genom att skicka OperationRetrySettings-objektet till ServiceProxyFactory-konstruktorn.

## <a name="use-the-remoting-v2-stack"></a>Använda fjärr kommunikation v2-stacken

Från och med version 2,8 av NuGet Remoting-paketet har du möjlighet att använda fjärr kommunikation v2-stacken. Fjärr kommunikation v2-stacken fungerar bättre. Den innehåller också funktioner som anpassad serialisering och fler anslutnings bara API: er.
Mallkod fortsätter att använda fjärr kommunikation v1-stacken.
Fjärr kommunikation v2 är inte kompatibelt med v1 (föregående Remoting-stack). Följ anvisningarna i artikeln  [Uppgradera från v1 till v2](#upgrade-from-remoting-v1-to-remoting-v2) för att undvika påverkan på tjänstens tillgänglighet.

Följande metoder är tillgängliga för att aktivera v2-stacken.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Använd ett Assembly-attribut för att använda v2-stacken

De här stegen ändrar mallkod för att använda v2-stacken med hjälp av ett Assembly-attribut.

1. Ändra slut punkts resursen från `"ServiceEndpoint"` till `"ServiceEndpointV2"` i tjänst manifestet.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Använd `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners`  tilläggs metoden för att skapa fjärrlyssnare för fjärrkommunikationer (samma för både v1 och v2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Markera den sammansättning som innehåller Remoting-gränssnitten med ett- `FabricTransportServiceRemotingProvider` attribut.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Inga kod ändringar krävs i klient projektet.
Skapa klient sammansättningen med gränssnitts sammansättningen för att se till att det Assembly-attribut som visas ovan används.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Använd explicita v2-klasser för att använda v2-stacken

Som ett alternativ till att använda ett Assembly-attribut kan v2-stacken också aktive ras genom att använda explicita v2-klasser.

De här stegen ändrar mallkod för att använda v2-stacken med hjälp av explicita v2-klasser.

1. Ändra slut punkts resursen från `"ServiceEndpoint"` till `"ServiceEndpointV2"` i tjänst manifestet.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Använd [FabricTransportServiceRemotingListener](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener) från `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` namn området.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. Använd [FabricTransportServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory) från `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` namn området för att skapa klienter.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Uppgradera från fjärr kommunikation v1 till fjärr kommunikation v2

För att kunna uppgradera från v1 till v2 krävs två steg uppgraderingar. Följ stegen i den här sekvensen.

1. Uppgradera v1-tjänsten till v2-tjänsten med hjälp av det här attributet.
Den här ändringen ser till att tjänsten lyssnar på belyssnaren v1 och v2.

    a. Lägg till en slut punkts resurs med namnet "ServiceEndpointV2" i tjänst manifestet.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Använd följande tilläggs metod för att skapa en Remoting-lyssnare.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Lägg till ett Assembly-attribut för fjärr kommunikations gränssnitt för att använda beskrivare v1 och v2 och v2-klienten.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Uppgradera v1-klienten till en v2-klient med hjälp av v2-klient-attributet.
Det här steget ser till att klienten använder v2-stacken.
Ingen ändring i klient projekt/tjänst krävs. Det räcker med att skapa klient projekt med uppdaterad gränssnitts sammansättning.

3. Det här är valfritt. Använd attributet v2 Listener och uppgradera sedan v2-tjänsten.
Det här steget ser till att tjänsten bara lyssnar på v2-lyssnaren.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Använd stacken fjärran vändning v2 (Interface Compatible)

 Fjärran vändning v2 (gränssnitts kompatibel, känd som V2_1) stack har alla funktioner i stacken v2 för fjärr kommunikation. Dess gränssnitts stack är kompatibel med fjärr kommunikation v1-stacken, men den är inte bakåtkompatibel med v2 och v1. Om du vill uppgradera från v1 till V2_1 utan att påverka tjänstens tillgänglighet följer du stegen i artikel uppgraderingen från v1 till v2 (gränssnitts kompatibel).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Använd ett Assembly-attribut för att använda stacken fjärran vändning v2 (Interface Compatible)

Följ dessa steg om du vill ändra till en V2_1 stack.

1. Lägg till en slut punkts resurs med namnet "ServiceEndpointV2_1" i tjänst manifestet.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Använd metoden för fjärr kommunikation för att skapa en Remoting-lyssnare.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Lägg till ett [Assembly-attribut](/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute) för fjärr kommunikations gränssnitt.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Inga ändringar krävs i klient projektet.
Skapa klient sammansättningen med gränssnitts sammansättningen för att se till att det tidigare Assembly-attributet används.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Använd explicita fjärr kommunikations klasser för att skapa en lyssnare/klient fabrik för v2-versionen (Interface Compatible)

Följ de här stegen:

1. Lägg till en slut punkts resurs med namnet "ServiceEndpointV2_1" i tjänst manifestet.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Använd [Remoting v2-lyssnaren](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener). Standard resurs namnet för tjänst slut punkten är "ServiceEndpointV2_1". Den måste definieras i tjänst manifestet.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. Använd v2- [klient fabriken](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory).
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Uppgradera från fjärr kommunikation v1 till fjärr kommunikation v2 (kompatibel med gränssnittet)

För att kunna uppgradera från v1 till v2 (gränssnitt som är kompatibelt, känt som V2_1), krävs två-steg-uppgraderingar. Följ stegen i den här sekvensen.

> [!NOTE]
> När du uppgraderar från v1 till v2 kontrollerar du att `Remoting` namn området har uppdaterats för att använda v2. Exempel: Microsoft. ServiceFabric. Services. Remoting. v2. FabricTransport. client
>
>

1. Uppgradera v1-tjänsten till V2_1 tjänsten med hjälp av följande attribut.
Den här ändringen ser till att tjänsten lyssnar på v1 och V2_1 lyssnare.

    a. Lägg till en slut punkts resurs med namnet "ServiceEndpointV2_1" i tjänst manifestet.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Använd följande tilläggs metod för att skapa en Remoting-lyssnare.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Lägg till ett Assembly-attribut för fjärr kommunikations gränssnitt för att använda v1, V2_1 lyssnare och V2_1 klienten.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Uppgradera v1-klienten till V2_1-klienten med hjälp av attributet V2_1 client.
Det här steget kontrollerar att klienten använder V2_1 stacken.
Ingen ändring i klient projekt/tjänst krävs. Det räcker med att skapa klient projekt med uppdaterad gränssnitts sammansättning.

3. Det här är valfritt. Ta bort v1-lyssnings versionen från attributet och uppgradera sedan v2-tjänsten.
Det här steget ser till att tjänsten bara lyssnar på v2-lyssnaren.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Använda anpassad serialisering med ett meddelande med figursatt meddelande hantering

För ett meddelande om figursatt meddelande hantering skapar vi ett enda figursatt objekt med alla parametrar som ett fält i det.
Följ de här stegen:

1. Implementera `IServiceRemotingMessageSerializationProvider` gränssnittet för att tillhandahålla implementering för anpassad serialisering.
    Det här kodfragmentet visar hur implementeringen ser ut.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. Åsidosätt standardprovidern för serialisering med `JsonSerializationProvider` för en Remoting-lyssnare.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. Åsidosätt standardprovidern för serialisering med `JsonSerializationProvider` för en klient fabrik för fjärr kommunikation.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Nästa steg

* [Webb-API med OWIN i Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Windows Communication Foundation kommunikation med Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Säker kommunikation för Reliable Services](service-fabric-reliable-services-secure-communication.md)
