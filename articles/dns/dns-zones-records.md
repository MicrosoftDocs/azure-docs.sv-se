---
title: Översikt över DNS-zoner och -poster – Azure DNS
description: Översikt över stöd för hantering av DNS-zoner och -poster i Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 04/20/2021
ms.author: rohink
ms.openlocfilehash: 26eefe5cbcef417dee1a400b492ee847394ca6a9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816971"
---
# <a name="overview-of-dns-zones-and-records"></a>Översikt över DNS-zoner och -poster

Den här artikeln beskriver viktiga begrepp för domäner, DNS-zoner, DNS-poster och postuppsättningar. Du får lära dig hur det stöds i Azure DNS.

## <a name="domain-names"></a>Domännamn

Domain Name System är en hierarki av domäner. Hierarkin startar från rotdomänen, vars namn är ”**.**”.  Under detta kommer toppdomänerna, till exempel ”com”, ”net”, ”org”, ”se” eller ”uk”.  Under toppnivådomänerna finns domäner på den andra nivån, till exempel "org.uk" eller "co.jp". Domänerna i DNS-hierarkin distribueras globalt med DNS-namnservrar runtom i världen.

En domännamnsregistrator är en organisation som gör att du kan köpa ett domännamn, till exempel `contoso.com` .  Att köpa ett domännamn ger dig rätt att styra DNS-hierarkin under det namnet, till exempel att du kan dirigera namnet till `www.contoso.com` företagets webbplats. Registratorn kan vara värd för domänen på sina egna namnservrar för din räkning eller låta dig ange alternativa namnservrar.

Azure DNS en globalt distribuerad namnserverinfrastruktur med hög tillgänglighet som du kan använda som värd för din domän. Genom att vara värd för dina domäner Azure DNS kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg, fakturering och support som dina andra Azure-tjänster.

Azure DNS stöder för närvarande inte köp av domännamn. Om du vill köpa ett domännamn måste du använda en domännamnsregistrator från tredje part. Registratorn debiterar vanligtvis en liten årlig avgift. Domänerna kan sedan finnas i en Azure DNS för hantering av DNS-poster. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).

## <a name="dns-zones"></a>DNS-zoner

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS-poster

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time-to-live

Time to live, eller TTL, anger hur länge varje post cachelagras av klienter innan de tas iqueried. I exemplet ovan är TTL-värdet 3 600 sekunder eller 1 timme.

I Azure DNS anges TTL för postuppsättningen, inte för varje post, så samma värde används för alla poster i postuppsättningen.  Du kan ange ett TTL-värde mellan 1 och 2 147 483 647 sekunder.

### <a name="wildcard-records"></a>Poster med jokertecken

