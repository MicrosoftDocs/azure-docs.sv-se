---
title: Läsa repliker – Azure Database for MySQL-flexibel Server
description: 'Lär dig mer om att läsa repliker i Azure Database for MySQL flexibel Server: Skapa repliker, ansluta till repliker, övervaka replikering och stoppa replikering.'
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: fa7cc9b9a09bfd2bc503640272b5e7ac3a0a7b58
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251309"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Läs repliker i Azure Database for MySQL-flexibel Server

> [!IMPORTANT]
> Läs repliker i Azure Database for MySQL-flexibel Server är i för hands version.

MySQL är en av de populära databas motorerna för att köra webb-och mobil program på Internet-skala. Många av våra kunder använder den för sina utbildnings tjänster online, video strömnings tjänster, digitala betalnings lösningar, e-handelsplattformar, spel tjänster, nyhets portaler, myndigheter och sjukvårds webbplatser. Dessa tjänster krävs för att betjäna och skala när trafiken på webben eller det mobila programmet ökar.

På program sidan, utvecklas programmet vanligt vis i Java eller php och migreras till att köras på Azures skalnings uppsättningar för virtuella datorer eller Azure App tjänster eller behållare som ska köras på Azure Kubernetes service (AKS). Med den virtuella datorns skalnings uppsättning, App Service eller AKS som en underliggande infrastruktur, är program skalningen förenklad genom att tillfälligt tillhandahålla nya virtuella datorer och replikera tillstånds lösa komponenter för program till följd av begär Anden, men ofta avslutas databasen som en Flask hals som centraliserad tillstånds känslig komponent.

