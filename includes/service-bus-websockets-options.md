---
title: ta med fil
description: ta med fil
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: ca483d0b71bde945a7e46da785dd6a76b3a8f177
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98693414"
---
Protokoll alternativet AMQP-över-WebSockets körs via port TCP 443 precis som HTTP/REST API, men är i övrigt identiskt med enkel AMQP. Det här alternativet har en högre första anslutnings fördröjning på grund av extra hand skakning och något mer som kompromisser för att dela HTTPS-porten. Om det här läget är valt räcker TCP-port 443 för kommunikation. Med följande alternativ kan du välja läget för enkel AMQP eller AMQP WebSockets:

| Språk | Alternativ   |
| -------- | ----- |
| .NET     | [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) -egenskapen med [TransportType. AMQP](/dotnet/api/microsoft.azure.servicebus.transporttype) eller [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype) |
| Java     | [com. Microsoft. Azure. Service Bus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings) med [com. Microsoft. Azure. Service Bus. primitivs. TransportType. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) eller [com.Microsoft.Azure.ServiceBus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| Nod  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions) har ett `webSocket` konstruktor argument. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) med [TransportType. AMQP](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) eller [TransportType. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |