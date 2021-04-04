---
title: Översikt över Reliable Services kommunikation
description: Översikt över Reliable Services kommunikations modellen, inklusive att öppna lyssnare på tjänster, lösa slut punkter och kommunicera mellan tjänster.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 3436d29446e963faea9bda47f5a5247b7de7d859
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97912622"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Använda API: erna för Reliable Services kommunikation
Azure Service Fabric som en plattform är helt oberoende om kommunikation mellan tjänster. Alla protokoll och stackar är acceptabla, från UDP till HTTP. Det är upp till tjänste utvecklaren att välja hur tjänsterna ska kommunicera. Reliable Services Application Framework innehåller inbyggda kommunikations stackar och API: er som du kan använda för att skapa anpassade kommunikations komponenter.

## <a name="set-up-service-communication"></a>Konfigurera tjänst kommunikation
Reliable Services-API: et använder ett enkelt gränssnitt för tjänst kommunikation. Om du vill öppna en slut punkt för din tjänst implementerar du bara det här gränssnittet:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

Du kan sedan lägga till din kommunikations avlyssnings implementering genom att returnera den i en tjänst baserad klass metod åsidosättning.

För tillstånds lösa tjänster:

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

För tillstånds känsliga tjänster:

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

I båda fallen returnerar du en samling lyssnare. Detta gör att din tjänst kan lyssna på flera slut punkter, vilket kan använda olika protokoll, genom att använda flera lyssnare. Du kan till exempel ha en HTTP-lyssnare och en separat WebSocket-lyssnare. Varje lyssnare får ett namn och den resulterande samling med *Namn: adress* par representeras som ett JSON-objekt när en klient begär lyssnings adresser för en tjänst instans eller en partition.

I en tillstånds lös tjänst returnerar åsidosättningen en samling ServiceInstanceListeners. En `ServiceInstanceListener` innehåller en funktion för att skapa en `ICommunicationListener(C#) / CommunicationListener(Java)` och ger den ett namn. För tillstånds känsliga tjänster returnerar åsidosättningen en samling ServiceReplicaListeners. Detta skiljer sig något från dess tillstånds lösa motsvarighet eftersom det `ServiceReplicaListener` finns ett alternativ för att öppna en `ICommunicationListener` på sekundära repliker. Du kan inte bara använda flera kommunikations lyssnare i en tjänst, men du kan också ange vilka lyssnare som accepterar begär Anden på sekundära repliker och som bara lyssnar på primära repliker.

Du kan till exempel ha en ServiceRemotingListener som bara tar RPC-anrop på primära repliker, och en andra, anpassad lyssnare som tar Läs begär Anden på sekundära repliker över HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> När du skapar flera lyssnare för en tjänst **måste** varje lyssnare tilldelas ett unikt namn.
>
>

Slutligen ska du beskriva de slut punkter som krävs för tjänsten i [tjänst manifestet](service-fabric-application-and-service-manifests.md) i avsnittet om slut punkter.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Kommunikations lyssnaren kan komma åt slut punkts resurserna som har allokerats till den från `CodePackageActivationContext` i `ServiceContext` . Lyssnaren kan sedan börja lyssna efter begär anden när den öppnas.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Slut punkts resurser är gemensamma för hela tjänst paketet och de allokeras av Service Fabric när tjänst paketet aktive ras. Flera tjänst repliker som finns i samma ServiceHost kan dela samma port. Det innebär att kommunikations lyssnaren ska stödja port delning. Det rekommenderade sättet att göra detta är att kommunikations lyssnaren använder partitions-ID: t och replik-/instans-ID när lyssnar adressen skapas.
>
>

### <a name="service-address-registration"></a>Registrering av tjänst adress
En system tjänst som kallas *Naming Service* körs på Service Fabric kluster. Naming Service är en registrator för tjänster och deras adresser som varje instans eller replik av tjänsten lyssnar på. När `OpenAsync(C#) / openAsync(Java)` metoden i en `ICommunicationListener(C#) / CommunicationListener(Java)` är slutförd registreras dess retur värde i Naming Service. Det här retur värdet som publiceras i Naming Service är en sträng vars värde kan vara allt alls. Detta sträng värde är det som klienter ser när de begär en adress för tjänsten från Naming Service.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

Service Fabric innehåller API: er som gör det möjligt för klienter och andra tjänster att fråga efter den här adressen efter tjänst namn. Detta är viktigt eftersom tjänst adressen inte är statisk. Tjänster flyttas runt i klustret för resurs balansering och tillgänglighets syfte. Detta är den mekanism som gör att klienter kan matcha lyssnings adressen för en tjänst.

> [!NOTE]
> En fullständig genom gång av hur du skriver en kommunikations lyssnare finns i [Service Fabric Web API-tjänster med OWIN Self-hosting](./service-fabric-reliable-services-communication-aspnetcore.md) för C#, medan du kan skriva en egen http-server implementering i avsnittet EchoServer program på https://github.com/Azure-Samples/service-fabric-java-getting-started .
>
>

## <a name="communicating-with-a-service"></a>Kommunicera med en tjänst
Reliable Services-API: et tillhandahåller följande bibliotek för att skriva klienter som kommunicerar med-tjänster.

### <a name="service-endpoint-resolution"></a>Matchning av tjänst slut punkt
Det första steget för att kommunicera med en tjänst är att matcha en slut punkts adress för den partition eller instans av tjänsten som du vill prata med. `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)`Verktygs klassen är en grundläggande primitiv som hjälper klienter att fastställa slut punkten för en tjänst vid körning. I Service Fabric terminologi kallas processen för att fastställa slut punkten för en tjänst *slut punkts upplösning*.

