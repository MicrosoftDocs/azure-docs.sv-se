---
title: Felsöka Azure VPN Gateway med hjälp av diagnostikloggar
description: Felsöka Azure VPN Gateway med hjälp av diagnostikloggar
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 232e084e44696c6aa88a9dd33092c48a96e35f85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772015"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>Felsöka Azure VPN Gateway med hjälp av diagnostikloggar

Den här artikeln hjälper dig att förstå de olika loggar som är tillgängliga för VPN Gateway diagnostik och hur du använder dem för att effektivt felsöka VPN gateway-problem.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Följande loggar är tillgängliga i Azure:

|***Namn** _ | _ *_Beskrivning_** |
|---        | ---               |
|**GatewayDiagnosticLog** | Innehåller diagnostikloggar för konfigurations händelser för gateway, primära ändringar och underhålls händelser. |
|**TunnelDiagnosticLog** | Innehåller ändringar av tunnel tillstånds händelser. Tunnel anslutning/från kopplings händelser har en sammanfattande orsak för tillstånds ändringen om det är tillämpligt. |
|**RouteDiagnosticLog** | Loggar ändringar av statiska vägar och BGP-händelser som inträffar på gatewayen. |
|**IKEDiagnosticLog** | Loggar IKE-kontrollmeddelanden och-händelser på gatewayen. |
|**P2SDiagnosticLog** | Loggar punkt-till-plats-kontrollmeddelanden och händelser på gatewayen. |

Observera att det finns flera tillgängliga kolumner i dessa tabeller. I den här artikeln presenterar vi bara de mest relevanta för enklare logg användning.

## <a name="set-up-logging"></a><a name="setup"></a>Konfigurera loggning

Information om hur du ställer in diagnostikloggar från Azure VPN Gateway med hjälp av Azure Log Analytics finns i [Konfigurera aviseringar för diagnostiska logg händelser från VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log).

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

Konfigurations ändringar granskas i **GatewayDiagnosticLog** -tabellen. Det kan ta några minuter innan ändringarna du kör avspeglas i loggarna.

Här har du en exempel fråga som referens.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

I den här frågan på **GatewayDiagnosticLog** visas flera kolumner.

|***Namn** _ | _ *_Beskrivning_** |
|---        | ---               |
|**TimeGenerated** | tidsstämpeln för varje händelse, i UTC-tidszonen.|
|**OperationName** |händelsen som hände. Det kan vara antingen *SetGatewayConfiguration, SetConnectionConfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove, ValidateGatewayConfiguration*.|
|**Meddelande** | information om vad som händer och visar en lista över lyckade/felaktiga resultat.|

Exemplet nedan visar den aktivitet som loggas när en ny konfiguration tillämpades:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="Exempel på en angiven Gateway-åtgärd som visas i GatewayDiagnosticLog.":::


Observera att en SetGatewayConfiguration loggas varje gång en viss konfiguration ändras både på en VPN Gateway eller en lokal nätverksgateway.
Att referera till resultaten från tabellen **GatewayDiagnosticLog** med de i **TunnelDiagnosticLog** -tabellen kan hjälpa oss att avgöra om ett fel i tunnel anslutningen har startats samtidigt som en konfiguration har ändrats eller om ett underhåll ägde rum. I så fall, har vi en bra pekare mot den möjliga rotor saken.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

**TunnelDiagnosticLog** -tabellen är mycket användbar för att kontrol lera den historiska anslutnings statusen för tunneln.

Här har du en exempel fråga som referens.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

I den här frågan på **TunnelDiagnosticLog** visas flera kolumner.


|***Namn** _ | _ *_Beskrivning_** |
|---        | ---               |
|**TimeGenerated** | tidsstämpeln för varje händelse, i UTC-tidszonen.|
|**OperationName** | händelsen som hände. Det kan vara antingen *TunnelConnected* eller *TunnelDisconnected*.|
| **Instans \_ s** | den gateway-roll instans som utlöste händelsen. Det kan vara antingen GatewayTenantWorker \_ i \_ 0 eller GatewayTenantWorker \_ i \_ 1, som är namnen på de två instanserna av gatewayen.|
| **Resurs** | Anger namnet på VPN-gatewayen. |
| **ResourceGroup** | anger resurs gruppen där gatewayen finns.|


Exempel på utdata:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="Exempel på en tunnel ansluten händelse som visas i TunnelDiagnosticLog.":::


**TunnelDiagnosticLog** är mycket användbart för att felsöka tidigare händelser om oväntade VPN-från kopplingar. Dess Lightweight-natur ger möjlighet att analysera stora tidsintervall över flera dagar med lite ansträngning.
Först när du har identifierat tidsstämpeln för en från koppling, kan du växla till den mer detaljerade analysen av **IKEdiagnosticLog** -tabellen för att gå djupare till orsaken till att anslutningarna är IPSec-relaterade.


