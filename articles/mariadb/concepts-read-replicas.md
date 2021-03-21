---
title: Läsa repliker – Azure Database for MariaDB
description: 'Lär dig mer om att läsa repliker i Azure Database for MariaDB: välja regioner, skapa repliker, ansluta till repliker, övervaka replikering och stoppa replikering.'
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.custom: references_regions
ms.openlocfilehash: 39547e3156a684293a0624f974a8b0930f656485
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664222"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Skrivskyddad replik i Azure Database for MariaDB

Med funktionen för skrivskyddade repliker kan du replikera data från en Azure Database for MariaDB-server till en skrivskyddad server. Du kan replikera från källservern till upp till fem repliker. Repliker uppdateras asynkront med MariaDB-motorns binära logg (BinLog)-baserad teknik för replikering med globalt transaktions-ID (GTID). Mer information om BinLog-replikering finns i [Översikt över BinLog-replikering](https://mariadb.com/kb/en/library/replication-overview/).

Repliker är nya servrar som du hanterar precis som vanliga Azure Database for MariaDB-servrar. För varje Läs replik debiteras du för den etablerade beräkningen i virtuella kärnor och lagring i GB/månad.

Mer information om GTID-replikering finns i [dokumentationen för MariaDB-replikering](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Den här artikeln innehåller referenser till termen _slav_, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

## <a name="when-to-use-a-read-replica"></a>När du ska använda en Läs replik

Funktionen Läs replik hjälper till att förbättra prestanda och skalning för Läs intensiva arbets belastningar. Läs arbets belastningar kan isoleras till replikerna, medan Skriv arbets belastningar kan dirigeras till den primära.

Ett vanligt scenario är att låta BI och analytiska arbets belastningar använda Läs repliken som data källa för rapportering.

Eftersom repliker är skrivskyddade kan de inte direkt minska Skriv kapacitets bördan på den primära. Den här funktionen är inte riktad mot skrivintensiva arbetsbelastningar.

Funktionen Läs replik använder asynkron replikering. Funktionen är inte avsedd för synkrona scenarier för replikering. Det kommer att bli en mätbar fördröjning mellan källan och repliken. Data på repliken kommer slutligen att bli konsekventa med data på den primära. Använd den här funktionen för arbets belastningar som kan hantera denna fördröjning.

## <a name="cross-region-replication"></a>Replikering mellan regioner

Du kan skapa en Läs replik i en annan region än käll servern. Replikering mellan regioner kan vara användbart för scenarier som haveri beredskap planering eller för att hämta data närmare dina användare.

Du kan ha en käll server i valfri [Azure Database for MariaDB region](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  En käll Server kan ha en replik i dess kopplade region eller Universal Replica-regioner. I bilden nedan visas vilka replik regioner som är tillgängliga beroende på din käll region.

[![Läs replik regioner](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universal Replica-regioner

Du kan skapa en Läs replik i någon av följande regioner, oavsett var käll servern finns. De Universal Replica-regioner som stöds är:

Östra Australien, sydöstra Australien, södra Brasilien, centrala Kanada, Östra Kanada, centrala USA, Asien, östra, östra USA, östra USA 2, Östra Japan, västra Japan, Korea, centrala, norra centrala USA, norra Europa, södra centrala USA, Sydostasien, Storbritannien, södra, Storbritannien, västra, västra Europa, västra USA, västra USA 2, västra centrala USA.

### <a name="paired-regions"></a>Länkade regioner

Förutom Universal Replica-regioner kan du skapa en Läs replik i den Azure-kopplade regionen på käll servern. Om du inte känner till din regions par kan du läsa mer i [artikeln Azure-kopplade regioner](../best-practices-availability-paired-regions.md).

Om du använder repliker över flera regioner för att planera haveri beredskap rekommenderar vi att du skapar repliken i den kopplade regionen i stället för någon av de andra regionerna. Kopplade regioner förhindrar samtidiga uppdateringar och prioriterar fysisk isolering och data placering.  

Det finns dock begränsningar att tänka på: 

* Regional tillgänglighet: Azure Database for MariaDB är tillgänglig i Frankrike Central, Förenade Arabemiraten nord och Tyskland, centrala. De kopplade regionerna är dock inte tillgängliga.

* Enkelriktade par: vissa Azure-regioner är bara kopplade till en riktning. I dessa regioner ingår västra Indien, södra Brasilien och US Gov, Virginia. 
   Det innebär att en käll server i västra Indien kan skapa en replik i södra Indien. En käll server i södra Indien kan dock inte skapa en replik i västra Indien. Detta beror på att den sekundära regionen västra Indien är södra Indien, men den sekundära regionen i södra Indien är inte västra Indien.

## <a name="create-a-replica"></a>Skapa en replik

> [!IMPORTANT]
> Funktionen Läs replik är bara tillgänglig för Azure Database for MariaDB servrar i Generell användning eller Minnesoptimerade pris nivåer. Se till att käll servern är i någon av dessa pris nivåer.

Om en käll Server inte har några befintliga replik servrar, kommer källan först att starta om för att förbereda sig för replikering.

När du startar arbets flödet skapa replik skapas en tom Azure Database for MariaDB-Server. Den nya servern fylls med de data som fanns på käll servern. Skapande tiden beror på mängden data på källan och tiden sedan den senaste veckovis fullständiga säkerhets kopieringen. Tiden kan vara från några minuter till flera timmar.

> [!NOTE]
> Om du inte har en lagrings avisering som kon figurer ATS på dina servrar, rekommenderar vi att du gör det. Aviseringen informerar dig när en server närmar sig lagrings gränsen, vilket kommer att påverka replikeringen.

Lär dig hur du [skapar en Läs replik i Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Anslut till en replik

Vid skapandet ärver en replik brand Väggs reglerna för käll servern. Därefter är dessa regler oberoende av käll servern.

Repliken ärver administratörs kontot från käll servern. Alla användar konton på käll servern replikeras till läsa repliker. Du kan bara ansluta till en Läs replik med hjälp av de användar konton som är tillgängliga på käll servern.

Du kan ansluta till repliken med hjälp av dess värdnamn och ett giltigt användar konto, precis som på en vanlig Azure Database for MariaDB Server. För en server med namnet **unreplica** med administratörs **användar namnet administratör kan** du ansluta till repliken med hjälp av MySQL CLI:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Ange lösen ordet för användar kontot vid prompten.

## <a name="monitor-replication"></a>Övervaka replikering

Azure Database for MariaDB anger måttet för **replikeringsfördröjning i sekunder** i Azure Monitor. Måttet är endast tillgängligt för repliker.

Måttet beräknas med hjälp av `seconds_behind_master` måttet som är tillgängligt i MariaDB- `SHOW SLAVE STATUS` kommandot.

Ange en avisering för att meddela dig när fördröjningen för replikering når ett värde som inte är acceptabelt för din arbets belastning.

## <a name="stop-replication"></a>Stoppa replikering

Du kan stoppa replikering mellan en källa och en replik. När replikeringen har stoppats mellan en käll Server och en Läs replik blir repliken en fristående server. Data i den fristående servern är de data som var tillgängliga på repliken när kommandot stoppa replikering startades. Den fristående servern är inte uppfångad med käll servern.

När du väljer att stoppa replikering till en replik förlorar den alla länkar till sin tidigare källa och andra repliker. Det finns ingen automatisk redundans mellan en källa och dess replik.

> [!IMPORTANT]
> Den fristående servern kan inte göras till en replik igen.
> Innan du stoppar replikeringen på en Läs replik måste du se till att repliken har alla data som du behöver.

Lär dig hur du [stoppar replikering till en replik](howto-read-replicas-portal.md).

## <a name="failover"></a>Redundans

Det finns ingen automatisk redundans mellan käll-och replik servrar.

Eftersom replikeringen är asynkron finns det en fördröjning mellan källan och repliken. Mängden fördröjning kan påverkas av ett antal faktorer, t. ex. hur mycket hög belastningen som körs på käll servern och fördröjningen mellan data Center. I de flesta fall varierar replikfördröjning mellan några sekunder och några minuter. Du kan spåra den faktiska replikeringens fördröjning med hjälp av mått *replik fördröjningen*, som är tillgänglig för varje replik. Det här måttet visar tiden sedan den senaste återspelade transaktionen. Vi rekommenderar att du identifierar den genomsnittliga fördröjningen genom att iaktta din replik fördröjning under en viss tids period. Du kan ställa in en avisering på replik fördröjningen, så att om den går utanför det förväntade intervallet kan du vidta åtgärder.

> [!Tip]
> Om du redundansväxlas till repliken kommer fördröjningen vid den tidpunkt då du avlänkar repliken från källan att indikera hur mycket data som förloras.

När du har valt att du vill redundansväxla till en replik,

1. Stoppa replikering till repliken.

   Det här steget är nödvändigt för att göra replik servern tillgänglig för skrivningar. Som en del av den här processen kommer replik servern att kopplas från den primära servern. När du har initierat stoppa replikering tar det vanligt vis ungefär 2 minuter att slutföra backend-processen. Se avsnittet [stoppa replikering](#stop-replication) i den här artikeln för att förstå konsekvenserna av den här åtgärden.

2. Peka ditt program till den (tidigare) repliken.

   Varje server har en unik anslutnings sträng. Uppdatera programmet så att det pekar på den (tidigare) repliken i stället för den primära.

När programmet har bearbetat läsningar och skrivningar har du slutfört redundansväxlingen. Hur lång tid det tar för program upplevelser att vara beroende av när du upptäcker ett problem och Slutför steg 1 och 2 ovan.

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

### <a name="pricing-tiers"></a>Prisnivåer

Läs repliker är för närvarande endast tillgängliga i Generell användning och minnesoptimerade pris nivåer.

> [!NOTE]
> Kostnaden för att köra replik servern baseras på den region där replik servern körs.

### <a name="source-server-restart"></a>Omstart av käll Server

När du skapar en replik för en källa som inte har några befintliga repliker startas källan om först för att förbereda sig för replikering. Ta detta i beaktande och utför dessa åtgärder under en låg belastnings period.

### <a name="new-replicas"></a>Nya repliker

En Läs replik skapas som en ny Azure Database for MariaDB Server. Det går inte att göra en befintlig server till en replik. Du kan inte skapa en replik av en annan Läs replik.

### <a name="replica-configuration"></a>Replik konfiguration

En replik skapas med hjälp av samma server konfiguration som den primära. När en replik har skapats kan flera inställningar ändras oberoende av käll servern: beräknings generation, virtuella kärnor, lagring, bevarande period för säkerhets kopior och MariaDB motor version. Pris nivån kan också ändras oberoende, förutom till eller från Basic-nivån.

> [!IMPORTANT]
> Uppdatera replikkonfigurationen till samma eller högre värden innan en källserverkonfiguration uppdateras till nya värden. Den här åtgärden säkerställer att repliken kan behålla alla ändringar som görs i den primära.

Brand Väggs regler och parameter inställningar ärvs från käll servern till repliken när repliken skapas. Därefter är replikens regler oberoende av varandra.

### <a name="stopped-replicas"></a>Stoppade repliker

Om du stoppar replikeringen mellan en käll Server och en Läs replik blir den stoppade repliken en fristående server som accepterar både läsning och skrivning. Den fristående servern kan inte göras till en replik igen.

### <a name="deleted-source-and-standalone-servers"></a>Borttagen källa och fristående servrar

När en käll server tas bort, stoppas replikeringen till alla Läs repliker. Dessa repliker blir automatiskt fristående servrar och kan acceptera både läsningar och skrivningar. Själva käll servern tas bort.

### <a name="user-accounts"></a>Användarkonton

Användare på käll servern replikeras till läsa repliker. Du kan bara ansluta till en Läs replik med de användar konton som är tillgängliga på käll servern.

### <a name="server-parameters"></a>Serverparametrar

I syfte att förhindra att data blir osynkroniserade samt att undvika potentiell dataförlust eller skadade data är vissa serverparametrar låsta från att uppdateras vid användning av skrivskyddade repliker.

Följande Server parametrar är låsta på både käll-och replik servern:

* [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
* [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

[`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler)Parametern är låst på replik servrarna.

Om du vill uppdatera en av parametrarna ovan på käll servern, måste du ta bort replik servrar, uppdatera parametervärdet på den primära och återskapa repliker.

### <a name="other"></a>Övrigt

* Det finns inte stöd för att skapa en replik av en replik.
* InMemory-tabeller kan orsaka att repliker blir osynkroniserade. Detta är en begränsning av MariaDB-replikerings teknik.
* Se till att käll Server tabellerna har primär nycklar. Brist på primär nycklar kan leda till replikeringsfördröjning mellan källan och replikerna.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar och hanterar Läs repliker med hjälp av Azure Portal](howto-read-replicas-portal.md)
* Lär dig hur du [skapar och hanterar Läs repliker med hjälp av Azure CLI och REST API](howto-read-replicas-cli.md)
