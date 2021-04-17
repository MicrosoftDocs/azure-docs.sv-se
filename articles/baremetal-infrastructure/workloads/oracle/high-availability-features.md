---
title: Funktioner för hög tillgänglighet för Oracle på Azure BareMetal
description: Lär dig mer om funktionerna som är tillgängliga i BareMetal för en Oracle-databas.
ms.topic: overview
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 75032cc6351504a8400be43d05091d2b47484229
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590450"
---
# <a name="high-availability-features-for-oracle-on-azure-baremetal"></a>Funktioner för hög tillgänglighet för Oracle på Azure BareMetal

I den här artikeln tittar vi på de viktigaste funktionerna för hög tillgänglighet och haveriberedskap i Oracle.

Oracle erbjuder många funktioner för att skapa en motståndskraftig plattform för att köra Oracle-databaser. Ingen enskild funktion ger täckning för alla typer av fel, men genom att kombinera tekniker i flera lager skapas ett system med hög tillgång. Det är inte alla funktioner som krävs för att upprätthålla tillgängligheten. Men genom att kombinera strategier får du det bästa skyddet från de olika fel som inträffar. 

## <a name="flashback-database"></a>Flashback-databas

Funktionen [Flashback Database](https://docs.oracle.com/en/database/oracle/oracle-database/21/rcmrf/FLASHBACK-DATABASE.html#GUID-584AC79A-40C5-45CA-8C63-DED3BE3A4511) finns i Oracle Database Enterprise Edition. Databasen rullas tillbaka till en viss tidpunkt. Den här funktionen skiljer sig från [en återställning till en Recovery Manager -tidpunkt (RMAN)](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/performing-general-restore-and-recovery-operations.html) på så sätt att den återställs från den aktuella tidpunkten i stället för framåt vindar efter en återställning. Det resulterar i mycket snabbare slutförandetider.
 
Du kan använda den här funktionen tillsammans [med Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590). Med Flashback Database kan en databasadministratör initiera om en misslyckad databas till en Data Guard-konfiguration utan en fullständig RMAN-återställning och återställning. Med den här funktionen kan du återställa haveriberedskapsfunktioner (och eventuella avlästa rapporterings- och säkerhetskopieringsfördelar med Active Data Guard) mycket snabbare.
 
Du kan använda den här funktionen i stället för en tidsfördröjd gör om i standby-databasen. En väntelägesdatabas kan flashas tillbaka till en punkt innan ett problem uppstår.
 
Den Oracle Database sparar flashback-loggar i det snabba återställningsområdet (RIB). De här loggarna är åtskilda från redo-loggarna och kräver mer utrymme i THEA. Som standard sparas 24 timmars flashback-loggar, men du kan ändra den här inställningen efter dina behov.

## <a name="oracle-real-application-clusters"></a>Verkliga Oracle-programkluster

[Med Oracle Real Application Clusters (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92) kan flera sammankopplade servrar visas som en databastjänst för slutanvändare och program. Den här funktionen tar bort många felpunkter och är en känd aktiv/aktiv lösning med hög tillgänglighet för Oracle-databaser.

Som du ser i följande bild från Oracles översikt över hög tillgänglighet och [bästa](https://docs.oracle.com/en/database/oracle/oracle-database/19/haovw/ha-features.html)praxis visas en enskild RAC-databas för programlagret. Programmen ansluter till SCAN-lyssnaren, som dirigerar trafik till en specifik databasinstans. RAC styr åtkomsten från flera instanser för att upprätthålla datakonsekvens mellan olika beräkningsnoder.

![Diagram som visar en översikt över arkitekturen i Oracle RAC.](media/oracle-high-availability/oracle-real-application-clusters.png)

Om en instans misslyckas fortsätter tjänsten på alla andra återstående instanser. Varje databas som distribueras i lösningen kommer att finnas i en RAC-konfiguration av n+1, där n är den minsta bearbetningskraft som krävs för att stödja tjänsten.

Oracle Database tjänster används för att tillåta att anslutningar redundansar mellan noder när en instans misslyckas transparent. Sådana fel kan vara planerade eller oplanerade. När en instans blir otillgänglig flyttas tjänsten till en kvarvarande nod när den arbetar med programmet (snabba programaviseringshändelser). Tjänsten flyttas till en nod som anges i tjänstkonfigurationen som antingen önskad eller tillgänglig.

En annan viktig funktion i Oracle Database tjänster är att bara starta en tjänst beroende på dess roll. Den här funktionen används när det finns en Data Guard-redundans. Alla mönster som distribueras med Data Guard krävs för att länka en databastjänst till en Data Guard-roll.

Två tjänster kan till exempel skapas, MY \_ DB APP och MY DB \_ \_ \_ AS. MY \_ DB \_ APP-tjänsten startas bara när databasinstansen startas med Data Guard-rollen PRIMÄR. MY \_ DB AS startas bara när Data \_ Guard-rollen är FYSISKT \_ VÄNTELÄGE. Med den här konfigurationen kan program peka på APP-tjänsten, samtidigt som de rapporterar, som kan avlastas till aktivt vänteläge och \_ peka på \_ AS-tjänsten.

## <a name="oracle-data-guard"></a>Oracle Data Guard

Med Data Guard kan du underhålla en identisk kopia av en databas på separat fysisk maskinvara. Vi rekommenderar att maskinvaran är geografiskt åtskild. Data Guard begränsar inte avståndet, även om avståndet påverkar skyddslägen. Ökat avstånd ökar svarstiden mellan platser, vilket kan göra att vissa alternativ (till exempel synkron replikering) inte längre är gångbara.

Data Guard har fördelar jämfört med replikering på lagringsnivå:

- Eftersom replikeringen är databasmedveten replikeras endast relevant trafik.
- Vissa arbetsbelastningar kan generera höga indata/utdata i tillfälliga tabellrymder, som inte krävs i vänteläge och därför inte replikeras.
- Validering av de replikerade blocken sker i väntelägesdatabasen, vilket säkerställer att fysiska skador som introduceras på den primära databasen inte replikeras till standby-databasen.
- Förhindrar logiska intrablockeringskorruptioner och skadade skrivfel. Det eliminerar också risken för att lagringsadministratörer gör misstag från att replikera till vänteläge.
Gör om kan fördröjas under en förbestämd period, så användarfel replikeras inte omedelbart till vänteläget.

## <a name="azure-netapp-files-snapshots"></a>Azure NetApp Files ögonblicksbilder

Med NetApp Files-lagringslösningen som används i BareMetal kan du skapa ögonblicksbilder av volymer. Med ögonblicksbilder kan du snabbt återställa ett filsystem till en viss tidpunkt. Tekniker för ögonblicksbilder möjliggör RTO-tider (mål för återställningstid) som bara är en bråkdel av tiden som är associerad med återställning av en databassäkerhetskopia.

Funktioner för ögonblicksbilder för Oracle-databaser är tillgängliga via Azure NetApp SnapCenter. Med SnapCenter kan du schemalägga och automatisera skapande och återställning av ögonblicksbilder av volymer.

## <a name="recovery-manager"></a>Recovery Manager

Recovery Manager (RMAN) är det bästa verktyget för att säkerhetskopiera fysiska databaser. RMAN interagerar med databaskontrollfilen (eller en central återställningskatalog) för att skydda de olika kärnkomponenterna i databasen, inklusive:

- Databasdatafiler
- Arkiverade redo-loggar
- Databaskontrollfiler
- Databas initialiseringsfiler (spfile)

Med RMAN kan du göra säkerhetskopieringar av heta eller kalla databaser. Du kan använda dessa säkerhetskopior för att skapa väntelägesdatabaser eller duplicera databaser för att klona miljöer. RMAN har också en funktion för återställningsverifiering. Den här funktionen läser en säkerhetskopia och avgör om du kan använda den för att återställa databasen till en viss tidpunkt.

Eftersom RMAN är ett Oracle-verktyg kan det läsa den interna strukturen för databasfiler. På så sätt kan du köra fysiska och logiska felkontroller under säkerhetskopierings- och återställningsåtgärder. Du kan också återställa databasdatafiler och återställa enskilda datafiler och tabellrymder till en viss tidpunkt. Det här är fördelar med RMAN-erbjudanden jämfört med ögonblicksbilder av lagring. RMAN-säkerhetskopieringar ger en sista försvarslinje mot fullständig dataförlust när du inte kan använda ögonblicksbilder.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om alternativ och rekommendationer för att optimera skydd och prestanda som kör Oracle på BareMetal Infrastructure:

> [!div class="nextstepaction"]
> [Alternativ för Oracle BareMetal Infrastructure-servrar](options-considerations-high-availability.md)
