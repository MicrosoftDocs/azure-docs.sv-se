---
title: Översikt över DNS-zoner och poster – Azure DNS
description: Översikt över stöd för DNS-zoner och-poster i Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 41eb15a38c97532951723f12d1ac74c90c838eb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968204"
---
# <a name="overview-of-dns-zones-and-records"></a>Översikt över DNS-zoner och -poster

Den här sidan förklarar viktiga begrepp för domäner, DNS-zoner och DNS-poster och post uppsättningar och hur de stöds i Azure DNS.

## <a name="domain-names"></a>Domännamn

Domain Name System är en hierarki av domäner. Hierarkin startar från rotdomänen, vars namn är ”**.**”.  Under detta kommer toppdomänerna, till exempel ”com”, ”net”, ”org”, ”se” eller ”uk”.  Under dessa finns domänerna på den andra nivån, till exempel ”org.se” eller ”co.uk”. Domänerna i DNS-hierarkin distribueras globalt, värdbaserade av DNS-namnservrar runtom i världen.

En domän namns registrator är en organisation som gör det möjligt att köpa ett domän namn, till exempel `contoso.com` .  Genom att köpa ett domän namn får du rätt att kontrol lera DNS-hierarkin under det namnet, till exempel så att du kan dirigera namnet `www.contoso.com` till företagets webbplats. Registratorn kan vara värd för domänen på sina egna namnservrar åt dig, eller så kan du ange alternativa namnservrar.

Azure DNS tillhandahåller en globalt distribuerad namn server infrastruktur med hög tillgänglighet, som du kan använda för att vara värd för din domän. Genom att vara värd för dina domäner i Azure DNS kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API: er, verktyg, fakturering och support som andra Azure-tjänster.

Azure DNS stöder för närvarande inte köp av domän namn. Om du vill köpa ett domän namn måste du använda en domän namns registrator från tredje part. Registratorn debiterar vanligt vis en liten års avgift. Domänerna kan sedan hanteras i Azure DNS för hantering av DNS-poster. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).

## <a name="dns-zones"></a>DNS-zoner

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS-poster

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time-to-Live

TTL-värdet (Time to Live) eller TTL anger hur länge varje post cachelagras av klienter innan den omfrågas. I exemplet ovan är TTL 3600 sekunder eller 1 timme.

I Azure DNS anges TTL-värdet för post uppsättningen, inte för varje post, så samma värde används för alla poster i den angivna post uppsättningen.  Du kan ange ett TTL-värde mellan 1 och 2 147 483 647 sekunder.

### <a name="wildcard-records"></a>Jokertecken poster

