---
title: Alternativ eller Oracle BareMetal Infrastructure-servrar
description: Lär dig mer om alternativ och överväganden för Oracle BareMetal Infrastructure-servrar.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: e8a2dece11884e3a659f14b30bce51e7f0244924
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590347"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>Alternativ för Oracle BareMetal Infrastructure-servrar

I den här artikeln överväger vi alternativ och rekommendationer för att få den högsta skyddsnivån och prestanda som kör Oracle på BareMetal-infrastrukturservrar. 

## <a name="data-guard-protection-modes"></a>Skyddslägen för Data Guard

Data Guard ger skydd som inte är tillgängligt enbart via Oracle Real Applications Cluster (RAC), logisk replikering (till exempel GoldenGate) och lagringsbaserad replikering. 

| Skyddsläge | Description |
| --- | --- |
| **Maximal prestanda** | Standardskyddsläget. Den ger den högsta skyddsnivån utan att påverka den primära databasens prestanda. Data betraktas som inskrivna så snart de har skrivits till den primära databasens redo-dataström. Den replikeras sedan till standby-databasen asynkront. I allmänhet tar väntelägesdatabasen emot den inom några sekunder, men det ges ingen garanti för detta. Det här läget uppfyller vanligtvis affärskraven (tillsammans med fördröjningsövervakning) utan behov av nätverksanslutning med låg latens mellan de primära platserna och väntelägesplatserna.<br /><br />Det ger bästa möjliga driftspersistence. Men det garanterar inte noll dataförlust.   |
| **Maximal tillgänglighet** | Ger den högsta skyddsnivån utan att den primära databasens tillgänglighet påverkas. Data anses aldrig vara indeade i den primära databasen förrän de också har genomförts i minst en reservdatabas. Om den primära databasen inte kan skriva om ändringarna till minst en väntelägesdatabas, faller den tillbaka till läget För maximal prestanda i stället för att bli otillgänglig. <br /><br />Det gör att tjänsten kan fortsätta om standby-platsen inte är tillgänglig. Om bara en plats fungerar behålls endast en kopia av data tills den andra platsen är online och synkroniseringen upprättas igen. |
| **Maximalt skydd** | Ger en skyddsnivå som liknar maximal tillgänglighet. Den primära databasen stängs av med den tillagda funktionen om den inte kan skriva om ändringarna till minst en väntelägesdatabas. Detta säkerställer att dataförlust inte kan ske, men på bekostnad av mer sårbar tillgänglighet. |

>[!IMPORTANT]
>Om du behöver ett mål för återställningspunkt (RPO) på noll rekommenderar vi konfigurationen för maximal tillgänglighet. RPO kan sedan garanteras även om flera fel inträffar. Till exempel även vid nätverksavbrott från den primära databasen följt av förlust av den primära databasen någon gång efteråt medan nätverksavbrottet fortfarande pågår.

### <a name="data-guard-deployment-patterns"></a>Data Guard-distributionsmönster

Med Oracle kan du konfigurera flera mål för att skapa om, vilket möjliggör flera väntelägesdatabaser. Den vanligaste konfigurationen visas i följande bild, en enkel standby-databas i en annan region.

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="Diagram som visar Oracles standarddistribution av Data Guard.":::

Data Guard har konfigurerats i läge för maximal prestanda för en standarddistribution. Den här konfigurationen ger datareplikering i nära realtid via asynkron upprepningstransport. Standby-databasen behöver inte köras i en RAC-distribution, men vi rekommenderar att den uppfyller prestandakraven för den primära platsen.

Vi rekommenderar en distribution som den som visas i följande bild för miljöer som kräver strikt drifttid eller ett RPO på noll. Konfigurationen för maximal tillgänglighet består av en lokal väntelägesdatabas som tillämpar gör om i synkront läge och en andra standby-databas som körs i en fjärrregion.

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="Diagram som visar maximal tillgänglighet för Data Guard-distribution.":::

Du kan skapa en lokal väntelägesdatabas när programprestandan drabbas av att köra databasen och programservrarna i olika regioner. I den här konfigurationen används en lokal standby-databas när planerat eller oplanerat underhåll krävs på det primära klustret. Du kan köra dessa databaser med synkron replikering eftersom de finns i samma region, vilket säkerställer att inga data går förlorade mellan dem.

### <a name="data-guard-configuration-considerations"></a>Överväganden för Data Guard-konfiguration

Data Guard Broker bör implementeras eftersom det förenklar implementeringen av en Data Guard-konfiguration och säkerställer att bästa praxis följs. Den tillhandahåller funktioner för prestandaövervakning och förenklar avsevärt procedurerna för växling, redundans och omvärdering.

Med Data Guard kan du köra en övervakningsprocess som övervakar alla databaser i en Data Guard-konfiguration för att fastställa databasens tillgänglighet. Om en primär databas slutar fungera kan Data Guard-observerare automatiskt starta en redundans till en standby-databas i konfigurationen. Du kan implementera Data Guard-observeren med flera observerare baserat på antalet fysiska platser (upp till tre). 

