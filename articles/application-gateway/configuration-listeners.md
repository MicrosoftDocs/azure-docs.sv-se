---
title: Konfiguration av Azure Application Gateway-lyssnare
description: Den här artikeln beskriver hur du konfigurerar Azure Application Gateway-lyssnare.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 15f68e8cbca65e7b970944f7ca5ef1952140cc6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397651"
---
# <a name="application-gateway-listener-configuration"></a>Application Gateway Listener-konfiguration

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

En lyssnare är en logisk entitet som söker efter inkommande anslutnings begär Anden med hjälp av port, protokoll, värd och IP-adress. När du konfigurerar lyssnaren måste du ange värden för de som matchar motsvarande värden i den inkommande begäran på gatewayen.

När du skapar en Programgateway med hjälp av Azure Portal skapar du också en standard lyssnare genom att välja protokoll och port för lyssnaren. Du kan välja om du vill aktivera HTTP2-stöd för lyssnaren. När du har skapat programgatewayen kan du redigera inställningarna för den standard-lyssnare (*appGatewayHttpListener*) eller skapa nya lyssnare.

## <a name="listener-type"></a>Typ av lyssnare

När du skapar en ny lyssnare väljer du mellan [ *grundläggande* och *flera platser*](./application-gateway-components.md#types-of-listeners).

- Om du vill att alla dina förfrågningar (för alla domäner) ska accepteras och vidarebefordras till backend-pooler väljer du grundläggande. Lär dig [hur du skapar en Programgateway med en grundläggande lyssnare](./quick-create-portal.md).

- Om du vill vidarebefordra begär anden till olika Server dels pooler baserat på *värd* rubriken eller värd namnen väljer du fler lyssnare för flera platser där du också måste ange ett värdnamn som matchar den inkommande begäran. Detta beror på att Application Gateway förlitar sig på HTTP 1,1-värdhuvuden för att vara värd för fler än en webbplats på samma offentliga IP-adress och port. Mer information finns i vara [värd för flera platser med Application Gateway](multiple-site-overview.md).

### <a name="order-of-processing-listeners"></a>Bearbetnings ordning för lyssnare

För v1 SKU: n matchas begär Anden enligt ordningen för reglerna och typen av lyssnare. Om en regel med Basic Listener kommer först i beställningen bearbetas den först och accepterar alla förfrågningar om denna port och IP-kombination. Undvik detta genom att konfigurera reglerna med lyssnare för flera platser först och skicka regeln med den grundläggande lyssnaren till den sista i listan.

För v2-SKU bearbetas flera plats lyssnare före grundläggande lyssnare.

## <a name="front-end-ip-address"></a>Klientdelens IP-adress

Välj den IP-adress för klient delen som du planerar att associera med den här lyssnaren. Lyssnaren lyssnar på inkommande begär Anden på den här IP-adressen.

## <a name="front-end-port"></a>Klient dels port

Välj klient dels port. Välj en befintlig port eller skapa en ny. Välj ett värde från det [tillåtna port intervallet](./application-gateway-components.md#ports). Du kan använda inte bara välkända portar, till exempel 80 och 443, men alla tillåtna anpassade portar som är lämpliga. En port kan användas för offentliga lyssnare eller privata lyssnare.

## <a name="protocol"></a>Protokoll

Välj HTTP eller HTTPS:

- Om du väljer HTTP är trafiken mellan klienten och programgatewayen okrypterad.

- Välj HTTPS om du vill ha [TLS-terminering](features.md#secure-sockets-layer-ssltls-termination) eller [slut punkt till slut punkt för TLS-kryptering](./ssl-overview.md). Trafiken mellan klienten och programgatewayen är krypterad. Och TLS-anslutningen avslutas vid Application Gateway. Om du vill använda TLS-kryptering från slut punkt till slut punkt måste du välja HTTPS och konfigurera **Server delens http** -inställning. Detta säkerställer att trafiken krypteras igen när den överförs från Application Gateway till Server delen.


Om du vill konfigurera TLS-terminering och slut punkt till slut punkt för TLS-kryptering måste du lägga till ett certifikat i lyssnaren för att programgatewayen ska kunna härleda en symmetrisk nyckel. Detta styrs av specifikationen TLS-protokoll. Den symmetriska nyckeln används för att kryptera och dekryptera trafiken som skickas till gatewayen. Gateway-certifikatet måste vara i PFX-format (personal information Exchange). Med det här formatet kan du exportera den privata nyckeln som gatewayen använder för att kryptera och dekryptera trafiken.

## <a name="supported-certificates"></a>Certifikat som stöds

Se [Översikt över TLS-terminering och slut punkt till slut punkt för TLS med Application Gateway](ssl-overview.md#certificates-supported-for-tls-termination)

## <a name="additional-protocol-support"></a>Ytterligare protokoll stöd

### <a name="http2-support"></a>HTTP2-stöd

Protokoll stöd för HTTP/2 är endast tillgängligt för klienter som ansluter till Application Gateway-lyssnare. Kommunikationen med backend-serverns pooler är över HTTP/1.1. Stöd för HTTP/2 är inaktiverat som standard. Följande Azure PowerShell kodfragment visar hur du aktiverar detta:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>WebSocket-stöd

WebSocket-stöd är aktiverat som standard. Det finns ingen användar konfigurerbar inställning för att aktivera eller inaktivera den. Du kan använda WebSockets med både HTTP-och HTTPS-lyssnare.

## <a name="custom-error-pages"></a>Anpassade felsidor

Du kan definiera anpassade fel på global nivå eller på lyssnar nivån. Men det finns inte stöd för att skapa anpassade fel sidor på global nivå från Azure Portal. Du kan konfigurera en anpassad felsida för ett 403-webb programs brand Väggs fel eller en 502 underhålls sida på lyssnar nivån. Du måste också ange en offentligt tillgänglig BLOB-URL för den angivna fel status koden. Mer information finns i [Skapa anpassade felsidor i Application Gateway](./custom-error.md).

![Application Gateway felkoder](/azure/application-gateway/media/custom-error/ag-error-codes.png)

Information om hur du konfigurerar en global anpassad felsida finns i [Azure PowerShell konfiguration](./custom-error.md#azure-powershell-configuration).

## <a name="tls-policy"></a>TLS-princip

Du kan centralisera TLS/SSL-certifikat hantering och minska belastningen på krypterings-dekryptering för en Server grupp på Server sidan. Med centraliserad TLS-hantering kan du också ange en central TLS-princip som passar dina säkerhets krav. Du kan välja *standard*, *fördefinierad* eller *anpassad* TLS-princip.

Du konfigurerar TLS-principen för att kontrol lera TLS-protokoll versioner. Du kan konfigurera en Programgateway att använda en minimi protokoll version för TLS-handskakning från TLS 1.0, TLS 1.1 och TLS 1.2. Som standard är SSL 2,0 och 3,0 inaktiverade och kan inte konfigureras. Mer information finns i [Översikt över Application Gateway TLS-princip](./application-gateway-ssl-policy-overview.md).

När du har skapat en lyssnare associerar du den med en regel för begäran-routning. Regeln bestämmer hur förfrågningar som tas emot på lyssnaren dirigeras till Server delen.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om regler för routning av förfrågningar](configuration-request-routing-rules.md).