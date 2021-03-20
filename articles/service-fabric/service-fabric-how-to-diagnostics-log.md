---
title: Generera logg händelser från en .NET-app
description: Lär dig mer om hur du lägger till loggning i ditt .NET Service Fabric-program som finns på ett Azure-kluster eller i ett fristående kluster.
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: a36425acf42a469c7f48b2e954bdacfdfcce1b10
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89011488"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Lägga till loggning för ditt Service Fabric-program

Ditt program måste tillhandahålla tillräckligt med information för att forensically ska kunna felsöka när problem uppstår. Loggning är en av de viktigaste saker som du kan lägga till i ditt Service Fabric-program. När ett fel inträffar kan en bra loggning ge dig ett sätt att undersöka fel. Genom att analysera logg mönster kan du se hur du kan förbättra programmets prestanda eller design. Det här dokumentet visar några olika loggnings alternativ.

## <a name="eventflow"></a>EventFlow

Med [EventFlow library](https://github.com/Azure/diagnostics-eventflow) Suite kan program definiera vilka diagnostikdata som ska samlas in och var de ska överföras till. Diagnostikdata kan vara allt från prestanda räknare till program spår. Den körs i samma process som programmet, så kommunikations kostnader minimeras. Mer information om EventFlow och Service Fabric finns i [Azure Service Fabric Event agg regering med EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Använda strukturerade EventSource-händelser

Genom att definiera meddelande händelser per användnings fall kan du paketera data om händelsen, i händelse av händelsen. Du kan enkelt söka efter och filtrera utifrån namn eller värden för de angivna händelse egenskaperna. Genom att strukturera instrumenterade utdata blir det enklare att läsa, men det kräver mer meningsfulla och dags att definiera en händelse för varje användnings fall. 

Vissa händelse definitioner kan delas i hela programmet. Till exempel skulle en metod start-eller stopp händelse återanvändas över flera tjänster i ett program. En domänbaserad tjänst, till exempel ett order system, kan ha en **CreateOrder** -händelse, som har sin egen unika händelse. Den här metoden kan generera många händelser och eventuellt kräva samordning av identifierare mellan projekt grupper. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>Använda EventSource allmänt

Eftersom det kan vara svårt att definiera vissa händelser kan många användare definiera några händelser med en gemensam uppsättning parametrar som vanligt vis skickar information som en sträng. Mycket av den strukturerade aspekten förloras och det är svårare att söka och filtrera resultaten. I den här metoden definieras några händelser som vanligt vis motsvarar loggnings nivåerna. Följande fragment definierar ett fel söknings-och fel meddelande:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

Att använda en hybrid av strukturerad och allmän Instrumentation fungerar också bra. Strukturerad Instrumentation används för rapportering av fel och mått. Allmänna händelser kan användas för detaljerad loggning som används av tekniker för fel sökning.

## <a name="microsoftextensionslogging"></a>Microsoft. Extensions. logging

ASP.NET Core loggning ([Microsoft. Extensions. logging NuGet-paketet](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) är ett loggnings ramverk som tillhandahåller ett API för standard loggning för ditt program. Stöd för andra loggnings server delar kan anslutas till ASP.NET Core loggning. Detta ger dig en mängd olika stöd för loggning i ditt program, utan att behöva ändra mycket kod.

1. Lägg till **Microsoft. Extensions. logging** NuGet-paketet till det projekt som du vill skapa instrument för. Lägg också till eventuella leverantörs paket. Mer information finns i [Logga in ASP.net Core](/aspnet/core/fundamentals/logging).
2. Lägg till ett **using** -direktiv för **Microsoft. Extensions. logging** till din tjänst fil.
3. Definiera en privat variabel i din tjänst klass.

   ```csharp
   private ILogger _logger = null;
   ```

4. I konstruktorn för din tjänst klass lägger du till följande kod:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Börja instrumentera din kod i dina metoder. Här följer några exempel:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Använda andra loggnings leverantörer

Vissa leverantörer av tredje part använder den metod som beskrivs i föregående avsnitt, inklusive [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/)och [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Du kan ansluta var och en av dessa till ASP.NET Core loggning, eller så kan du använda dem separat. Serilog har en funktion som berikar alla meddelanden som skickas från en loggare. Den här funktionen kan vara användbar för att mata ut tjänst namn, typ och partitionsinformation. Gör så här för att använda den här funktionen i ASP.NET Core-infrastrukturen:

1. Lägg till **Serilog**, **Serilog. Extensions. logging**, **Serilog. sinks. translittererad** och **Serilog. sinks.** det går att lägga till NuGet-paket i projektet. 
2. Skapa en `LoggerConfiguration` och logg instansen.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Lägg till ett `Serilog.ILogger` argument i konstruktorn för tjänsten och skicka den nyligen skapade loggen.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. I konstruktorn för tjänster skapas egenskaper för **ServiceTypeName**, **ServiceName**, **PartitionID** och **InstanceID**.

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Instrumenterar koden på samma sätt som om du använde ASP.NET Core utan Serilog.

   >[!NOTE]
   >Vi rekommenderar att du *inte* använder den statiska `Log.Logger` med föregående exempel. Service Fabric kan vara värd för flera instanser av samma tjänst typ inom en enda process. Om du använder den statiska `Log.Logger` , kommer den sista skrivaren av egenskaps berikare att visa värden för alla instanser som kör. Detta är en orsak till att _logger-variabeln är en privat medlems variabel för tjänste klassen. Du måste också vara `_logger` tillgänglig för vanlig kod som kan användas i olika tjänster.

## <a name="next-steps"></a>Nästa steg

- Läs mer information om [program övervakning i Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Läs om loggning med [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) och [Windows Azure-diagnostik](service-fabric-diagnostics-event-aggregation-wad.md).
