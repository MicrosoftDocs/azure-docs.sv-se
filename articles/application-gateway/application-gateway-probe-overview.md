---
title: Översikt över hälso övervakning för Azure Application Gateway
description: Azure Application Gateway övervakar hälsan för alla resurser i sin backend-pool och tar automatiskt bort alla resurser som betraktas som felaktiga från poolen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: f0e5a153efe26640e54f386600f07c7b3d4711d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89649060"
---
# <a name="application-gateway-health-monitoring-overview"></a>Översikt över Application Gateway Health Monitoring

Azure Application Gateway övervakar som standard hälsan för alla resurser i sin backend-pool och tar automatiskt bort alla resurser som betraktas som felaktiga från poolen. Application Gateway fortsätter att övervaka de felaktiga instanserna och lägger till dem i den felfria serverdelpoolen när de är tillgängliga och svarar på hälsokontroller. Som standard skickar Application Gateway hälso avsökningar med samma port som definieras i Server dels-HTTP-inställningarna. En anpassad avsöknings port kan konfigureras med en anpassad hälso avsökning.

Käll-IP-adressen Application Gateway använder för hälso avsökningar beror på backend-poolen:
 
- Om Server adressen i backend-poolen är en offentlig slut punkt är käll adressen programgatewayens offentliga IP-adress för klient delen.
- Om Server adressen i backend-poolen är en privat slut punkt kommer käll-IP-adressen från det privata IP-adressutrymmet i Application Gateway-undernätet.

![exempel på avsökning av Application Gateway][1]

Förutom att använda övervakning av standard hälso avsökning kan du också anpassa hälso avsökningen så att den passar ditt programs krav. I den här artikeln omfattas både standard-och anpassade hälso avsökningar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Standard hälso avsökning

En Programgateway konfigurerar automatiskt en standard hälso avsökning när du inte konfigurerar någon anpassad avsöknings konfiguration. Övervaknings beteendet fungerar genom att göra en HTTP GET-begäran till IP-adresserna eller FQDN som kon figurer ATS i backend-poolen. För standard avsökningar om http-inställningarna för Server delen har kon figurer ATS för HTTPS använder avsökningen HTTPS för att testa backend-servrarnas hälso tillstånd.

