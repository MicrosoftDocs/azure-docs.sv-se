---
title: WebSocket-stöd i Azure Application Gateway
description: Application Gateway har inbyggt stöd för WebSocket i alla gatewaystorlekar. Det finns inga användar konfigurerbara inställningar.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: 59ca7708b24d2e75381290b80adeb671e2b49822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91362703"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Översikt över WebSocket-stöd i Application Gateway

Application Gateway har inbyggt stöd för WebSocket i alla gatewaystorlekar. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd. 

Med WebSocket-protokollet standardiserat i [RFC6455](https://tools.ietf.org/html/rfc6455) kan en fullständig duplex-kommunikation mellan en server och en klient under en tids krävande TCP-anslutning. Den här funktionen gör det möjligt att använda en mer interaktiv kommunikation mellan webb servern och klienten, som kan vara dubbelriktad utan att det krävs någon avsökning som krävs i HTTP-baserade implementeringar. WebSocket har låg belastning till skillnad från HTTP och kan återanvända samma TCP-anslutning för flera begär Anden/svar som resulterar i en mer effektiv användning av resurser. WebSocket-protokollen är utformade för att fungera över traditionella HTTP-portar på 80 och 443.

Du kan fortsätta att använda en HTTP-lyssnare som är standard på port 80 eller 443 för att ta emot WebSocket-trafik. WebSocket-trafik dirigeras sedan till den WebSocket-aktiverade backend-servern med hjälp av lämplig backend-pool som anges i Application Gateway-regler. Backend-servern måste svara på Application Gateway-avsökningarna, som beskrivs i avsnittet [Översikt över hälso avsökning](application-gateway-probe-overview.md) . Hälso avsökningar för Application Gateway är endast HTTP/HTTPS. Varje backend-server måste svara på HTTP-avsökningar för att Application Gateway ska kunna dirigera WebSocket-trafik till servern.

Den används i appar som drar nytta av snabb kommunikation i real tid, till exempel chatt, instrument panel och spelappar.

## <a name="how-does-websocket-work"></a>Hur fungerar WebSocket

För att upprätta en WebSocket-anslutning utbyts en unik HTTP-baserad hand skakning mellan klienten och servern. Om det lyckas, är applikations lager protokollet "uppgraderat" från HTTP till WebSockets med hjälp av den tidigare upprättade TCP-anslutningen. När detta inträffar är HTTP helt ur bilden. data kan skickas eller tas emot med hjälp av WebSocket-protokollet av båda slut punkterna, tills WebSocket-anslutningen har stängts. 

![Diagrammet jämför en klient som interagerar med en webb server och ansluter två gånger för att få två svar, med en WebSocket-interaktion, där en klient ansluter till en server en gång för att få flera svar.](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Listener-konfigurationselementet

En befintlig HTTP-lyssnare kan användas för att stödja WebSocket-trafik. Följande är ett kodfragment för ett httpListeners-element från en exempel mal len fil. Du behöver både HTTP-och HTTPS-lyssnare för att stödja WebSocket-och Secure WebSocket-trafik. På samma sätt kan du använda portalen eller Azure PowerShell för att skapa en Programgateway med lyssnare på port 80/443 för att stödja WebSocket-trafik.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Konfiguration av BackendAddressPool, BackendHttpSetting och routnings regel

En BackendAddressPool används för att definiera en backend-pool med WebSocket-aktiverade servrar. BackendHttpSetting definieras med Server dels port 80 och 443. Timeout-värdet för begäran i HTTP-inställningarna gäller även för WebSocket-sessionen. Ingen ändring krävs i regeln för routning, som används för att koppla lämplig lyssnare till motsvarande Server dels adress. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>WebSocket-aktiverad server del

Server delen måste ha en HTTP/HTTPS-webbserver som körs på den konfigurerade porten (vanligt vis 80/443) för att WebSocket ska fungera. Detta krav beror på att WebSocket-protokollet kräver att den första hand skakningen är HTTP med uppgradering till WebSocket-protokollet som ett rubrik fält. Följande är ett exempel på ett sidhuvud:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

En annan orsak till detta är att Application Gateway server dels hälso avsökning stöder endast HTTP-och HTTPS-protokoll. Om backend-servern inte svarar på HTTP-eller HTTPS-avsökningar, tas den bort från backend-poolen.

## <a name="next-steps"></a>Nästa steg

När du har lärt dig om WebSocket-stöd går du till [skapa en Programgateway](quick-create-powershell.md) för att komma igång med ett WebSocket-aktiverat webb program.