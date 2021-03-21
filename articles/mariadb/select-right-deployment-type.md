---
title: Välja rätt distributions typ – Azure Database for MariaDB
description: I den här artikeln beskrivs vilka faktorer du bör tänka på innan du distribuerar Azure Database for MariaDB som antingen infrastruktur som en tjänst (IaaS) eller Platform as a Service (PaaS).
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d8885e374142b3d916803fc472ae18351ca6d470
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664526"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Välj rätt MariaDB Server-alternativ i Azure

Med Azure kan dina MariaDB Server-arbetsbelastningar köras i en värdbaserad virtuell dator infrastruktur som en tjänst (IaaS) eller som en tjänst som är värd plattform som en tjänst (PaaS). PaaS har flera distributions alternativ och det finns tjänst nivåer inom varje distributions alternativ. När du väljer mellan IaaS och PaaS måste du bestämma om du vill hantera din databas, tillämpa korrigeringar och göra säkerhets kopior, eller om du vill delegera dessa åtgärder till Azure.

När du fattar ditt beslut bör du tänka på följande två alternativ:

- **Azure Database for MariaDB:** Det här alternativet är en fullständigt hanterad MariaDB-databasmotor baserat på den stabila versionen av MariaDB Community Edition. Den här Relations databas som en tjänst (DBaaS), som finns på Azure-molnets plattform, hamnar i bransch kategorin för PaaS.

  Med en hanterad instans av MariaDB på Azure kan du använda inbyggda funktioner som annars kräver omfattande konfiguration när MariaDB-servern är antingen lokalt eller i en virtuell Azure-dator.

  När du använder MariaDB som en tjänst betalar du per användning med alternativ för att skala upp eller ut för bättre kontroll utan avbrott. Till skillnad från fristående MariaDB-Server har Azure Database for MariaDB ytterligare funktioner som inbyggd hög tillgänglighet, intelligens och hantering.

- **MariaDB på virtuella Azure-datorer:** Det här alternativet är inställt på IaaS för bransch kategorin. Med den här tjänsten kan du köra MariaDB-server i en helt hanterad virtuell dator på Azures moln plattform. Alla nya versioner och utgåvor av MariaDB kan installeras på en IaaS virtuell dator.

  MariaDB på virtuella Azure-datorer har en kontroll över databas motorn i den viktigaste skillnaden från Azure Database for MariaDB. Den här kontrollen kommer dock att ansvara för att hantera de virtuella datorerna och många databas administrations uppgifter (DBA). Dessa uppgifter omfattar att underhålla och korrigera databas servrar, databas återställning och design med hög tillgänglighet.

De huvudsakliga skillnaderna mellan dessa alternativ visas i följande tabell:

