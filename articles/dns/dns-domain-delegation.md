---
title: Översikt över Azure DNS delegering
description: Lär dig hur du ändrar domändelegering och använder Azure DNS-namnservrar för att tillhandahålla domänvärdtjänster.
services: dns
author: rohinkoul
ms.service: dns
ms.date: 2/19/2019
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: 9304556edb5e6207296d8ee4e8392e345869cb92
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "76939043"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Delegering av DNS-zoner med Azure DNS

Med Azure DNS kan du vara värd för en DNS-zon och hantera DNS-posterna för en domän i Azure. För att DNS-frågor för en domän ska nå Azure DNS måste domänen delegeras till Azure DNS från den överordnade domänen. Tänk på att Azure DNS inte är domänregistratorn. Den här artikeln förklarar hur domändelegering fungerar och hur du delegerar domäner till Azure DNS.

## <a name="how-dns-delegation-works"></a>Så här fungerar DNS-delegering

### <a name="domains-and-zones"></a>Domäner och zoner

Domain Name System är en hierarki av domäner. Hierarkin startar från rotdomänen, vars namn är ”**.**”.  Under detta kommer toppdomänerna, till exempel ”com”, ”net”, ”org”, ”se” eller ”uk”.  Under dessa toppnivådomäner finns domäner på den andra nivån, till exempel ”org.se” eller ”co.uk”.  Och så vidare. Domänerna i DNS-hierarkin använder separata DNS-zoner. Zonerna distribueras globalt och finns på DNS-namnservrar runtom i världen.

**DNS-zon** – En domän är ett unikt namn i Domain Name System, t.ex. ”contoso.com”. En DNS-zon används som värd åt DNS-posterna för en viss domän. Domänen contoso.com kan t.ex. innehålla flera DNS-poster, som mail.contoso.com (för en e-postserver) och www.contoso.com (för en webbplats).

**Domänregistrator** – En domänregistrator är ett företag som kan tillhandahålla Internetdomännamn. De kontrollerar om Internetdomänen som du vill använda är tillgänglig så att du kan köpa den. När domännamnet har registrerats är du domännamnets juridiska ägare. Om du redan har en Internetdomän använder du domänregistratorn för att delegera till Azure DNS.

Mer information om ackrediterade domän registrators finns i [ICANN-ackrediterade registrators](https://www.icann.org/registrar-reports/accredited-list.html).

### <a name="resolution-and-delegation"></a>Matchning och delegering

Det finns två typer av DNS-servrar:

* En *auktoritativ* DNS-server är värd för DNS-zoner. Den svarar på DNS-frågor om poster i just dessa zoner.
* En *rekursiv* DNS-server är inte värd för DNS-zoner. Den svarar på alla DNS-frågor genom att anropa auktoritativa DNS-servrar för att samla in de data som den behöver.

Azure DNS tillhandahåller en auktoritativ DNS-tjänst.  Någon rekursiv DNS-tjänst tillhandahålls inte. Cloud Services och virtuella datorer i Azure konfigureras automatiskt för användning av en rekursiv DNS-tjänst som tillhandahålls separat som en del av Azures infrastruktur. Information om hur du ändrar dessa DNS-inställningar finns i [Namnmatchning i Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

DNS-klienter på datorer eller mobila enheter anropar vanligtvis en rekursiv DNS-server för att utföra DNS-frågor som klientprogrammen behöver.

När en rekursiv DNS-server tar emot en fråga om en DNS-post, till exempel ”www.contoso.com”, måste den först hitta namnservern som är värd för zonen för ”contoso.com”-domänen. För att hitta namnservern startar den vid rotnamnservrarna och letar därifrån upp namnservrarna som är värdar för ”com”-zonen. Därefter beordrar den ”com”-namnservrarna att hitta namnservrarna som är värdar för zonen ”contoso.com”.  Slutligen kan den fråga dessa namnservrar efter ”www.contoso.com”.

Den här proceduren kallas att ”matcha” DNS-namnet. Strikt sett omfattar DNS-matchningen ytterligare steg, till exempel att följa CNAME-poster, men detta är inte viktigt för att förstå hur DNS-delegering fungerar.

Hur ”pekar” en överordnad zon på namnservrarna för en underordnad zon? Detta sker med hjälp av en särskild typ av DNS-post kallad en NS-post (NS står för namnserver). Rotzonen innehåller exempelvis NS-poster för ”com” och visar namnservrar för ”com”-zonen. ”Com”-zonen innehåller i sin tur NS-poster för ”contoso.com”, som visar namnservrarna för zonen ”contoso.com”. Att konfigurera NS-posterna för en underordnad zon i en överordnad zon kallas för att delegera domänen.

Följande bild visar ett exempel på en DNS-fråga. Contoso.net och partners.contoso.net är Azure DNS-zoner.

![DNS-namnserver](./media/dns-domain-delegation/image1.png)

1. Klientbegäranden `www.partners.contoso.net` från den lokala DNS-servern.
2. Eftersom den lokala DNS-servern inte har posten skickar den en begäran till rotservern.
3. Rotservern har inte posten, men känner till adressen till `.net`-servern och ger den adressen till DNS-servern
4. Den lokala DNS-servern skickar begäran till `.net` namnserver servern.
5. `.net`Namnserver posten har inte posten, men känner till adressen till `contoso.net` namn servern. I det här fallet svarar den med adressen till namn servern för den DNS-zon som finns i Azure DNS.
6. Den lokala DNS-servern skickar begäran till namnservern för zonen som `contoso.net` finns i Azure DNS.
7. Zonen saknar `contoso.net` posten men känner till namnserver för `partners.contoso.net` och svarar med adressen. I det här fallet är det en DNS-zon som finns i Azure DNS.
8. Den lokala DNS-servern skickar begäran till namnserver `partners.contoso.net` zonen.
9. `partners.contoso.net`Zonen har en post och svarar med IP-adressen.
10. Den lokala DNS-servern tillhandahåller klientens IP-adress
11. Klienten ansluter till webbplatsen `www.partners.contoso.net`.

Varje delegering har i själva verket två kopior av NS-posterna: en i den överordnade zonen som pekar på den underordnade zonen och en annan i den underordnade zonen. Contoso.net-zonen innehåller NS-posterna för contoso.net (utöver NS-posterna i ”net”). Dessa poster kallas för auktoritativa NS-poster och finns överst i den underordnade zonen.

## <a name="next-steps"></a>Nästa steg

Läs om hur du [delegerar din domän till Azure DNS](dns-delegate-domain-azure-dns.md)

