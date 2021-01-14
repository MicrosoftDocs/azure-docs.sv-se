---
title: Metoder för routning av Azure Traffic Manager-trafik
description: De här artiklarna hjälper dig att förstå de olika metoder för trafikroutning som används av Traffic Manager
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: duau
ms.openlocfilehash: 0eb49f3c2acc31cba7b245995cf3bcb579113e4c
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183821"
---
# <a name="traffic-manager-routing-methods"></a>Traffic Manager-dirigeringsmetoder

Azure Traffic Manager stöder sex metoder för trafik cirkulation för att fastställa hur nätverks trafik ska dirigeras till de olika tjänst slut punkterna. För en profil använder Traffic Manager den trafik-routningsmetod som är kopplad till den för varje DNS-fråga den tar emot. Traffic-routing-metoden avgör vilken slut punkt som returneras i DNS-svaret.

Följande metoder för trafik cirkulation är tillgängliga i Traffic Manager:

* **[Prioritet](#priority-traffic-routing-method):** Välj **prioritet** när du vill använda en primär tjänst slut punkt för all trafik och ange säkerhets kopior om den primära eller säkerhets kopierings slut punkterna inte är tillgängliga.
* **[Viktad](#weighted):** Välj **viktad** när du vill distribuera trafik över en uppsättning slut punkter, antingen jämnt eller enligt de vikter som du definierar.
* **[Prestanda](#performance):** Välj **prestanda** när du har slut punkter på olika geografiska platser och du vill att slutanvändarna ska använda den "närmast" slut punkten i förhållande till den lägsta nätverks fördröjningen.
* **[Geografisk](#geographic):** Välj **geografisk** så att användarna dirigeras till specifika slut punkter (Azure, externa eller kapslade) baserat på vilken geografisk plats deras DNS-fråga kommer från. Detta ger Traffic Manager kunder möjlighet att aktivera scenarier där man känner till en användares geografiska region och dirigerar dem baserat på det är viktigt. Exempel innefattar att följa data suveränitets uppdrag, lokalisering av innehåll & användar upplevelse och mätning av trafik från olika regioner.
* **[Multivärde](#multivalue):** Välj **multivärde** för Traffic Manager profiler som bara kan ha IPv4/IPv6-adresser som slut punkter. När en fråga tas emot för den här profilen returneras alla felfria slut punkter.
* **[Undernät](#subnet):** Välj **under näts** trafik-routningsmetod för att mappa uppsättningar av IP-adressintervall till en speciell slut punkt i en Traffic Manager profil. När en begäran tas emot är slut punkten som returneras den som är mappad för den begärda käll-IP-adressen. 


Alla Traffic Manager profiler omfattar övervakning av slut punkts hälsa och automatisk slut punkts växling. Mer information finns i [Traffic Manager slut punkts övervakning](traffic-manager-monitoring.md). En enskild Traffic Manager-profil kan bara använda en Traffic routing-metod. Du kan när som helst välja en annan Traffic routing-metod för din profil. Ändringarna tillämpas inom en minut och ingen stillestånds tid uppstår. Trafik-routningsmetoder kan kombineras med hjälp av kapslade Traffic Manager-profiler. Kapsling möjliggör sofistikerade och flexibla konfigurationer för trafikroutning som uppfyller behoven hos större, komplexa program. Mer information finns i [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Metod för prioritets trafik

En organisation vill ofta tillhandahålla tillförlitlighet för sina tjänster genom att distribuera en eller flera säkerhets kopierings tjänster, om deras primära tjänst slutar fungera. Med Traffic-metoden Traffic-routing kan Azure-kunder enkelt implementera det här mönstret för växling vid fel.

![Traffic-routningsmetod för Azure Traffic Manager](media/traffic-manager-routing-methods/priority.png)

Traffic Manager-profilen innehåller en prioriterad lista över tjänstslutpunkter. Som standard skickar Traffic Manager all trafik till den primära slutpunkten (högsta prioritet). Om den primära slutpunkten inte är tillgänglig dirigerar Traffic Manager trafiken till den andra slutpunkten. Om både den primära och den sekundära slutpunkter är otillgängliga går trafiken till den tredje och så vidare. Tillgängligheten för slutpunkten baseras på den konfigurerade statusen (aktiverad eller inaktiverad) samt pågående slutpunktsövervakning.

### <a name="configuring-endpoints"></a>Konfigurera slut punkter

Med Azure Resource Manager konfigurerar du slut punkts prioriteten uttryckligen med hjälp av egenskapen Priority för varje slut punkt. Den här egenskapen är ett värde mellan 1 och 1000. Lägre värden representerar en högre prioritet. Slut punkter kan inte dela prioritets värden. Det är valfritt att ange egenskapen. När det utelämnas används en standard prioritet baserat på slut punkts ordningen.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Viktad metod för trafik dragning
Med metoden "viktad" för trafik dragning kan du distribuera trafik jämnt eller använda en fördefinierad viktning.

![Azure Traffic Manager "viktad" Traffic-routing-metod](media/traffic-manager-routing-methods/weighted.png)

I den viktade metoden för trafik cirkulation tilldelar du en vikt för varje slut punkt i Traffic Manager profil konfigurationen. Vikten är ett heltal mellan 1 och 1 000. Den här parametern är valfri. Om detta utelämnas använder Traffic Manager en standard vikt på 1. Ju högre vikt, desto högre prioritet.

För varje DNS-fråga som tas emot väljer Traffic Manager slumpmässigt en tillgänglig slutpunkt. Sannolikheten för att välja en slutpunkt baseras på de vikter som tilldelats till alla tillgängliga slutpunkter. Om du använder samma vikt i alla slut punkter resulterar det i en jämn trafik distribution. Om du använder högre eller lägre vikter på vissa slut punkter returneras dessa slut punkter mer eller mindre ofta i DNS-svaren.

Den viktade metoden möjliggör vissa användbara scenarier:

* Gradvis program uppgradering: allokera en procent andel av trafiken som ska dirigeras till en ny slut punkt och öka trafiken gradvis över tid till 100%.
* Programmigrering till Azure: skapa en profil med både Azure och externa slut punkter. Justera slut punkternas tjocklek för att föredra de nya slut punkterna.
* Cloud-burst för ytterligare kapacitet: utöka snabbt en lokal distribution till molnet genom att placera den bakom en Traffic Manager profil. När du behöver extra kapacitet i molnet kan du lägga till eller aktivera fler slut punkter och ange vilken del av trafiken som går till varje slut punkt.

Förutom att använda Azure Portal kan du konfigurera vikter med Azure PowerShell, CLI och REST-API: er.

Det är viktigt att förstå att DNS-svar cachelagras av klienter och av de rekursiva DNS-servrar som klienterna använder för att matcha DNS-namn. Denna cachelagring kan påverka viktade trafik distributioner. När antalet klienter och rekursiva DNS-servrar är stora fungerar trafik distributionen som förväntat. Men när antalet klienter eller rekursiva DNS-servrar är litet, kan cachelagringen dramatiskt skeva trafik distributionen.

Vanliga användnings områden är:

* Utvecklings-och testnings miljöer
* Kommunikation mellan program och program
* Program som syftar till en begränsad användar bas som delar en vanlig rekursiv DNS-infrastruktur (till exempel anställda av företag som ansluter via en proxy)

Dessa konsekvenser för DNS-cachelagring är gemensamma för alla DNS-baserade trafikflödes system, inte bara Azure Traffic Manager. I vissa fall kan det vara en lösning att explicit rensa DNS-cachen. I andra fall kan en alternativ trafik cirkulations metod vara lämpligare.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Metod för att dirigera prestanda

Distribution av slut punkter på två eller flera platser över hela världen kan förbättra svars tiden för många program genom att dirigera trafiken till den plats som är närmast dig. Metoden "prestanda" för Traffic-routing tillhandahåller den här funktionen.

![Azure Traffic Manager "prestanda" för Traffic routing-metod](media/traffic-manager-routing-methods/performance.png)

Slut punkten närmast är inte nödvändigt vis närmast uppmätt av geografiskt avstånd. I stället avgör "prestanda" Traffic-routing-metoden den närmaste slut punkten genom att mäta nätverks fördröjningen. Traffic Manager underhåller en Internet fördröjnings tabell för att spåra tiden för fördröjningen mellan IP-adressintervall och varje Azure-datacenter.

Traffic Manager letar upp käll-IP-adressen för den inkommande DNS-begäran i tabellen över Internet svars tider. Traffic Manager väljer sedan en tillgänglig slut punkt i Azure-datacenter som har den lägsta svars tiden för det IP-adressintervallet och returnerar slut punkten i DNS-svaret.

Som förklaras i [hur Traffic Manager fungerar](traffic-manager-how-it-works.md)Traffic Manager inte att ta emot DNS-frågor direkt från klienter. DNS-frågor kommer istället från den rekursiva DNS-tjänst som klienterna är konfigurerade att använda. Därför är IP-adressen som används för att fastställa den närmast slut punkten inte klientens IP-adress, men den är IP-adressen till den rekursiva DNS-tjänsten. I praktiken är den här IP-adressen en bra proxy för klienten.


Traffic Manager uppdaterar regelbundet tabellen Internet latens för att redovisa ändringar i globala Internet och nya Azure-regioner. Program prestanda varierar dock beroende på real tids variationer i belastningen på Internet. Prestanda trafik – routning övervakar inte belastningen på en specifik tjänst slut punkt. Men om en slut punkt blir otillgänglig, inkluderas inte Traffic Manager i DNS-fråge svar.


Poäng till Anmärkning:

* Om din profil innehåller flera slut punkter i samma Azure-region distribuerar Traffic Manager trafiken jämnt över tillgängliga slut punkter i den regionen. Om du föredrar en annan trafik distribution inom en region kan du använda [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).
* Om alla aktiverade slut punkter i den närmaste Azure-regionen försämras, Traffic Manager flyttar trafik till slut punkterna i nästa närmaste Azure-region. Om du vill definiera en prioriterad växlings ordning använder du [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).
* När du använder routningsmetoden för prestanda trafik med externa slut punkter eller kapslade slut punkter måste du ange platsen för dessa slut punkter. Välj den Azure-region som är närmast din distribution. Dessa platser är de värden som stöds av tabellen Internet fördröjning.
* Algoritmen som väljer slut punkten är deterministisk. Upprepade DNS-frågor från samma klient dirigeras till samma slut punkt. Normalt använder klienter olika rekursiva DNS-servrar vid resor. Klienten kan dirigeras till en annan slut punkt. Routning kan också påverkas av uppdateringar av tabellen Internet fördröjning. Därför garanterar metoden för prestanda trafik-routning inte att en klient alltid dirigeras till samma slut punkt.
* När Internet fördröjnings tabellen ändras kan du märka att vissa klienter dirigeras till en annan slut punkt. Den här Dirigerings ändringen är mer exakt baserad på aktuella latens-data. De här uppdateringarna är nödvändiga för att upprätthålla noggrannheten i prestanda trafiken i takt med att Internet utvecklas kontinuerligt.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Geografisk metod för trafik cirkulation

Traffic Manager profiler kan konfigureras att använda den geografiska routningsmetod så att användarna dirigeras till specifika slut punkter (Azure, externt eller kapslade) baserat på vilken geografisk plats deras DNS-fråga kommer från. Detta ger Traffic Manager kunder möjlighet att aktivera scenarier där man känner till en användares geografiska region och dirigerar dem baserat på det är viktigt. Exempel innefattar att följa data suveränitets uppdrag, lokalisering av innehåll & användar upplevelse och mätning av trafik från olika regioner.
När en profil har kon figurer ATS för geografisk routning måste varje slut punkt som är associerad med profilen ha en uppsättning geografiska regioner tilldelade till sig. En geografisk region kan vara i följande nivåer av granularitet 
- Värld – valfri region
- Regional gruppering – till exempel Afrika, Mellanöstern, Australien/Stilla havs området osv. 
- Land/region – till exempel Irland, Peru, Hong Kong SAR osv. 
- Delstat/provins – till exempel USA-Kalifornien, Australien-Queensland, Canada-Alberta osv. (OBS! den här granularitet nivån stöds bara för delstater/provinser i Australien, Kanada och USA).

När en region eller en uppsättning regioner tilldelas till en slut punkt dirigeras alla förfrågningar från dessa regioner endast till den slut punkten. Traffic Manager använder käll-IP-adressen för DNS-frågan för att fastställa den region som en användare frågar från – vanligt vis är detta IP-adressen för den lokala DNS-matcharen som utför frågan för användarens räkning.  

![Azure Traffic Manager "geografisk" Traffic-routing-metod](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager läser käll-IP-adressen för DNS-frågan och bestämmer vilken geografisk region den kommer från. Den ser sedan till att det finns en slut punkt som har mappats till den här geografiska regionen. Den här sökningen börjar på den lägsta granularitet nivån (delstat/provins där det stöds, annars på land/region-nivå) och går hela vägen upp till den högsta nivån, som är **värld**. Den första matchning som påträffas med den här genom gången anges som slut punkten som ska returneras i fråge svaret. Vid matchning med en kapslad typ slut punkt returneras en slut punkt inom den underordnade profilen, baserat på dess routningsmetod. Följande punkter gäller för detta beteende:

- En geografisk region kan bara mappas till en slut punkt i en Traffic Manager profil när routningstjänsten är geografisk routning. Detta säkerställer att routningen av användare är deterministisk och kunder kan aktivera scenarier som kräver otvetydiga geografiska gränser.
- Om en användares region kommer under två olika slut punkter geografisk mappning, Traffic Manager väljer slut punkten med den lägsta precisionen och tar inte över förfrågningar från den regionen till den andra slut punkten. Anta till exempel en geografisk typ profil för routning med två slut punkter – Endpoint1 och Endpoint2. Endpoint1 har kon figurer ATS för att ta emot trafik från Irland och Endpoint2 har kon figurer ATS för att ta emot trafik från Europa. Om en begäran kommer från Irland dirigeras den alltid till Endpoint1.
- Eftersom en region bara kan mappas till en slut punkt returnerar Traffic Manager den oavsett om slut punkten är felfri eller inte.

    >[!IMPORTANT]
    >Vi rekommenderar starkt att kunder som använder den geografiska routningsmetod associerar den med de kapslade typ slut punkterna som har underordnade profiler som innehåller minst två slut punkter inom var och en.
- Om en slut punkts matchning hittas och den slut punkten är i **stoppat** läge returnerar Traffic Manager ett nodata-svar. I det här fallet görs inga ytterligare sökningar högre upp i hierarkin för geografisk region. Detta beteende gäller också för kapslade slut punkts typer när den underordnade profilen är i läget **stoppad** eller **inaktive rad** .
- Om en slut punkt visar en **inaktive rad** status tas den inte med i matchnings processen för regioner. Detta beteende gäller också för kapslade slut punkts typer när slut punkten är i **inaktiverat** läge.
- Om en fråga kommer från en geografisk region som inte har någon mappning i profilen returnerar Traffic Manager ett nodata-svar. Vi rekommenderar därför att kunderna använder geografisk routning med en slut punkt, helst av typen kapslad med minst två slut punkter i den underordnade profilen, med **den region som** är tilldelad till den. Detta säkerställer också att alla IP-adresser som inte mappas till en region hanteras.

Som förklaras i [hur Traffic Manager fungerar](traffic-manager-how-it-works.md)Traffic Manager inte att ta emot DNS-frågor direkt från klienter. DNS-frågor kommer istället från den rekursiva DNS-tjänst som klienterna är konfigurerade att använda. Därför är IP-adressen som används för att fastställa regionen inte klientens IP-adress, men den är IP-adressen till den rekursiva DNS-tjänsten. I praktiken är den här IP-adressen en bra proxy för klienten.

### <a name="faqs"></a>Vanliga frågor och svar

* [Vad är några användnings fall där geografisk routning är användbart?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Hur gör jag för att avgöra om jag bör använda routningsmetod för prestanda eller geografisk routningsmetod?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Vilka regioner stöds av Traffic Manager för geografisk routning?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Hur avgör Traffic Manager var en användare frågar från?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Är det garanterat att Traffic Manager korrekt kan fastställa den exakta geografiska platsen för användaren i varje fall?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Behöver en slut punkt vara fysiskt placerad i samma region som den har kon figurer ATS för geografisk routning?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Kan jag tilldela geografiska regioner till slut punkter i en profil som inte har kon figurer ATS för att göra geografisk routning?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Varför får jag ett fel när jag försöker ändra routningsmetod för en befintlig profil till geografisk?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Varför rekommenderas det starkt att kunder skapar kapslade profiler i stället för slut punkter under en profil med geografisk routning aktive rad?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Finns det några begränsningar för API-versionen som stöder den här typen av cirkulations typ?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Metod för Multivärdes trafik – routningsmetod
Med metoden för **Multivärdes** trafik-routning kan du hämta flera felfria slut punkter i ett enda DNS-fråge svar. Detta gör att anroparen kan utföra återförsök på klient sidan med andra slut punkter i händelse av en returnerad slut punkt som inte svarar. Det här mönstret kan öka tillgängligheten för en tjänst och minska den svarstid som är associerad med en ny DNS-fråga för att hämta en felfri slutpunkt. Routningsmetod för flera värden fungerar bara om alla slut punkter av typen ' external ' och anges som IPv4-eller IPv6-adresser. När en fråga tas emot för den här profilen returneras alla felfria slut punkter och omfattas av ett konfigurerbart Max antal retur antal.

### <a name="faqs"></a>Vanliga frågor och svar

* [Vad är några användnings fall där multivärde-routning är användbart?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Hur många slut punkter returneras när multivärde-routning används?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Får jag samma uppsättning slut punkter när multivärde-routning används?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Under näts trafik – routningsmetod
Med metoden för att dirigera **under nät** trafiken kan du mappa en uppsättning IP-adressintervall för slutanvändare till särskilda slut punkter i en profil. Om Traffic Manager tar emot en DNS-fråga för profilen, kommer den att inspektera käll-IP-adressen för den begäran (i de flesta fall är detta den utgående IP-adressen för den DNS-matchare som används av anroparen), avgöra vilken slut punkt den är mappad till och kommer att returnera slut punkten i fråge svaret. 

IP-adressen som ska mappas till en slut punkt kan anges som CIDR-intervall (t. ex. 1.2.3.0/24) eller som ett adress intervall (t. ex. 1.2.3.4-5.6.7.8). IP-intervallen som associeras med en slut punkt måste vara unika i profilen och får inte överlappa IP-adresserna för en annan slut punkt i samma profil.
Om du definierar en slut punkt utan ett adress intervall fungerar det som en reserv och tar trafik från eventuella kvarvarande undernät. Om ingen återställnings punkt ingår, skickar Traffic Manager ett nodata-svar för alla odefinierade intervall. Vi rekommenderar därför starkt att du antingen definierar en återställnings slut punkt eller att du ser till att alla möjliga IP-intervall anges i dina slut punkter.

Under näts dirigering kan användas för att ge en annan upplevelse för användare som ansluter från ett särskilt IP-utrymme. Med undernätsdirigering kan en kund till exempel göra så att alla begäranden från företagskontoret dirigeras till en annan slutpunkt där de kanske testar en internt exklusiv version av företages app. Ett annat scenario är om du vill ge användare som ansluter från en specifik ISP en annan upplevelse (till exempel blockera användare från en viss ISP).

### <a name="faqs"></a>Vanliga frågor och svar

* [Vad är några användnings fall där under näts dirigering är användbart?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Hur vet Traffic Manager IP-adressen för slutanvändaren?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Hur kan jag ange IP-adresser när jag använder Subnet-routning?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Hur kan jag ange en återställnings slut punkt när jag använder under näts dirigering?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Vad händer om en slut punkt är inaktive rad i en under näts typ profil?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Nästa steg

Lär dig hur du utvecklar program med hög tillgänglighet med [Traffic Manager slut punkts övervakning](traffic-manager-monitoring.md)