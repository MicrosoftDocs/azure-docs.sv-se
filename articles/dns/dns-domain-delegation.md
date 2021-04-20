---
title: översikt Azure DNS delegering
description: Lär dig hur du ändrar domändelegering och använder Azure DNS-namnservrar för att tillhandahålla domänvärdtjänster.
services: dns
author: rohinkoul
ms.service: dns
ms.date: 04/19/2021
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: 4753b07cc2f3ccd998c26a3392eb08c8761dd6f7
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738854"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Delegering av DNS-zoner med Azure DNS

Med Azure DNS kan du vara värd för en DNS-zon och hantera DNS-posterna för en domän i Azure. För att DNS-frågor för en domän ska nå Azure DNS måste domänen delegeras till Azure DNS från den överordnade domänen. Tänk på Azure DNS inte är domänregistratorn. Den här artikeln förklarar hur domändelegering fungerar och hur du delegerar domäner till Azure DNS.

## <a name="how-dns-delegation-works"></a>Så här fungerar DNS-delegering

### <a name="domains-and-zones"></a>Domäner och zoner

Domain Name System är en hierarki av domäner. Hierarkin startar från rotdomänen, vars namn är ”**.**”.  Under detta kommer toppdomänerna, till exempel ”com”, ”net”, ”org”, ”se” eller ”uk”.  Under dessa toppnivådomäner finns domäner på den andra nivån, till exempel ”org.se” eller ”co.uk”.  Och så vidare. Domänerna i DNS-hierarkin använder separata DNS-zoner. Zonerna distribueras globalt och finns på DNS-namnservrar runtom i världen.

**DNS-zon** – En domän är ett unikt namn i Domain Name System, t.ex. ”contoso.com”. En DNS-zon används som värd åt DNS-posterna för en viss domän. Domänen contoso.com kan t.ex. innehålla flera DNS-poster, som mail.contoso.com (för en e-postserver) och www.contoso.com (för en webbplats).

**Domänregistrator** – En domänregistrator är ett företag som kan tillhandahålla Internetdomännamn. De kontrollerar om Internetdomänen som du vill använda är tillgänglig så att du kan köpa den. När domännamnet har registrerats är du domännamnets juridiska ägare. Om du redan har en Internetdomän använder du den aktuella domänregistratorn för att delegera till Azure DNS.

Mer information om auktoriserade domänregistratorer finns i [ICANN-Ackrediterade registratorer.](https://www.icann.org/registrar-reports/accredited-list.html)

### <a name="resolution-and-delegation"></a>Matchning och delegering

Det finns två typer av DNS-servrar:

* En *auktoritativ* DNS-server är värd för DNS-zoner. Den svarar på DNS-frågor om poster i just dessa zoner.
* En *rekursiv* DNS-server är inte värd för DNS-zoner. Den svarar på alla DNS-frågor genom att anropa auktoritativa DNS-servrar för att samla in de data som den behöver.

Azure DNS tillhandahåller en auktoritativ DNS-tjänst.  Den tillhandahåller inte en rekursiv DNS-tjänst. Cloud Services och virtuella datorer i Azure konfigureras automatiskt för användning av en rekursiv DNS-tjänst som tillhandahålls separat som en del av Azures infrastruktur. Information om hur du ändrar dessa DNS-inställningar finns i [Namnmatchning i Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

DNS-klienter på datorer eller mobila enheter anropar vanligtvis en rekursiv DNS-server för att göra dns-frågor som klientprogrammen behöver.

När en rekursiv DNS-server tar emot en fråga om en DNS-post, till exempel ”www.contoso.com”, måste den först hitta namnservern som är värd för zonen för ”contoso.com”-domänen. För att hitta namnservern startar den vid rotnamnservrarna och letar därifrån upp namnservrarna som är värdar för ”com”-zonen. Därefter beordrar den ”com”-namnservrarna att hitta namnservrarna som är värdar för zonen ”contoso.com”.  Slutligen kan den fråga dessa namnservrar efter "www.contoso.com".

Den här proceduren kallas att ”matcha” DNS-namnet. Strikt sett innehåller DNS-upplösningen fler steg, till exempel att följa CME-poster, men det är inte viktigt för att förstå hur DNS-delegering fungerar.

Hur ”pekar” en överordnad zon på namnservrarna för en underordnad zon? Detta sker med hjälp av en särskild typ av DNS-post kallad en NS-post (NS står för namnserver). Rotzonen innehåller exempelvis NS-poster för ”com” och visar namnservrar för ”com”-zonen. ”Com”-zonen innehåller i sin tur NS-poster för ”contoso.com”, som visar namnservrarna för zonen ”contoso.com”. Att konfigurera NS-posterna för en underordnad zon i en överordnad zon kallas för att delegera domänen.

Följande bild visar ett exempel på en DNS-fråga. Contoso.net och partners.contoso.net är Azure DNS-zoner.

![DNS-namnserver](./media/dns-domain-delegation/image1.png)

1. Klientbegäranden `www.partners.contoso.net` från den lokala DNS-servern.
2. Den lokala DNS-servern har inte posten, så den gör en begäran till rotnamnservern.
3. Rotnamnservern har inte posten, men känner till adressen till namnservern och tillhandahåller den adressen `.net` till DNS-servern
4. Den lokala DNS-servern skickar begäran till `.net` namnservern.
5. Namnservern `.net` har inte posten men känner till adressen till `contoso.net` namnservern. I det här fallet svarar den med adressen till namnservern för DNS-zonen som finns i Azure DNS.
6. Den lokala DNS-servern skickar begäran till namnservern för zonen `contoso.net` som finns i Azure DNS.
7. Zonen `contoso.net` har inte posten men känner till namnservern för `partners.contoso.net` och svarar med adressen. I det här fallet är det en DNS-zon som finns i Azure DNS.
8. Den lokala DNS-servern skickar begäran till namnservern för `partners.contoso.net` zonen.
9. Zonen `partners.contoso.net` har A-posten och svarar med IP-adressen.
10. Den lokala DNS-servern tillhandahåller IP-adressen till klienten
11. Klienten ansluter till webbplatsen `www.partners.contoso.net`.

Varje delegering har i själva verket två kopior av NS-posterna: en i den överordnade zonen som pekar på den underordnade zonen och en annan i den underordnade zonen. Contoso.net-zonen innehåller NS-posterna för contoso.net (utöver NS-posterna i ”net”). Dessa poster kallas för auktoritativa NS-poster och finns överst i den underordnade zonen.

## <a name="next-steps"></a>Nästa steg

Läs om hur du [delegerar din domän till Azure DNS](dns-delegate-domain-azure-dns.md)
