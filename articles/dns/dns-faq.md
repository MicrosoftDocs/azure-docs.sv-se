---
title: Vanliga frågor och svar – Azure DNS
description: I den här artikeln får du veta mer om vanliga frågor och svar om Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/11/2021
ms.author: rohink
ms.openlocfilehash: cb14cb95ec1362782a634b0e62cfa2f8237a5852
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246390"
---
# <a name="azure-dns-faq"></a>Azure DNS vanliga frågor och svar

## <a name="about-azure-dns"></a>Om Azure DNS

### <a name="what-is-azure-dns"></a>Vad är Azure DNS?

Domain Name System (DNS) översätter eller matchar en webbplats eller ett tjänst namn till dess IP-adress. Azure DNS är en värdtjänst för DNS-domäner. Den ger namn matchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med hjälp av samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster.

DNS-domäner i Azure DNS finns i Azures globala nätverk med DNS-namnservrar. Det här systemet använder anycast-nätverk så att varje DNS-fråga besvaras av den närmast tillgängliga DNS-servern. Azure DNS ger snabb prestanda och hög tillgänglighet för din domän.

Azure DNS baseras på Azure Resource Manager. Azure DNS fördelarna med Resource Manager-funktioner som Azure-rollbaserad åtkomst kontroll, gransknings loggar och resurs låsning. Du kan hantera domäner och poster via Azure Portal, Azure PowerShell-cmdletar och plattforms oberoende Azure CLI. Program som kräver automatisk DNS-hantering kan integreras med tjänsten via REST API och SDK: er.

### <a name="how-much-does-azure-dns-cost"></a>Hur mycket kostar Azure DNS?

Azure DNS fakturerings modell baseras på antalet DNS-zoner som finns i Azure DNS. Den baseras också på antalet DNS-frågor som de får. Rabatter tillhandahålls utifrån användning.