Några felsökningstips:
- Om du ser en från kopplings händelse på en gateway-instans följt av en anslutnings händelse för en **annan** Gateway-instans på några sekunder, tittar du på en gateway-redundansväxling. Detta är vanligt vis ett förväntat beteende på grund av underhåll på en gateway-instans. Mer information om det här problemet finns i [om Azure VPN gateway-redundans](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy).
- Samma sak kommer att observeras om du avsiktligt kör en gateway-återställning på Azure-sidan – vilket orsakar en omstart av den aktiva Gateway-instansen. Läs mer om det här beteendet i [återställa en VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic).
- Om du ser en från kopplings händelse på en gateway-instans följt av en anslutnings händelse i **samma** Gateway-instans på några sekunder, kan du titta på ett nätverks fel som orsakar en DPD eller en från koppling som felaktigt skickats av den lokala enheten.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

**RouteDiagnosticLog** -tabellen spårar aktiviteten för statiskt ändrade vägar eller vägar som tas emot via BGP.

Här har du en exempel fråga som referens.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

I den här frågan på **RouteDiagnosticLog** visas flera kolumner.

|***Namn** _ | _ *_Beskrivning_** |
|---        | ---               |
|**TimeGenerated** | tidsstämpeln för varje händelse, i UTC-tidszonen.|
|**OperationName** | händelsen som hände. Kan vara antingen *StaticRouteUpdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent*.|
| **Meddelande** | information om vad som händer.|

I utdata visas användbar information om BGP-peers anslutna/frånkopplade och vägar som utbyts.

Exempel:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="Exempel på BGP Route Exchange-aktivitet som visas i RouteDiagnosticLog.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

**IKEDiagnosticLog** -tabellen erbjuder utförlig fel söknings loggning för IKE/IPSec. Detta är mycket användbart för att granska när du felsöker från kopplingar eller om du inte kan ansluta VPN-scenarier.

Här har du en exempel fråga som referens.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

I den här frågan på **IKEDiagnosticLog** visas flera kolumner.


|***Namn** _ | _ *_Beskrivning_** |
|---        | ---               |
|**TimeGenerated** | tidsstämpeln för varje händelse, i UTC-tidszonen.|
| **RemoteIP** | IP-adressen för den lokala VPN-enheten. I verkliga scenarier är det praktiskt att filtrera efter IP-adressen för den relevanta lokala enheten, och det finns fler än en. |
|**LocalIP** | IP-adressen för VPN Gateway vi felsöker. I verkliga scenarier är det praktiskt att filtrera efter IP-adressen för den relevanta VPN-gatewayen om det finns mer än en i din prenumeration. |
|**Händelse** | innehåller ett diagnostiskt meddelande som är användbart för fel sökning. De börjar vanligt vis med ett nyckelord och refererar till de åtgärder som utförs av Azure Gateway: **\[ send \]** anger en händelse som orsakas av ett IPSec-paket som skickats av Azure-gatewayen.  **\[ Receive \]** anger en händelse i följd av ett paket som tagits emot från den lokala enheten.  **\[ Lokalt \]** indikerar en åtgärd som har vidtagits lokalt av Azure-gatewayen. |


Observera hur RemoteIP-, LocalIP-och event-kolumner inte finns i den ursprungliga kolumn listan i AzureDiagnostics Database, men som läggs till i frågan genom att parsa utdata från kolumnen "meddelande" för att förenkla analysen.

Fel söknings tips:

- För att kunna identifiera starten av en IPSec-förhandling måste du hitta det första SA \_ init-meddelandet. Detta meddelande kan skickas via endera sidan av tunneln. Vem som än skickar det första paketet kallas "INITIATOR" i IPsec-terminologi medan den andra sidan blir "responder". Det första SA \_ init-meddelandet är alltid ett där rCookie = 0.

- Om IPsec-tunneln inte kan etableras, fortsätter Azure att försöka med några sekunder. Av den anledningen är fel sökning av "VPN ned"-problem mycket användbart på IKEdiagnosticLog eftersom du inte behöver vänta en stund för att återskapa problemet. Dessutom är felet i teorin alltid detsamma varje gång vi försöker, så att du kan zooma in till ett "exempel" som misslyckat förhandling när som helst.

- SA \_ -init innehåller de IPSec-parametrar som peer vill använda för den här IPsec-förhandlingen. Det officiella dokumentet   
[Standard-IPSec/IKE-parametrar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec) listar IPSec-parametrarna som stöds av Azure Gateway med standardinställningar.


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

Den senast tillgängliga tabellen för VPN-diagnostik är **P2SDiagnosticLog**. Den här tabellen spårar aktiviteten för plats-till-plats.

Här har du en exempel fråga som referens.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

I den här frågan på **P2SDiagnosticLog** visas flera kolumner.

|***Namn** _ | _ *_Beskrivning_** |
|---        | ---               |
|**TimeGenerated** | tidsstämpeln för varje händelse, i UTC-tidszonen.|
|**OperationName** | händelsen som hände. Kommer att vara *P2SLogEvent*.|
| **Meddelande** | information om vad som händer.|

I utdata visas alla platser för plats inställningar som gatewayen har tillämpat, samt IPsec-principerna på plats.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="Exempel på punkt-till-plats-anslutning som visas i P2SDiagnosticLog.":::

När en klient ansluter via IKEv2 eller OpenVPN pekar på plats, kommer tabellen att logga paket aktivitet, EAP/RADIUS-konversationer och lyckade/felaktiga resultat per användare.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="Exempel på EAP-autentisering som visas i P2SDiagnosticLog.":::

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar aviseringar för tunnel resurs loggar finns i [Konfigurera aviseringar på VPN gateway resurs loggar](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).