| Attribut          | Azure Database for MariaDB | MariaDB på virtuella Azure-datorer    |
|:-------------------|:-----------------------------|:--------------------|
| Service nivå avtal (SLA)                | Erbjuder SLA med 99,99% tillgänglighet| Upp till 99,95% tillgänglighet med två eller fler instanser i samma tillgänglighets uppsättning.<br/><br/>99,9% tillgänglighet med en enskild instans av en virtuell dator med Premium Storage.<br/><br/>99,99% med Tillgänglighetszoner med flera instanser i flera tillgänglighets uppsättningar.<br/><br/>Se [Virtual Machines service avtal](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Uppdatering av operativ system        | Automatiskt  | Hanteras av kunder |
| MariaDB korrigering     | Automatiskt  | Hanteras av kunder |
| Hög tillgänglighet | Modellen med hög tillgänglighet (HA) baseras på inbyggda funktioner för redundansväxling för när ett avbrott på nod-nivå sker. I sådana fall skapar tjänsten automatiskt en ny instans och kopplar lagring till den här instansen. | Kunders arkitekt, implementera, testa och underhålla hög tillgänglighet. Funktioner kan omfatta Always on-redundanskluster, Always on-gruppreplikering, logg överföring eller transaktionell replikering.|
| Zonredundans | Stöds inte för närvarande | Virtuella Azure-datorer kan konfigureras för att köras i olika tillgänglighets zoner. För en lokal lösning måste kunderna skapa, hantera och underhålla ett eget sekundärt Data Center.|
| Hybrid scenarier | Med [datareplikering](concepts-data-in-replication.md)kan du synkronisera data från en extern MariaDB-server till tjänsten Azure Database for MariaDB. Den externa servern kan vara lokalt, i virtuella datorer eller i en databas tjänst som är värd för andra moln leverantörer.<br/><br/> Med funktionen [Läs replik](concepts-read-replicas.md) kan du replikera data från en Azure Database for MariaDB käll server till upp till fem skrivskyddade replik servrar. Replikerna är antingen inom samma Azure-region eller i flera regioner. Skrivskyddade repliker uppdateras asynkront med BinLog-replikering.<br/><br/>Läs replikering mellan regioner är för närvarande en offentlig för hands version.| Hanteras av kunder
| Säkerhets kopiering och återställning | Skapar automatiskt [Server säkerhets kopior](concepts-backup.md#backups) och lagrar dem i användar konfigurations lagring som antingen är lokalt redundant eller Geo-redundant. Tjänsten tar full, differentiell och transaktions logg säkerhets kopior | Hanteras av kunder |
| Övervaka databas åtgärder | Ger kunderna möjlighet att [ställa in aviseringar](concepts-monitoring.md) för databas åtgärden och vidta åtgärder för att nå tröskelvärden. | Hanteras av kunder |
| Advanced Threat Protection | Tillhandahåller [Avancerat skydd](howto-database-threat-protection-portal.md). Det här skyddet identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.<br/><br/>Avancerat skydd är för närvarande en offentlig för hands version.| Kunderna måste bygga det här skyddet för sig själva.
| Haveriberedskap | Lagrar automatiserade säkerhets kopieringar i användar konfiguration [lokalt redundant eller Geo-redundant lagring](howto-restore-server-portal.md). Säkerhets kopieringar kan också återställa en server till en tidpunkt. Kvarhållningsperioden är var som helst från 7 till 35 dagar. Återställningen utförs med hjälp av Azure Portal. | Fullständigt hanterad av kunder. Ansvaret omfattar men är inte begränsat till schemaläggning, testning, arkivering, lagring och kvarhållning. Ett annat alternativ är att använda ett Azure Recovery Services-valv för att säkerhetskopiera virtuella Azure-datorer och databaser på virtuella datorer. Det här alternativet är i för hands version. |
| Prestandarekommendationer | Ger kunderna [prestanda rekommendationer](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) baserat på systemgenererade användnings loggar. Rekommendationerna hjälper till att optimera arbets belastningar.<br/><br/>Prestanda rekommendationer är för närvarande en offentlig för hands version. | Hanteras av kunder |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Affärs motivation för att välja PaaS eller IaaS

Det finns flera faktorer som kan påverka ditt beslut att välja PaaS eller IaaS för att vara värd för dina MariaDB-databaser.

### <a name="cost"></a>Cost

Begränsad finansiering är ofta den främsta överväganden som avgör den bästa lösningen för dina databaser. Detta gäller oavsett om du är en start med lite kontant eller ett team i ett etablerat företag som arbetar under tätt budget begränsningar. I det här avsnittet beskrivs grundläggande information om fakturering och licensiering i Azure eftersom de gäller för Azure Database for MariaDB och MariaDB på virtuella Azure-datorer.

#### <a name="billing"></a>Fakturering

Azure Database for MariaDB är för närvarande tillgängligt som en tjänst på flera nivåer med olika priser för resurser. Alla resurser debiteras per timme med ett fast pris. Den senaste informationen om de tjänst nivåer, beräknings storlekar och lagrings belopp som stöds för närvarande finns i [vCore-baserad inköps modell](concepts-pricing-tiers.md). Du kan justera tjänst nivåerna dynamiskt och beräknings storlekarna så att de överensstämmer med programmets varierande data flödes behov. Du debiteras för utgående Internet trafik med regelbundna [data överförings kostnader](https://azure.microsoft.com/pricing/details/data-transfers/).

Med Azure Database for MariaDB konfigurerar Microsoft automatiskt, patchar och uppgraderar databas program varan. Dessa automatiserade åtgärder minskar dina administrations kostnader. Azure Database for MariaDB har också [inbyggda funktioner för säkerhets kopiering](concepts-backup.md) . Dessa funktioner hjälper dig att uppnå avsevärda kostnads besparingar, särskilt när du har ett stort antal databaser. Med MariaDB på virtuella Azure-datorer kan du däremot välja och köra valfri MariaDB-version. Oavsett vilken MariaDB-version du använder betalar du för den etablerade virtuella datorn och kostnaderna för den aktuella MariaDB-licens typen som används.

Azure Database for MariaDB ger en inbyggd hög tillgänglighet för alla typer av avbrott på nodnivå samtidigt som service avtalet för service avtalet för service avtal fortfarande 99,99 upprätthålls. För databas hög tillgänglighet i virtuella datorer bör dock kunderna använda alternativen för hög tillgänglighet, t. ex. [MariaDB-replikering](https://mariadb.com/kb/en/library/setting-up-replication/) som är tillgängliga i en MariaDB-databas. Att använda ett alternativ för hög tillgänglighet som stöds ger inte ytterligare ett service avtal. Men det gör att du kan uppnå mer än 99,99% tillgänglighet till databaser med extra kostnad och administrativa kostnader.

Mer information om priser finns i följande artiklar:
* [Azure Database for MariaDB priser](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Prissättning för virtuell dator](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [ Priskalkylator för Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Administration

För många företag är beslutet att gå över till en moln tjänst lika mycket om att avlasta komplexiteten för administration eftersom det kostar mer. Med IaaS och PaaS, Microsoft:

- Administrerar den underliggande infrastrukturen.
- Replikerar automatiskt alla data för att tillhandahålla haveri beredskap.
- Konfigurerar och uppgraderar databasens program vara.
- Hanterar belastnings utjämning.
- Fungerar transparent om det uppstår ett Server haveri.

I följande lista beskrivs administrativa överväganden för varje alternativ:

* Med Azure Database for MariaDB kan du fortsätta att administrera din databas. Men du behöver inte längre hantera databas motorn, operativ systemet eller maskin varan. Exempel på objekt som du kan fortsätta att administrera är:

  - Databaser
  - Logga in
  - Index justering
  - Frågejustering
  - Granskning
  - Säkerhet

  Dessutom kräver en minimal konfiguration eller administration att konfigurera hög tillgänglighet till ett annat data Center.

* Med MariaDB på virtuella Azure-datorer har du fullständig kontroll över operativ systemet och konfigurationen av MariaDB-serverinstansen. Med en virtuell dator bestämmer du när du vill uppdatera eller uppgradera operativ systemet och databas program varan. Du bestämmer också när du ska installera ytterligare program vara, till exempel ett antivirus program. Vissa automatiserade funktioner är till för att avsevärt förenkla korrigering, säkerhets kopiering och hög tillgänglighet. Du kan kontrol lera storleken på den virtuella datorn, antalet diskar och deras lagrings konfiguration. Mer information finns i [virtuella datorer och moln tjänst storlekar för Azure](../virtual-machines/sizes.md).

### <a name="time-to-move-to-azure"></a>Tid att flytta till Azure

* Azure Database for MariaDB är rätt lösning för molnbaserade program när produktiviteten för utvecklare och snabb tid till marknaden för nya lösningar är viktiga. Med programmerings funktioner som fungerar som DBA är tjänsten lämplig för moln arkitekter och utvecklare, eftersom det minskar behovet av att hantera det underliggande operativ systemet och databasen.

* När du vill undvika tid och kostnader för att förvärva ny lokal maskin vara är MariaDB på virtuella Azure-datorer rätt lösning för program som kräver en MariaDB-databas eller åtkomst till MariaDB-funktioner i Windows eller Linux. Den här lösningen är också lämplig för att migrera befintliga lokala program och databaser till Azure intakt, i de fall där Azure Database for MariaDB är en dålig anpassning.

  Eftersom du inte behöver ändra presentations-, program-och data lager, sparar du tid och budget för att bygga om din befintliga lösning. I stället kan du fokusera på att migrera alla dina lösningar till Azure och åtgärda vissa prestanda optimeringar som Azure-plattformen kan kräva.

## <a name="next-steps"></a>Nästa steg

* Se [Azure Database for MariaDB prissättning](https://azure.microsoft.com/pricing/details/MariaDB/).
* Kom igång genom [att skapa din första server](quickstart-create-mariadb-server-database-using-azure-portal.md).
