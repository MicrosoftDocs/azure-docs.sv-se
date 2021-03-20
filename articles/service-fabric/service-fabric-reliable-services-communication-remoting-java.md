---
title: Service Remoting med java i Azure Service Fabric
description: Med Service Fabric fjärr kommunikation kan klienter och tjänster kommunicera med Java-tjänster med hjälp av ett fjärran rop.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: d53d20510db70d81aab796efab48de40c880bb3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87316132"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Fjärrhantering av tjänster i Java med Reliable Services
> [!div class="op_single_selector"]
> * [C# i Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java i Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

För tjänster som inte är knutna till ett visst kommunikations protokoll eller en viss stack, till exempel WebAPI, Windows Communication Foundation (WCF) eller andra, är Reliable Services Framework en fjärran sluten mekanism för att snabbt och enkelt konfigurera fjärran rop för tjänster.  Den här artikeln beskriver hur du konfigurerar fjärran rop för tjänster som skrivits med Java.

## <a name="set-up-remoting-on-a-service"></a>Konfigurera fjärr kommunikation på en tjänst
Att konfigurera fjärr kommunikation för en tjänst görs i två enkla steg:

1. Skapa ett gränssnitt för tjänsten som ska implementeras. Det här gränssnittet definierar de metoder som är tillgängliga för ett fjärran rop på din tjänst. Metoderna måste vara en uppgift som returnerar asynkrona metoder. Gränssnittet måste implementera `microsoft.serviceFabric.services.remoting.Service` för att signalera att tjänsten har ett Remoting-gränssnitt.
2. Använd en Remoting-lyssnare i din tjänst. Det här är en `CommunicationListener` implementering som tillhandahåller funktioner för fjärr kommunikation. `FabricTransportServiceRemotingListener` kan användas för att skapa en Remoting-lyssnare med hjälp av standard transport protokollet för fjärr kommunikation.

Till exempel visar följande tillstånds lösa tjänst en enda metod för att få "Hello World" över ett fjärran rop.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Argumenten och retur typerna i tjänst gränssnittet kan vara alla enkla, komplexa eller anpassade typer, men de måste kunna serialiseras.
>
>

## <a name="call-remote-service-methods"></a>Anropa fjärrtjänstens metoder
Anrops metoder för en tjänst med hjälp av fjärran vändning görs med hjälp av en lokal proxy till tjänsten via `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` klassen. `ServiceProxyBase`Metoden skapar en lokal proxy med samma gränssnitt som tjänsten implementerar. Med den proxyservern kan du bara anropa metoder i gränssnittet via fjärr anslutning.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Fjärrramverket för fjärr kommunikation sprider undantag som har utlösts av tjänsten till klienten. Så undantags hanterings logiken på klienten med hjälp av `ServiceProxyBase` kan direkt hantera undantag som tjänsten genererar.

## <a name="service-proxy-lifetime"></a>Livstid för Tjänstproxy
ServiceProxy-skapande är en förenklad åtgärd så att du kan skapa så många som du behöver. Service proxy-instanser kan återanvändas så länge de behövs. Om ett RPC-anrop genererar ett undantag kan du fortfarande återanvända samma proxy-instans. Varje ServiceProxy innehåller en kommunikations klient som används för att skicka meddelanden via kabeln. Vid RPC-anrop utförs interna kontroller för att avgöra om kommunikations klienten är giltig. Baserat på resultatet av dessa kontroller återskapas kommunikations klienten om det behövs. Om ett undantag inträffar behöver du därför inte återskapa `ServiceProxy` .

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory livs längd
[FabricServiceProxyFactory](/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) är en fabrik som skapar proxy för olika fjärr kommunikations gränssnitt. Om du använder API `ServiceProxyBase.create` för att skapa proxy skapar ramverket en `FabricServiceProxyFactory` .
Det är praktiskt att skapa en manuellt när du behöver åsidosätta [ServiceRemotingClientFactory](/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) -egenskaper.
Fabriken är en dyr åtgärd. `FabricServiceProxyFactory` upprätthåller cacheminnet för kommunikations klienter.
Bästa praxis är att cachelagra så `FabricServiceProxyFactory` länge som möjligt.

## <a name="remoting-exception-handling"></a>Undantags hantering för fjärr kommunikation
Alla fjärrundantag som har utlösts av tjänst-API: et skickas tillbaka till klienten antingen som RuntimeException eller FabricException.

ServiceProxy hanterar alla undantags fel för den tjänstmall som den skapas för. Den löser in slut punkterna igen om det finns undantags fel (icke-tillfälliga undantag) och försöker anropa anropet på nytt med rätt slut punkt. Antalet nya försök för ett redundanskluster är oändligt.
I händelse av TransientExceptions försöker den bara att ringa upp.

Standard parametrarna för återförsök är etablerar av [OperationRetrySettings](/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Du kan konfigurera dessa värden genom att skicka OperationRetrySettings-objekt till ServiceProxyFactory-konstruktorn.

## <a name="next-steps"></a>Nästa steg
* [Skydda kommunikation för Reliable Services](service-fabric-reliable-services-secure-communication-java.md)