Om du vill ansluta till tjänster i ett kluster kan du skapa ServicePartitionResolver med standardinställningar. Detta är den rekommenderade användningen för de flesta situationer:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Om du vill ansluta till tjänster i ett annat kluster kan du skapa en ServicePartitionResolver med en uppsättning av kluster-Gateway-slutpunkter. Observera att Gateway-slutpunkter bara är olika slut punkter för att ansluta till samma kluster. Exempel:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Du `ServicePartitionResolver` kan också få en funktion för att skapa en `FabricClient` att använda internt:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient` är det objekt som används för att kommunicera med Service Fabric-klustret för olika hanterings åtgärder i klustret. Detta är användbart när du vill ha mer kontroll över hur en matchning av en tjänst partition interagerar med klustret. `FabricClient` utför cachelagring internt och är i allmänhet dyrt att skapa, så det är viktigt att återanvända `FabricClient` instanser så mycket som möjligt.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

En lösnings metod används sedan för att hämta adressen till en tjänst eller en partition för partitionerade tjänster.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

En tjänst adress kan lösas enkelt med hjälp av en ServicePartitionResolver, men mer arbete krävs för att säkerställa att den matchade adressen kan användas på rätt sätt. Klienten måste identifiera om anslutnings försöket misslyckades på grund av ett tillfälligt fel och kan provas igen (t. ex. om tjänsten flyttats eller är tillfälligt otillgänglig) eller ett permanent fel (t. ex. om tjänsten togs bort eller om den begärda resursen inte längre finns). Tjänst instanser eller repliker kan flyttas från nod till nod när som helst av flera orsaker. Tjänst adressen som löses genom ServicePartitionResolver kan vara inaktuell när klient koden försöker ansluta. I så fall måste klienten matcha adressen igen. Om du `ResolvedServicePartition` anger föregående indikerar det att matcharen måste försöka igen i stället för att bara hämta en cachelagrad adress.

Normalt behöver klient koden inte fungera med ServicePartitionResolver direkt. Den skapas och skickas till kommunikations klient fabriker i Reliable Services-API: et. Fabrikerna använder matcharen internt för att generera ett klient objekt som kan användas för att kommunicera med tjänster.

### <a name="communication-clients-and-factories"></a>Kommunikations klienter och fabriker
Kommunikations fabriks biblioteket implementerar ett typiskt mönster för fel hanterings försök som gör det enklare att försöka ansluta till löst tjänst slut punkter. Fabriks biblioteket tillhandahåller omförsöket när du anger fel hanterings funktionen.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` definierar det grundläggande gränssnitt som implementeras av en kommunikations klient fabrik som skapar klienter som kan kommunicera med en Service Fabric-tjänst. Implementeringen av CommunicationClientFactory beror på den kommunikations stack som används av den Service Fabric tjänst där klienten vill kommunicera. Reliable Services-API: et tillhandahåller en `CommunicationClientFactoryBase<TCommunicationClient>` . Detta ger en grundläggande implementering av CommunicationClientFactory-gränssnittet och utför uppgifter som är gemensamma för alla kommunikations stackar. (De här uppgifterna inkluderar att använda en ServicePartitionResolver för att fastställa tjänstens slut punkt). Klienter implementerar vanligt vis den abstrakta CommunicationClientFactoryBase-klassen för att hantera logik som är speciell för kommunikations stacken.

Kommunikations klienten tar bara emot en adress och använder den för att ansluta till en tjänst. Klienten kan använda det protokoll som det vill.

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

Klient fabriken ansvarar främst för att skapa kommunikations klienter. För klienter som inte upprätthåller en permanent anslutning, till exempel en HTTP-klient, behöver fabriken bara skapa och returnera klienten. Andra protokoll som upprätthåller en permanent anslutning, till exempel vissa binära protokoll, bör också verifieras ( `ValidateClient(string endpoint, MyCommunicationClient client)` ) av fabriken för att avgöra om anslutningen måste skapas på nytt.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Slutligen är en undantags hanterare ansvarig för att fastställa vilken åtgärd som ska vidtas när ett undantag inträffar. Undantag kategoriseras i **försök att försöka igen** och går **inte att försöka igen**.

* Undantag som **inte kan upprepas** får bara återkastas tillbaka till anroparen.
* de **nya** undantagen kategoriseras ytterligare i **temporära** och **icke-tillfälliga**.
  * **Tillfälliga** undantag är sådana som bara kan provas igen utan att lösa tjänstens slut punkts adress. Detta inkluderar tillfälliga nätverks problem eller tjänst fel svar förutom de som anger att tjänstens slut punkts adress inte finns.
  * **Icke-tillfälliga** undantag är de som kräver att tjänstens slut punkts adress löses igen. Detta inkluderar undantag som indikerar att tjänst slut punkten inte kunde nås, vilket indikerar att tjänsten har flyttats till en annan nod.

Fattar `TryHandleException` ett beslut om ett angivet undantag. Om du **inte vet** vilka beslut som ska fattas om ett undantag bör det returnera **falskt**. Om du **vet** vilket beslut du ska fatta bör du ange resultatet och returnera **Sant**.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Färdigställa allt
Med ett `ICommunicationClient(C#) / CommunicationClient(Java)` , `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` , och `IExceptionHandler(C#) / ExceptionHandler(Java)` byggt runt ett kommunikations protokoll, `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` radbryts allt tillsammans och ger en matchnings slinga för fel hantering och tjänst partition runt dessa komponenter.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Nästa steg
* [ASP.NET Core med Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Fjärran rop med Reliable Services fjärr kommunikation](service-fabric-reliable-services-communication-remoting.md)
* [WCF-kommunikation med hjälp av Reliable Services](service-fabric-reliable-services-communication-wcf.md)
