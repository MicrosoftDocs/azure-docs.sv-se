---
title: Felsöka Azure Service Fabric-appar i Linux
description: Lär dig hur du övervakar och diagnostiserar dina Service Fabric-tjänster på en lokal Linux-utvecklings dator.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 523cb0d1a8e8f322c1936f1fe52a954399b2acc5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88999775"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Övervaka och diagnostisera tjänster i en lokal installation av Linux-datorer


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Övervakning, identifiering, diagnostisering och fel sökning av tjänster för att kunna fortsätta med minimalt avbrott i användar upplevelsen. Övervakning och diagnostik är avgörande i en verklig distribuerad produktions miljö. Att anta en liknande modell under utveckling av tjänster säkerställer att den diagnostiska pipelinen fungerar när du flyttar till en produktions miljö. Service Fabric gör det enkelt för tjänst utvecklare att implementera diagnostik som fungerar smidigt i både lokala utvecklings installationer med en enda dator och verkliga produktions kluster inställningar.


## <a name="debugging-service-fabric-java-applications"></a>Felsöka Service Fabric Java-program

För Java-program är [flera loggnings ramverk](https://en.wikipedia.org/wiki/Java_logging_framework) tillgängliga. Eftersom `java.util.logging` är standard alternativet med JRE, används det också för [kod exemplen i GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). I följande diskussion förklaras hur du konfigurerar `java.util.logging` ramverket.

Med Java. util. logging kan du omdirigera dina program loggar till minne, utgående data strömmar, konsolfiler eller Sockets. För var och en av dessa alternativ finns standard hanterare som redan finns i ramverket. Du kan skapa en `app.properties` fil om du vill konfigurera fil hanteraren för ditt program att omdirigera alla loggar till en lokal fil.

Följande kodfragment innehåller en exempel konfiguration:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Mappen som filen pekar på `app.properties` måste finnas. När `app.properties` filen har skapats måste du också ändra ditt Start punkts skript `entrypoint.sh` i `<applicationfolder>/<servicePkg>/Code/` mappen för att ställa in egenskapen `java.util.logging.config.file` till `app.properties` fil. Posten bör se ut som i följande kodfragment:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Den här konfigurationen resulterar i att loggar samlas in vid en roterande tidpunkt `/tmp/servicefabric/logs/` . Logg filen i det här fallet heter mysfapp% u .% g. log där:
* **% u** är ett unikt nummer för att lösa konflikter mellan samtidiga Java-processer.
* **% g** är det generations nummer som används för att skilja mellan rotations loggar.

Som standard om ingen hanterare uttryckligen har kon figurer ATS, registreras konsol hanteraren. En kan visa loggarna i syslog under/var/log/syslog.

Mer information finns i [kod exemplen i GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Felsöka Service Fabric C#-program


Det finns flera ramverk för spårning av CoreCLR-program i Linux. Mer information finns i [.net-tillägg för loggning](https://github.com/dotnet/extensions/tree/master/src/Logging).  Eftersom EventSource är bekant för C#-utvecklare, använder den här artikeln EventSource för spårning i CoreCLR-exempel i Linux.

Det första steget är att inkludera system. Diagnostics. tracing så att du kan skriva dina loggar till minne, utdataström eller konsolfiler.  Om du vill logga in med EventSource lägger du till följande projekt i din project.jspå:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Du kan använda en anpassad EventListener för att lyssna efter tjänst händelsen och sedan på lämpligt sätt omdirigera dem till spårningsfiler. Följande kodfragment visar en exempel implementering av loggning med EventSource och en anpassad EventListener:


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


Föregående fragment matar ut loggarna till en fil i `/tmp/MyServiceLog.txt` . Det här fil namnet måste uppdateras på rätt sätt. Om du vill omdirigera loggarna till-konsolen använder du följande kod avsnitt i din anpassade EventListener-klass:

```csharp
public static TextWriter Out = Console.Out;
```

Exemplen i [C#-exempel](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) använder EventSource och en anpassad EventListener för att logga händelser till en fil.



## <a name="next-steps"></a>Nästa steg
Samma spårnings kod som läggs till i programmet fungerar även med diagnostiken för ditt program i ett Azure-kluster. Kolla in de här artiklarna som diskuterar de olika alternativen för verktygen och beskriver hur du konfigurerar dem.
* [Samla in loggar med Azure-diagnostik](./service-fabric-diagnostics-event-aggregation-lad.md)
