---
title: Så här fungerar en Application Gateway
description: Den här artikeln innehåller information om hur en Application Gateway accepterar inkommande begär Anden och dirigerar dem till Server delen.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: d2055bf812c3dc986a907d4358fa0e74e8af20fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599671"
---
# <a name="how-an-application-gateway-works"></a>Så här fungerar en Application Gateway

Den här artikeln förklarar hur en Programgateway accepterar inkommande begär Anden och dirigerar dem till Server delen.

![Hur en Application Gateway accepterar en begäran](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Hur en Application Gateway accepterar en begäran

1. Innan en klient skickar en begäran till en Programgateway, matchar den domän namnet för programgatewayen med hjälp av en Domain Name System-Server (DNS). Azure kontrollerar DNS-posten eftersom alla programgatewayer finns i azure.com-domänen.

2. Azure DNS returnerar IP-adressen till klienten, som är klient delens IP-adress för Application Gateway.

3. Programgatewayen godkänner inkommande trafik på en eller flera lyssnare. En lyssnare är en logisk entitet som söker efter anslutnings begär Anden. Den har kon figurer ATS med en IP-adress, protokoll och port nummer för klient delen för anslutningar från klienter till Application Gateway.

4. Om en brand vägg för webbaserade program (WAF) används, kontrollerar programgatewayen begärandehuvuden och brödtext, om det finns, mot WAF-regler. Den här åtgärden avgör om begäran är giltig eller om en säkerhets risk. Om begäran är giltig dirigeras den till Server delen. Om begäran inte är giltig och WAF är i förebyggande läge, blockeras den som ett säkerhetshot. Om det är i identifierings läge utvärderas och loggas begäran, men den vidarebefordras fortfarande till backend-servern.

Azure Application Gateway kan användas som en intern programs belastningsutjämnare eller som en belastningsutjämnare mot Internet. En Internetbaserad Application Gateway använder offentliga IP-adresser. DNS-namnet på en Internetbaserad Application Gateway kan matchas offentligt mot dess offentliga IP-adress. Därför kan Internet-riktade programgatewayer dirigera klient förfrågningar från Internet.

Interna programgatewayer använder endast privata IP-adresser. Om du använder en anpassad eller [privat DNS zon](../dns/private-dns-overview.md)ska domän namnet vara internt matchat till den privata IP-adressen för Application Gateway. Därför kan interna belastnings utjämning bara dirigera begär Anden från klienter med åtkomst till ett virtuellt nätverk för programgatewayen.

## <a name="how-an-application-gateway-routes-a-request"></a>Hur en Application Gateway dirigerar en begäran

Om en begäran är giltig och inte blockeras av WAF utvärderar programgatewayen regeln för routning av förfrågningar som är associerad med lyssnaren. Den här åtgärden avgör vilken backend-pool som begäran ska skickas till.

Programgatewayen, som baseras på regeln för anslutningsbegäran, bestämmer om alla begär Anden på lyssnaren ska dirigeras till en viss backend-pool, dirigera begär anden till olika backend-pooler baserat på URL-sökvägen eller omdirigera begär anden till en annan port eller extern plats.
>[!NOTE]
>Regler bearbetas i den ordning som de visas i portalen för v1 SKU. 

När programgatewayen väljer backend-poolen skickar den begäran till en av de felfria backend-servrarna i poolen (y. y. y. y). Serverns hälso tillstånd avgörs av en hälso avsökning. Om backend-poolen innehåller flera servrar, använder Application Gateway en algoritm för resursallokering för att dirigera begär Anden mellan felfria servrar. Den här belastningen balanserar förfrågningarna på servrarna.

När programgatewayen bestämmer backend-servern öppnas en ny TCP-session med backend-servern baserat på HTTP-inställningar. HTTP-inställningar anger protokoll, port och andra inställningar för routning som krävs för att upprätta en ny session med backend-servern.

Den port och det protokoll som används i HTTP-inställningarna avgör om trafiken mellan programgatewayen och backend-servrarna är krypterad (vilket gör TLS-till-slutpunkt-TLS) eller är okrypterad.

När en Programgateway skickar den ursprungliga begäran till backend-servern, sker en anpassad konfiguration som gjorts i HTTP-inställningarna för att åsidosätta värdnamn, sökväg och protokoll. Den här åtgärden upprätthåller cookie-baserad sessionsgräns, anslutning tömning, val av värd namn från Server delen och så vidare.

 >[!NOTE]
>Om backend-poolen:
> - **Är en offentlig slut punkt** använder Application Gateway dess offentliga IP-adress för att komma åt servern. Om det inte finns någon offentlig IP-adress för klient delen tilldelas en för utgående extern anslutning.
> - **Innehåller en internt matchad FQDN eller en privat IP-adress**, och programgatewayen dirigerar begäran till backend-servern genom att använda dess privata IP-adresser.
> - **Innehåller en extern slut punkt eller ett externt matchat fullständigt domän namn**, programgatewayen dirigerar begäran till backend-servern genom att använda dess offentliga IP-adress för klient delen. DNS-matchningen baseras på en privat DNS-zon eller en anpassad DNS-server, om den är konfigurerad, eller om den använder standard-Azure-standardvärdet. Om det inte finns någon offentlig IP-adress för klient delen tilldelas en för utgående extern anslutning.

### <a name="modifications-to-the-request"></a>Ändringar i begäran

En Programgateway infogar fyra ytterligare huvuden för alla begär Anden innan den vidarebefordrar begär anden till Server delen. Dessa huvuden är x-vidarebefordrade – för, x-vidarebefordrade-proto, x-forwarded-port och x-original-Host. Formatet för x-vidarebefordrad – för rubrik är en kommaavgränsad lista över IP: port.

Giltiga värden för x-forwarded-proto är HTTP eller HTTPS. X-vidarebefordrad Port anger den port där begäran har nått Application Gateway. X-ursprunglig-värd huvud innehåller det ursprungliga värd huvudet som begäran anlänt till. Den här rubriken är användbar i Azures webbplats integrering, där den inkommande värd rubriken ändras innan trafik dirigeras till Server delen. Om session tillhörighet är aktiverat som ett alternativ lägger den till en Gateway-hanterad tillhörighets-cookie.

Du kan konfigurera Application Gateway för att ändra begär ande-och svarshuvuden och URL: er med hjälp av [Skriv-HTTP-huvuden och URL](rewrite-http-headers-url.md) eller ändra URI-sökvägen med hjälp av en sökväg – åsidosättande inställning. Men om det inte har kon figurer ATS att göra det, är alla inkommande begär Anden proxy till Server delen.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Application Gateway-komponenter](application-gateway-components.md)
