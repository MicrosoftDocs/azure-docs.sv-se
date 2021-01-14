---
title: Så här fungerar Azure Traffic Manager | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Traffic Manager dirigerar trafik för hög prestanda och tillgänglighet för dina webb program
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: duau
ms.openlocfilehash: a1e1bd107e8b3b9209f99d1abfc4d7e391c3c4a6
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184347"
---
# <a name="how-traffic-manager-works"></a>Så här fungerar Traffic Manager

Med Azure Traffic Manager kan du styra trafiken mellan dina program slut punkter. En slutpunkt är en Internetansluten tjänst i eller utanför Azure.

Traffic Manager ger två viktiga fördelar:

- Fördelning av trafik enligt en av flera [metoder för trafik cirkulation](traffic-manager-routing-methods.md)
- [Kontinuerlig övervakning av slut punkts hälsa](traffic-manager-monitoring.md) och automatisk redundans när slut punkter inte fungerar

När en klient försöker ansluta till en tjänst måste den först matcha tjänstens DNS-namn till en IP-adress. Klienten ansluter sedan till den IP-adressen för att få åtkomst till tjänsten.

**Den viktigaste punkten att förstå är att Traffic Manager fungerar på DNS-nivå.**  Traffic Manager använder DNS för att dirigera klienter till vissa tjänst slut punkter baserat på reglerna för metoden för trafik dirigering. Klienterna ansluter till den valda slut punkten **direkt**. Traffic Manager är inte en proxy eller gateway. Traffic Manager ser inte trafiken som passerar mellan klienten och tjänsten.

## <a name="traffic-manager-example"></a>Traffic Manager exempel

Contoso Corp har utvecklat en ny partner Portal. URL: en för den här portalen är `https://partners.contoso.com/login.aspx` . Programmet finns i tre regioner i Azure. För att förbättra tillgängligheten och maximera globala prestanda använder de Traffic Manager för att distribuera klient trafik till den närmast tillgängliga slut punkten.

För att uppnå den här konfigurationen slutförs följande steg:

1. Distribuera tre instanser av tjänsten. DNS-namnen för dessa distributioner är "contoso-us.cloudapp.net", "contoso-eu.cloudapp.net" och "contoso-asia.cloudapp.net".
1. Skapa en Traffic Manager-profil med namnet "contoso.trafficmanager.net" och konfigurera den så att den använder metoden "prestanda" för Traffic-routing över de tre slut punkterna.
1. Konfigurera deras anpassad domän namn, "partners.contoso.com", så att de pekar på "contoso.trafficmanager.net" med en DNS CNAME-post.

![Traffic Manager DNS-konfiguration][1]

> [!NOTE]
> När du använder en anpassad-domän med Azure Traffic Manager måste du använda en CNAME för att peka ditt anpassad-domännamn till ditt Traffic Manager domän namn. DNS-standarder tillåter inte att du skapar en CNAME vid en domäns "Apex" (eller rot). Därför kan du inte skapa en CNAME för "contoso.com" (kallas ibland för en "blott"-domän). Du kan bara skapa en CNAME för en domän under "contoso.com", till exempel "www.contoso.com". För att undvika den här begränsningen rekommenderar vi att du är värd för din DNS-domän på [Azure DNS](../dns/dns-overview.md) och använder [Ali Aset](../dns/tutorial-alias-tm.md) för att peka på din Traffic Manager-profil. Alternativt kan du använda en enkel HTTP-omdirigering för att dirigera begär Anden för "contoso.com" till ett alternativt namn, till exempel "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Hur klienter ansluter med hjälp av Traffic Manager

När en klient begär sidan från föregående exempel `https://partners.contoso.com/login.aspx` utför klienten följande steg för att lösa DNS-namnet och upprätta en anslutning:

![Anslutnings etablering med hjälp av Traffic Manager][2]

