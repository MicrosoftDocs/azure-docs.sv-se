---
title: Agera värd åt flera webbplatser i Azure Application Gateway
description: Den här artikeln innehåller en översikt över stödet för Azure Application Gateway för flera platser.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 07/20/2020
ms.author: surmb
ms.topic: conceptual
ms.openlocfilehash: 53f6f37454de886934a483b40daad24204958baf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87474333"
---
# <a name="application-gateway-multiple-site-hosting"></a>Flera webbplatser i Application Gateway

Med värd tjänster för flera webbplatser kan du konfigurera fler än ett webb program på samma port i en Programgateway. Den här funktionen gör att du kan konfigurera en mer effektiv topologi för dina distributioner genom att lägga till fler än 100 webbplatser i samma appgateway. Varje webbplats kan dirigeras till en egen serverdelspool. Tänk dig till exempel att de tre domänerna contoso.com, fabrikam.com och adatum.com pekar på appgatewayens IP-adress. Du skapar tre lyssnare för flera platser och konfigurerar varje lyssnare enligt respektive inställningar för port och protokoll. 

Du kan också definiera värdnamn med jokertecken i lyssnare för flera platser och upp till 5 värdnamn per lyssnare. Mer information finns i [namn på jokertecken i lyssnaren](#wildcard-host-names-in-listener-preview).

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="Application Gateway för flera platser":::

> [!IMPORTANT]
> Regler bearbetas i den ordning som de visas i portalen för v1 SKU. För v2-SKU: n har exakta matchningar högre prioritet. Vi rekommenderar starkt att konfigurera lyssnare för flera platser första innan du konfigurerar en grundläggande lyssnare.  Detta säkerställer att trafik dirigeras till rätt serverdel. Om en grundläggande lyssnare visas först och matchar en inkommande begäran kommer den att bearbetas av den lyssnaren.

Begäranden för `http://contoso.com` dirigeras till ContosoServerPool och `http://fabrikam.com` dirigeras till FabrikamServerPool.

På samma sätt kan du vara värd för flera under domäner för samma överordnade domän på samma Application Gateway-distribution. Du kan till exempel vara värd för `http://blog.contoso.com` och `http://app.contoso.com` på en enskild Application Gateway-distribution.

## <a name="wildcard-host-names-in-listener-preview"></a>Jokertecken värd namn i lyssnare (förhands granskning)

Application Gateway tillåter värdbaserad routning med HTTP (S)-lyssnare för flera platser. Nu kan du använda jokertecken som asterisk (*) och frågetecken (?) i värd namnet och upp till 5 värdnamn per HTTP (S)-lyssnare för flera platser. Till exempel `*.contoso.com`.

Med ett jokertecken i värd namnet kan du matcha flera värdnamn i en enda lyssnare. Kan till exempel `*.contoso.com` Matcha med `ecom.contoso.com` , `b2b.contoso.com` `customer1.b2b.contoso.com` och så vidare. Med hjälp av en matris med värdnamn kan du konfigurera fler än ett värdnamn för en lyssnare, för att dirigera begär anden till en backend-pool. En lyssnare kan till exempel innehålla `contoso.com, fabrikam.com` som kommer att godkänna begär Anden för båda värd namnen.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="Lyssnare med jokertecken":::

>[!NOTE]
> Den här funktionen är i för hands version och är endast tillgänglig för Standard_v2 och WAF_v2 SKU för Application Gateway. Läs mer om för hands versionerna i [användnings villkor här](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

>[!NOTE]
>Den här funktionen är för närvarande endast tillgänglig via [Azure PowerShell](tutorial-multiple-sites-powershell.md) och [Azure CLI](tutorial-multiple-sites-cli.md). Portal support kommer snart.
> Observera att eftersom Portal stödet inte är helt tillgängligt, och om du bara använder parametern HostName, visas lyssnaren som en grundläggande lyssnare i portalen och kolumnen värd namn i list visningen visar inte värd namnen som är konfigurerade. Om du vill ändra en lyssnare med jokertecken kontrollerar du att du använder Azure PowerShell eller CLI tills det stöds i portalen.

I [Azure PowerShell](tutorial-multiple-sites-powershell.md)måste du använda `-HostNames` i stället för `-HostName` . Med värd namn kan du nämna upp till 5 värdnamn som kommaavgränsade värden och använda jokertecken. Till exempel `-HostNames "*.contoso.com,*.fabrikam.com"`

I [Azure CLI](tutorial-multiple-sites-cli.md)måste du använda `--host-names` i stället för `--host-name` . Med värd namn kan du nämna upp till 5 värdnamn som kommaavgränsade värden och använda jokertecken. Till exempel `--host-names "*.contoso.com,*.fabrikam.com"`

### <a name="allowed-characters-in-the-host-names-field"></a>Tillåtna tecken i fältet värdnamn:

* `(A-Z,a-z,0-9)` -alfanumeriska tecken
* `-` – bindestreck eller minus
* `.` -period som avgränsare
*   `*` -kan matcha flera tecken i det tillåtna intervallet
*   `?` -kan matcha med ett enskilt Character i det tillåtna intervallet

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>Villkor för användning av jokertecken och flera värdnamn i en lyssnare:

*   Du kan bara nämna upp till 5 värdnamn i en enda lyssnare
*   Asterisk `*` kan bara anges en gång i en komponent i ett domän format namn eller värdnamn. Till exempel component1 *. component2*. component3. `(*.contoso-*.com)` är giltig.
*   Det får bara finnas upp till två asterisker `*` i ett värdnamn. Är till exempel `*.contoso.*` giltigt och `*.contoso.*.*.com` ogiltigt.
*   Det får bara finnas 4 jokertecken i ett värdnamn. Till exempel, `????.contoso.com` `w??.contoso*.edu.*` är giltigt, men `????.contoso.*` är ogiltigt.
*   Att använda asterisk `*` och frågetecken `?` tillsammans i en komponent i ett värdnamn ( `*?` eller `?*` eller `**` ) är ogiltigt. Till exempel, `*?.contoso.com` och `**.contoso.com` är ogiltiga.

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>Överväganden och begränsningar för att använda jokertecken eller flera värdnamn i en lyssnare:

*   [SSL-terminering och slut punkt till slut punkt](ssl-overview.md) kräver att du konfigurerar protokollet som https och laddar upp ett certifikat som ska användas i lyssnar konfigurationen. Om det är en lyssnare för flera platser kan du även mata in värd namnet, vanligt vis är det CN för SSL-certifikatet. När du anger flera värdnamn i lyssnaren eller använder jokertecken måste du tänka på följande:
    *   Om det är ett jokertecken som *. contoso.com måste du ladda upp ett jokertecken med CN som *. contoso.com
    *   Om flera värdnamn anges i samma lyssnare måste du ladda upp ett SAN-certifikat (alternativa namn för certifikat mottagare) med CNs som matchar de värdnamn som anges.
*   Du kan inte använda ett reguljärt uttryck för att nämna värd namnet. Du kan bara använda jokertecken som asterisk (*) och frågetecken (?) för att skapa värd namns mönstret.
*   För hälso kontroll av Server delen kan du inte associera flera [anpassade avsökningar](application-gateway-probe-overview.md) per http-inställningar. I stället kan du avsöka en av webbplatserna på Server delen eller använda "127.0.0.1" för att avsöka backend-serverns localhost. Men när du använder jokertecken eller flera värdnamn i en lyssnare kommer begär Anden för alla angivna domän mönster att dirigeras till backend-poolen beroende på regeltyp (Basic eller Path-based).
*   Egenskaperna "hostname" tar en sträng som indatamängd, där du endast kan ange ett domän namn som inte är jokertecken och "värdnamn" använder en sträng mat ris som indatamängd, där du kan nämna upp till fem domän namn med jokertecken. Men båda egenskaperna kan inte användas samtidigt.
*   Det går inte att skapa en [omdirigerings](redirect-overview.md) regel med en mål lyssnare som använder jokertecken eller flera värdnamn.

Se [skapa flera webbplatser med Azure PowerShell](tutorial-multiple-sites-powershell.md) eller [med Azure CLI](tutorial-multiple-sites-cli.md) för steg-för-steg-guide om hur du konfigurerar jokertecken i en lyssnare för flera platser.

## <a name="host-headers-and-server-name-indication-sni"></a>Värdhuvuden och servernamnsindikator (SNI)

Det finns tre vanliga mekanismer för att aktivera flera platser inom samma infrastruktur.

1. Flera webbprogram på varsin unik IP-adress.
2. Använd värdnamn för att ha flera webbprogram på samma IP-adress.
3. Använd olika portar för att har flera webbprogram på samma IP-adress.

För närvarande Application Gateway stöder en enskild offentlig IP-adress där den lyssnar efter trafik. Det finns för närvarande inte stöd för att ha flera program, var och en med sin egen IP-adress. 

Application Gateway stöder flera program som var och en lyssnar på olika portar, men det här scenariot kräver att programmen accepterar trafik på portar som inte är standard. Detta är ofta inte en konfiguration som du vill använda.

Application Gateway förlitar sig på HTTP 1.1 värdhuvuden för att ha mer än en webbplats på samma offentliga IP-adress och port. De platser som finns i Application Gateway kan också ha stöd för TLS-avlastning med Servernamnindikator (SNI) TLS-tillägg. Det här scenariot innebär att klientens webbläsare och serverdels-webbservergrupp måste ha stöd för HTTP/1.1 och TLS-tillägg som det definieras i RFC 6066.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar flera plats värdar i Application Gateway
* [Använda Azure Portal](create-multiple-sites-portal.md)
* [Använda Azure PowerShell](tutorial-multiple-sites-powershell.md) 
* [Använda Azure CLI](tutorial-multiple-sites-cli.md)

Du kan besöka [Resource Manager-mallen för flera webbplatser](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) för en slutpunkt-till-slutpunkts mallbaserad distribution.