Den här hanteraren bör finnas på den infrastruktur som ska stödja programnivån. Den primära observeren bör alltid finnas på den fysiska platsen där den primära databasen inte finns. Vi rekommenderar att du är försiktig när du automatiserar redundansåtgärder som utlöses av en Data Guard-observer. Kontrollera först att dina program är utformade och testade för att tillhandahålla acceptabel tjänst när databasen körs på en separat plats.

Om programmet bara kan fungera lokalt måste redundansen till den sekundära platsen vara manuell. Miljöer som kräver hög tillgänglighet (99,99 % eller 99,999 % drifttid) bör använda både en lokal databas och en fjärrdatabas i vänteläge, som du ser i föregående bild. I dessa fall anges parametern FastStartFailoverTarget endast till den lokala standby-databasen.

För alla program som har stöd för program-/databasåtkomst mellan webbplatser är FastStartFailoverTarget inställt på alla väntelägesdatabaser i Data Guard-konfigurationen.

### <a name="active-data-guard"></a>Active Data Guard

Oracle Active Data Guard (ADG) är en supermängd av grundläggande Data Guard-funktioner som ingår i Oracle Database Enterprise Edition. Den innehåller följande funktioner som kommer att användas i Oracle Exadata-distributionen:

- Unik identifiering av skador och automatisk reparation.
- Snabb redundans till den synkroniserade repliken av produktion – manuell eller automatisk.
- Avlasta produktionsarbetsbelastningen till ett synkroniserat öppet vänteläge skrivskyddat.
- Löpande uppgraderingar och vänteläge för databasen. Första korrigeringen med fysiskt vänteläge.
- Avlasta inkrementella säkerhetskopieringar till vänteläge.
- Inget dataförlustskydd för dataåterställning över ett avstånd utan att påverka prestandan.

Den white paper som [https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf) finns på ger en bra översikt över de föregående funktionerna, som du ser i följande bild.

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="Diagram som visar en översikt över Oracles Active Data Guard-funktioner.":::

## <a name="backup-recommendations"></a>Rekommendationer för säkerhetskopiering

Se till att backa upp dina databaser. Använd funktionerna för återställning och återställning för att återställa en databas till samma eller ett annat system, eller för att återställa databasfiler.

Det är viktigt att skapa en strategi för säkerhetskopieringsåterställning som skyddar Oracle Database Appliance-databaser mot dataförlust. En sådan förlust kan uppstå på grund av ett fysiskt problem med en disk som orsakar ett fel i en läsning eller skrivning till en diskfil som krävs för att köra databasen. Användarfel kan också orsaka dataförlust. Funktionen för säkerhetskopiering ger möjlighet att återställa databasen till tidpunkt **(PITR), återställning av systemändringsnummer (SCN) och den senaste återställningen.** Du kan skapa en säkerhetskopieringspolicy i webbläsaren Användargränssnitt eller från kommandoradsgränssnittet.

Följande säkerhetskopieringsalternativ är tillgängliga:

- Back up to NFS storage volume (Fast Recovery Area-GF- /u98).
- Använda Azure NetApp Files SnapCenter – ögonblicksbild.

Process att tänka på:

- Manuella eller automatiska säkerhetskopieringar.
- Automatiska säkerhetskopieringar skrivs till NFS-lagringsvolymer (till exempel /u98).
- Säkerhetskopieringar körs mellan 12.00 och 06.00 i databassystemets tidszon.
- Aktuella kvarhållningsperioder: 7, 15, 30, 45 och 60 dagar.

- Återställa databasen från en säkerhetskopia som lagras i objektlagring:
  - Till det senaste kända tillståndet med minsta möjliga dataförlust.
  - Använd angiven tidsstämpel.
  - Med det angivna SCN:et.
  - BackupReport – _använder SCN från säkerhetskopieringsrapport i stället för angiven SCN_.

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="Diagram som visar kundens databas som är tillbaka till UI (/u98) och icke-PROCESS (/u95).":::

### <a name="backup-policy"></a>Säkerhetskopieringsprincip

Säkerhetskopieringsprincipen definierar säkerhetskopieringsinformationen. När du skapar en säkerhetskopieringspolicy definierar du målet för databassäkerhetskopiornas PLATS (NFS-plats) och definierar återställningsfönstret.

Som standard används BASIC-komprimeringsalgoritmen. När du använder låg-, medel- eller hög komprimeringsalgoritmer för säkerhetskopieringspolicy för diskar eller NFS finns det licensöverväganden.

### <a name="backup-levels"></a>Säkerhetskopieringsnivåer

Ange säkerhetskopieringsnivå när du gör en säkerhetskopia.

- Nivå 0 – Fullständig
- Nivå 1 – Inkrementell
- LongTerm/Archievelog – med undantag för policyn för kvarhållning av säkerhetskopior använder du en plats som inte är PLATS för säkerhetskopior (till exempel /u95).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du återställer Oracle-databasen när ett fel inträffar:

> [!div class="nextstepaction"]
> [Återställa din Oracle-databas i Azure BareMetal-infrastrukturen](oracle-high-availability-recovery.md)