1. Klienten skickar en DNS-fråga till den konfigurerade rekursiva DNS-tjänsten för att matcha namnet "partners.contoso.com". En rekursiv DNS-tjänst, som ibland kallas för en lokal DNS-tjänst, är inte värd för DNS-domäner direkt. I stället för att klienten ska kunna kontakta de olika auktoritativa DNS-tjänsterna över Internet som behövs för att matcha ett DNS-namn.
2. Den rekursiva DNS-tjänsten hittar namnservrarna för domänen "contoso.com" för att matcha DNS-namnet. Den kontaktar sedan dessa namnservrar för att begära DNS-posten "partners.contoso.com". Contoso.com DNS-servrarna returnerar CNAME-posten som pekar på contoso.trafficmanager.net.
3. Sedan hittar den rekursiva DNS-tjänsten namnservrarna för domänen "trafficmanager.net", som tillhandahålls av Azure Traffic Manager-tjänsten. Den skickar sedan en begäran om DNS-posten "contoso.trafficmanager.net" till dessa DNS-servrar.
4. Traffic Manager namnservrar får begäran. De väljer en slut punkt baserat på:

    - Det konfigurerade läget för varje slut punkt (inaktiverade slut punkter returneras inte)
    - Den aktuella hälsan för varje slut punkt, enligt Traffic Manager hälso kontroller. Mer information finns i [Traffic Manager slut punkts övervakning](traffic-manager-monitoring.md).
    - Den valda metoden för trafik dirigering. Mer information finns i [Traffic Manager metoder för routning](traffic-manager-routing-methods.md).

5. Den valda slut punkten returneras som en annan DNS CNAME-post. I det här fallet kan vi anta att contoso-eu.cloudapp.net returneras.
6. Sedan hittar den rekursiva DNS-tjänsten namnservrarna för domänen "cloudapp.net". Den kontaktar dessa namnservrar för att begära DNS-posten "contoso-eu.cloudapp.net". En DNS-post som innehåller IP-adressen för den EU-baserade tjänst slut punkten returneras.
7. Den rekursiva DNS-tjänsten konsoliderar resultaten och returnerar ett enskilt DNS-svar till klienten.
8. Klienten får DNS-resultatet och ansluter till den tilldelade IP-adressen. Klienten ansluter direkt till program tjänstens slut punkt, inte via Traffic Manager. Eftersom det är en HTTPS-slutpunkt utför klienten den nödvändiga SSL/TLS-hand skakningen och gör sedan en HTTP GET-begäran för sidan "/login.aspx".

Den rekursiva DNS-tjänsten cachelagrar de DNS-svar som den tar emot. DNS-matcharen på klient enheten cachelagrar också resultatet. Cachelagring gör att efterföljande DNS-frågor kan besvaras snabbare med hjälp av data från cachen i stället för att fråga andra namnservrar. Längden på cachen bestäms av egenskapen "Time-to-Live" (TTL) för varje DNS-post. Kortare värden leder till snabbare cache-utgång och därmed fler turer till Traffic Manager namnservrar. Längre värden innebär att det kan ta längre tid att dirigera trafik bort från en misslyckad slut punkt. Med Traffic Manager kan du konfigurera det TTL som används i Traffic Manager DNS-svar så att det blir så lågt som 0 sekunder och så högt som 2 147 483 647 sekunder (det maximala intervallet är kompatibelt med [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)), så att du kan välja det värde som bäst balanserar behoven hos ditt program.

## <a name="faqs"></a>Vanliga frågor och svar

* [Vilken IP-adress använder Traffic Manager?](./traffic-manager-faqs.md#what-ip-address-does-traffic-manager-use)

* [Vilka typer av trafik kan dirigeras med hjälp av Traffic Manager?](./traffic-manager-faqs.md#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [Stöder Traffic Manager "tröga" sessioner?](./traffic-manager-faqs.md#does-traffic-manager-support-sticky-sessions)

* [Varför ser jag ett HTTP-fel när jag använder Traffic Manager?](./traffic-manager-faqs.md#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Vilken prestanda påverkas om du använder Traffic Manager?](./traffic-manager-faqs.md#what-is-the-performance-impact-of-using-traffic-manager)

* [Vilka program protokoll kan jag använda med Traffic Manager?](./traffic-manager-faqs.md#what-application-protocols-can-i-use-with-traffic-manager)

* [Kan jag använda Traffic Manager med domän namnet "blott"?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-a-naked-domain-name)

* [Anser Traffic Manager klient under nät adressen vid hantering av DNS-frågor?](./traffic-manager-faqs.md#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [Vad är DNS TTL och hur påverkar det mina användare?](./traffic-manager-faqs.md#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [Hur hög eller låg kan jag ange TTL för Traffic Manager svar?](./traffic-manager-faqs.md#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Hur kan jag förstå hur många frågor som kommer till min profil?](./traffic-manager-faqs.md#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Nästa steg

Läs mer om Traffic Manager [slut punkts övervakning och automatisk redundans](traffic-manager-monitoring.md).

Läs mer om hur du Traffic Manager [metoder för trafik cirkulation](traffic-manager-routing-methods.md).

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png