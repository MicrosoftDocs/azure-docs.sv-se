---
title: 'Skapa ditt första Service Fabric-program i C #'
description: Introduktion till att skapa ett Microsoft Azure Service Fabric-program med tillstånds lösa och tillstånds känsliga tjänster.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: sfrev, devx-track-csharp
ms.openlocfilehash: 45341c98a40cbcabfa8b96f2016f02f1755fe2b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791535"
---
# <a name="get-started-with-reliable-services"></a>Kom igång med Reliable Services

> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-quick-start.md)
> * [Java i Linux](service-fabric-reliable-services-quick-start-java.md)

Ett Azure Service Fabric-program innehåller en eller flera tjänster som kör din kod. Den här guiden visar hur du skapar både tillstånds lösa och tillstånds känsliga Service Fabric program med [Reliable Services](service-fabric-reliable-services-introduction.md).  

## <a name="basic-concepts"></a>Grundläggande begrepp

För att komma igång med Reliable Services behöver du bara förstå några grundläggande begrepp:

* **Tjänst typ**: det här är tjänst implementeringen. Den definieras av den klass som du skriver som utökar `StatelessService` och annan kod eller beroenden som används däri, tillsammans med ett namn och ett versions nummer.
* **Namngiven tjänst instans**: om du vill köra tjänsten skapar du namngivna instanser av tjänst typen, ungefär som du skapar objekt instanser av en klass typ. En tjänst instans har ett namn i form av en URI med hjälp av "Fabric:/" schema, till exempel "Fabric:/MyApp/unservice".
* **Tjänst värd**: de namngivna tjänst instanser som du skapar måste köras i en värd process. Tjänste värden är bara en process där instanser av tjänsten kan köras.
* **Tjänst registrering**: registreringen samlar ihop allt. Tjänst typen måste vara registrerad med Service Fabric runtime i en tjänst värd för att Service Fabric ska kunna skapa instanser av den som ska köras.  

## <a name="create-a-stateless-service"></a>Skapa en tillstånds lös tjänst

En tillstånds lös tjänst är en typ av tjänst som för närvarande är normen i moln program. Den betraktas som tillstånds lös eftersom själva tjänsten inte innehåller data som behöver lagras på ett tillförlitligt sätt eller göras med hög tillgänglighet. Om en instans av en tillstånds lös tjänst stängs av försvinner allt internt tillstånd. I den här typen av tjänst måste tillstånd vara beständigt till en extern lagrings plats, till exempel Azure-tabeller eller SQL Database, för att det ska bli hög tillgänglig och tillförlitligt.

Starta Visual Studio 2017 eller Visual Studio 2019 som administratör och skapa ett nytt Service Fabric program projekt med namnet *HelloWorld*:

