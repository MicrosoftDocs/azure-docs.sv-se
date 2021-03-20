---
title: Reliable Services stack för WCF-kommunikation
description: Den inbyggda WCF Communication-stacken i Service Fabric tillhandahåller WCF-kommunikation via klient för Reliable Services.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: c5b5d413eee2528e2d5c7d04d06f1607949beaae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89012712"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>WCF-baserad kommunikations stack för Reliable Services
Reliable Services Framework gör det möjligt för tjänst författarna att välja den kommunikations stack som de vill använda för tjänsten. De kan ansluta till kommunikations stacken som de väljer via **ICommunicationListener** som returneras från [CreateServiceReplicaListeners-eller CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) -metoderna. Ramverket innehåller en implementering av kommunikations stacken baserat på Windows Communication Foundation (WCF) för tjänst författare som vill använda WCF-baserad kommunikation.

## <a name="wcf-communication-listener"></a>Lyssnare för WCF-kommunikation
Den WCF-/regionsspecifika implementeringen av **ICommunicationListener** tillhandahålls av klassen **Microsoft. ServiceFabric. Services. Communication. WCF. Runtime. WcfCommunicationListener** .

Lest säger att vi har ett tjänst kontrakt av typen `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

Vi kan skapa en lyssnare för WCF-kommunikation i tjänsten på följande sätt.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Skriver klienter för WCF Communication-stacken
För att klienter ska kunna kommunicera med tjänster med hjälp av WCF tillhandahåller ramverket **WcfClientCommunicationFactory**, som är den WCF-/regionsspecifika implementeringen av [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md).

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

WCF Communication-kanalen kan nås från **WcfCommunicationClient** som skapats av **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Klient koden kan använda **WcfCommunicationClientFactory** tillsammans med **WcfCommunicationClient** som implementerar **ServicePartitionClient** för att fastställa tjänstens slut punkt och kommunicera med tjänsten.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> Standard-ServicePartitionResolver förutsätter att klienten körs i samma kluster som tjänsten. Om detta inte är fallet skapar du ett ServicePartitionResolver-objekt och skickar slut punkterna för kluster anslutning.
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Fjärran rop med Reliable Services fjärr kommunikation](service-fabric-reliable-services-communication-remoting.md)
* [Webb-API med OWIN i Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Skydda kommunikation för Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)
