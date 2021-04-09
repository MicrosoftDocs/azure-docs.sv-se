---
title: Felsöka dåliga Gateway-fel – Azure Application Gateway
description: 'Lär dig hur du felsöker Application Gateway server fel: 502-webb servern tog emot ett ogiltigt svar när den fungerade som en gateway eller proxyserver.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: d44f9109540c3899ab50bd5c4c02afa19045bafb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182945"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Felsöka felaktig gateway i Application Gateway

Lär dig hur du felsöker Felaktiga Gateway-fel (502) som tas emot när du använder Azure Application Gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt

När du har konfigurerat en Programgateway är ett av de fel som visas är "Server fel: 502-webb servern tog emot ett ogiltigt svar när den fungerade som en gateway eller proxyserver". Det här felet kan inträffa av följande skäl:

* NSG, UDR eller anpassad DNS blockerar åtkomsten till medlemmar i Server delen.
* Virtuella backend-datorer eller instanser av skalnings uppsättningar för virtuella datorer svarar inte på standard hälso avsökningen.
* Ogiltig eller felaktig konfiguration av anpassade hälsoavsökningar.
* Azure Application gatewayens [backend-pool är inte konfigurerad eller tom](#empty-backendaddresspool).
* Ingen av de virtuella datorerna eller instanserna i den [virtuella datorns skalnings uppsättning är felfria](#unhealthy-instances-in-backendaddresspool).
* [Begär timeout-eller anslutnings problem](#request-time-out) med användar förfrågningar.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Nätverks säkerhets grupp, användardefinierad väg eller anpassat DNS-problem

### <a name="cause"></a>Orsak

Om åtkomst till Server delen blockeras på grund av en NSG, UDR eller anpassad DNS, kan Application Gateway-instanser inte nå backend-poolen. Detta orsakar avsöknings fel, vilket resulterar i 502 fel.

NSG/UDR kan finnas antingen i Application Gateway-undernätet eller under nätet där de virtuella program datorerna distribueras.

På samma sätt kan förekomsten av en anpassad DNS i VNet också orsaka problem. Ett FQDN som används för medlemmar i Server delens medlemmar kanske inte kan lösas korrekt av den konfigurerade DNS-servern för det virtuella nätverket.

### <a name="solution"></a>Lösning

Verifiera NSG, UDR och DNS-konfiguration genom att gå igenom följande steg:

* Kontrol lera NSG: er som är associerade med Application Gateway-undernätet. Se till att kommunikationen med Server delen inte är blockerad.
* Kontrol lera UDR som är associerade med Application Gateway-undernätet. Se till att UDR inte dirigerar trafik från backend-undernätet. Sök till exempel efter routning till virtuella nätverks enheter eller standard vägar som annonseras till Application Gateway-undernätet via ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Kontrol lera den effektiva NSG och dirigera med den virtuella server delen

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Kontrol lera närvaron av anpassad DNS i VNet. DNS kan kontrol leras genom att se information om VNet-egenskaperna i utdata.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Om den är tillgänglig kontrollerar du att DNS-servern kan matcha FQDN-medlemmarnas FQDN korrekt.

## <a name="problems-with-default-health-probe"></a>Problem med standard hälso avsökning

### <a name="cause"></a>Orsak

502-fel kan också vara frekventa indikatorer som standard hälso avsökningen inte kan komma åt backend-VM: ar.

När en instans av en Application Gateway har tillhandahållits, konfigurerar den automatiskt en standard hälso avsökning för varje BackendAddressPool med hjälp av egenskaperna för BackendHttpSetting. Inga användarindata krävs för att ställa in den här avsökningen. När en belastnings Utjämnings regel har kon figurer ATS görs en association mellan en BackendHttpSetting och en BackendAddressPool. En standard avsökning har kon figurer ATS för var och en av dessa associationer och programgatewayen startar en regelbunden hälso kontroll anslutning till varje instans i BackendAddressPool på den port som anges i BackendHttpSetting-elementet. 

I följande tabell visas de värden som är associerade med standard hälso avsökningen:

| Egenskapen avsökning | Värde | Beskrivning |
| --- | --- | --- |
| Avsöknings-URL |`http://127.0.0.1/` |URL-sökväg |
| Intervall |30 |Avsöknings intervall i sekunder |
| Timeout |30 |Timeout för avsökning i sekunder |
| Tröskelvärde för ej felfri |3 |Antal nya försök för avsökning. Backend-servern är markerad när det efterföljande antalet avsöknings fel uppnår tröskelvärdet. |

### <a name="solution"></a>Lösning

* Kontrol lera att en standard plats har kon figurer ATS och att den lyssnar på 127.0.0.1.
* Om BackendHttpSetting anger en annan port än 80, ska standard platsen konfigureras för att lyssna på den porten.
* Anropet till `http://127.0.0.1:port` ska returnera en HTTP-resultat kod på 200. Detta bör returneras inom 30 sekunders tids period.
* Kontrol lera att den konfigurerade porten är öppen och att det inte finns några brand Väggs regler eller Azure-nätverks säkerhets grupper, som blockerar inkommande eller utgående trafik på den konfigurerade porten.
* Om de klassiska virtuella Azure-datorerna eller moln tjänsten används med ett fullständigt domän namn eller en offentlig IP-adress kontrollerar du att motsvarande [slut punkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=%2fazure%2fapplication-gateway%2ftoc.json) är öppen.
* Om den virtuella datorn har kon figurer ATS via Azure Resource Manager och är utanför det virtuella nätverk där programgatewayen distribueras, måste en [nätverks säkerhets grupp](../virtual-network/network-security-groups-overview.md) konfigureras för att tillåta åtkomst på önskad port.

## <a name="problems-with-custom-health-probe"></a>Problem med anpassad hälso avsökning

### <a name="cause"></a>Orsak

Anpassade hälso avsökningar ger ytterligare flexibilitet för standard beteendet för avsökning. När du använder anpassade avsökningar kan du konfigurera avsöknings intervallet, URL: en, sökvägen till testet och hur många misslyckade svar som ska accepteras innan du markerar backend-instansen som ohälsosam.

Följande ytterligare egenskaper läggs till:

| Egenskapen avsökning | Beskrivning |
| --- | --- |
| Name |Namn på avsökningen. Det här namnet används för att referera till avsökningen i HTTP-inställningarna på backend-sidan. |
| Protokoll |Protokoll som används för att skicka avsökningen. Avsökningen använder protokollet som definierats i Server delens HTTP-inställningar |
| Värd |Värdnamn för att skicka avsökningen. Gäller endast när flera platser har kon figurer ATS på Application Gateway. Detta skiljer sig från värd namnet för den virtuella datorn. |
| Sökväg |Den relativa sökvägen för avsökningen. Den giltiga sökvägen börjar från/. Avsökningen skickas till \<protocol\> :// \<host\> :\<port\>\<path\> |
| Intervall |Avsöknings intervall i sekunder. Detta är ett tidsintervall mellan två på varandra följande avsökningar. |
| Timeout |Timeout för avsökning i sekunder. Om ett giltigt svar inte tas emot inom den här tids perioden markeras avsökningen som misslyckad. |
| Tröskelvärde för ej felfri |Antal nya försök för avsökning. Backend-servern är markerad när det efterföljande antalet avsöknings fel uppnår tröskelvärdet. |

### <a name="solution"></a>Lösning

Kontrol lera att den anpassade hälso avsökningen är korrekt konfigurerad som föregående tabell. Förutom föregående fel söknings steg kontrollerar du också följande:

* Se till att avsökningen anges korrekt enligt [hand boken](application-gateway-create-probe-ps.md).
* Om programgatewayen har kon figurer ATS för en enda plats, som standard, ska värd namnet anges som `127.0.0.1` , om inget annat anges i anpassad avsökning.
* Se till att ett anrop till http:// \<host\> : \<port\> \<path\> returnerar en http-resultat kod på 200.
* Se till att intervallet, tids gränsen och UnhealtyThreshold ligger inom de acceptabla intervallen.
* Om du använder en HTTPS-avsökning kontrollerar du att backend-servern inte kräver SNI genom att konfigurera ett återställnings certifikat på backend-servern.

## <a name="request-time-out"></a>Timeout för begäran

### <a name="cause"></a>Orsak

När en användarbegäran tas emot tillämpar programgatewayen de konfigurerade reglerna på begäran och dirigerar den till en instans på backend-poolen. Det väntar ett konfigurerbart tidsintervall för ett svar från backend-instansen. Som standard är det här intervallet **20** sekunder. Om Application Gateway inte får något svar från backend-appen i det här intervallet får användaren ett 502-fel.

### <a name="solution"></a>Lösning

Med Application Gateway kan du konfigurera den här inställningen via BackendHttpSetting, som sedan kan tillämpas på olika pooler. Olika Server dels pooler kan ha olika BackendHttpSetting och en annan begäran-timeout har kon figurer ATS.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Tom BackendAddressPool

### <a name="cause"></a>Orsak

Om programgatewayen inte har några virtuella datorer eller skalnings uppsättningar för virtuella datorer som kon figurer ATS i backend-adresspoolen, kan den inte dirigera någon kund förfrågan och skickar ett felaktigt Gateway-fel.

### <a name="solution"></a>Lösning

Se till att backend-adresspoolen inte är tom. Detta kan göras antingen via PowerShell, CLI eller Portal.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Utdata från föregående cmdlet ska innehålla en icke-tom backend-adresspool. I följande exempel visas två pooler som har kon figurer ATS med ett fullständigt domän namn eller IP-adresser för de virtuella datorerna i Server delen. Etablerings statusen för BackendAddressPool måste vara lyckades.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Skadade instanser i BackendAddressPool

### <a name="cause"></a>Orsak

Om alla instanser av BackendAddressPool inte är felfria har programgatewayen ingen backend-server för att dirigera användar förfrågan till. Detta kan också vara fallet när backend-instanser är felfria men inte har det program som krävs distribuerat.

### <a name="solution"></a>Lösning

Kontrol lera att instanserna är felfria och att programmet har kon figurer ATS korrekt. Kontrol lera om Server dels instanserna kan svara på ett ping från en annan virtuell dator i samma VNet. Om den har kon figurer ATS med en offentlig slut punkt bör du se till att en webb program förfrågan till webb programmet kan betjänas.

## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet öppnar du ett [support ärende](https://azure.microsoft.com/support/options/).