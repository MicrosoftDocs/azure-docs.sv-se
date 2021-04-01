---
title: Azure Relay port inställningar | Microsoft Docs
description: Den här artikeln innehåller en tabell som beskriver den konfiguration som krävs för port värden för Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 97640debe81041ff7e2b082c6a9ac606d6088664
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85314279"
---
# <a name="azure-relay-port-settings"></a>Azure Relay port inställningar

I följande tabell beskrivs den konfiguration som krävs för port värden för Azure Relay.

## <a name="hybrid-connections"></a>Hybridanslutningar

Hybridanslutningar använder WebSockets på port 443 med TLS som underliggande transport funktion, som endast använder **https** . 

## <a name="wcf-relays"></a>WCF-reläer
  
|Bindning|Transport säkerhet|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (klient)|Ja|HTTPS| 
|" |Inga|HTTP|  
|[BasicHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (tjänst)|Vilken som helst|9351/HTTP|  
|[NetEventRelayBinding-klass](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (klient)|Ja|9351/HTTPS|  
|" |Inga|9350/HTTP|  
|[NetEventRelayBinding-klass](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (tjänst)|Vilken som helst|9351/HTTP|  
|[NetTcpRelayBinding-klass](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (klient/tjänst)|Vilken som helst|5671/9352/HTTP (9352/9353 om du använder hybrid)|  
|[NetOnewayRelayBinding-klass](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (klient)|Ja|9351/HTTPS|  
|" |Inga|9350/HTTP|  
|[NetOnewayRelayBinding-klass](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (tjänst)|Vilken som helst|9351/HTTP|  
|[WebHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (klient)|Ja|HTTPS|  
|" |Inga|HTTP|  
|[WebHttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (tjänst)|Vilken som helst|9351/HTTP|  
|[WS2007HttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (klient)|Ja|HTTPS|  
|" |Inga|HTTP|  
|[WS2007HttpRelayBinding-klass](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (tjänst)|Vilken som helst|9351/HTTP|

## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Azure Relay går du till följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Vanliga frågor och svar om Relay](relay-faq.md)