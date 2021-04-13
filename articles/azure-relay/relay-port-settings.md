---
title: Azure Relay port inställningar | Microsoft Docs
description: Den här artikeln innehåller en tabell som beskriver den konfiguration som krävs för port värden för Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 81551055d967babaac6f12c3a23ce6b1e78afbd5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314175"
---
# <a name="azure-relay-port-settings"></a>Azure Relay port inställningar

I följande tabell beskrivs den konfiguration som krävs för port värden för Azure Relay.

## <a name="hybrid-connections"></a>Hybridanslutningar

Hybridanslutningar använder WebSockets på port 443 med TLS som underliggande transport funktion, som endast använder **https** . 

## <a name="wcf-relays"></a>WCF-reläer
  
|Bindning|Transport säkerhet|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Yes|HTTPS| 
|" |No|HTTP|  
|[BasicHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (tjänst)|Vilken som helst|9351/HTTP|  
|[NetEventRelayBinding-klass](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Yes|9351/HTTPS|  
|" |No|9350/HTTP|  
|[NetEventRelayBinding-klass](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (tjänst)|Vilken som helst|9351/HTTP|  
|[NetTcpRelayBinding-klass](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klient/tjänst)|Vilken som helst|5671/9352/HTTP (9352/9353 om du använder hybrid)|  
|[NetOnewayRelayBinding-klass](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Yes|9351/HTTPS|  
|" |No|9350/HTTP|  
|[NetOnewayRelayBinding-klass](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (tjänst)|Vilken som helst|9351/HTTP|  
|[WebHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Yes|HTTPS|  
|" |No|HTTP|  
|[WebHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (tjänst)|Vilken som helst|9351/HTTP|  
|[WS2007HttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Yes|HTTPS|  
|" |No|HTTP|  
|[WS2007HttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (tjänst)|Vilken som helst|9351/HTTP|

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Azure Relay går du till följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Vanliga frågor och svar om Relay](relay-faq.yml)