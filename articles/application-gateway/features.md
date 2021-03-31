---
title: Azure Application Gateway-funktioner
description: Läs mer om funktioner i Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: victorh
ms.openlocfilehash: ba9f42bc932a37e1052f17db2ae00413e0769d59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91355745"
---
# <a name="azure-application-gateway-features"></a>Azure Application Gateway-funktioner

[Azure Application Gateway](overview.md) är en belastningsutjämnare för webb trafik som gör att du kan hantera trafik till dina webb program.

![Begreppsmässigt om Application Gateway](media/overview/figure1-720.png)

Application Gateway innehåller följande funktioner:

- [Secure Sockets Layer (SSL/TLS) avslutning](#secure-sockets-layer-ssltls-termination)
- [Automatisk skalning](#autoscaling)
- [Zonredundans](#zone-redundancy)
- [Statisk VIP](#static-vip)
- [Brandvägg för webbaserade program](#web-application-firewall)
- [Ingress-kontrollant för AKS](#ingress-controller-for-aks)
- [URL-baserad routning](#url-based-routing)
- [Värd för flera platser](#multiple-site-hosting)
- [Omdirigering](#redirection)
- [Tillhörighet för session](#session-affinity)
- [Websocket- och HTTP/2-trafik](#websocket-and-http2-traffic)
- [Anslutningstömning](#connection-draining)
- [Anpassade felsidor](#custom-error-pages)
- [Skriva om HTTP-huvuden och URL](#rewrite-http-headers-and-url)
- [Storlekar](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Secure Sockets Layer (SSL/TLS) avslutning

Application Gateway stöder SSL/TLS-avslutning på gatewayen, efter vilken trafiken vanligt vis flödar okrypterad till backend-servrarna. Den här funktionen bidrar till att befria webbservrarna från kostsam kryptering och dekryptering. Men ibland är okrypterad kommunikation med servrarna inte ett acceptabelt alternativ. Detta kan bero på säkerhets krav, krav på efterlevnad eller att programmet bara kan godkänna en säker anslutning. Programgatewayen stöder SSL/TLS-kryptering från slut punkt till slut punkt för dessa program.

Mer information finns i [Översikt över SSL-terminering och slut punkt till slut punkt SSL med Application Gateway](ssl-overview.md)

## <a name="autoscaling"></a>Automatisk skalning

Application Gateway Standard_v2 stöder automatisk skalning och kan skalas upp eller ned baserat på ändrade trafik inläsnings mönster. Automatisk skalning tar även bort behovet av att välja distributionsstorlek eller instansantal under etablering. 

Mer information om Application Gateway Standard_v2 funktioner finns i avsnittet [autoskalning v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Zonredundans

En Standard_v2 Application Gateway kan omfatta flera Tillgänglighetszoner, vilket ger bättre fel återhämtning och tar bort behovet av att etablera separata programgatewayer i varje zon.

## <a name="static-vip"></a>Statisk VIP

Application Gateway Standard_v2 SKU stöder enbart statisk VIP-typ. Detta säkerställer att den VIP som är associerad med Application Gateway inte ändras ens under Application Gatewayens livstid.

## <a name="web-application-firewall"></a>Brandvägg för webbaserade program

Brand vägg för webbaserade program (WAF) är en tjänst som tillhandahåller centraliserat skydd av dina webb program från vanliga sårbarheter och sårbarheter. WAF baseras på regler från [OWASP (Open Web Application Security Project) Core Rule set](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1 (endast WAF_v2), 3,0 och 2.2.9. 

Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Bland annat är SQL-inmatningsattacker och skriptangrepp mellan webbplatser vanliga. Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av många skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en brand vägg för webbaserade program.

Mer information finns i [Vad är en brand vägg för Azure Web Application?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Ingress-kontrollant för AKS
Med Application Gateway ingångs kontroll (AGIC) kan du använda Application Gateway som ingångs punkt för ett [Azure Kubernetes service-kluster (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . 

Ingångs kontrollen körs som en POD i AKS-klustret och använder [Kubernetes ingångs resurser](https://kubernetes.io/docs/concepts/services-networking/ingress/) och konverterar dem till en Application Gateway konfiguration, vilket gör att gatewayen kan belastningsutjämna trafik till Kubernetes poddar. Ingångs styrenheten har endast stöd för Application Gateway Standard_v2 och WAF_v2 SKU: er. 

Mer information finns i [Application Gateway ingress-styrenhet (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>URL-baserad routning

URL-sökvägsbaserad routning låter dig routa trafik till serverdels-serverpooler baserat på URL-sökvägen till begäranden. Ett av scenarierna är att dirigera begäranden för olika innehållstyper till olika pooler.

Till exempel dirigeras begäranden för `http://contoso.com/video/*` till VideoServerPool och `http://contoso.com/images/*` dirigeras till ImageServerPool. DefaultServerPool väljs om inget av sökvägsmönstren matchar.

Mer information finns i [Översikt över URL-sökväg baserad routning](url-route-overview.md).

## <a name="multiple-site-hosting"></a>Värd för flera platser

Med Application Gateway kan du konfigurera routning baserat på värdnamn eller domän namn för mer än ett webb program på samma Application Gateway. Den här funktionen gör att du kan konfigurera en mer effektiv topologi för dina distributioner genom att lägga till fler än 100 webbplatser i samma appgateway. Varje webbplats kan dirigeras till en egen serverdelspool. Tänk dig till exempel att de tre domänerna contoso.com, fabrikam.com och adatum.com pekar på appgatewayens IP-adress. Du skapar tre lyssnare för flera platser och konfigurerar varje lyssnare enligt respektive inställningar för port och protokoll. 

Begär Anden för `http://contoso.com` dirigeras till ContosoServerPool, `http://fabrikam.com` dirigeras till FabrikamServerPool och så vidare.

På samma sätt kan två underdomäner i samma överordnade domän finnas på samma distribution av en programgateway. Exempel på användning av underdomäner kan vara `http://blog.contoso.com` och `http://app.contoso.com` på samma distribution av en programgateway. Mer information finns i [Application Gateway flera webbplats värdar](multiple-site-overview.md).

Du kan också definiera värdnamn med jokertecken i lyssnare för flera platser och upp till 5 värdnamn per lyssnare. Mer information finns i [namn på jokertecken i lyssnare (för hands version)](multiple-site-overview.md#wildcard-host-names-in-listener-preview).

## <a name="redirection"></a>Omdirigering

Ett vanligt scenario för många webbappar är att stödja automatisk HTTP till HTTPS-omdirigering för att säkerställa att all kommunikation mellan en app och dess användare sker via en krypterad sökväg.

Tidigare kan du ha använt tekniker, till exempel att en dedikerad pool ska skapas vars enda syfte är att omdirigera begär Anden som tas emot på HTTP till HTTPS. Application Gateway stöder möjligheten att omdirigera trafik på programgatewayen. Detta förenklar programkonfigurationen, optimerar resursanvändningen och stöder nya omdirigeringsscenarier, inklusive global och sökvägsbaserade omdirigering. Stöd för Application Gateway omdirigering är inte begränsat till enbart HTTP till HTTPS-omdirigering. Det här är en allmän mekanism för omdirigering, så du kan omdirigera från och till valfri port som du anger med regler. Den har även stöd för omdirigering till en extern plats.

Stöd för Application Gateway-stöd har följande funktioner:

- Global omdirigering från en port till en annan port på gatewayen. Det möjliggör HTTP till HTTPS-omdirigering på en webbplats.
- Sökvägsbaserad omdirigering. Den här typen av omdirigering möjliggör bara HTTP till HTTPS-omdirigering på ett specifikt webbplatsområde, till exempel en kundvagn som betecknas av `/cart/*`.
- Omdirigera till en extern webbplats.

Mer information finns i [Application Gateway omdirigerings översikt](redirect-overview.md).

## <a name="session-affinity"></a>Sessionstillhörighet

Den cookie-baserade sessionstillhörighetsfunktionen är användbar när du vill behålla en användarsession på samma server. Genom att använda gatewayhanterade cookies kan Application Gateway dirigera efterföljande trafik från en användarsession till samma serverdel för bearbetning. Det här är viktigt i de fall där sessionstillstånd har sparats lokalt på servern för en användarsession.

Mer information finns i [så här fungerar en Application Gateway](how-application-gateway-works.md#modifications-to-the-request).

## <a name="websocket-and-http2-traffic"></a>Websocket- och HTTP/2-trafik

Application Gateway har inbyggt stöd för WebSocket- och HTTP/2-protokoll. Det finns inga inställningar som kan konfigureras av användaren för att selektivt aktivera eller inaktivera WebSocket-stöd.

WebSocket- och HTTP/2-protokollen aktiverar full duplex-kommunikation mellan en server och en klient över en tidskrävande TCP-anslutning. Det här tillåter en mer interaktiv kommunikation mellan webbservern och klienten, som kan vara dubbelriktad utan att behöva avsökning som krävs i HTTP-baserade implementeringar. Dessa protokoll har låg belastning, till skillnad från HTTP, och kan återanvända samma TCP-anslutning för flera begär Anden/svar som resulterar i en mer effektiv resursutnyttjande. Dessa protokoll är utformade att fungera via de traditionella HTTP-portarna 80 och 443.

Mer information finns i stöd för [WebSocket-support](application-gateway-websocket.md) och [http/2](configuration-listeners.md#http2-support).

## <a name="connection-draining"></a>Anslutningstömning

Anslutningstömning hjälper dig att få korrekt borttagning av medlemmar i serverdelspoolen under planerade serviceuppdateringar. Den här inställningen aktiveras via serverdelens http-inställning och kan tillämpas på alla medlemmar i en serverdelspool i samband med regelskapandet. Application Gateway säkerställer att alla avregistrera instanser av en backend-pool inte får någon ny begäran samtidigt som de tillåter att befintliga förfrågningar slutförs inom en angiven tids gräns. Detta gäller för båda Server dels instanserna som uttryckligen tas bort från backend-poolen av en användar konfigurations ändring och Server dels instanser som rapporteras som ohälsosama enligt vad som fastställs av hälso avsökningarna. Det enda undantaget till detta är begär Anden som är kopplade till avregistrering av instanser, som har avregistrerats uttryckligen, på grund av en Gateway-hanterad session tillhörighet och fortsätter att vara proxy till de deregistrerade instanserna.

Mer information finns i [Application Gateway konfigurations översikt](configuration-http-settings.md#connection-draining).

## <a name="custom-error-pages"></a>Anpassade felsidor

Med Application Gateway kan du skapa anpassade felsidor i stället för att visa standardmässiga felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida.

Mer information finns i [anpassade fel](custom-error.md).

## <a name="rewrite-http-headers-and-url"></a>Skriva om HTTP-huvuden och URL

HTTP-huvuden gör att klienten och servern kan skicka ytterligare information med begäran eller svaret. Genom att skriva om dessa HTTP-huvuden kan du utföra flera viktiga scenarier, t. ex.:

- Lägga till säkerhetsrelaterade huvud fält som HSTS/X-XSS-Protection.
- Tar bort fält för svars huvud som kan avslöja känslig information.
- Tar fram portinformation från X-vidarebefordrade – för meddelandehuvuden.

Med Application Gateway och WAF v2-SKU kan du lägga till, ta bort eller uppdatera huvudena för HTTP-begäran och HTTP-svar när begäran- och svarspaketen flyttas mellan klientens och serverdelens pooler. Du kan också skriva om URL:er, frågesträngsparametrar och värdnamn. Med URL-omskrivning och URL-baserad routning kan du välja att antingen dirigera begäranden till en av backend-poolerna baserat på den ursprungliga sökvägen eller den omskrivna sökvägen med hjälp av alternativet för att utvärdera sökvägskartan igen. 

Det ger dig också möjlighet att lägga till villkor så att de angivna huvudena eller URL bara skrivs om när vissa villkor uppfylls. Dessa villkor baseras på informationen för begäran och svar.

Mer information finns i [skriva om HTTP-sidhuvuden och URL: en](rewrite-http-headers-url.md).

## <a name="sizing"></a>Storlekar

Application Gateway Standard_v2 kan konfigureras för automatisk skalning eller distributioner med fast storlek. V2-SKU: n erbjuder inte olika instans storlekar. Mer information om v2-prestanda och priser finns i [autoskalning v2](application-gateway-autoscaling-zone-redundant.md) och [förstå prissättning](understanding-pricing.md).

Application Gateway standard (v1) erbjuds i tre storlekar: **liten**, **medel** och **stor**. Smål instansstorlekar är avsedda för utvecklings- och testningsscenarier.

En fullständig lista över gränserna för programgateways finns i avsnittet om [gränser för Application Gateway-tjänsten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

I följande tabell visas ett genomsnittligt prestanda data flöde för varje Application Gateway v1-instans med SSL-avlastning aktive rad:

| Genomsnittligt sidsvarsstorlek för serverdel | Liten | Medel | Stor |
| --- | --- | --- | --- |
| 6 kB |7.5 Mbit/s |13 Mbit/s |50 Mbit/s |
| 100 kB |35 Mbit/s |100 Mbit/s |200 Mbit/s |

> [!NOTE]
> De här värdena är genomsnittliga värden för ett Application Gateway-dataflöde. Det faktiska dataflödet beror på olika miljöfaktorer som genomsnittlig sidstorlek, plats för serverdelsinstanserna och bearbetningstid för att serva en sida. Du bör köra egna test för exakta prestandavärden. Dessa värden är bara för vägledning vid kapacitetsplanering.

## <a name="version-feature-comparison"></a>Jämförelse av versions funktion

För en Application Gateway v1-v2 funktions jämförelse, se [autoskalning och zon-redundant Application Gateway v2](application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur Application Gateway fungerar – [hur en Application Gateway fungerar](how-application-gateway-works.md)