![Använd dialog rutan nytt projekt för att skapa ett nytt Service Fabric program](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Skapa sedan ett tillstånds lösa tjänst projekt med **.net Core 2,0** med namnet *HelloWorldStateless*:

![I den andra dialog rutan skapar du ett tillstånds löst tjänst projekt](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

Din lösning innehåller nu två projekt:

* *HelloWorld*. Detta är det *program* projekt som innehåller dina *tjänster*. Den innehåller också applikations manifestet som beskriver programmet, samt ett antal PowerShell-skript som hjälper dig att distribuera ditt program.
* *HelloWorldStateless*. Det här är tjänst projektet. Den innehåller den tillstånds lösa tjänst implementeringen.

## <a name="implement-the-service"></a>Implementera tjänsten

Öppna filen **HelloWorldStateless. cs** i tjänste projektet. I Service Fabric kan en tjänst köra vilken affärs logik som helst. Tjänst-API: et tillhandahåller två start punkter för din kod:

* En öppen start punkt metod som kallas *RunAsync*, där du kan börja köra alla arbets belastningar, inklusive tids krävande beräknings arbets belastningar.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* En kommunikations start punkt där du kan ansluta till kommunikations stacken, t. ex. ASP.NET Core. Det är här du kan börja ta emot förfrågningar från användare och andra tjänster.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

I den här självstudien kommer vi att fokusera på `RunAsync()` metoden för start punkt. Det är här du kan börja köra koden direkt.
Projekt mal len innehåller en exempel implementering av `RunAsync()` som ökar antalet rullandeor.

> [!NOTE]
> Information om hur du arbetar med en kommunikations stack finns i [tjänst kommunikation med ASP.net Core](service-fabric-reliable-services-communication-aspnetcore.md)

### <a name="runasync"></a>RunAsync

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

Plattformen anropar den här metoden när en instans av en tjänst placeras och är klar att köras. För en tillstånds lös tjänst innebär det bara när tjänst instansen öppnas. En avbrotts-token anges för att koordineras när tjänst instansen måste stängas. I Service Fabric kan den här öppna/stäng-cykeln för en tjänst instans ske många gånger över hela livs längden för tjänsten. Detta kan inträffa av olika orsaker, inklusive:

* Systemet flyttar tjänst instanserna för resurs balansering.
* Fel uppstår i koden.
* Programmet eller systemet har uppgraderats.
* Den underliggande maskin varan upplever ett avbrott.

Den här dirigeringen hanteras av systemet för att hålla tjänsten hög tillgänglig och korrekt bal anse rad.

`RunAsync()` bör inte blockera synkront. Din implementering av RunAsync ska returnera en uppgift eller vänta på eventuella tids krävande eller blockerade åtgärder så att körningen kan fortsätta. Observera i `while(true)` slingan i föregående exempel används en uppgift som returnerar `await Task.Delay()` . Om din arbets belastning måste blockera synkront bör du schemalägga en ny aktivitet med `Task.Run()` i din `RunAsync` implementering.

Annullering av din arbets belastning är en samarbets insats som dirigeras av den angivna avbrotts-token. Systemet väntar på att aktiviteten ska avslutas (genom slutförd, annullering eller fel) innan den går vidare. Det är viktigt att du följer token för uppsägning, slutför allt arbete och avslutar `RunAsync()` så snabbt som möjligt när systemet begär uppsägning.

I det här tillstånds lösa tjänst exemplet lagras antalet i en lokal variabel. Men eftersom det här är en tillstånds lös tjänst finns bara det värde som lagras för den aktuella livs cykeln för tjänst instansen. När tjänsten flyttas eller startas om går värdet förlorat.

## <a name="create-a-stateful-service"></a>Skapa en tillstånds känslig tjänst

Service Fabric introducerar en ny typ av tjänst som är tillstånds känslig. En tillstånds känslig tjänst kan upprätthålla tillstånd på ett tillförlitligt sätt i själva tjänsten, tillsammans med den kod som används. Tillstånd har gjorts med hög tillgänglighet genom Service Fabric utan att behöva beständigt tillstånd till en extern lagrings plats.

Om du vill konvertera ett räknar värde från tillstånds lös till hög tillgänglighet och beständigt, även när tjänsten flyttas eller startas om, behöver du en tillstånds känslig tjänst.

I samma *HelloWorld* -program kan du lägga till en ny tjänst genom att högerklicka på tjänst referenserna i programprojektet och välja **Add-> New Service Fabric service**.

![Lägga till en tjänst i Service Fabric programmet](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Välj **.net Core 2,0-> tillstånds känslig tjänst** och ge den namnet *HelloWorldStateful*. Klicka på **OK**.

![Använd dialog rutan nytt projekt för att skapa en ny Service Fabric tillstånds känslig tjänst](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

Ditt program bör nu ha två tjänster: den tillstånds lösa tjänsten *HelloWorldStateless* och den tillstånds känsliga tjänsten *HelloWorldStateful*.

En tillstånds känslig tjänst har samma start punkter som en tillstånds lös tjänst. Den största skillnaden är tillgängligheten för en *tillstånds leverantör* som kan lagra tillstånd på ett tillförlitligt sätt. Service Fabric levereras med en tillstånds leverantörs implementering som kallas [Reliable Collections](service-fabric-reliable-services-reliable-collections.md), vilket gör att du kan skapa replikerade data strukturer via den pålitliga tillstånds hanteraren. En tillstånds känslig tillförlitlig tjänst använder denna tillstånds leverantör som standard.

Öppna **HelloWorldStateful. cs** i *HelloWorldStateful*, som innehåller följande RunAsync-metod:

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync

`RunAsync()` fungerar på samma sätt i tillstånds känsliga och tillstånds lösa tjänster. Men i en tillstånds känslig tjänst utför plattformen ytterligare arbete för din räkning innan den körs `RunAsync()` . Detta arbete kan omfatta att se till att de pålitliga tillstånds hanteraren och pålitliga samlingar är klara att använda.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>Tillförlitliga samlingar och tillförlitliga tillstånds hanterare

```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](/dotnet/api/microsoft.servicefabric.data.collections.ireliabledictionary-2#microsoft_servicefabric_data_collections_ireliabledictionary_2) är en ordboks implementering som du kan använda för att lagra tillstånd i tjänsten på ett tillförlitligt sätt. Med Service Fabric och pålitliga samlingar kan du lagra data direkt i tjänsten utan att ha behov av ett externt beständigt arkiv. Tillförlitliga samlingar gör dina data hög tillgängliga. Service Fabric uppnår detta genom att skapa och hantera flera *repliker* av tjänsten åt dig. Det innehåller också ett API som sammanfattar komplexiteten vid hantering av dessa repliker och deras tillstånds över gångar.

Tillförlitliga samlingar kan lagra alla .NET-typer, inklusive anpassade typer, med ett par varningar:

* Service Fabric gör ditt tillstånd hög tillgängligt genom att *Replikera* tillstånd mellan noder, och tillförlitliga samlingar lagrar dina data till en lokal disk på varje replik. Det innebär att allt som lagras i pålitliga samlingar måste kunna *serialiseras*. Som standard använder pålitliga samlingar [DataContract](/dotnet/api/system.runtime.serialization.datacontractattribute) för serialisering, så det är viktigt att se till att dina typer [stöds av data kontrakts serialiseringen](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer) när du använder standard-serialiseraren.
* Objekt replikeras för hög tillgänglighet när du genomför transaktioner på pålitliga samlingar. Objekt som lagras i Reliable Collections lagras i lokalt minne i tjänsten. Det innebär att du har en lokal referens till objektet.
  
   Det är viktigt att du inte följer lokala instanser av dessa objekt utan att utföra en uppdaterings åtgärd för den tillförlitliga samlingen i en transaktion. Detta beror på att ändringar av lokala instanser av objekt inte replikeras automatiskt. Du måste infoga objektet i ord listan igen eller använda en av *uppdaterings* metoderna i ord listan.

Den pålitliga tillstånds hanteraren hanterar pålitliga samlingar åt dig. Du kan helt enkelt fråga den pålitliga tillstånds hanteraren för en tillförlitlig samling efter namn och var som helst i din tjänst. Den tillförlitliga tillstånds hanteraren garanterar att du får tillbaka en referens. Vi rekommenderar inte att du sparar referenser till tillförlitliga samlings instanser i klass medlems variabler eller egenskaper. Särskild försiktighet måste vidtas för att säkerställa att referensen anges till en instans hela tiden i tjänste livs cykeln. Den Reliable State Manager hanterar det här arbetet åt dig, och den är optimerad för upprepade besök.

### <a name="transactional-and-asynchronous-operations"></a>Transaktionella och asynkrona åtgärder

```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Reliable Collections har många av samma åtgärder som deras `System.Collections.Generic` och `System.Collections.Concurrent` motsvarigheter, förutom för språkintegrerad fråga (LINQ). Åtgärder på Reliable Collections är asynkrona. Detta beror på att Skriv åtgärder med tillförlitliga samlingar utför I/O-åtgärder för att replikera och bevara data till disk.

Tillförlitliga samlings åtgärder är *transaktionella*, så att du kan hålla tillstånd konsekvent över flera pålitliga samlingar och åtgärder. Du kan till exempel ta bort en arbets uppgift från en tillförlitlig kö, utföra en åtgärd och spara resultatet i en tillförlitlig ord lista i en enda transaktion. Detta behandlas som en atomisk åtgärd och garanterar att hela åtgärden kommer att lyckas eller att hela åtgärden kommer att återställas. Om ett fel inträffar när du har avplacerat objektet, men innan du sparar resultatet, återställs hela transaktionen och objektet behålls i kön för bearbetning.

## <a name="run-the-application"></a>Kör programmet
Nu återgår vi till programmet *HelloWorld* . Nu kan du bygga och distribuera dina tjänster. När du trycker på **F5** skapas och distribueras ditt program till det lokala klustret.

När tjänsterna har startats kan du Visa de genererade ETW (Event Tracing for Windows)-händelserna (ETW) i ett fönster med **diagnostiska händelser** . Observera att de händelser som visas kommer från både den tillstånds lösa tjänsten och den tillstånds känsliga tjänsten i programmet. Du kan pausa strömmen genom att klicka på **paus** knappen. Du kan sedan granska informationen i ett meddelande genom att expandera meddelandet.

> [!NOTE]
> Innan du kör programmet måste du kontrol lera att du har ett lokalt utvecklings kluster som kör. Se [komma igång-guiden](service-fabric-get-started.md) för information om hur du konfigurerar din lokala miljö.
> 
> 

![Visa diagnostiska händelser i Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>Nästa steg
[Felsöka ditt Service Fabric program i Visual Studio](service-fabric-debugging-your-application.md)

[Kom igång: Service Fabric webb-API-tjänster med OWIN själv värdskap](./service-fabric-reliable-services-communication-aspnetcore.md)

[Läs mer om pålitliga samlingar](service-fabric-reliable-services-reliable-collections.md)

[Distribuera ett program](service-fabric-deploy-remove-applications.md)

[Programuppgradering](service-fabric-application-upgrade.md)

[Referens för utvecklare för Reliable Services](/previous-versions/azure/dn706529(v=azure.100))