Azure DNS stöder [poster med jokertecken](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Jokertecken returneras som svar på en fråga med ett matchande namn (om det inte finns en närmare matchning från en post uppsättning som inte är jokertecken). Azure DNS stöder post uppsättningar med jokertecken för alla post typer utom NS och SOA.

Om du vill skapa en post uppsättning med jokertecken använder du post uppsättningens namn \* . Du kan också använda ett namn med ' \* ' som dess vänstra etikett, till exempel " \* . foo".

### <a name="caa-records"></a>CAA-poster

CAA-poster låter domän ägare ange vilka certifikat utfärdare som har behörighet att utfärda certifikat för deras domän. Detta gör det möjligt för ca: er att undvika certifikat som utfärdas i vissa fall. CAA-poster har tre egenskaper:
* **Flaggor**: Detta är ett heltal mellan 0 och 255 som används för att representera den kritiska flagga som har en särskild betydelse per [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tagg**: en ASCII-sträng som kan vara något av följande:
    * **problem**: Använd det här om du vill ange ca: er som tillåts utfärda certifikat (alla typer)
    * **issuewild**: Använd det här om du vill ange ca: er som tillåts utfärda certifikat (endast certifikat för jokertecken)
    * **iodef**: Ange en e-postadress eller ett värdnamn som certifikat utfärdare kan meddela om obehöriga problem med certifikat förfrågningar
* **Värde**: värdet för en viss tagg som valts

### <a name="cname-records"></a>CNAME-poster

CNAME-postuppsättningar kan inte samexistera med andra postuppsättningar med samma namn. Du kan till exempel inte skapa en CNAME-postuppsättning med det relativa namnet "www" och en A-post med det relativa namnet "www" på samma gång.

Eftersom zonens Apex (namn = \@ ) alltid innehåller ns-och SOA-postuppsättningar som skapades när zonen skapades, kan du inte skapa en CNAME-postuppsättning på zonens Apex.

Dessa begränsningar uppstår i DNS-standarder och är inte begränsningar för Azure DNS.

### <a name="ns-records"></a>NS-poster

NS-postuppsättningen vid zonens Apex (namn \@ ) skapas automatiskt med varje DNS-zon och tas bort automatiskt när zonen tas bort (den kan inte tas bort separat).

Den här post uppsättningen innehåller namnen på de Azure DNS namnservrar som har tilldelats zonen. Du kan lägga till fler namnservrar i den här NS-postuppsättningen för att stödja samvärdbaserade domäner med fler än en DNS-Provider. Du kan också ändra TTL och metadata för den här post uppsättningen. Du kan dock inte ta bort eller ändra de förifyllda Azure DNS namnservrarna. 

Detta gäller endast NS-postuppsättningen i zonens Apex. Andra NAMNSERVER post uppsättningar i din zon (som används för att delegera underordnade zoner) kan skapas, ändras och tas bort utan begränsning.

### <a name="soa-records"></a>SOA-poster

En SOA-postuppsättning skapas automatiskt vid Apex för varje zon (Name = ' \@ ') och tas bort automatiskt när zonen tas bort.  Det går inte att skapa eller ta bort SOA-poster separat.

Du kan ändra alla egenskaper för SOA-posten förutom egenskapen Host, som är förkonfigurerad så att den refererar till det primära namn server namnet som tillhandahålls av Azure DNS.

Zon serie numret i SOA-posten uppdateras inte automatiskt när ändringar görs i posterna i zonen. Den kan uppdateras manuellt genom att du redigerar SOA-posten, om det behövs.

### <a name="spf-records"></a>SPF-poster

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-poster

[SRV-poster](https://en.wikipedia.org/wiki/SRV_record) används av olika tjänster för att ange server platser. När du anger en SRV-post i Azure DNS:

* *Tjänsten* och *protokollet* måste anges som en del av namnet på post uppsättningen, prefixet med under streck.  Till exempel " \_ SIP". \_ tcp.name '.  För en post i zonens Apex behöver du inte ange \@ i post namnet. Använd bara tjänsten och protokollet, till exempel " \_ SIP". \_ TCP.
* *Prioritet*, *vikt*, *port* och *mål* anges som parametrar för varje post i post uppsättningen.

### <a name="txt-records"></a>TXT-poster

TXT-poster används för att mappa domän namn till godtyckliga text strängar. De används i flera program, särskilt relaterade till e-postkonfiguration, till exempel en [SPF-princip (Sender Policy Framework)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) och [DomainKeys-identifierad e-post (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

DNS-standarder tillåter att en enskild TXT-post innehåller flera strängar, som var och en kan vara upp till 254 tecken långa. Om flera strängar används sammanfogas de av klienterna och behandlas som en enskild sträng.

När du anropar Azure DNS REST API måste du ange varje TXT-sträng separat.  När du använder Azure Portal-, PowerShell-eller CLI-gränssnitt bör du ange en enskild sträng per post, som automatiskt delas upp i 254 tecken segment om det behövs.

Flera strängar i en DNS-post ska inte förväxlas med flera TXT-poster i en TXT-postuppsättning.  En TXT-postuppsättning kan innehålla flera poster, *som var* och en kan innehålla flera strängar.  Azure DNS stöder en total sträng längd på upp till 1024 tecken i varje TXT-postuppsättning (i alla poster som kombineras).

## <a name="tags-and-metadata"></a>Taggar och metadata

### <a name="tags"></a>Taggar

Taggar är en lista över namn-värdepar och används av Azure Resource Manager för att märka resurser.  Azure Resource Manager använder taggar för att aktivera filtrerade vyer av din Azure-faktura och du kan också ange en princip för vilka taggar krävs. Mer information om taggar finns i [Ordna dina Azure-resurser med hjälp av taggar](../azure-resource-manager/management/tag-resources.md).

Azure DNS stöder användning av Azure Resource Manager-taggar på resurser i DNS-zonen.  Det går inte att använda taggar i DNS-postuppsättningar, även om alternativa metadata stöds på DNS-postuppsättningar enligt beskrivningen nedan.

### <a name="metadata"></a>Metadata

Som ett alternativ till att registrera uppsättnings taggar, Azure DNS stöder anteckningar av post uppsättningar med "metadata".  På samma sätt som taggar kan du använda metadata för att associera namn-värdepar med varje post uppsättning.  Detta kan vara användbart, till exempel för att registrera syftet med varje post uppsättning.  Till skillnad från taggar kan metadata inte användas för att tillhandahålla en filtrerad vy av din Azure-faktura och den kan inte anges i en Azure Resource Manager princip.

## <a name="etags"></a>ETags

Anta att två personer eller två processer försöker ändra en DNS-post på samma gång. Vilken är en WINS? Och vet vinnare att de har skrivit över ändringar som har skapats av någon annan?

Azure DNS använder ETags för att hantera samtidiga ändringar till samma resurs säkert. ETags skiljer sig från [Azure Resource Manager Taggar](#tags). Varje DNS-resurs (zon eller post uppsättning) har en associerad etag. När en resurs hämtas, hämtas även dess etag. När du uppdaterar en resurs kan du välja att skicka tillbaka etag så Azure DNS kan verifiera att etag på servern matchar. Eftersom varje uppdatering av en resurs resulterar i att etag återskapas, indikerar en etag-matchning att en samtidig ändring har skett. ETags kan också användas när du skapar en ny resurs för att se till att resursen inte redan finns.

Azure DNS PowerShell använder som standard ETags för att blockera samtidiga ändringar i zoner och post uppsättningar. Den valfria växeln kan användas för att dölja *etag-kontroller* , vilket innebär att alla samtidiga ändringar som har inträffat skrivs över.

På nivån för Azure DNS REST API anges ETags med HTTP-huvuden.  Deras beteende anges i följande tabell:

| Huvud | Beteende |
| --- | --- |
| Inget |PLACERINGen lyckas (inga etag-kontroller) |
| If-Match \<etag> |PLACERINGen lyckas endast om resursen finns och etag matchar |
| If-Match * |PLACERINGen lyckas endast om resursen finns |
| If-None-Match * |PLACERINGen lyckas endast om resursen inte finns |


## <a name="limits"></a>Gränser

Följande standard gränser gäller när du använder Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Nästa steg

* Om du vill börja använda Azure DNS kan du läsa om hur du [skapar en DNS-zon](./dns-getstarted-portal.md) och hur du [skapar DNS-poster](./dns-getstarted-portal.md).
* Om du vill migrera en befintlig DNS-zon, lär du dig att [Importera och exportera en DNS-zonfil](dns-import-export.md).