---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304434"
---
Protokoll alternativet AMQP-över-WebSockets körs via port TCP 443 precis som HTTP/REST API, men är i övrigt identiskt med enkel AMQP. Det här alternativet har högre första anslutnings fördröjning på grund av extra hand skakning och något mer som kompromisser för att dela HTTPS-porten. Om det här läget är valt räcker TCP-port 443 för kommunikation. Med följande alternativ kan du välja AMQP WebSockets-läge. 

| Språk | Alternativ   |
| -------- | ----- |
| .NET (Azure.Messaging.ServiceBus)    | Skapa [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor) med en konstruktor som tar [ServiceBusClientOptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions) som en parameter. Ange [ServiceBusClientOptions. TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype) till [ServiceBusTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype) |
| .NET (Microsoft.Azure.ServiceBus)    | När du skapar klient objekt använder du konstruktorer som tar [TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype), [ServiceBusConnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection)eller [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) som parametrar. <p>För den konstruktion som tar `transportType` som parameter anger du parametern till [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>För konstruktorn som tar `ServiceBusConnection` som parameter anger du [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) till [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Om du använder `ServiceBusConnectionStringBuilder` använder du konstruktorer som ger dig ett alternativ för att ange `transportType` .</p> |
| Java (com. Azure. Messaging. Service Bus)     | När du skapar klienter ställer du in [ServiceBusClientBuilder. transportType](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype) på [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Java (com. Microsoft. Azure. Service Bus)    | När du skapar klienter ställer `transportType` du in i [com. Microsoft. Azure. Service Bus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_)  för att [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| JavaScript  | När du skapar Service Bus klient objekt använder du `webSocketOptions` egenskapen i [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions). |
| Python | När du skapar Service Bus klienter anger [ServiceBusClient.transport_type](/python/api/azure-servicebus/azure.servicebus.servicebusclient) till [TransportType. AmqpOverWebSocket](/python/api/azure-servicebus/azure.servicebus.transporttype) |

