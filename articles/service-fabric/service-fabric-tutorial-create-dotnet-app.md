---
title: Skapa en .NET-app på Service Fabric i Azure
description: I den här självstudien får du se hur du skapar ett program med en ASP.NET Core-klientdel och en tillförlitlig serverdel med en tillståndskänslig tjänst, och hur du distribuerar programmet till ett kluster.
ms.topic: tutorial
ms.date: 07/10/2019
ms.custom: mvc, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8fe9f1fcb85e58122290f89819aa721c8f0e632a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035334"
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Självstudie: Skapa och distribuera en app med en ASP.NET Core Web API-klientdelstjänst och en tillståndskänslig serverdelstjänst

Den här självstudien ingår i en serie.  Du får se hur du skapar ett Azure Service Fabric-program med en ASP.NET Core Web API-klientdel och en tillståndskänslig serverdelstjänst för att lagra dina data. När du är klar har du ett röstningsprogam med ASP.NET Core-webbklient som sparar röstningsresultat i en tillståndskänslig backend-tjänst i klustret. Den här själv studie serien kräver en Windows Developer-dator. Om du inte vill skapa röstningsprogrammet manuellt kan du [ladda ned källkoden](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) för det färdiga programmet och gå vidare till [Gå igenom exempelprogrammet för röstning](#walkthrough_anchor).  Om du vill kan du visa en [videogenomgång](https://channel9.msdn.com/Events/Connect/2017/E100) av den här kursen.

![AngularJS + ASP. NET API-klient del, ansluta till en tillstånds känslig Server dels tjänst på Service Fabric](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * Skapa en ASP.NET Core Web API-tjänst som en tillståndskänslig, tillförlitlig tjänst
> * Skapa en ASP.NET Core-webbprogramtjänst som en tillståndskänslig, tillförlitlig webbtjänst
> * Använda omvänd proxy för att kommunicera med den tillståndskänsliga tjänsten

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett .NET Service Fabric-program
> * [Distribuera programmet till ett fjärran slutet kluster](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Lägga till en HTTPS-slutpunkt i en klienttjänst i ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Konfigurera CI/CD med hjälp av Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:
* Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Installera Visual Studio 2019](https://www.visualstudio.com/) version 15,5 eller senare med arbets belastningarna **Azure Development** och **ASP.net och webb utveckling** .
* [Installera Service Fabric SDK](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Skapa en ASP.NET Core Web API-tjänst som en tillförlitlig tjänst

Skapa först webbklientdelen i röstningsprogrammet med ASP.NET Core. ASP.NET Core är ett lätt, plattformsoberoende utvecklingsverktyg som du kan använda för att skapa moderna webbgränssnitt och webb-API: er. För att du ska förstå hur ASP.NET Core kan integreras med Service Fabric rekommenderar vi att du läser igenom artikeln [ASP.NET Core i Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md). Just nu kan du bara följa den här kursen och komma igång snabbt. Läs mer om ASP.NET Core i [ASP.NET Core-dokumentationen](/aspnet/core/).

1. Starta Visual Studio som **administratör**.

2. Skapa ett projekt med **filen** -> **nytt** -> **projekt**.

3. Klicka på **Moln > Service Fabric-program** i dialogrutan **Nytt projekt**.

4. Ge programmet namnet **Voting** och klicka på **OK**.

   ![Dialogrutan Nytt projekt i Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. På sidan **Ny Service Fabric-tjänst** väljer du **Tillståndslös ASP.NET Core**, ger tjänsten namnet **VotingWeb** och klickar på **OK**.
   
   ![Välja ASP.NET-webbtjänst i dialogrutan Ny tjänst](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. På nästa sida finns en uppsättning ASP.NET Core-projektmallar. För den här kursen väljer du **Webbapp (Model-View-Controller)** och klickar på **OK**.
   
   ![Välj typ av ASP.NET-projekt](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio skapar ett programprojekt och ett tjänstprojekt, och de visas i Solution Explorer.

   ![Solution Explorer när ett program med ASP.NET Core Web API-tjänst har skapats]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="update-the-sitejs-file"></a>Uppdatera site.js-filen
Öppna **wwwroot/js/site.js**.  Ersätt innehållet med följande JavaScript-kod, som används i startvyerna, och spara dina ändringar.

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Uppdatera filen Index.cshtml

Öppna **Views/Home/Index.cshtml**, den vy som används för startkontrollanten.  Byt ut innehållet mot följande och spara sedan ändringarna.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.Key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.Value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-_layoutcshtml-file"></a>Uppdatera filen _Layout.cshtml

Öppna **Views/Shared/_Layout.cshtml**, som är standardlayouten för ASP.NET-appen.  Byt ut innehållet mot följande och spara sedan ändringarna.


```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="https://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.0/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Uppdatera filen VotingWeb.cs

Öppna filen *VotingWeb.cs* som skapar ASP.NET Core WebHost i den tillståndslösa tjänsten med hjälp av WebListener-webbservern.

Lägg till `using System.Net.Http;`-direktivet överst i filen.

Byt ut funktionen `CreateServiceInstanceListeners()` mot följande kod och spara sedan ändringarna.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Lägg även till metoden `GetVotingDataServiceName` under `CreateServiceInstanceListeners()` och spara ändringarna. `GetVotingDataServiceName` returnerar namnet på tjänsten när det efterfrågas.

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>Lägga till filen VotesController.cs

Lägg till en kontrollant som definierar röstningsåtgärderna. Högerklicka på mappen **Controllers** och välj **Lägg till-> Nytt objekt-> Visual C# -> ASP.NET Core -> Klass**. Ge filen namnet **VotesController.cs** och klicka på **Lägg till**.  

Byt ut innehållet i filen *VotesController.cs* mot följande kod och spara sedan ändringarna.  Längre fram, i [Uppdatera filen VotesController.cs](#updatevotecontroller_anchor), ändras den här filen för att läsa och skriva röstningsdata från serverdelstjänsten.  För tillfället returneras statiska strängdata till vyn av kontrollanten.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>Konfigurera lyssningsporten

När klientdelstjänsten VotingWeb skapas väljer Visual Studio slumpmässigt en port där tjänsten ska lyssna.  VotingWeb-tjänsten fungerar som klientdel för det här programmet och godkänner extern trafik, så vi kopplar den tjänsten till en fast och välkänd port.  I [tjänstmanifestet](service-fabric-application-and-service-manifests.md) anges tjänstens slutpunkter.

I Solution Explorer öppnar du  *VotingWeb/PackageRoot/ServiceManifest.xml*.  Leta rätt på elementet **Endpoint** i avsnittet **Resources** och ändra värdet för **Port** till **8080**. Om du vill distribuera och köra programmet lokalt måste programmets lyssningsport vara öppen och tillgänglig på datorn.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
```

Uppdatera också värdet för egenskapen Application URL i projektet Voting så att webbläsare öppnar rätt port när du felsöker appen.  Välj projektet **Voting** i Solution Explorer och uppdatera egenskapen **Application URL** till **8080**.

### <a name="deploy-and-run-the-voting-application-locally"></a>Distribuera och köra appen Voting lokalt
Du kan nu köra appen Voting och felsöka den. Tryck på **F5** i Visual Studio så att du distribuerar appen till ditt lokala Service Fabric-kluster i felsökningsläge. Det här går inte om du inte öppnade Visual Studio som **administratör** tidigare.

> [!NOTE]
> Första gången du kör och distribuerar appen lokalt skapar Visual Studio ett lokalt Service Fabric-kluster för felsökning.  Det kan ta lite tid att skapa klustret. Statusen för klustergenereringen visas i utdatafönstret i Visual Studio.

När du har distribuerat appen Voting till det lokala Service Fabric-klustret öppnas webbappen automatiskt i en ny flik i webbläsaren. Den bör se ut så här:

![ASP.NET Core-klientdel](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Om du vill stoppa felsökningen av programmet går du tillbaka till Visual Studio och trycker på **SKIFT + F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Lägg till en tillståndskänslig serverdelstjänst i ditt program

Nu när ett ASP.NET Web API-tjänsten körs i programmet kan du fortsätta med att lägga till en tillståndskänslig tillförlitlig tjänst för att lagra data i programmet.

I Service Fabric kan du konsekvent och tillförlitligt lagra dina data direkt i tjänsten med hjälp av tillförlitliga samlingar. En tillförlitlig samling är en uppsättning samlingsklasser med hög tillgänglighet och tillförlitlighet som är välbekanta för alla som har använt C#-samlingar.

I den här självstudiekursen skapar du en tjänst som lagrar ett räknarvärde i en tillförlitlig samling.

1. I Solution Explorer högerklickar du på **Tjänster** i projektet för Voting-appen och väljer **Lägg till > Ny Service Fabric-tjänst**.
    
2. I dialogrutan **Ny Service Fabric-tjänst** väljer du **Tillståndskänslig ASP.NET Core**, ger tjänsten namnet **VotingData** och trycker på **OK**.

    När tjänstprojektet har skapats har du två tjänster i ditt program. Du kan lägga till fler tjänster på samma sätt allt eftersom du fortsätter att bygga på ditt program. Tjänsterna kan ha olika versionsnummer och uppgraderas fristående.

3. På nästa sida finns en uppsättning ASP.NET Core-projektmallar. För den här självstudiekursen väljer du **API**.

    Visual Studio skapar ett projekt för VotingData-tjänsten och visar det i Solution Explorer.

    ![Solution Explorer](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Lägga till filen VotesDataController.cs

I projektet **VotingData** högerklickar du på mappen **Controllers** och väljer **Lägg till -> Nytt objekt -> Klass**. Ge filen namnet **VoteDataController.cs** och klicka på **Lägg till**. Byt ut filens innehåll mot följande och spara sedan ändringarna.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```

## <a name="connect-the-services"></a>Ansluta tjänsterna

I nästa steg ansluter du de två tjänsterna och får webbprogrammets klientdel att hämta och ange röstningsinformation från serverdelstjänsten.

I Service Fabric har du full flexibilitet i kommunikationen med tillförlitliga tjänster. I ett och samma program kan du ha tjänster som är tillgängliga TCP. Andra tjänster som kan nås via ett HTTP-REST-API och ännu fler tjänster kan vara tillgängliga via webbsockets. Bakgrundsinformation om de tillgängliga alternativen och vilka kompromisser du kan behöva göra finns i [Kommunicera med tjänster](service-fabric-connect-and-communicate-with-services.md).

I den här självstudien använder du [ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md) och [Omvänd proxy för Service Fabric](service-fabric-reverseproxy.md) så att VotingWeb-webbtjänsten i klientdelen kan kommunicera med VotingData-tjänsten i serverdelen. Den omvända proxyn är som standard konfigurerad att använda port 19081, och det bör fungera i den här självstudien. Porten för omvänd proxy har angetts i Azure Resource Manager-mallen som används för att konfigurera klustret. Om du vill veta vilken port som använts, titta i klustermallen i resursen **Microsoft.ServiceFabric/clusters**: 

```json
"nodeTypes": [
          {
            ...
            "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
            "isPrimary": true,
            "vmInstanceCount": "[parameters('nt0InstanceCount')]",
            "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]"
          }
        ],
```
Porten för omvänd proxy som används i det lokala utvecklingsklustret visar elementet **HttpApplicationGatewayEndpoint** i det lokala Service Fabric-klustermanifestet:
1. Öppna ett webbläsarfönster och gå till http: \/ /localhost: 19080 för att öppna Service Fabric Explorer-verktyget.
2. Välj **Kluster -> Manifest**.
3. Anteckna porten för elementet HttpApplicationGatewayEndpoint. Den bör vara 19081. Om den inte är 19081 måste du ändra porten i metoden GetProxyAddress i följande VotesController.cs-kod.

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Uppdatera filen VotesController.cs

I projektet **VotingWeb** öppnar du filen *Controllers/VotesController.cs*.  Byt ut innehållet i klassdefinition `VotesController` mot följande och spara sedan ändringarna. Om porten för omvänd proxy som du identifierade i föregående steg inte är 19081 ändrar du den port som används i metoden GetProxyAddress från 19081 till den port du identifierade.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```

<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Gå igenom exempelprogrammet för röstning

Röstningsprogrammet består av två tjänster:

* Webbklienttjänst (VotingWeb) – En webbklienttjänst för ASP.NET Core som används av webbsidan och visar webb-API:er för att kommunicera med serverdelstjänsten.
* Serverdelstjänst (VotingData) – En webbtjänst för ASP.NET Core som visar en API för att lagra röstningsresultat i en tillförlitlig ordlista på disken.

![Diagram över programmet](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

När du röstar i programmet händer följande:

1. Ett JavaScript skickar röstningsbegäran till webb-API i webbklienttjänsten som en HTTP PUT-begäran.

2. Webbklienttjänsten använder en proxy för att hitta och vidarebefordra en HTTP PUT-begäran till serverdelstjänsten.

3. Serverdelstjänsten tar den inkommande begäran och lagrar det uppdaterade resultatet i en tillförlitlig ordlista, som replikeras till flera noder i klustret och sparas på disken. Alla programdata lagras i klustret, så det behövs ingen databas.

## <a name="debug-in-visual-studio"></a>Felsökning i Visual Studio

När du felsöker programmet i Visual Studio använder du ett lokalt utvecklingskluster för Service Fabric. Du kan välja att anpassa felsökningen så att det passar ditt scenario. I det här programmet lagras data i serverdelstjänsten med hjälp av en tillförlitlig ordlista. Visual Studio tar som standard bort programmet när du stoppar felsökningsprogrammet. När programmet tas bort kommer även data i serverdelstjänsten att tas bort. Om du vill spara data mellan felsökningssessionerna kan du ändra **programmets felsökningsläge** som en egenskap i projektet **Voting** i Visual Studio.

Gör så här om du vill se vad som händer i koden:

1. Öppna filen **VotingWeb\VotesController.cs** och ange en brytpunkt i metoden **Put** i webb-API:t (rad 72).

2. Öppna filen **VotingData\VoteDataController.cs** och ange en brytpunkt i metoden **Put** i det här webb-API:t (rad 54).

3. Tryck på **F5** för att starta appen i felsökningsläge.

4. Gå tillbaka till webbläsaren och klicka på ett röstningsalternativ eller lägg till ett nytt röstningsalternativ. Du kommer till den första brytpunkten i webbklientens api-kontroll.
    

   1. Här skickar JavaScript i webbläsaren en begäran till webb-API-kontrollen i frontwebbtjänsten.

      ![Lägg till röst för frontwebbtjänst](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

   2. Skapa först URL:en till ReverseProxy för serverdelstjänsten **(1)**.
   3. Skicka sedan HTTP PUT-begäran till ReverseProxy **(2)**.
   4. Till sist returneras svaret från serverdelstjänsten till klienten **(3)**.

5. Tryck på **F5** för att fortsätta.
   1. Du befinner dig nu på brytpunkten i serverdelstjänsten.

      ![Lägg till röst för serverdelstjänst](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

   2. På den första raden i metoden **(1)** använder du `StateManager` för att hämta eller lägga till en tillförlitlig ordlista med namnet `counts`.
   3. All interaktion med värden i en tillförlitlig ordlista kräver en transaktion, den här använder instruktionen **(2)** som skapar den transaktionen.
   4. I transaktionen uppdaterar du värdet för den relevanta nyckeln för röstningsalternativet och utför åtgärden **(3)**. När utförandemetoden returneras uppdateras data i ordlistan och replikeras till andra noder i klustret. Data har nu lagrats i klustret och serverdelstjänsten kan redundansväxla till andra noder och fortfarande ha data tillgängliga.
6. Tryck på **F5** för att fortsätta.

Stoppa felsökningssessionen genom att trycka på **Skift + F5**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skapa en ASP.NET Core Web API-tjänst som en tillståndskänslig, tillförlitlig tjänst
> * Skapa en ASP.NET Core-webbprogramtjänst som en tillståndskänslig, tillförlitlig webbtjänst
> * Använda omvänd proxy för att kommunicera med den tillståndskänsliga tjänsten

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Distribuera appen till Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