Azure DNS stöder [poster med jokertecken](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Poster med jokertecken returneras som svar på en fråga med ett matchande namn, såvida det inte finns en närmare matchning från en postuppsättning med icke-jokertecken. Azure DNS har stöd för postuppsättningar med jokertecken för alla posttyper utom NS och SOA.

Om du vill skapa en postuppsättning med jokertecken använder du postuppsättningsnamnet \* ' '. Du kan också använda ett namn med " " som den vänstra \* etiketten, till exempel \* ".foo".

### <a name="caa-records"></a>CAA-poster

MED CAA-poster kan domänägare ange vilka certifikatutfärdare (CA) som har behörighet att utfärda certifikat för sin domän. Den här posten gör det möjligt för certifikat certifikat att undvika felaktig utfärdande av certifikat i vissa fall. CAA-poster har tre egenskaper:
* **Flaggor:** Det här fältet är ett heltal mellan 0 och 255, som används för att representera den kritiska flaggan som har särskild betydelse enligt [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tagg:** en ASCII-sträng som kan vara något av följande:
    * **problem:** om du vill ange certifikat som har tillåtelse att utfärda certifikat (alla typer)
    * **issuewild:** om du vill ange certifikat som har tillåtelse att utfärda certifikat (endast jokertecken))
    * **iodef:** ange en e-postadress eller värddatornamn som certifikatvärdar kan meddela om begäranden om att utfärda obehöriga certifikat
* **Värde:** värdet för den valda taggen

### <a name="cname-records"></a>CNAME-poster

CNAME-postuppsättningar kan inte samexistera med andra postuppsättningar med samma namn. Du kan till exempel inte skapa en CNAME-postuppsättning med det relativa namnet "www" och en A-post med det relativa namnet "www" samtidigt.

Eftersom zonens toppdomän (namn = ' ') alltid innehåller NS- och SOA-postuppsättningarna när zonen skapas, kan du inte skapa en CNAME-postuppsättning i \@ zonens toppdomän.

Den här begränsningarna kommer sig av DNS-standarderna och är inte begränsningar i Azure DNS.

### <a name="ns-records"></a>NS-poster

NS-posten som anges i zonens toppdomän (namn ' ') skapas automatiskt med varje DNS-zon och tas bort automatiskt när \@ zonen tas bort. Den kan inte tas bort separat.

Den här postuppsättningen innehåller namnen på de Azure DNS namnservrar som tilldelats zonen. Du kan lägga till fler namnservrar i den här NS-postuppsättningen för att stödja värdbytesdomäner med fler än en DNS-provider. Du kan också ändra TTL och metadata för den här postuppsättningen. Det är dock inte tillåtet att ta bort eller ändra Azure DNS ifyllda namnservrar. 

Den här begränsningen gäller endast för NS-postuppsättningen i zonens toppdomän. Andra NS-postuppsättningar i din zon (som används för att delegera underordnade zoner) kan skapas, ändras och tas bort utan begränsning.

### <a name="soa-records"></a>SOA-poster

En SOA-postuppsättning skapas automatiskt högst upp i varje zon (namn = ' ') och tas bort automatiskt när \@ zonen tas bort.  SOA-poster kan inte skapas eller tas bort separat.

Du kan ändra alla egenskaper för SOA-posten förutom egenskapen "host". Den här egenskapen är förkonfigurerad så att den refererar till det primära namnservernamnet som anges Azure DNS.

Zonserienumret i SOA-posten uppdateras inte automatiskt när ändringar görs i posterna i zonen. Den kan uppdateras manuellt genom att redigera SOA-posten om det behövs.

### <a name="spf-records"></a>SPF-poster

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-poster

[SRV-poster](https://en.wikipedia.org/wiki/SRV_record) används av olika tjänster för att ange serverplatser. När du anger en SRV-post i Azure DNS:

* Tjänsten *och* protokollet *måste* anges som en del av postuppsättningens namn, med prefixet understreck.  Till exempel \_ " sip. \_ tcp.name".  För en post i zonens toppdomän behöver du inte ange " " i postnamnet, bara använda tjänsten och \@ protokollet, till exempel \_ " sip. \_ tcp'.
* Prioritet, *vikt,* *port* och *mål anges* som parametrar för varje post i postuppsättningen. 

### <a name="txt-records"></a>TXT-poster

TXT-poster används för att mappa domännamn till godtyckliga textsträngar. De används i flera program, särskilt relaterade till e-postkonfiguration, till exempel [SPF (Sender Policy Framework)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) och [DomainKeys Identified Mail (DKIM).](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)

DNS-standarderna tillåter att en enda TXT-post innehåller flera strängar, som var och en kan innehålla upp till 254 tecken. Om flera strängar används sammanfogas de av klienter och behandlas som en enda sträng.

När du anropar Azure DNS REST API måste du ange varje TXT-sträng separat.  När du använder Azure Portal-, PowerShell- eller CLI-gränssnitt bör du ange en enda sträng per post, som automatiskt delas upp i segment med 254 tecken om det behövs.

De flera strängarna i en DNS-post ska inte förväxlas med flera TXT-poster i en TXT-postuppsättning.  En TXT-postuppsättning kan innehålla flera poster som *var och en* kan innehålla flera strängar.  Azure DNS stöder en total stränglängd på upp till 1 024 tecken i varje TXT-postuppsättning (för alla poster i kombination).

## <a name="tags-and-metadata"></a>Taggar och metadata

### <a name="tags"></a>Taggar

Taggar är en lista över namn/värde-par och används av Azure Resource Manager för att märka resurser. Azure Resource Manager använder taggar för att aktivera filtrerade vyer för din Azure-faktura och du kan även ange en princip för vissa taggar. Mer information om taggar finns i [Ordna dina Azure-resurser med hjälp av taggar](../azure-resource-manager/management/tag-resources.md).

Azure DNS stöder användning Azure Resource Manager taggar på DNS-zonresurser.  Den stöder inte taggar på DNS-postuppsättningar, men som en alternativ "metadata" stöds på DNS-postuppsättningar enligt förklaringen nedan.

### <a name="metadata"></a>Metadata

Som ett alternativ till postuppsättningstaggar Azure DNS stöd för att kommentera postuppsättningar med hjälp av "metadata".  I likhet med taggar kan du med metadata associera namn/värde-par med varje postuppsättning.  Den här funktionen kan vara användbar, till exempel för att registrera syftet med varje postuppsättning.  Till skillnad från taggar kan metadata inte användas för att tillhandahålla en filtrerad vy över din Azure-faktura och kan inte anges i en Azure Resource Manager princip.

## <a name="etags"></a>Etags

Anta att två personer eller två processer försöker ändra en DNS-post på samma gång. Vilken vinner? Och vet vinnare att de har skrivit över ändringar som skapats av någon annan?

Azure DNS använder Etags för att hantera samtidiga ändringar i samma resurs på ett säkert sätt. Etags är separata från [Azure Resource Manager "Taggar"](#tags). Varje DNS-resurs (zon eller postuppsättning) har en Etag som är associerad med den. När en resurs hämtas hämtas även dess Etag. När du uppdaterar en resurs kan du välja att skicka tillbaka Etag så Azure DNS kan verifiera att Etag på servern matchar. Eftersom varje uppdatering av en resurs resulterar i att Etag återskapas indikerar ett matchningsfel för Etag att en samtidig ändring har skett. Etags kan också användas när du skapar en ny resurs för att säkerställa att resursen inte redan finns.

Som standard använder Azure DNS PowerShell Etags för att blockera samtidiga ändringar av zoner och postuppsättningar. Den *valfria växeln -Overwrite* kan användas för att förhindra Etag-kontroller, i vilket fall eventuella samtidiga ändringar som har inträffat skrivs över.

På nivån för Azure DNS REST API anges Etags med HTTP-huvuden.  Beteendet anges i följande tabell:

| Huvud | Beteende |
| --- | --- |
| Inget |PUT lyckas alltid (inga Etag-kontroller) |
| If-match \<etag> |PUT lyckas bara om resursen finns och Etag matchar |
| If-match * |PUT lyckas bara om det finns en resurs |
| If-none-match * |PUT lyckas bara om resursen inte finns |


## <a name="limits"></a>Gränser

Följande standardgränser gäller när du använder Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Nästa steg

* Om du vill Azure DNS kan du lära dig hur du [skapar en DNS-zon](./dns-getstarted-portal.md) och [skapar DNS-poster.](./dns-getstarted-portal.md)
* Om du vill migrera en befintlig DNS-zon lär du dig [hur du importerar och exporterar en DNS-zonfil.](dns-import-export.md)