Mer information finns på sidan med [Azure DNS priser](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Vad är serviceavtalet för Azure DNS?

Azure garanterar att giltiga DNS-begäranden får ett svar från minst en Azure DNS namn server 100% av tiden.

Mer information finns på sidan om [Azure DNS service avtal](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Vad är en DNS-zon? Är det samma som en DNS-domän? 

En domän är ett unikt namn i domän namn systemet. Ett exempel kan vara contoso.com.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Domänen contoso.com kan till exempel innehålla flera DNS-poster. Posterna kan innehålla mail.contoso.com för en e-postserver och \. en webb-contoso.com för en webbplats. Dessa poster finns i DNS-zonen contoso.com.

Ett domän namn är *bara ett namn*. En DNS-zon är en data resurs som innehåller DNS-poster för ett domän namn. Med Azure DNS kan du vara värd för en DNS-zon och hantera DNS-posterna för en domän i Azure. Den innehåller också DNS-namnservrar för att besvara DNS-frågor från Internet.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Måste jag köpa ett DNS-domännamn för att använda Azure DNS? 

Inte nödvändigt vis.

Du behöver inte köpa en domän som värd för en DNS-zon i Azure DNS. Du kan skapa en DNS-zon när som helst utan att du äger domän namnet. DNS-frågor för den här zonen löser endast om de dirigeras till de Azure DNS namnservrar som har tilldelats zonen.

Om du vill länka DNS-zonen till den globala DNS-hierarkin måste du köpa domän namnet. Sedan kan DNS-frågor var som helst i världen hitta din DNS-zon och svara med dina DNS-poster.

## <a name="azure-dns-features"></a>Azure DNS funktioner

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Finns det några begränsningar när du använder Ali Aset-poster för ett domän namns spetsigt Traffic Manager?

Ja. Du måste använda statiska offentliga IP-adresser med Azure Traffic Manager. Konfigurera det **externa slut punkts** målet genom att använda en statisk IP-adress. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Stöder Azure DNS DNS-baserad trafik dirigering eller slut punkts växling vid fel?

DNS-baserad trafik dirigering och slut punkts växling tillhandahålls av Traffic Manager. Traffic Manager är en separat Azure-tjänst som kan användas med Azure DNS. Mer information finns i [Översikt över Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Azure DNS stöder bara värdbaserade statiska DNS-domäner, där varje DNS-fråga för en specifik DNS-post alltid får samma DNS-svar.

### <a name="does-azure-dns-support-domain-name-registration"></a>Stöder Azure DNS domän namns registrering?

Nej. Azure DNS stöder för närvarande inte alternativet att köpa domän namn. Om du vill köpa domäner måste du använda en domän namns registrator från tredje part. Registratorn debiterar vanligt vis en liten års avgift. Domänerna kan sedan ligga i Azure DNS för hantering av DNS-poster. Mer information finns i [delegera en domän till Azure DNS](dns-domain-delegation.md).

Funktionen för att köpa domän namn spåras i Azures efter släpning. Använd feedback-webbplatsen för att [Registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Stöder Azure DNS DNSSEC?

Nej. Azure DNS stöder för närvarande inte DNSSEC (Domain Name System Security Extensions).

DNSSEC-funktionen spåras i Azure DNS efter släpning. Använd feedback-webbplatsen för att [Registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Stöder Azure DNS zon överföringar (AXFR/IXFR)?

Nej. Azure DNS stöder för närvarande zon överföringar. DNS-zoner kan [importeras till Azure DNS med hjälp av Azure CLI](dns-import-export.md). DNS-poster hanteras via [Azure DNS hanterings Portal](dns-operations-recordsets-portal.md), [REST API](/powershell/module/az.dns), [SDK](dns-sdk.md), [PowerShell-cmdletar](dns-operations-recordsets.md)eller [CLI-verktyget](dns-operations-recordsets-cli.md).

Zon överförings funktionen spåras i Azure DNS efter släpning. Använd feedback-webbplatsen för att [Registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Omdirigerar Azure DNS URL: er för support?

Nej. URL-omdirigerings tjänster är inte en DNS-tjänst. De fungerar på HTTP-nivå i stället för DNS-nivån. Vissa DNS-providrar är en URL-Omdirigerad tjänst som en del av det övergripande erbjudandet. Den här tjänsten stöds för närvarande inte av Azure DNS.

Funktionen URL-omdirigering spåras i Azure DNS efter släpning. Använd feedback-webbplatsen för att [Registrera ditt stöd för den här funktionen](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Stöder Azure DNS Utökad ASCII-kodning (8-bitars) för TXT-postuppsättningar?

Ja. Azure DNS stöder utökade ASCII-teckenuppsättningar för TXT-postuppsättningar. Men du måste använda den senaste versionen av Azure REST-API: er, SDK: er, PowerShell och CLI. Versioner som är äldre än 1 oktober 2017 eller SDK 2,1 stöder inte den utökade ASCII-uppsättningen. 

Du kan till exempel ange en sträng som värde för en TXT-post som har det utökade ASCII-tecken \ 128. Ett exempel är "abcd\128efgh." Azure DNS använder byte-värdet för det här specialtecknet, som är 128, i intern representation. Vid DNS-matchning returneras det här byte-värdet i svaret. Observera också att "ABC" och "\ 097 \ 098 \ 099" är utbytbara i mån av en lösning. 

Vi följer [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) Zone File Master format Escape Rules for TXT-poster. Nu kan du till exempel i `\` själva verket undanta allting per RFC. Om du anger `A\B` som TXT-postvärdet representeras och matchas det bara `AB` . Om du verkligen vill att TXT-posten ska ha en `A\B` upplösning måste du kringgå `\` igen. Som exempel anger du `A\\B` .

Det här stödet är för närvarande inte tillgängligt för TXT-poster som skapats från Azure Portal.

## <a name="alias-records"></a>Aliasposter

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Vad är några scenarier där Ali Asets poster är användbara?

Se avsnittet scenarier i [Översikt över Azure DNS Ali Aset-poster](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Vilka post typer stöds för Alian ost post uppsättningar?

Alias post uppsättningar stöds för följande post typer i en Azure DNS zon:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Vilka resurser stöds som mål för post uppsättningar för Ali Aset?

- **Peka på en offentlig IP-resurs från en DNS A/AAAA-postuppsättning.** Du kan skapa en A/AAAA-postuppsättning och göra den till en angiven aliasresurspost som pekar på en offentlig IP-resurs.
- **Peka på en Traffic Manager profil från en post uppsättning för DNS A/AAAA/CNAME.** Du kan peka på CNAME-Traffic Manager profil från en DNS CNAME-postuppsättning. Ett exempel är contoso.trafficmanager.net. Nu kan du också peka på en Traffic Manager profil som har externa slut punkter från en A-eller AAAA-postuppsättning i din DNS-zon.
- **Peka på en Azure Content Delivery Network-slutpunkt (CDN)**. Detta är användbart när du skapar statiska webbplatser med hjälp av Azure Storage och Azure CDN.
- **Peka på en annan DNS-post uppsättning inom samma zon.** Alias poster kan referera till andra post uppsättningar av samma typ. Du kan till exempel låta en DNS CNAME-postuppsättning vara ett alias för en annan CNAME-postuppsättning av samma typ. Den här ordningen är användbar om du vill att vissa post uppsättningar ska vara alias och vissa icke-alias.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Kan jag skapa och uppdatera Ali Asets poster från Azure Portal?

Ja. Du kan skapa eller hantera Ali Aset-poster i Azure Portal tillsammans med Azure REST-API: er, PowerShell, CLI och SDK: er.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Kommer det att finnas poster som hjälper till att kontrol lera att min DNS-uppsättning tas bort när den underliggande offentliga IP-adressen tas bort?

Ja. Den här funktionen är en av kärn funktionerna i Ali Aset-poster. Det hjälper dig att undvika potentiella avbrott för användare av ditt program.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Kommer det att finnas poster för att kontrol lera att min DNS-postuppsättning har uppdaterats till rätt IP-adress när den underliggande offentliga IP-adressen ändras?

Ja. Den här funktionen är en av kärn funktionerna i Ali Aset-poster. Det hjälper dig att undvika potentiella avbrott eller säkerhets risker för ditt program.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Finns det några begränsningar när du använder Alian A post uppsättningar för A-eller AAAA-poster för att peka på Traffic Manager?

Ja. För att peka på en Traffic Manager-profil som ett alias från en A-eller AAAA-postuppsättning får Traffic Manager profilen endast använda externa slut punkter. När du skapar de externa slut punkterna i Traffic Manager anger du slut punkternas faktiska IP-adresser.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Finns det ytterligare kostnad för att använda Ali Aset-poster?

Ali Aset är en kvalificering på en giltig DNS-postuppsättning. Det finns ingen ytterligare fakturering för Ali Aset-poster.

## <a name="use-azure-dns"></a>Använd Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Kan jag kombinera en domän med hjälp av Azure DNS och en annan DNS-Provider?

Ja. Azure DNS stöder samvärdande domäner med andra DNS-tjänster.

Om du vill konfigurera Co-hosting ändrar du NS-posterna för domänen så att de pekar på namnservrarna för båda providern. Namnserver posterna styr vilka providrar som tar emot DNS-frågor för domänen. Du kan ändra dessa NS-poster i Azure DNS, i den andra providern och i den överordnade zonen. Den överordnade zonen konfigureras vanligt vis via domän namns registratorn. Mer information om DNS-delegering finns i [delegering av DNS-domän](dns-domain-delegation.md).

Kontrol lera också att DNS-posterna för domänen är synkroniserade mellan båda DNS-leverantörerna. Azure DNS stöder för närvarande inte överföring av DNS-zoner. DNS-poster måste synkroniseras med hjälp av antingen [Azure DNS hanterings Portal](dns-operations-recordsets-portal.md), [REST API](/rest/api/dns/), [SDK](dns-sdk.md), [PowerShell-cmdletar](dns-operations-recordsets.md)eller [CLI-verktyget](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Måste jag delegera min domän till alla fyra Azure DNS namnservrar?

Ja. Azure DNS tilldelar fyra namnservrar till varje DNS-zon. Detta avtal är för fel isolering och ökad återhämtning. Delegera din domän till alla fyra namnservrar för att kvalificera dig för Azure DNS service avtal.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Vilka är användnings gränserna för Azure DNS?

Följande standard gränser gäller när du använder Azure DNS.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Kan jag flytta en Azure DNS zon mellan resurs grupper eller mellan prenumerationer?

Ja. DNS-zoner kan flyttas mellan resurs grupper eller mellan prenumerationer.

Det finns ingen inverkan på DNS-frågor när du flyttar en DNS-zon. Namnservrarna som tilldelas zonen förblir desamma. DNS-frågor bearbetas som normalt i hela.

Mer information och anvisningar om hur du flyttar DNS-zoner finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Hur lång tid tar det för DNS-ändringar att gälla?

Nya DNS-zoner och DNS-poster visas vanligt vis i Azure DNS namnservrar. Tids inställningen är några sekunder.

Det kan ta lite längre tid att ändra befintliga DNS-poster. De visas vanligt vis i Azure DNS namnservrar inom 60 sekunder. DNS-cachelagring av DNS-klienter och rekursiva DNS-matchare utanför Azure DNS kan också påverka tiden. Använd TTL-egenskapen (Time-to-Live) för varje post uppsättning för att kontrol lera den här cachens varaktighet.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Hur kan jag skydda mina DNS-zoner mot oavsiktlig borttagning?

Azure DNS hanteras med hjälp av Azure Resource Manager. Azure DNS fördelarna med de funktioner för åtkomst kontroll som Azure Resource Manager tillhandahåller. Rollbaserad åtkomst kontroll i Azure kan användas för att kontrol lera vilka användare som har Läs-eller Skriv behörighet till DNS-zoner och post uppsättningar. Resurs lås förhindrar oavsiktlig ändring eller borttagning av DNS-zoner och post uppsättningar.

Mer information finns i [skydda DNS-zoner och-poster](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Hur gör jag för att skapar du SPF-poster i Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Matchar Azure DNS namnservrar över IPv6? 

Ja. Azure DNS namnservrar är dubbla stackar. Dubbla stackar innebär att de har IPv4-och IPv6-adresser. Använd ett verktyg som nslookup för att hitta IPv6-adressen för de Azure DNS namnservrarna som tilldelats din DNS-zon. Ett exempel är `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Hur gör jag för att konfigurera ett IDN i Azure DNS?

Internationella domän namn (IDN) koda varje DNS-namn med hjälp av [punycode](https://en.wikipedia.org/wiki/Punycode). DNS-frågor görs med hjälp av dessa punycode-kodade namn.

Om du vill konfigurera IDN: er i Azure DNS konverterar du zon namnet eller post uppsättnings namnet till Punycode. Azure DNS stöder för närvarande inte inbyggd konvertering till eller från Punycode.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure DNS](dns-overview.md).

- [Lär dig mer om hur du använder Azure DNS för privata domäner](private-dns-overview.md).

- [Läs mer om DNS-zoner och-poster](dns-zones-records.md).

- [Kom igång med Azure DNS](dns-getstarted-portal.md).
