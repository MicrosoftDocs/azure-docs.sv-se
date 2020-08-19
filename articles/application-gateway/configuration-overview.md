---
title: Översikt över Azure Application Gateway-konfiguration
description: Den här artikeln beskriver hur du konfigurerar komponenterna i Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: absha
ms.openlocfilehash: 32809c33e1c365d8d333bb89a5c2f773b311c2ff
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511090"
---
# <a name="application-gateway-configuration-overview"></a>Översikt över Application Gateway konfiguration

Azure Application Gateway består av flera komponenter som du kan konfigurera på olika sätt för olika scenarier. Den här artikeln visar hur du konfigurerar varje komponent.

![Flödes diagram för Application Gateway-komponenter](./media/configuration-overview/configuration-overview1.png)

Den här bilden illustrerar ett program som har tre lyssnare. De första två är lyssnare för flera platser för `http://acme.com/*` respektive `http://fabrikam.com/*` . Båda lyssnar på port 80. Den tredje är en grundläggande lyssnare som har slut punkt till slut punkt Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL) avslutades.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Virtuellt Azure-nätverk och dedikerat undernät

En Application Gateway är en dedikerad distribution i det virtuella nätverket. I det virtuella nätverket krävs ett dedikerat undernät för Application Gateway. Du kan ha flera instanser av en specifik Programgateway-distribution i ett undernät. Du kan också distribuera andra programgatewayer i under nätet. Men du kan inte distribuera någon annan resurs i Application Gateway-undernätet.

> [!NOTE]
> Du kan inte mixa Standard_v2 och standard Azure Application Gateway i samma undernät.

#### <a name="size-of-the-subnet"></a>Storlek på under nätet

Application Gateway använder en privat IP-adress per instans, plus en annan privat IP-adress om en privat klient-IP-adress har kon figurer ATS.

Azure reserverar också fem IP-adresser i varje undernät för internt bruk: de första fyra och sista IP-adresserna. Överväg till exempel 15 Application Gateway-instanser utan privat frontend-IP. Du behöver minst 20 IP-adresser för det här under nätet: fem för intern användning och 15 för Application Gateway-instanserna.

Överväg ett undernät som har 27 Application Gateway-instanser och en IP-adress för en privat klient dels-IP. I det här fallet behöver du 33 IP-adresser: 27 för Application Gateway-instanserna, en för den privata klient delen och fem för internt bruk.

Application Gateway-SKU: n (standard eller WAF) har stöd för upp till 32 instanser (32 instans-IP-adresser + 1 privat frontend IP + 5 Azure reserverad) – så en minsta under näts storlek på/26 rekommenderas

Application Gateway (Standard_v2 eller WAF_v2 SKU) har stöd för upp till 125 instanser (125 instans-IP-adresser + 1 privat frontend IP + 5 Azure reserverad) – så en minsta under näts storlek på/24 rekommenderas

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Nätverks säkerhets grupper på Application Gateway under nätet

Nätverks säkerhets grupper (NSG: er) stöds på Application Gateway. Men det finns vissa begränsningar:

- Du måste tillåta inkommande Internet trafik på TCP-portarna 65503-65534 för Application Gateway v1 SKU och TCP-portarna 65200-65535 för v2-SKU: n med mål under nätet som **valfri** och källa som **GatewayManager** service tag. Det här port intervallet krävs för kommunikation mellan Azure-infrastrukturen. Dessa portar är skyddade (låsta) av Azure-certifikat. Externa entiteter, inklusive kunder av dessa gateways, kan inte kommunicera med dessa slut punkter.

- Det går inte att blockera utgående Internet anslutning. Standard regler för utgående trafik i NSG tillåter Internet anslutning. Vi rekommenderar att du gör följande:

  - Ta inte bort standard reglerna för utgående trafik.
  - Skapa inte andra utgående regler som nekar utgående anslutningar.

- Trafik från **AzureLoadBalancer** -taggen med mål under **nätet måste vara** tillåten.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Tillåt Application Gateway åtkomst till några käll-IP-adresser

I det här scenariot använder du NSG: er i under nätet Application Gateway. Lägg till följande begränsningar i under nätet i prioritetsordning:

