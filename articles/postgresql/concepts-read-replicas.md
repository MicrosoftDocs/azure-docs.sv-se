---
title: Läsa repliker – Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs funktionen Läs replik i Azure Database for PostgreSQL-enskild server.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 62ef47e7d8f98241009c1c1f3d8c111113be432c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99220776"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Läsa repliker i Azure Database for PostgreSQL-enskild server

Med funktionen Läs replik kan du replikera data från en Azure Database for PostgreSQL-server till en skrivskyddad Server. Repliker uppdateras **asynkront** med postgresql-motorns inbyggda teknik för fysisk replikering. Du kan replikera från den primära servern till upp till fem repliker.

Repliker är nya servrar som du hanterar ungefär som vanliga Azure Database for PostgreSQL-servrar. För varje Läs replik debiteras du för den etablerade beräkningen i virtuella kärnor och lagring i GB/månad.

Lär dig hur du [skapar och hanterar repliker](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>När du ska använda en Läs replik
Funktionen Läs replik hjälper till att förbättra prestanda och skalning för Läs intensiva arbets belastningar. Läs arbets belastningar kan isoleras till replikerna, medan Skriv arbets belastningar kan dirigeras till den primära. Det går också att distribuera Läs repliker i en annan region och kan uppgraderas till att vara en Läs-/skriv Server vid haveri beredskap.

Ett vanligt scenario är att låta BI och analytiska arbets belastningar använda Läs repliken som data källa för rapportering.

Eftersom repliker är skrivskyddade kan de inte direkt minska Skriv kapacitets bördan på den primära.

### <a name="considerations"></a>Överväganden
Funktionen är avsedd för scenarier där fördröjningen är acceptabel och avsedd för avlastning av frågor. Det är inte avsett för synkrona replikeringsalternativ där replik data förväntas vara uppdaterade. Det kommer att bli en mätbar fördröjning mellan den primära servern och repliken. Detta kan vara på några minuter eller till och med timmar beroende på arbets belastningen och fördröjningen mellan den primära och repliken. Data på repliken kommer slutligen att bli konsekventa med data på den primära. Använd den här funktionen för arbets belastningar som kan hantera denna fördröjning. 

> [!NOTE]
> För de flesta arbets belastningar ger Läs repliker nästan real tids uppdateringar från den primära. Men med beständig tung Skriv intensiv primär arbets belastning kan replikeringen fortsätta att växa och kanske aldrig kan fånga upp med den primära. Detta kan också öka lagrings användningen på den primära servern eftersom WAL-filerna inte tas bort förrän de tas emot på repliken. Om den här situationen kvarstår tar du bort och återskapar Läs repliken när de Skriv intensiva arbets belastningarna är klara, vilket innebär att repliken återgår till ett lyckat tillstånd med avseende på fördröjning.
> Asynkrona Läs repliker lämpar sig inte för sådana tung Skriv arbets belastningar. När du utvärderar Läs repliker för ditt program kan du övervaka fördröjningen på repliken för att få en fullständig inläsnings cykel för appar under sin högsta och låg belastnings tid för att få åtkomst till den möjliga fördröjningen och den förväntade RTO/återställningen vid olika tidpunkter för arbets belastning
> 
## <a name="cross-region-replication"></a>Replikering mellan regioner
Du kan skapa en Läs replik i en annan region än den primära servern. Replikering mellan regioner kan vara användbart för scenarier som haveri beredskap planering eller för att hämta data närmare dina användare.

>[!NOTE]
> Basic-nivå servrar stöder bara replikering med samma region.

Du kan ha en primär server i valfri [Azure Database for PostgreSQL region](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). En primär server kan ha en replik i dess kopplade region eller Universal Replica-regioner. I bilden nedan visas vilka replik regioner som är tillgängliga beroende på din primära region.

[:::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Läs replik regioner":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universal Replica-regioner
Du kan alltid skapa en Läs replik i någon av följande regioner, oavsett var den primära servern finns. Det här är Universal Replica-regionerna:

Östra Australien, sydöstra Australien, södra Brasilien, centrala Kanada, Östra Kanada, centrala USA, Asien, östra, östra USA, östra USA 2, Östra Japan, västra Japan, Korea, centrala, norra centrala USA, norra Europa, södra centrala USA, Sydostasien, Storbritannien, södra, Storbritannien, västra, västra Europa, västra USA, västra USA 2, västra centrala USA.

### <a name="paired-regions"></a>Länkade regioner
Förutom Universal Replica-regioner kan du skapa en Läs replik i den Azure-kopplade regionen på den primära servern. Om du inte känner till din regions par kan du läsa mer i [artikeln Azure-kopplade regioner](../best-practices-availability-paired-regions.md).

Om du använder repliker över flera regioner för att planera haveri beredskap rekommenderar vi att du skapar repliken i den kopplade regionen i stället för någon av de andra regionerna. Kopplade regioner förhindrar samtidiga uppdateringar och prioriterar fysisk isolering och data placering.  

Det finns begränsningar att tänka på: 
    
* Enkelriktade par: vissa Azure-regioner är bara kopplade till en riktning. I dessa regioner ingår västra Indien, södra Brasilien. 
   Det innebär att en primär server i västra Indien kan skapa en replik i södra Indien. En primär server i södra Indien kan dock inte skapa en replik i västra Indien. Detta beror på att den sekundära regionen västra Indien är södra Indien, men den sekundära regionen i södra Indien är inte västra Indien.


## <a name="create-a-replica"></a>Skapa en replik
När du startar arbets flödet skapa replik skapas en tom Azure Database for PostgreSQL-Server. Den nya servern fylls med de data som fanns på den primära servern. Hur lång tid det tar att skapa beror på mängden data som är primär och tiden sedan den senaste veckovis fullständiga säkerhets kopieringen. Tiden kan vara från några minuter till flera timmar.

Varje replik är aktive rad för att utöka lagringen [automatiskt](concepts-pricing-tiers.md#storage-auto-grow). Funktionen för automatisk storleks ökning gör det möjligt för repliken att hålla sig uppdaterad med de data som replikeras till den och förhindrar en paus i replikeringen som orsakas av fel i lagrings utrymmet.

Funktionen Läs replik använder PostgreSQL fysisk replikering, inte logisk replikering. Att strömma replikering med hjälp av Replikerings-platser är standard åtgärds läget. Vid behov används logg överföring för att komma igång.

Lär dig hur du [skapar en Läs replik i Azure Portal](howto-read-replicas-portal.md).

Om din käll PostgreSQL-Server är krypterad med Kundhanterade nycklar kan du läsa [dokumentationen](concepts-data-encryption-postgresql.md) för ytterligare överväganden.

## <a name="connect-to-a-replica"></a>Anslut till en replik
När du skapar en replik ärver den inte brand Väggs reglerna eller slut punkten för VNet-tjänsten för den primära servern. Dessa regler måste konfigureras separat för repliken.

Repliken ärver administratörs kontot från den primära servern. Alla användar konton på den primära servern replikeras till de skrivskyddade replikerna. Du kan bara ansluta till en Läs replik med hjälp av de användar konton som är tillgängliga på den primära servern.

Du kan ansluta till repliken med hjälp av dess värdnamn och ett giltigt användar konto, precis som på en vanlig Azure Database for PostgreSQL Server. För en server med namnet **min replik** med admin username- **administratören** kan du ansluta till repliken med hjälp av psql:

```bash
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Ange lösen ordet för användar kontot vid prompten.

## <a name="monitor-replication"></a>Övervaka replikering
Azure Database for PostgreSQL tillhandahåller två mått för övervakning av replikering. De två måtten är **maximal fördröjning mellan repliker** och **replik fördröjning**. Information om hur du visar dessa mått finns i avsnittet **övervaka en replik** i [artikeln Läs mer om att läsa replikering](howto-read-replicas-portal.md).

Måttet **Max fördröjning över repliker** visar fördröjningen i byte mellan den primära och den mest avbildade repliken. Det här måttet är endast tillgängligt på den primära servern och är bara tillgängligt om minst en av Läs repliken är ansluten till den primära servern och den primära är i läget för strömnings-replikering. Fördröjnings informationen visar inte information när repliken håller på att fångas upp med den primära informationen med hjälp av de arkiverade loggarna för den primära i ett fil överförings läge för replikering.

Värdet för **replik fördröjningen** visar tiden sedan den senaste återspelade transaktionen. Om det inte finns några transaktioner på den primära servern återspeglar måttet denna tids fördröjning. Detta mått är bara tillämpligt och tillgängligt för replik servrar. Replik fördröjningen beräknas från `pg_stat_wal_receiver` vyn:

```SQL
SELECT EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Ange en avisering som meddelar dig när replik fördröjningen når ett värde som inte är acceptabelt för din arbets belastning. 

För ytterligare insikter kan du fråga den primära servern direkt för att hämta fördröjningen i byte på alla repliker.

> [!NOTE]
> Om en primär server eller en Läs replik startas om återspeglas den tid det tar att starta om och fånga upp i replikens fördröjnings mått.

## <a name="stop-replication--promote-replica"></a>Stoppa replikering/befordra replik
Du kan när som helst stoppa replikeringen mellan en primär och en replik. Åtgärden stoppa gör att repliken startas om och befordrar replikeringen som en oberoende, fristående skrivskyddad Server. Data i den fristående servern är de data som var tillgängliga på replik servern vid den tidpunkt då replikeringen stoppades. Eventuella efterföljande uppdateringar på den primära kommer inte att spridas till repliken. Men replik servern kan ha ackumulerade loggar som inte tillämpas än. Som en del av processen för omstart, tillämpar repliken alla väntande loggar innan klient anslutningar accepteras.  

### <a name="considerations"></a>Överväganden
- Innan du stoppar replikeringen på en Läs replik kontrollerar du att replikeringen innehåller alla data som du behöver. 
- Eftersom Läs repliken måste tillämpa alla väntande loggar innan den kan göras till en fristående server, kan RTO vara högre för Skriv tunga arbets belastningar när stopp-replikeringen inträffar eftersom det kan finnas en betydande fördröjning på repliken. Kom noga med detta när du planerar att uppgradera en replik.
- Den upphöjda replik servern kan inte göras till en replik igen.
- Om du befordrar en replik som primär server kan du inte upprätta replikering tillbaka till den gamla primära servern. Om du vill gå tillbaka till den gamla primära regionen kan du antingen upprätta en ny replik server med ett nytt namn (eller) ta bort den gamla primära servern och skapa en replik med hjälp av det gamla primära namnet.
- Om du har flera Läs repliker, och om du befordrar en av dem som din primära server, är andra replik servrar fortfarande anslutna till den gamla primära servern. Du kan behöva återskapa repliker från den nya, uppgraderade servern.

När du stoppar replikering förlorar repliken alla länkar till dess tidigare primära och andra repliker.

Lär dig hur du [stoppar replikering till en replik](howto-read-replicas-portal.md).

## <a name="failover-to-replica"></a>Redundans till replik

Vid fel på en primär server växlar det **inte** automatiskt över till Läs repliken. 

Eftersom replikeringen är asynkron, kan det finnas en stor fördröjning mellan den primära servern och repliken. Förskjutnings mängden påverkas av ett antal faktorer, till exempel vilken typ av arbets belastning som körs på den primära servern och fördröjningen mellan den primära servern och replik servern. I vanliga fall med nominell Skriv arbets belastning förväntas replik fördröjningen mellan några sekunder till några minuter. Men i de fall där den primära kör mycket tung Skriv intensiv arbets belastning och repliken inte blir tillräckligt snabb, kan fördröjningen vara mycket högre. Du kan spåra replikeringens fördröjning för varje replik med hjälp av måttets *replik fördröjning*. Det här måttet visar tiden sedan den senaste återspelade transaktionen på repliken. Vi rekommenderar att du identifierar den genomsnittliga fördröjningen genom att observera replik fördröjningen under en viss tids period. Du kan ställa in en avisering på replik fördröjningen, så att om den går utanför det förväntade intervallet får du ett meddelande om att vidta åtgärder.

> [!Tip]
> Om du redundansväxlas till repliken kommer fördröjningen vid den tidpunkt då du tar bort repliken från den primära informationen att visa hur mycket data som förloras.

När du har valt att du vill redundansväxla till en replik, 

1. Stoppa replikering till repliken<br/>
   Det här steget är nödvändigt för att göra replik servern till en fristående server och kunna godkänna skrivningar. Som en del av den här processen startas replik servern om och kopplas från den primära. När du har initierat stoppa replikering tar det vanligt vis några minuter för Server dels processen att tillämpa eventuella kvarvarande loggar som inte har tillämpats och för att öppna databasen som en skrivskyddad Server. Se avsnittet [stoppa replikering](#stop-replication--promote-replica) i den här artikeln för att förstå konsekvenserna av den här åtgärden.
    
2. Peka ditt program till den (tidigare) repliken<br/>
   Varje server har en unik anslutnings sträng. Uppdatera din program anslutnings sträng så att den pekar på den (tidigare) repliken i stället för den primära.
    
När ditt program har bearbetat läsningar och skrivningar har du slutfört redundansväxlingen. Hur lång tid det tar för program upplevelser att vara beroende av när du upptäcker ett problem och Slutför steg 1 och 2 ovan.

### <a name="disaster-recovery"></a>Haveriberedskap

Om det finns en viktig katastrof händelse, till exempel tillgänglighets zon eller regionala haverier, kan du utföra haveri beredskap genom att uppgradera din Läs replik. Från UI-portalen kan du gå till Läs replik servern. Klicka sedan på fliken replikering och du kan stoppa replikeringen så att den blir en oberoende Server. Du kan också använda [Azure CLI](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) för att stoppa och uppgradera replik servern.

## <a name="considerations"></a>Överväganden

I det här avsnittet sammanfattas överväganden om funktionen Läs replik.

### <a name="prerequisites"></a>Förutsättningar
Läsning av repliker och [logisk avkodning](concepts-logical.md) är beroende av postgres Write Ahead-loggen (Wal) för information. De här två funktionerna behöver olika loggnings nivåer från postgres. Logisk avkodning kräver en högre loggnings nivå än Läs repliker.

Om du vill konfigurera rätt loggnings nivå använder du parametern Azure Replication support. Support för Azure-replikering har tre inställnings alternativ:

* **Off** – lägger till minst information i Wal. Den här inställningen är inte tillgänglig på de flesta Azure Database for PostgreSQL-servrar.  
* **Replik** – mer utförligt än **.** Detta är den lägsta loggnings nivå som krävs för att [läsa repliker](concepts-read-replicas.md) ska fungera. Den här inställningen är standard på de flesta servrar.
* **Logisk** – mer utförlig än **replik**. Detta är den lägsta loggnings nivån för logisk avkodning att arbeta. Läs repliker fungerar också med den här inställningen.


### <a name="new-replicas"></a>Nya repliker
En Läs replik skapas som en ny Azure Database for PostgreSQL Server. Det går inte att göra en befintlig server till en replik. Du kan inte skapa en replik av en annan Läs replik.

### <a name="replica-configuration"></a>Replik konfiguration
En replik skapas med samma beräknings-och lagrings inställningar som den primära. När en replik har skapats kan flera inställningar ändras, inklusive lagrings-och bevarande period för säkerhets kopior.

Brand Väggs regler, regler för virtuella nätverk och parameter inställningar ärvs inte från den primära servern till repliken när repliken skapas eller efteråt.

### <a name="scaling"></a>Skalning
Skala virtuella kärnor eller mellan Generell användning och Minnesoptimerade:
* PostgreSQL kräver att `max_connections` inställningen på en sekundär server är [större än eller lika med inställningen på den primära](https://www.postgresql.org/docs/current/hot-standby.html), annars kommer den sekundära inte att starta.
* I Azure Database for PostgreSQL, åtgärdas det högsta antalet tillåtna anslutningar för varje server till beräknings-SKU: n eftersom anslutningar upptar minne. Du kan lära dig mer om [mappningen mellan max_connections och beräknings-SKU: er](concepts-limits.md).
* **Skala upp**: skala först upp en repliks beräkning och skala sedan upp den primära. Den här ordningen förhindrar att fel bryter mot `max_connections` kravet.
* **Skala ned**: först skala ned den primära data bearbetningen och skala sedan ned repliken. Om du försöker skala repliken lägre än den primära, uppstår ett fel eftersom detta strider mot `max_connections` kravet.

Skala lagring:
* Alla repliker har automatisk utökning av lagring aktive rad för att förhindra replikeringsfel från en lagrings full replik. Den här inställningen kan inte inaktive ras.
* Du kan även skala upp lagrings utrymme manuellt, precis som du gör på andra servrar


### <a name="basic-tier"></a>Basic-nivå
Basic-nivå servrar stöder bara replikering med samma region.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[Postgresql kräver](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) att värdet för `max_prepared_transactions` parametern på Läs repliken är större än eller lika med det primära värdet. annars startar inte repliken. Om du vill ändra `max_prepared_transactions` på den primära måste du först ändra den på replikerna.

### <a name="stopped-replicas"></a>Stoppade repliker
Om du stoppar replikeringen mellan en primär server och en Läs replik, startar repliken om för att tillämpa ändringen. Den stoppade repliken blir en fristående server som accepterar både läsning och skrivning. Den fristående servern kan inte göras till en replik igen.

### <a name="deleted-primary-and-standalone-servers"></a>Borttagna primära och fristående servrar
När en primär server tas bort blir alla dess läsnings repliker fristående servrar. Replikerna har startats om för att återspegla den här ändringen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapar och hanterar Läs repliker i Azure Portal](howto-read-replicas-portal.md).
* Lär dig hur du [skapar och hanterar Läs repliker i Azure CLI och REST API](howto-read-replicas-cli.md).