Med funktionen Läs replik kan du replikera data från en Azure Database for MySQL flexibel server till en skrivskyddad Server. Du kan replikera från käll servern till upp till **10** repliker. Replikerna uppdateras asynkront med MySQL-motorns interna replikeringsteknik som utgår från replikernas position i en binär loggfil (binlog). Mer information om BinLog-replikering finns i [Översikt över MySQL BinLog-replikering](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliker är nya servrar som du hanterar precis som din källa Azure Database for MySQL flexibla servrar. Du debiteras fakturerings avgifter för varje Läs replik baserat på den etablerade beräkningen i virtuella kärnor och lagring i GB/månad. Mer information finns i [prissättning](./concepts-compute-storage.md#pricing).

Mer information om funktioner och problem med MySQL-replikering finns i [dokumentationen för MySQL-replikering](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Den här artikeln innehåller referenser till termen _slav_, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.
>

## <a name="common-use-cases-for-read-replica"></a>Vanliga användnings fall för Läs replik

Funktionen Läs replik hjälper till att förbättra prestanda och skalning för Läs intensiva arbets belastningar. Läs arbets belastningar kan isoleras till replikerna, medan Skriv arbets belastningar kan dirigeras till källan.

Vanliga scenarier är:

* Skala Läs-arbets belastningar från programmet med hjälp av proxyservern för förenklad anslutning som [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica) eller använda mikrotjänster-baserade mönster för att skala ut dina Läs frågor som kommer från programmet till att läsa repliker
* Arbets belastningar för BI-eller analys rapporter kan använda Läs repliker som data källa för rapportering
* För IoT-eller tillverknings scenario där telemetri-information matas in i MySQL-databasmotorn samtidigt som flera Läs repliker används för rapportering av data

Eftersom repliker är skrivskyddade kan de inte direkt minska Skriv kapacitets bördan på källan. Den här funktionen är inte riktad mot skrivintensiva arbetsbelastningar.

Funktionen Läs replik använder MySQL-asynkron replikering. Funktionen är inte avsedd för synkrona scenarier för replikering. Det kommer att bli en mätbar fördröjning mellan källan och repliken. Data på repliken kommer slutligen att bli konsekventa med data på källan. Använd den här funktionen för arbets belastningar som kan hantera denna fördröjning.

## <a name="create-a-replica"></a>Skapa en replik

Om en käll Server inte har några befintliga replik servrar, kommer källan först att starta om för att förbereda sig för replikering.

När du startar arbets flödet skapa replik skapas en tom Azure Database for MySQL-server. Den nya servern fylls med de data som fanns på käll servern. Skapande tiden beror på mängden data på källan och tiden sedan den senaste veckovis fullständiga säkerhets kopieringen. Tiden kan vara från några minuter till flera timmar.

> [!NOTE]
> Läs repliker skapas med samma server konfiguration som källan. Replik Server konfigurationen kan ändras efter att den har skapats. Replik servern skapas alltid i samma resurs grupp, samma plats och samma prenumeration som käll servern. Om du vill skapa en replik server till en annan resurs grupp eller en annan prenumeration kan du [Flytta replik servern](../../azure-resource-manager/management/move-resource-group-and-subscription.md) när du har skapat den. Vi rekommenderar att replik serverns konfiguration ska hållas på lika stora eller större värden än källan för att säkerställa att repliken kan fortsätta med källan.

Lär dig hur du [skapar en Läs replik i Azure Portal](how-to-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Anslut till en replik

Vid skapandet ärver en replik anslutnings metoden för käll servern. Det går inte att ändra anslutnings metod för repliken. Till exempel om käll servern har **privat åtkomst (VNet-integrering)** kan repliken inte vara i **offentlig åtkomst (tillåtna IP-adresser)**.

Repliken ärver administratörs kontot från käll servern. Alla användar konton på käll servern replikeras till läsa repliker. Du kan bara ansluta till en Läs replik med hjälp av de användar konton som är tillgängliga på käll servern.

Du kan ansluta till repliken med hjälp av dess värdnamn och ett giltigt användar konto, precis som på en vanlig Azure Database for MySQL flexibel Server. För en server med namnet **unreplica** med administratörs **användar namnet administratör kan** du ansluta till repliken med hjälp av MySQL CLI:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

Ange lösen ordet för användar kontot vid prompten.

## <a name="monitor-replication"></a>Övervaka replikering

Azure Database for MySQL flexibel Server ger måttet **replikeringsfördröjning i sekunder** i Azure Monitor. Måttet är endast tillgängligt för repliker. Detta mått beräknas med hjälp av `seconds_behind_master` måttet som är tillgängligt i MySQL- `SHOW SLAVE STATUS` kommandot. Ange en avisering för att meddela dig när fördröjningen för replikering når ett värde som inte är acceptabelt för din arbets belastning.

Om du ser ökad replikeringsfördröjning läser du [Felsöka](./../howto-troubleshoot-replication-latency.md) replikeringsfördröjning för att felsöka och förstå möjliga orsaker.

## <a name="stop-replication"></a>Stoppa replikering

Du kan stoppa replikering mellan en källa och en replik. När replikeringen har stoppats mellan en käll Server och en Läs replik blir repliken en fristående server. Data i den fristående servern är de data som var tillgängliga på repliken när kommandot stoppa replikering startades. Den fristående servern är inte uppfångad med käll servern.

När du väljer att stoppa replikering till en replik förlorar den alla länkar till sin tidigare källa och andra repliker. Det finns ingen automatisk redundans mellan en källa och dess replik.

> [!IMPORTANT]
> Den fristående servern kan inte göras till en replik igen.
> Innan du stoppar replikeringen på en Läs replik måste du se till att repliken har alla data som du behöver.

Lär dig hur du [stoppar replikering till en replik](how-to-read-replicas-portal.md).

## <a name="failover"></a>Redundans

Det finns ingen automatisk redundans mellan käll-och replik servrar.

Läs repliker är avsedd för skalning av Läs intensiva arbets belastningar och har inte utformats för att uppfylla hög tillgänglighets behoven hos en server. Det finns ingen automatisk redundans mellan käll-och replik servrar. Att stoppa replikeringen på en Läs replik för att ta den online i Läs-/skriv läge är det sätt som den manuella redundansväxlingen utförs med.

Eftersom replikeringen är asynkron finns det en fördröjning mellan källan och repliken. Mängden fördröjning kan påverkas av många faktorer, till exempel hur mycket belastningen på käll servern som körs på käll servern och fördröjningen mellan data Center. I de flesta fall varierar replikfördröjning mellan några sekunder och några minuter. Du kan spåra den faktiska replikeringens fördröjning med hjälp av mått *replik fördröjningen*, som är tillgänglig för varje replik. Det här måttet visar tiden sedan den senaste återspelade transaktionen. Vi rekommenderar att du identifierar den genomsnittliga fördröjningen genom att iaktta din replik fördröjning under en viss tids period. Du kan ställa in en avisering på replik fördröjningen, så att om den går utanför det förväntade intervallet kan du vidta åtgärder.

> [!Tip]
> Om du redundansväxlas till repliken kommer fördröjningen vid den tidpunkt då du avlänkar repliken från källan att indikera hur mycket data som förloras.

När du har valt att du vill redundansväxla till en replik:

1. Stoppa replikering till repliken<br/>
   Det här steget är nödvändigt för att göra replik servern tillgänglig för skrivningar. Som en del av den här processen kopplas replik servern från källan. När du har initierat stoppa replikering tar det vanligt vis ungefär 2 minuter att slutföra backend-processen. Se avsnittet [stoppa replikering](#stop-replication) i den här artikeln för att förstå konsekvenserna av den här åtgärden.

2. Peka ditt program till den (tidigare) repliken<br/>
   Varje server har en unik anslutnings sträng. Uppdatera programmet så att det pekar på den (tidigare) repliken i stället för källan.

När programmet har bearbetat läsningar och skrivningar har du slutfört redundansväxlingen. Hur lång tid det tar för program upplevelser att vara beroende av när du upptäcker ett problem och Slutför steg 1 och 2 ovan.

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

| Scenario | Begränsning/övervägande |
|:-|:-|
| Replik på server med zon-redundant HA aktiverat | Stöds inte |
| Läs replikering mellan regioner | Stöds inte |
| Priser | Kostnaden för att köra replik servern baseras på den region där replik servern körs |
| Omstart av käll Server | När du skapar en replik för en källa som inte har några befintliga repliker startas källan om först för att förbereda sig för replikering. Ta detta i beaktande och utför dessa åtgärder under en låg belastnings period |
| Nya repliker | En Läs replik skapas som en ny Azure Database for MySQL flexibel Server. Det går inte att göra en befintlig server till en replik. Du kan inte skapa en replik av en annan Läs replik |
| Replik konfiguration | En replik skapas med samma server konfiguration som källan. När en replik har skapats kan flera inställningar ändras oberoende av käll servern: beräknings generering, virtuella kärnor, lagring och kvarhållning av säkerhets kopior. Beräknings nivån kan också ändras oberoende av varandra.<br> <br> **Viktigt!**  <br> – Innan en käll Server konfiguration uppdateras till nya värden uppdaterar du replik konfigurationen till samma eller större värden. På så sätt säkerställer du att repliken klarar alla ändringar som görs av källan. <br/> Anslutnings metoden och parameter inställningarna ärvs från käll servern till repliken när repliken skapas. Därefter är replikens regler oberoende av varandra. |
| Stoppade repliker | Om du stoppar replikeringen mellan en käll Server och en Läs replik blir den stoppade repliken en fristående server som accepterar både läsning och skrivning. Den fristående servern kan inte göras till en replik igen. |
| Borttagen källa och fristående servrar | När en käll server tas bort, stoppas replikeringen till alla Läs repliker. Dessa repliker blir automatiskt fristående servrar och kan acceptera både läsningar och skrivningar. Själva käll servern tas bort. |
| Användarkonton | Användare på käll servern replikeras till läsa repliker. Du kan bara ansluta till en Läs replik med de användar konton som är tillgängliga på käll servern. |
| Serverparametrar | I syfte att förhindra att data blir osynkroniserade samt att undvika potentiell dataförlust eller skadade data är vissa serverparametrar låsta från att uppdateras vid användning av skrivskyddade repliker. <br> Följande Server parametrar är låsta på både käll-och replik servern:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler)Parametern är låst på replik servrarna. <br> Om du vill uppdatera en av parametrarna ovan på käll servern tar du bort replik servrar, uppdaterar parametervärdet på källan och återskapar repliker. |
| Övrigt | – Det finns inte stöd för att skapa en replik av en replik. <br> -InMemory-tabeller kan orsaka att repliker inte längre är synkroniserade. Detta är en begränsning av MySQL-replikeringstrafiken. Mer information finns i [referens dokumentationen för MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) . <br>– Kontrol lera att käll Server tabellerna har primär nycklar. Brist på primär nycklar kan leda till replikeringsfördröjning mellan källan och replikerna.<br>– Granska den fullständiga listan över begränsningar för MySQL-replikering i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) |

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar och hanterar Läs repliker med hjälp av Azure Portal](how-to-read-replicas-portal.md)
* Lär dig hur du [skapar och hanterar Läs repliker med Azure CLI](how-to-read-replicas-cli.md)