1. Tillåt inkommande trafik från en käll-IP-adress eller ett IP-intervall med målet som hela Application Gateway under nätets adress intervall och målport som din port för inkommande åtkomst, till exempel port 80 för HTTP-åtkomst.
2. Tillåt inkommande begär Anden från källan som **GatewayManager** service tag och destination som **alla** och mål portar som 65503-65534 för Application Gateway v1 sku och portarna 65200-65535 för v2 SKU för [kommunikation med backend-hälsostatus](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Det här port intervallet krävs för kommunikation mellan Azure-infrastrukturen. Dessa portar är skyddade (låsta) av Azure-certifikat. Externa entiteter kan inte initiera ändringar på dessa slut punkter utan lämpliga certifikat på plats.
3. Tillåt inkommande Azure Load Balancer-avsökningar (*AzureLoadBalancer* -tagg) och inkommande virtuell nätverks trafik (*VirtualNetwork* -tagg) i [nätverks säkerhets gruppen](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Blockera all annan inkommande trafik med hjälp av en regel för neka-alla.
5. Tillåt utgående trafik till Internet för alla destinationer.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Användardefinierade vägar som stöds på Application Gateway-undernätet

> [!IMPORTANT]
> Om du använder UDR på Application Gateway under nätet kan hälso tillståndet i [hälso läget för Server delen](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) visas som **Okänt**. Det kan också leda till att Application Gateway loggar och mät värden Miss fungerar. Vi rekommenderar att du inte använder UDR på Application Gateway under nätet så att du kan visa backend-hälsa, loggar och mått.

- **v1**

   För v1-SKU: n (UDR) stöds de användardefinierade vägarna i Application Gateway under nätet, så länge de inte ändrar svars-/svars kommunikation från slut punkt till slut punkt. Du kan till exempel konfigurera en UDR i Application Gateway under nätet så att den pekar på en brand Väggs enhet för paket granskning. Men du måste se till att paketet når sitt avsedda mål efter inspektion. Om du inte gör det kan det leda till felaktig hälso avsökning eller trafik-routning. Detta inkluderar inlärda vägar eller standard 0.0.0.0/0-vägar som sprids av Azure ExpressRoute eller VPN-gatewayer i det virtuella nätverket. Alla scenarier där 0.0.0.0/0 måste omdirigeras lokalt (Tvingad tunnel trafik) stöds inte för v1.

- **v2**

   För v2-SKU: n finns det stöd för och scenarier som inte stöds:

   **scenarier som stöds i v2**
   > [!WARNING]
   > En felaktig konfiguration av väg tabellen kan resultera i asymmetrisk routning i Application Gateway v2. Se till att all hanterings-och kontroll Plans trafik skickas direkt till Internet och inte via en virtuell installation. Loggning och mått kan också påverkas.


  **Scenario 1**: UDR för att inaktivera Border Gateway Protocol (BGP)-spridning av vägar till Application Gateway-undernätet

   Ibland annonseras standard-gateway-vägen (0.0.0.0/0) via de ExpressRoute eller VPN-gatewayer som är associerade med Application Gateway virtuella nätverket. Detta bryter hanterings Plans trafik, som kräver en direkt sökväg till Internet. I sådana fall kan en UDR användas för att inaktivera spridning av BGP-vägar. 

   Använd följande steg för att inaktivera instabilitetsdämpning av BGP-vägar:

   1. Skapa en väg tabell resurs i Azure.
   2. Inaktivera parametern för **väg spridning av virtuell nätverks-Gateway** . 
   3. Koppla routningstabellen till lämpligt undernät. 

   Om du aktiverar UDR för det här scenariot bör du inte bryta eventuella befintliga inställningar.

  **Scenario 2**: UDR till Direct 0.0.0.0/0 till Internet

   Du kan skapa en UDR för att skicka 0.0.0.0/0-trafik direkt till Internet. 

  **Scenario 3**: UDR för Azure Kubernetes service med Kubernetes

  Om du använder Kubernetes med Azure Kubernetes service (AKS) och Application Gateway ingress (AGIC) behöver du en routningstabell för att tillåta trafik som skickas till poddar från Application Gateway att dirigeras till rätt nod. Detta behövs inte om du använder Azure-CNI. 

  Följ stegen nedan om du vill använda routningstabellen för att Kubernetes ska fungera:

  1. Gå till resurs gruppen som skapats av AKS (namnet på resurs gruppen måste börja med "MC_")
  2. Hitta routningstabellen som skapats av AKS i den resurs gruppen. Routningstabellen ska vara ifylld med följande information:
     - Adressprefixet ska vara IP-intervallet för de poddar som du vill uppnå i AKS. 
     - Nästa hopp typ ska vara virtuell installation. 
     - Nästa hopp adress ska vara IP-adressen för noden som är värd för poddar.
  3. Koppla den här routningstabellen till Application Gateway under nätet. 
    
  **scenarier som inte stöds i v2**

  **Scenario 1**: UDR för virtuella enheter

  Alla scenarier där 0.0.0.0/0 behöver omdirigeras via en virtuell installation, ett virtuellt nätverk för hubb/eker eller lokalt (Tvingad tunnel trafik) stöds inte för v2.

## <a name="front-end-ip"></a>Klient delens IP-adress

Du kan konfigurera programgatewayen att ha en offentlig IP-adress, en privat IP-adress eller både och. En offentlig IP-adress krävs när du är värd för en server del som klienterna måste komma åt via Internet via en virtuell IP-adress (VIP).

> [!NOTE]
> Application Gateway v2 stöder för närvarande inte enbart privat IP-läge. Det stöder följande kombinationer:
>* Privat IP och offentlig IP
>* Endast offentlig IP
>
> Mer information finns i [vanliga frågor och svar om Application Gateway](application-gateway-faq.md#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address).


Det krävs ingen offentlig IP-adress för en intern slut punkt som inte är exponerad för Internet. Det kallas för en *intern belastningsutjämnare* (ILB) eller privat klient dels-IP. En Application Gateway-ILB är användbar för interna branschspecifika program som inte är utsatta för Internet. Det är också användbart för tjänster och nivåer i ett program med flera nivåer inom en säkerhets gränser som inte exponeras för Internet men som kräver belastnings fördelning för resursallokering, varaktighet eller TLS-avslutning.

Det finns bara stöd för en offentlig IP-adress eller en privat IP-adress. Du väljer klient delens IP-adress när du skapar programgatewayen.

- För en offentlig IP-adress kan du skapa en ny offentlig IP-adress eller använda en befintlig offentlig IP-adress på samma plats som Application Gateway. Mer information finns i [statisk eller dynamisk offentlig IP-adress](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- För en privat IP-adress kan du ange en privat IP-adress från det undernät där programgatewayen skapas. Om du inte anger något väljs en godtycklig IP-adress automatiskt från under nätet. Den IP-adress typ som du väljer (statisk eller dynamisk) kan inte ändras senare. Mer information finns i [skapa en Programgateway med en intern belastningsutjämnare](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

En IP-adress på klient sidan är kopplad till en *lyssnare*som söker efter inkommande begär Anden på klient delens IP.

## <a name="listeners"></a>Lyssnare

En lyssnare är en logisk entitet som söker efter inkommande anslutnings begär Anden med hjälp av port, protokoll, värd och IP-adress. När du konfigurerar lyssnaren måste du ange värden för de som matchar motsvarande värden i den inkommande begäran på gatewayen.

När du skapar en Programgateway med hjälp av Azure Portal skapar du också en standard lyssnare genom att välja protokoll och port för lyssnaren. Du kan välja om du vill aktivera HTTP2-stöd för lyssnaren. När du har skapat programgatewayen kan du redigera inställningarna för den standard-lyssnare (*appGatewayHttpListener*) eller skapa nya lyssnare.

### <a name="listener-type"></a>Typ av lyssnare

När du skapar en ny lyssnare väljer du mellan [ *grundläggande* och *flera platser*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Om du vill att alla dina förfrågningar (för alla domäner) ska accepteras och vidarebefordras till backend-pooler väljer du grundläggande. Lär dig [hur du skapar en Programgateway med en grundläggande lyssnare](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Om du vill vidarebefordra begär anden till olika Server dels pooler baserat på *värd* rubriken eller värd namnen väljer du fler lyssnare för flera platser där du också måste ange ett värdnamn som matchar den inkommande begäran. Detta beror på att Application Gateway förlitar sig på HTTP 1,1-värdhuvuden för att vara värd för fler än en webbplats på samma offentliga IP-adress och port. Mer information finns i vara [värd för flera platser med Application Gateway](multiple-site-overview.md).

#### <a name="order-of-processing-listeners"></a>Bearbetnings ordning för lyssnare

För v1 SKU: n matchas begär Anden enligt ordningen för reglerna och typen av lyssnare. Om en regel med Basic Listener kommer först i beställningen bearbetas den först och accepterar alla förfrågningar om denna port och IP-kombination. Undvik detta genom att konfigurera reglerna med lyssnare för flera platser först och skicka regeln med den grundläggande lyssnaren till den sista i listan.

För v2-SKU bearbetas flera plats lyssnare före grundläggande lyssnare.

### <a name="front-end-ip"></a>Klient delens IP-adress

Välj den IP-adress för klient delen som du planerar att associera med den här lyssnaren. Lyssnaren lyssnar på inkommande begär Anden på den här IP-adressen.

### <a name="front-end-port"></a>Klient dels port

Välj klient dels port. Välj en befintlig port eller skapa en ny. Välj ett värde från det [tillåtna port intervallet](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Du kan använda inte bara välkända portar, till exempel 80 och 443, men alla tillåtna anpassade portar som är lämpliga. En port kan användas för offentliga lyssnare eller privata lyssnare.

### <a name="protocol"></a>Protokoll

Välj HTTP eller HTTPS:

- Om du väljer HTTP är trafiken mellan klienten och programgatewayen okrypterad.

- Välj HTTPS om du vill ha [TLS-terminering](features.md#secure-sockets-layer-ssltls-termination) eller [slut punkt till slut punkt för TLS-kryptering](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Trafiken mellan klienten och programgatewayen är krypterad. Och TLS-anslutningen avslutas vid Application Gateway. Om du vill använda TLS-kryptering från slut punkt till slut punkt måste du välja HTTPS och konfigurera **Server delens http** -inställning. Detta säkerställer att trafiken krypteras igen när den överförs från Application Gateway till Server delen.


Om du vill konfigurera TLS-terminering och slut punkt till slut punkt för TLS-kryptering måste du lägga till ett certifikat i lyssnaren för att programgatewayen ska kunna härleda en symmetrisk nyckel. Detta styrs av specifikationen TLS-protokoll. Den symmetriska nyckeln används för att kryptera och dekryptera trafiken som skickas till gatewayen. Gateway-certifikatet måste vara i PFX-format (personal information Exchange). Med det här formatet kan du exportera den privata nyckeln som gatewayen använder för att kryptera och dekryptera trafiken.

#### <a name="supported-certificates"></a>Certifikat som stöds

Se [certifikat som stöds för TLS-avslutning](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Ytterligare protokoll stöd

#### <a name="http2-support"></a>HTTP2-stöd

Protokoll stöd för HTTP/2 är endast tillgängligt för klienter som ansluter till Application Gateway-lyssnare. Kommunikationen med backend-serverns pooler är över HTTP/1.1. Stöd för HTTP/2 är inaktiverat som standard. Följande Azure PowerShell kodfragment visar hur du aktiverar detta:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket-stöd

WebSocket-stöd är aktiverat som standard. Det finns ingen användar konfigurerbar inställning för att aktivera eller inaktivera den. Du kan använda WebSockets med både HTTP-och HTTPS-lyssnare.

### <a name="custom-error-pages"></a>Anpassade felsidor

Du kan definiera anpassade fel på global nivå eller på lyssnar nivån. Men det finns inte stöd för att skapa anpassade fel sidor på global nivå från Azure Portal. Du kan konfigurera en anpassad felsida för ett 403-webb programs brand Väggs fel eller en 502 underhålls sida på lyssnar nivån. Du måste också ange en offentligt tillgänglig BLOB-URL för den angivna fel status koden. Mer information finns i [Skapa anpassade felsidor i Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Application Gateway felkoder](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Information om hur du konfigurerar en global anpassad felsida finns i [Azure PowerShell konfiguration](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="tls-policy"></a>TLS-princip

Du kan centralisera TLS/SSL-certifikat hantering och minska belastningen på krypterings-dekryptering för en Server grupp på Server sidan. Med centraliserad TLS-hantering kan du också ange en central TLS-princip som passar dina säkerhets krav. Du kan välja *standard*, *fördefinierad*eller *anpassad* TLS-princip.

Du konfigurerar TLS-principen för att kontrol lera TLS-protokoll versioner. Du kan konfigurera en Programgateway att använda en minimi protokoll version för TLS-handskakning från TLS 1.0, TLS 1.1 och TLS 1.2. Som standard är SSL 2,0 och 3,0 inaktiverade och kan inte konfigureras. Mer information finns i [Översikt över Application Gateway TLS-princip](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

När du har skapat en lyssnare associerar du den med en regel för begäran-routning. Regeln bestämmer hur förfrågningar som tas emot på lyssnaren dirigeras till Server delen.

## <a name="request-routing-rules"></a>Regler för routning av begäran

När du skapar en Programgateway med hjälp av Azure Portal skapar du en standard regel (*regel 1*). Den här regeln binder standard avlyssningen (*appGatewayHttpListener*) med standard server delen (*appGatewayBackendPool*) och standardvärdet för Server delens http-inställningar (*appGatewayBackendHttpSettings*). När du har skapat gatewayen kan du redigera inställningarna för standard regeln eller skapa nya regler.

### <a name="rule-type"></a>Regeltyp

När du skapar en regel väljer du mellan [ *Basic* och *Path-based*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Välj Basic om du vill vidarebefordra alla begär Anden på den associerade lyssnaren (till exempel *blogg <i></i> . contoso.com/ \* )* till en enda backend-pool.
- Välj sökväg – baserat om du vill dirigera begär Anden från särskilda URL-sökvägar till särskilda backend-pooler. Sök vägs mönstret används bara på sökvägen till URL: en, inte dess frågeparametrar.

#### <a name="order-of-processing-rules"></a>Bearbetnings ordning för regler

För v1-och v2-SKU bearbetas mönster matchning av inkommande begär anden i den ordning som Sök vägarna visas i sökvägen till URL-sökvägen för den Sök vägs baserade regeln. Om en begäran matchar mönstret i två eller flera sökvägar i Sök vägs kartan matchas den sökväg som anges först. Och begäran vidarebefordras till Server delen som är kopplad till den sökvägen.

### <a name="associated-listener"></a>Associerad lyssnare

Koppla en lyssnare till regeln så att regeln för *begär ande routning* som är associerad med lyssnaren utvärderas för att fastställa backend-poolen för att skicka begäran till.

### <a name="associated-back-end-pool"></a>Associerad backend-pool

Koppla till regeln till den backend-pool som innehåller backend-målen som hanterar begär Anden som lyssnaren tar emot.

 - Endast en backend-pool tillåts för en grundläggande regel. Alla begär Anden på den associerade lyssnaren vidarebefordras till den backend-poolen.

 - För en sökväg-baserad regel lägger du till flera Server dels pooler som motsvarar varje URL-sökväg. De begär Anden som matchar URL-sökvägen som anges vidarebefordras till motsvarande backend-pool. Lägg också till en standardpool för Server delen. Begär Anden som inte matchar någon URL-sökväg i regeln vidarebefordras till poolen.

### <a name="associated-back-end-http-setting"></a>Associerad HTTP-inställning på Server Sidan

Lägg till en HTTP-inställning på Server sidan för varje regel. Begär Anden dirigeras från programgatewayen till backend-målen genom att använda port nummer, protokoll och annan information som anges i den här inställningen.

För en grundläggande regel tillåts bara en HTTP-inställning på Server sidan. Alla begär Anden på den associerade lyssnaren vidarebefordras till motsvarande backend-mål genom att använda den här HTTP-inställningen.

För en sökväg-baserad regel lägger du till flera Server dels-HTTP-inställningar som motsvarar varje URL-sökväg. Begär Anden som matchar URL-sökvägen i den här inställningen vidarebefordras till motsvarande Server dels mål genom att använda de HTTP-inställningar som motsvarar varje URL-sökväg. Lägg också till en standard-HTTP-inställning. Begär Anden som inte matchar någon URL-sökväg i den här regeln vidarebefordras till standard-slutpunkt-poolen med hjälp av standard-HTTP-inställningen.

### <a name="redirection-setting"></a>Inställning för omdirigering

Om omdirigering har kon figurer ATS för en grundläggande regel omdirigeras alla begär Anden på den associerade lyssnaren till målet. Detta är *Global* omdirigering. Om omdirigering har kon figurer ATS för en Sök vägs baserad regel omdirigeras endast begär anden i ett särskilt plats område. Ett exempel är ett shopping vagns områden som betecknas av */Cart/ \* *. Detta är en *Path-baserad* omdirigering.

Mer information om omdirigering finns i [Application Gateway omdirigerings översikt](redirect-overview.md).

#### <a name="redirection-type"></a>Typ av omdirigering

Välj den typ av omdirigering som krävs: *permanent (301)*, *temporär (307)*, *found (302)* eller *Se övrigt (303)*.

#### <a name="redirection-target"></a>Omdirigerings mål

Välj en annan lyssnare eller en extern plats som omdirigerings mål.

##### <a name="listener"></a>Lyssnare

Välj lyssnare som omdirigerings mål för att omdirigera trafik från en lyssnare till en annan på gatewayen. Den här inställningen krävs om du vill aktivera HTTP-till-HTTPS-omdirigering. Den omdirigerar trafik från den käll lyssnare som söker efter inkommande HTTP-begäranden till mål lyssnaren som söker efter inkommande HTTPS-begäranden. Du kan också välja att inkludera frågesträngen och sökvägen från den ursprungliga begäran i begäran som vidarebefordras till omdirigerings målet.

![Dialog rutan Application Gateway-komponenter](./media/configuration-overview/configure-redirection.png)

Mer information om omdirigering av HTTP-till-HTTPS finns i:
- [HTTP-till-HTTPS-omdirigering med hjälp av Azure Portal](redirect-http-to-https-portal.md)
- [HTTP-till-HTTPS-omdirigering med hjälp av PowerShell](redirect-http-to-https-powershell.md)
- [HTTP-till-HTTPS-omdirigering med hjälp av Azure CLI](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Extern webbplats

Välj extern plats när du vill dirigera om trafiken för den lyssnare som är kopplad till den här regeln till en extern plats. Du kan välja att inkludera frågesträngen från den ursprungliga begäran i begäran som vidarebefordras till omdirigerings målet. Du kan inte vidarebefordra sökvägen till den externa platsen som fanns i den ursprungliga begäran.

Mer information om omdirigering finns i:
- [Omdirigera trafik till en extern plats med hjälp av PowerShell](redirect-external-site-powershell.md)
- [Omdirigera trafik till en extern plats med hjälp av CLI](redirect-external-site-cli.md)

### <a name="rewrite-http-headers-and-url"></a>Skriva om HTTP-huvuden och URL

Genom att använda regler för att skriva om, kan du lägga till, ta bort eller uppdatera HTTP (S)-begäran och svars rubriker samt URL-sökväg och parametrar för frågesträngar som begär ande-och svars paket flyttas mellan klienten och backend-pooler via Application Gateway.

Parametrarna headers och URL kan anges till statiska värden eller till andra huvuden och servervariabler. Detta hjälper till med viktiga användnings fall, till exempel att extrahera klientens IP-adresser, ta bort känslig information om Server delen, lägga till mer säkerhet och så vidare.
Mer information finns i:

 - [Skriv över översikt över HTTP-rubriker och URL](rewrite-http-headers-url.md)
 - [Konfigurera omskrivning av HTTP-huvud](rewrite-http-headers-portal.md)
 - [Konfigurera URL-omskrivning](rewrite-url-portal.md)

## <a name="http-settings"></a>HTTP-inställningar

Application Gateway dirigerar trafik till backend-servrarna med hjälp av den konfiguration som du anger här. När du har skapat en HTTP-inställning måste du associera den med en eller flera regler för begäran-routning.

### <a name="cookie-based-affinity"></a>Cookie-baserad tillhörighet

Azure Application Gateway använder Gateway-hanterade cookies för att underhålla användarsessioner. När en användare skickar den första begäran till Application Gateway, ställer den in en tillhörighets-cookie i svaret med ett hash-värde som innehåller sessionsinformation, så att efterföljande begär Anden som använder tillhörighets-cookien dirigeras till samma backend-server för att upprätthålla varaktighet. 

Den här funktionen är användbar när du vill behålla en användarsession på samma server och när sessionstillstånd sparas lokalt på servern för en användarsession. Om programmet inte kan hantera cookie-baserad tillhörighet kan du inte använda den här funktionen. Kontrol lera att klienterna har stöd för cookies för att använda den.

Chrome [Browser](https://www.chromium.org/Home) - [V80 uppdaterar](https://chromiumdash.appspot.com/schedule) ett uppdrag där http-cookies utan [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) -attribut måste behandlas som SameSite = lax. Om en cookie måste skickas i en tredjeparts kontext måste den använda *SameSite = none för CORS-begäranden (Cross-Origin resurs delning). Säkra* attribut och bör endast skickas över https. I annat fall skickar webbläsaren inte cookies i den tredje partens sammanhang. Målet med den här uppdateringen från Chrome är att förbättra säkerheten och undvika CSRF-attacker (Cross-Site request förfalskning). 

För att stödja den här ändringen, från och med 17 2020 februari, kommer Application Gateway (alla SKU-typer) att injicera en annan cookie med namnet *ApplicationGatewayAffinityCORS* förutom den befintliga *ApplicationGatewayAffinity* -cookien. *ApplicationGatewayAffinityCORS* -cookien har två fler attribut som läggs till i den (*"SameSite = none; Säker "*) så att den tröga sessionen upprätthålls även för frågor över andra ursprung.

Observera att standard tillhörighets-Cookiens namn är *ApplicationGatewayAffinity* och att du kan ändra det. Om du använder ett namn på en anpassad tillhörighet, läggs en ytterligare cookie till med CORS som suffix. Till exempel *CustomCookieNameCORS*.

> [!NOTE]
> Om attributet *SameSite = inget* anges är det obligatoriskt att cookien även innehåller en *säker* flagga och måste skickas via https.  Om session tillhörighet krävs över CORS måste du migrera din arbets belastning till HTTPS. Läs mer i TLS Offload och end-to-end TLS-dokumentation för Application Gateway här – [Översikt](ssl-overview.md), [Konfigurera en PROGRAMgateway med TLS-avslutning med hjälp av Azure Portal](create-ssl-portal.md), [Konfigurera end-to-end-TLS genom att använda Application Gateway med portalen](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Anslutningstömning

Med anslutnings tömning kan du på ett smidigt sätt ta bort backend-medlemmarnas medlemmar under planerade tjänst uppdateringar. Du kan använda den här inställningen för alla medlemmar i en backend-pool genom att aktivera anslutning som töms på HTTP-inställningen. Det garanterar att alla avregistreringar av instanser av en backend-pool fortsätter att underhålla befintliga anslutningar och att betjäna begär Anden för en konfigurerbar tids gräns och inte får några nya förfrågningar eller anslutningar. Det enda undantaget till detta är begär Anden som är kopplade till deregistrering av instanser på grund av en Gateway-hanterad session tillhörighet och fortsätter att vidarebefordras till Avregistrerings instanserna. Anslutnings tömning gäller för Server dels instanser som uttryckligen tas bort från backend-poolen.

### <a name="protocol"></a>Protokoll

Application Gateway stöder både HTTP och HTTPS för routnings begär anden till backend-servrarna. Om du väljer HTTP är trafik till backend-servrarna okrypterad. Om okrypterad kommunikation inte är acceptabel väljer du HTTPS.

Den här inställningen kombinerad med HTTPS i lyssnaren stöder [end-to-end-TLS](ssl-overview.md). På så sätt kan du på ett säkert sätt överföra känsliga data som har krypterats till Server delen. Varje backend-server i backend-poolen där TLS från slut punkt till slut punkt är aktiverat måste konfigureras med ett certifikat för att tillåta säker kommunikation.

### <a name="port"></a>Port

Den här inställningen anger den port där backend-servrarna lyssnar på trafik från programgatewayen. Du kan konfigurera portar mellan 1 och 65535.

### <a name="request-timeout"></a>Timeout för begäran

Den här inställningen är antalet sekunder som Application Gateway väntar på att få svar från backend-servern.

### <a name="override-back-end-path"></a>Åsidosätt backend-sökväg

Med den här inställningen kan du konfigurera en valfri anpassad vidarebefordrings Sök väg som ska användas när begäran vidarebefordras till Server delen. Alla delar av den inkommande sökvägen som matchar den anpassade sökvägen i fältet **Åsidosätt sökväg till Server** del kopieras till den vidarebefordrade sökvägen. Följande tabell visar hur den här funktionen fungerar:

- När HTTP-inställningen är kopplad till en grundläggande regel för begäran-routning:

  | Ursprunglig begäran  | Åsidosätt backend-sökväg | Begäran vidarebefordras till Server delen |
  | ----------------- | --------------------- | ---------------------------- |
  | bostad            | åsidosättningsinställning            | /override/home/              |
  | /home/secondhome/ | åsidosättningsinställning            | /override/home/secondhome/   |

- När HTTP-inställningen är kopplad till en Sök vägs baserad regel för begäran-routning:

  | Ursprunglig begäran           | Sök vägs regel       | Åsidosätt backend-sökväg | Begäran vidarebefordras till Server delen |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | åsidosättningsinställning            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | åsidosättningsinställning            | /override/home/secondhome/   |
  | bostad                     | pathrule      | åsidosättningsinställning            | /override/home/              |
  | /home/secondhome/          | pathrule      | åsidosättningsinställning            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | åsidosättningsinställning            | åsidosättningsinställning                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | åsidosättningsinställning            | /override/secondhome/        |
  | pathrule                 | pathrule      | åsidosättningsinställning            | åsidosättningsinställning                   |

### <a name="use-for-app-service"></a>Använd för App Service

Det här är endast ett användar gränssnitt som väljer de två nödvändiga inställningarna för Azure App Service server del. Det aktiverar **Välj värd namnet från backend-adressen**och skapar en ny anpassad avsökning om du inte redan har en. (Mer information finns i avsnittet [Välj värdnamn från backend-adress](#pick) i den här artikeln.) En ny avsökning skapas och avsöknings huvudet plockas från Server delens medlem adress.

### <a name="use-custom-probe"></a>Använd anpassad avsökning

Den här inställningen associerar en [anpassad avsökning](application-gateway-probe-overview.md#custom-health-probe) med en http-inställning. Du kan bara associera en anpassad avsökning med en HTTP-inställning. Om du inte uttryckligen associerar en anpassad avsökning används [standard avsökningen](application-gateway-probe-overview.md#default-health-probe-settings) för att övervaka Server delens hälso tillstånd. Vi rekommenderar att du skapar en anpassad avsökning för bättre kontroll över hälso övervakningen av dina Server delar.

> [!NOTE]
> Den anpassade avsökningen övervakar inte Server delens hälso tillstånd om inte den motsvarande HTTP-inställningen uttryckligen är associerad med en lyssnare.

### <a name="pick-host-name-from-back-end-address"></a><a name="pick"></a>Välj värdnamn från Server dels adress

Den här funktionen ställer dynamiskt in *värd* rubriken i begäran till värd namnet för backend-poolen. Den använder en IP-adress eller ett fullständigt domän namn.

Den här funktionen hjälper när Server dels domän namnet skiljer sig från DNS-namnet på programgatewayen och Server delen förlitar sig på en specifik värd rubrik för att matcha rätt slut punkt.

Ett exempel är ett exempel på flera klient tjänster som server delen. En app service är en tjänst för flera innehavare som använder ett delat utrymme med en enda IP-adress. Därför kan en app service bara nås via de värdnamn som kon figurer ATS i inställningarna för den anpassade domänen.

Som standard är det anpassade domän namnet *example.azurewebsites.net*. För att få åtkomst till din app service genom att använda en Programgateway via ett värdnamn som inte uttryckligen registreras i App Service eller via programgatewayens FQDN, åsidosätter du värd namnet i den ursprungliga begäran till App Service-värdnamnet. Det gör du genom att aktivera inställningen **Välj värd namn från Server dels adress** .

För en anpassad domän vars befintliga anpassade DNS-namn mappas till App Service behöver du inte aktivera den här inställningen.

> [!NOTE]
> Den här inställningen krävs inte för App Service-miljön, som är en dedikerad distribution.

### <a name="host-name-override"></a>Åsidosätt värdnamn

Den här funktionen ersätter *värd* rubriken i den inkommande begäran på programgatewayen med det värdnamn som du anger.

Om *www.contoso.com* till exempel anges i inställningen **värd namn** , ändras den ursprungliga begäran * `https://appgw.eastus.cloudapp.azure.com/path1` till * `https://www.contoso.com/path1` när begäran vidarebefordras till backend-servern.

## <a name="back-end-pool"></a>Serverdelspool

Du kan peka en backend-pool till fyra typer av Server dels medlemmar: en viss virtuell dator, en skalnings uppsättning för virtuella datorer, en IP-adress/FQDN eller en app service. 

När du har skapat en backend-pool måste du associera den med en eller flera regler för begäran-routning. Du måste också konfigurera hälso avsökningar för varje backend-pool på din Application Gateway. När ett villkor för en regel för anslutningsbegäran uppfylls, vidarebefordrar Application Gateway trafiken till de felfria servrarna (som fastställs av hälso avsökningarna) i motsvarande backend-pool.

## <a name="health-probes"></a>Hälsotillståndsavsökningar

En Programgateway övervakar hälsan för alla resurser i Server delen som standard. Men vi rekommenderar starkt att du skapar en anpassad avsökning för varje server dels-HTTP-inställning för att få bättre kontroll över hälso övervakningen. Information om hur du konfigurerar en anpassad avsökning finns i [Inställningar för anpassade hälso avsökningar](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> När du har skapat en anpassad hälso avsökning måste du koppla den till en HTTP-inställning på Server sidan. En anpassad avsökning övervakar inte hälsan för backend-poolen om inte den motsvarande HTTP-inställningen uttryckligen är associerad med en lyssnare med hjälp av en regel.

## <a name="next-steps"></a>Nästa steg

Nu när du känner till Application Gateway-komponenter kan du:

- [Skapa en Application Gateway i Azure Portal](quick-create-portal.md)
- [Skapa en Programgateway med hjälp av PowerShell](quick-create-powershell.md)
- [Skapa en Programgateway med hjälp av Azure CLI](quick-create-cli.md)
