---
title: Application Gateway-komponenter
description: Den här artikeln innehåller information om de olika komponenterna i en Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: surmb
ms.openlocfilehash: ebd06b0b78ee511dce535ff4220df03087fb6906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88723324"
---
# <a name="application-gateway-components"></a>Application Gateway-komponenter

 En Programgateway fungerar som en enda kontakt punkt för klienterna. Den distribuerar inkommande program trafik över flera backend-pooler, inklusive virtuella Azure-datorer, skalnings uppsättningar för virtuella datorer, Azure App Service och lokala/externa servrar. För att distribuera trafik använder en Application Gateway flera komponenter som beskrivs i den här artikeln.

![De komponenter som används i en Application Gateway](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>IP-adresser för klient del

En IP-adress för klient delen är den IP-adress som är associerad med en Application Gateway. Du kan konfigurera en Programgateway så att den har en offentlig IP-adress, en privat IP-adress eller både och. En Application Gateway stöder en offentlig eller en privat IP-adress. Ditt virtuella nätverk och den offentliga IP-adressen måste finnas på samma plats som din Application Gateway. När den har skapats associeras en IP-adress för klient delen med en lyssnare.

### <a name="static-versus-dynamic-public-ip-address"></a>Statisk kontra dynamisk offentlig IP-adress

Azure Application Gateway v2-SKU: n kan konfigureras för att stödja både statisk, statisk IP-adress och statisk offentlig IP-adress, eller endast en statisk offentlig IP-adress. Den kan inte konfigureras så att den endast stöder statisk intern IP-adress.

V1-SKU: n kan konfigureras för att stödja statisk eller dynamisk intern IP-adress och dynamisk offentlig IP-adress. Den dynamiska IP-adressen för Application Gateway ändras inte på en gateway som körs. Den kan bara ändras när du stoppar eller startar gatewayen. Den ändras inte vid systemfel, uppdateringar, Azure Host-uppdateringar osv. 

Det DNS-namn som är associerat med en Programgateway ändras inte över gatewayens livs längd. Därför bör du använda ett CNAME-alias och peka det mot DNS-adressen för programgatewayen.

## <a name="listeners"></a>Lyssnare

En lyssnare är en logisk entitet som söker efter inkommande anslutnings begär Anden. En lyssnare accepterar en begäran om protokollet, porten, värd namnet och IP-adressen som är kopplade till begäran matchar samma element som är associerade med lyssnar konfigurationen.

Innan du använder en Application Gateway måste du lägga till minst en lyssnare. Det kan finnas flera lyssnare kopplade till en Programgateway och de kan användas för samma protokoll.

När en lyssnare identifierar inkommande begär Anden från klienter dirigerar programgatewayen dessa förfrågningar till medlemmar i backend-poolen som kon figurer ATS i regeln.

Lyssnare stöder följande portar och protokoll.

### <a name="ports"></a>Portar

En port är den plats där en lyssnare lyssnar efter klient förfrågan. Du kan konfigurera portar mellan 1 och 65502 för v1 SKU och 1 till 65199 för v2-SKU: n.

### <a name="protocols"></a>Protokoll

Application Gateway stöder fyra protokoll: HTTP, HTTPS, HTTP/2 och WebSocket:
>[!NOTE]
>Protokoll stöd för HTTP/2 är endast tillgängligt för klienter som ansluter till Application Gateway-lyssnare. Kommunikationen till backend-serverns pooler är alltid över HTTP/1.1. Stöd för HTTP/2 är inaktiverat som standard. Du kan välja att aktivera det.

- Ange mellan HTTP-och HTTPS-protokollen i lyssnar konfigurationen.
- Stöd för [WebSockets-och http/2-protokoll](features.md#websocket-and-http2-traffic) anges internt och [WebSocket-stöd](application-gateway-websocket.md) är aktiverat som standard. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd. Använd WebSockets med både HTTP-och HTTPS-lyssnare.

Använd en HTTPS-lyssnare för TLS-avslutning. En HTTPS-lyssnare avlastar krypterings-och dekrypterings arbetet till din Application Gateway, så dina webb servrar är inte inaktuella vid omkostnaderna.

### <a name="custom-error-pages"></a>Anpassade felsidor

Med Application Gateway kan du skapa anpassade felsidor istället för att Visa standard fel sidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida. Application Gateway visar en anpassad felsida när en förfrågan inte kan komma åt Server delen.

Mer information finns i [anpassade felsidor för Application Gateway](custom-error.md).

### <a name="types-of-listeners"></a>Typer av lyssnare

Det finns två typer av lyssnare:

- **Basic**. Den här typen av lyssnare lyssnar på en enda domän plats, där den har en enda DNS-mappning till programgatewayens IP-adress. Den här lyssnar konfigurationen krävs när du är värd för en enskild plats bakom en Programgateway.

- **Flera platser**. Den här lyssnar konfigurationen krävs om du vill konfigurera routning baserat på värdnamn eller domän namn för mer än ett webb program på samma Application Gateway. Den här funktionen gör att du kan konfigurera en mer effektiv topologi för dina distributioner genom att lägga till fler än 100 webbplatser i samma appgateway. Varje webbplats kan dirigeras till en egen serverdelspool. Tänk dig till exempel att de tre domänerna contoso.com, fabrikam.com och adatum.com pekar på appgatewayens IP-adress. Du skapar tre [lyssnare för flera platser](multiple-site-overview.md) och konfigurerar varje lyssnare för respektive port-och protokoll inställning. 

    Du kan också definiera värdnamn med jokertecken i lyssnare för flera platser och upp till 5 värdnamn per lyssnare. Mer information finns i [namn på jokertecken i lyssnare (för hands version)](multiple-site-overview.md#wildcard-host-names-in-listener-preview).

    Mer information om hur du konfigurerar en lyssnare för flera platser finns [i Application Gateway som är värd för flera platser i att använda Azure Portal](create-multiple-sites-portal.md).

När du har skapat en lyssnare associerar du den med en regel för anslutningsbegäran. Den här regeln avgör hur begäran som tagits emot på lyssnaren ska dirigeras till Server delen. Regeln för routning av förfrågningar innehåller även den backend-pool som ska dirigeras till och HTTP-inställningen där backend-porten, protokollet osv.

## <a name="request-routing-rules"></a>Begär routningsregler

En regel för routning av förfrågningar är en viktig komponent i en Programgateway eftersom den avgör hur trafiken ska dirigeras i lyssnaren. Regeln binder lyssnaren, backend-serverpoolen och Server delens HTTP-inställningar.

När en lyssnare accepterar en begäran vidarebefordrar regeln för routning av förfrågningar begäran till Server delen eller dirigerar om den någon annan stans. Om begäran vidarebefordras till Server delen definierar regeln för routning av förfrågningar vilken backend-server pool som vidarebefordrar den till. Regeln för routning av förfrågningar avgör också om huvudena i begäran ska skrivas om. En lyssnare kan kopplas till en regel.

Det finns två typer av regler för routning av förfrågningar:

- **Basic**. Alla begär Anden på den associerade lyssnaren (till exempel blog.contoso.com/*) vidarebefordras till den associerade backend-poolen med hjälp av den associerade HTTP-inställningen.

- **Sökväg-baserad**. Med den här regeln kan du dirigera begär Anden på den associerade lyssnaren till en viss backend-pool, baserat på URL: en i begäran. Om sökvägen till URL: en i en begäran matchar Sök vägs mönstret i en Sök vägs-baserad regel dirigerar regeln den begäran. Sök vägs mönstret tillämpas bara på URL-sökvägen, inte dess frågeparametrar. Om URL-sökvägen på en lyssnare-begäran inte matchar någon av Sök vägs reglerna dirigerar den begäran till standard server delen och HTTP-inställningarna.

Mer information finns i [URL-baserad routning](url-route-overview.md).

### <a name="redirection-support"></a>Stöd för omdirigering

Med regeln för routning av begär Anden kan du också dirigera om trafik på programgatewayen. Detta är en allmän mekanism för omdirigering, så att du kan omdirigera till och från alla portar som du definierar med hjälp av regler.

Du kan välja det omdirigerings mål som ska vara en annan lyssnare (som kan hjälpa dig att aktivera automatisk HTTP till HTTPS-omdirigering) eller en extern plats. Du kan också välja att omdirigeringen ska vara tillfällig eller permanent, eller lägga till URI-sökvägen och frågesträngen till den omdirigerade URL: en.

Mer information finns i [omdirigera trafik på din Application Gateway](redirect-overview.md).

### <a name="rewrite-http-headers-and-url"></a>Skriva om HTTP-huvuden och URL

Genom att använda regler för att skriva om, kan du lägga till, ta bort eller uppdatera HTTP (S)-begäran och svars rubriker samt URL-sökväg och parametrar för frågesträngar som begär ande-och svars paket flyttas mellan klienten och backend-pooler via Application Gateway.

Parametrarna headers och URL kan anges till statiska värden eller till andra huvuden och servervariabler. Detta hjälper till med viktiga användnings fall, till exempel att extrahera klientens IP-adresser, ta bort känslig information om Server delen, lägga till mer säkerhet och så vidare.

Mer information finns i [skriva om HTTP-sidhuvuden och URL: en i Application Gateway](rewrite-http-headers-url.md).

## <a name="http-settings"></a>HTTP-inställningar

En Programgateway dirigerar trafik till backend-servrarna (anges i regeln för routning av förfrågningar som inkluderar HTTP-inställningar) med hjälp av port numret, protokollet och andra inställningar som beskrivs i den här komponenten.

Porten och protokollet som används i HTTP-inställningarna avgör om trafiken mellan programgatewayen och backend-servrarna är krypterad (vilket ger en slut punkt till slutpunkt-TLS) eller okrypterad.

Den här komponenten används också för att:

- Ta reda på om en användarsession ska behållas på samma server med hjälp av [cookie-baserad session tillhörighet](features.md#session-affinity).

- Ta bort medlemmar i Server delens medlemmar genom att använda [anslutnings tömning](features.md#connection-draining).

- Associera en anpassad avsökning för att övervaka Server dels hälsan, ange tids gräns intervallet för begäran, Åsidosätt värdnamn och sökväg i begäran och ge enkel klickning för att ange inställningar för den App Service server delen.

## <a name="backend-pools"></a>Backend-pooler

En backend-pool dirigerar begäran till backend-servrar, som servar begäran. Backend-pooler kan innehålla:

- Nätverkskort
- Skalningsuppsättningar för virtuella datorer
- Offentliga IP-adresser
- Interna IP-adresser
- FQDN
- Server delar för flera klient organisationer (till exempel App Service)

Application Gateway medlemmar i Server delen inte är knutna till en tillgänglighets uppsättning. En Application Gateway kan kommunicera med instanser utanför det virtuella nätverk där den finns. Därför kan medlemmarna i backend-pooler vara mellan kluster, i flera data Center eller utanför Azure, så länge det finns en IP-anslutning.

Om du använder interna IP-adresser som medlemmar i backend-poolen måste du använda [peering för virtuella nätverk](../virtual-network/virtual-network-peering-overview.md) eller en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Peering av virtuella nätverk stöds och är ett bra alternativ för belastnings Utjämnings trafik i andra virtuella nätverk.

En Programgateway kan också kommunicera med lokala servrar när de är anslutna via Azure ExpressRoute eller VPN-tunnlar om trafik tillåts.

Du kan skapa olika backend-pooler för olika typer av begär Anden. Du kan till exempel skapa en backend-pool för allmänna begär Anden och sedan en annan backend-pool för förfrågningar till mikrotjänster för ditt program.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

Som standard övervakar en Programgateway hälsan för alla resurser i sin backend-pool och tar automatiskt bort felaktiga. Den övervakar sedan skadade instanser och lägger tillbaka dem till den felfria backend-poolen när de blir tillgängliga och svarar på hälso avsökningar.

Förutom att använda övervakning av standard hälso avsökning kan du också anpassa hälso avsökningen så att den passar ditt programs krav. Anpassade avsökningar ger mer detaljerad kontroll över hälso övervakningen. När du använder anpassade avsökningar kan du konfigurera ett anpassat värdnamn, URL-sökväg, avsöknings intervall och hur många misslyckade svar som ska accepteras innan du markerar backend-instansen som ohälsosam, anpassade status koder och svars text matchning osv. Vi rekommenderar att du konfigurerar anpassade avsökningar för att övervaka hälso tillståndet för varje backend-pool.

Mer information finns i [övervaka hälso tillståndet för din Application Gateway](../application-gateway/application-gateway-probe-overview.md).

## <a name="next-steps"></a>Nästa steg

Skapa en Programgateway:

* [I Azure Portal](quick-create-portal.md)
* [Med hjälp av Azure PowerShell](quick-create-powershell.md)
* [Med hjälp av Azure CLI](quick-create-cli.md)
