---
title: Server delar för flera klient organisationer
titleSuffix: Azure Application Gateway
description: Den här sidan beskriver hur du kan använda serverdelar för flera klientorganisationer med Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: fe6ea6f348d796962141bd39ff858d891a29a2f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397696"
---
# <a name="application-gateway-support-for-multi-tenant-back-ends-such-as-app-service"></a>Application Gateway stöd för Server delar för flera klient organisationer, till exempel App Service

I arkitektur design för flera innehavare på webb servrar körs flera webbplatser på samma webb Server instans. Värdnamn används för att skilja mellan olika program som finns. Som standard ändrar inte Application Gateway den inkommande HTTP-rubriken från klienten. Rubriken skickas oförändrad till servern. Detta fungerar bra för medlemmar i Server delen, till exempel nätverkskort, skalnings uppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser och FQDN eftersom dessa inte förlitar sig på ett särskilt värd huvud eller SNI-tillägg för att matcha rätt slut punkt. Det finns dock många tjänster som Azure App Service Web Apps och Azure API Management som är flera klient organisationer och som förlitar sig på ett särskilt värd huvud eller SNI-tillägg för att lösas till rätt slut punkt. DNS-namnet på programmet, som i sin tur är det DNS-namn som är associerat med programgatewayen, skiljer sig vanligt vis från Server dels tjänstens domän namn. Därför är värd rubriken i den ursprungliga begäran som togs emot av Application Gateway inte samma som värd namnet för backend-tjänsten. På grund av detta, om inte värd huvudet i begäran från programgatewayen till Server delen ändras till Server dels tjänstens värdnamn, kan inte Server delen för flera klient organisationer lösa begäran till rätt slut punkt. 

Application Gateway erbjuder en funktion där användarna kan åsidosätta HTTP-värdrubriken i begäran baserat på namnet på serverdelen. Detta gör det möjligt att använda serverdelar med flera klientorganisationer som Azure App Service-webbappar och API-hantering. Den här funktionen är tillgänglig för både v1-, v2-, standard- och WAF-SKU:er. 

![åsidosättning av värd](./media/application-gateway-web-app-overview/host-override.png)

> [!NOTE]
> Detta gäller inte för Azure App Service Environment (ASE) eftersom ASE är en dedikerad resurs till skillnad från Azure App tjänst som är en resurs för flera innehavare.

## <a name="override-host-header-in-the-request"></a>Åsidosätt värd rubriken i begäran

Möjligheten att ange en åsidosättning av värden definieras i [http-inställningarna](./configuration-overview.md#http-settings) och kan tillämpas på alla backend-pooler när regeln skapas. Följande två sätt att åsidosätta värd huvud-och SNI-tillägget för Server delar med flera innehavare stöds:

- Möjligheten att ange värd namnet till ett fast värde som uttryckligen anges i HTTP-inställningarna. Den här funktionen säkerställer att värd huvudet åsidosätts till det här värdet för all trafik till backend-poolen där de specifika HTTP-inställningarna tillämpas. När du använder TLS-to-end-TLS används det åsidosatta värd namnet i SNI-tillägget. Den här funktionen möjliggör scenarier där en Server grupp för Server delen förväntar sig en värd rubrik som inte är samma som för den inkommande kund värd rubriken.

- Möjlighet att härleda värd namnet från IP eller FQDN för backend-poolens medlemmar. HTTP-inställningar innehåller också ett alternativ för att dynamiskt välja värd namnet från en medlem i Server delens FQDN om det kon figurer ATS med alternativet att härleda värd namnet från en enskild medlem i Server delen. När du använder TLS-to-end-TLS härleds detta värdnamn från det fullständiga domän namnet och används i SNI-tillägget. Den här funktionen möjliggör scenarier där en backend-pool kan ha två eller flera PaaS-tjänster för flera innehavare, t. ex. Azure Web Apps och begärans värd rubrik till varje medlem, innehåller värd namnet som härleds från dess FQDN. För att implementera det här scenariot använder vi en växel i HTTP-inställningarna som kallas [Välj värdnamn från Server dels adressen](./configuration-http-settings.md#pick-host-name-from-back-end-address) som dynamiskt åsidosätter värd rubriken i den ursprungliga begäran till den som anges i backend-poolen.  Om FQDN-adresspoolen till exempel innehåller "contoso11.azurewebsites.net" och "contoso22.azurewebsites.net", kommer den ursprungliga begär ande värd rubriken som contoso.com att åsidosättas till contoso11.azurewebsites.net eller contoso22.azurewebsites.net när begäran skickas till lämplig backend-server. 

  ![Scenario för webbappar](./media/application-gateway-web-app-overview/scenario.png)

Med den här funktionen skapar kunderna lämplig konfiguration genom att konfigurera alternativen i HTTP-inställningarna och de anpassade avsökningarna. Den här inställningen är sedan knuten till en lyssnare och en backend-pool med hjälp av en regel.

## <a name="special-considerations"></a>Särskilda överväganden

### <a name="tls-termination-and-end-to-end-tls-with-multi-tenant-services"></a>TLS-terminering och slut punkt till slut punkt för TLS med flera klient tjänster

Både TLS-kryptering och slut punkt till slut punkt stöds med tjänster för flera innehavare. För TLS-avslutning i Application Gateway fortsätter TLS-certifikatet att läggas till i Application Gateway-lyssnaren. Men i händelse av TLS-till-slutpunkt-TLS behöver inte betrodda Azure-tjänster som Azure App tjänst webbapparna tillåta Server delar i programgatewayen. Därför behöver du inte lägga till några autentiseringscertifikat. 

![slut punkt till slut punkt-TLS](./media/application-gateway-web-app-overview/end-to-end-ssl.png)

Observera att det inte finns något krav för att lägga till certifikat för autentisering när App Service har valts som Server del i bilden ovan.

### <a name="health-probe"></a>Hälsoavsökning

Att åsidosätta värd rubriken i **http-inställningarna** påverkar bara begäran och dess routning. det påverkar inte hälso avsöknings beteendet. För att funktionen för slutpunkt till slutpunkt ska fungera måste både avsöknings- och HTTP-inställningarna ändras så att de återspeglar korrekt konfiguration. Förutom att tillhandahålla möjligheten att ange en värd rubrik i avsöknings konfigurationen, stöder anpassade avsökningar även möjligheten att härleda värd rubriken från de konfigurerade HTTP-inställningarna. Den här konfigurationen kan anges med hjälp av `PickHostNameFromBackendHttpSettings`-parametern i avsökningskonfigurationen.

### <a name="redirection-to-app-services-url-scenario"></a>Omdirigering till App Serviceens URL-scenario

Det kan finnas scenarier där värd namnet i svaret från App Service kan dirigera slut användar webbläsaren till *. azurewebsites.net-värdnamnet i stället för den domän som är kopplad till Application Gateway. Det här problemet kan inträffa när:

- Du har konfigurerat omdirigeringen på App Service. Omdirigering kan vara lika enkelt som att lägga till ett avslutande snedstreck i begäran.
- Du har Azure AD-autentisering som gör omdirigeringen.

Information om hur du löser sådana fall finns i [Felsöka omdirigering till URL-problem i App Service](./troubleshoot-app-service-redirection-app-service-url.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar en Programgateway med en app för flera klienter, till exempel Azure App tjänstens webbapp som en medlem i en server del genom att gå [till konfigurera App Service webbappar med Application Gateway](./configure-web-app-portal.md)