Exempel: du konfigurerar din Programgateway att använda backend-servrar A, B och C för att ta emot HTTP-nätverkstrafik på port 80. Standard hälso övervakningen testar de tre servrarna var 30: e sekund för ett felfritt HTTP-svar med 30 sekunders tids gräns för varje begäran. Ett felfritt HTTP-svar har en [status kod](https://msdn.microsoft.com/library/aa287675.aspx) mellan 200 och 399. I det här fallet kommer HTTP GET-begäran för hälso avsökningen att se ut http://127.0.0.1/ .

Om standard avsöknings kontrollen Miss lyckas för Server A slutar programgatewayen att vidarebefordra begär anden till den här servern. Standard avsökningen fortsätter fortfarande att söka efter servern var 30: e sekund. När servern svarar på en begäran från en standard hälso avsökning börjar Application Gateway vidarebefordra förfrågningarna till servern igen.

### <a name="default-health-probe-settings"></a>Inställningar för standard hälso avsökning

| Egenskapen avsökning | Värde | Beskrivning |
| --- | --- | --- |
| Avsöknings-URL |\<protocol\>://127.0.0.1:\<port\>/ |Protokollet och porten ärvs från Server delens HTTP-inställningar som avsökningen är kopplad till |
| Intervall |30 |Vänte tiden i sekunder innan nästa hälso avsökning skickas.|
| Timeout |30 |Hur lång tid i sekunder som Application Gateway väntar på ett avsöknings svar innan avsökningen markeras som ohälsosam. Om en avsökning returneras som felfri markeras motsvarande Server del omedelbart som felfri.|
| Tröskelvärde för ej felfri |3 |Reglerar hur många avsökningar som ska skickas om det uppstår ett fel i den normala hälso avsökningen. I v1 SKU skickas dessa ytterligare hälso avsökningar i snabb följd för att snabbt fastställa Server serverns hälso tillstånd och inte vänta på avsöknings intervallet. I händelse av v2 SKU: er väntar hälso avsökningar på intervallet. Backend-servern är markerad när det efterföljande antalet avsöknings fel uppnår tröskelvärdet. |

Standard avsökningen ser bara ut på \<protocol\> : \/ /127.0.0.1: \<port\> för att fastställa hälso status. Om du behöver konfigurera hälso avsökningen för att gå till en anpassad URL eller ändra andra inställningar måste du använda anpassade avsökningar. Mer information om HTTPS-avsökningar finns i [Översikt över TLS-terminering och slut punkt till slut punkt för TLS med Application Gateway](ssl-overview.md#for-probe-traffic).

### <a name="probe-intervals"></a>Avsöknings intervall

Alla instanser av Application Gateway avsöker Server delen oberoende av varandra. Samma avsöknings konfiguration gäller för varje Application Gateway instans. Om avsöknings konfigurationen till exempel skickar hälso avsökningar var 30: e sekund och programgatewayen har två instanser, skickar båda instanserna hälso avsökningen var 30: e sekund.

Även om det finns flera lyssnare avsöks varje lyssnare av Server delen oberoende av varandra. Om det till exempel finns två lyssnare som pekar på samma backend-pool på två olika portar (konfigurerade av två http-inställningar för Server delen), avsöker varje lyssnare samma server del oberoende av varandra. I det här fallet finns det två avsökningar från varje Application Gateway-instans för de två lyssnarna. Om det finns två instanser av Application Gateway i det här scenariot visas fyra avsökningar per det konfigurerade avsöknings intervallet i den virtuella datorns Server del.

## <a name="custom-health-probe"></a>Anpassad hälso avsökning

Med anpassade avsökningar kan du få mer detaljerad kontroll över hälso övervakningen. När du använder anpassade avsökningar kan du konfigurera ett anpassat värdnamn, URL-sökväg, avsöknings intervall och hur många misslyckade svar som ska accepteras innan du markerar backend-instansen som ohälsosam, osv.

### <a name="custom-health-probe-settings"></a>Inställningar för anpassad hälso avsökning

Följande tabell innehåller definitioner av egenskaperna för en anpassad hälso avsökning.

| Egenskapen avsökning | Beskrivning |
| --- | --- |
| Name |Namn på avsökningen. Det här namnet används för att identifiera och referera till avsökningen i Server dels-HTTP-inställningar. |
| Protokoll |Protokoll som används för att skicka avsökningen. Detta måste överensstämma med det protokoll som definierats i Server dels-HTTP-inställningarna som det är kopplat till|
| Värd |Värd namn att skicka avsökningen med. I v1 SKU används det här värdet endast för värd rubriken för avsöknings förfrågan. I v2 SKU används både som värd huvud och SNI |
| Sökväg |Den relativa sökvägen för avsökningen. En giltig sökväg börjar med "/" |
| Port |Om det här alternativet definieras används det som mål Port. Annars används samma port som de HTTP-inställningar som den är kopplad till. Den här egenskapen är endast tillgänglig i v2-SKU: n
| Intervall |Avsöknings intervall i sekunder. Det här värdet är tidsintervallet mellan två på varandra följande avsökningar |
| Timeout |Timeout för avsökning i sekunder. Om ett giltigt svar inte tas emot inom den här tids perioden markeras avsökningen som misslyckad  |
| Tröskelvärde för ej felfri |Antal nya försök för avsökning. Backend-servern är markerad när det efterföljande antalet avsöknings fel når tröskelvärdet |

### <a name="probe-matching"></a>Avsöknings matchning

Som standard betraktas ett HTTP (S)-svar med status kod mellan 200 och 399 som felfri. Anpassade hälso avsökningar stöder även två matchnings villkor. Matchnings villkor kan användas för att välja att ändra standard tolkningen av vad som gör ett felfritt svar.

Följande är matchnings villkor: 

- **Http-svar status kod matchning** -matchnings villkor för avsökning för att acceptera användarens angivna HTTP-svarskod eller svars kod intervall. Enskilda kommaavgränsade svars status koder eller ett intervall med status koder stöds.
- **Svars text matchning i HTTP-svar** – matchnings villkor för avsökning som söker efter http-svars text och matchningar med en användardefinierad sträng. Matchningen söker bara efter en användardefinierad sträng i svars texten och är inte en fullständig matchning av reguljära uttryck.

Matchnings villkor kan anges med hjälp av `New-AzApplicationGatewayProbeHealthResponseMatch` cmdleten.

Exempel:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
När matchnings villkoren har angetts kan den kopplas till avsöknings konfigurationen med hjälp av en `-Match` parameter i PowerShell.

## <a name="nsg-considerations"></a>NSG överväganden

Du måste tillåta inkommande Internet trafik på TCP-portarna 65503-65534 för Application Gateway v1 SKU och TCP-portarna 65200-65535 för v2-SKU: n med mål under nätet som **valfri** och källa som **GatewayManager** service tag. Det här port intervallet krävs för kommunikation mellan Azure-infrastrukturen.

Dessutom kan utgående Internet-anslutning inte blockeras och inkommande trafik från **AzureLoadBalancer** -taggen måste tillåtas.

Mer information finns i [Application Gateway konfigurations översikt](configuration-infrastructure.md#network-security-groups).

## <a name="next-steps"></a>Nästa steg
När du har lärt dig om Application Gateway hälso övervakning kan du konfigurera en [anpassad hälso avsökning](application-gateway-create-probe-portal.md) i Azure Portal eller en [anpassad hälso avsökning](application-gateway-create-probe-ps.md) med hjälp av PowerShell och Azure Resource Manager distributions modellen.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
