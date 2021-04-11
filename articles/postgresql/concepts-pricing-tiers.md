---
title: Pris nivåer – Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs beräknings-och lagrings alternativen i Azure Database for PostgreSQL-enskild server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 119a68c4081cfc4fff6dc35b21badfac0397970b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605118"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Prisnivåer i Azure Database for PostgreSQL – Enskild server

Du kan skapa en Azure Database for PostgreSQL-server på en av tre olika pris nivåer: Basic, Generell användning och Minnesoptimerade. Pris nivåerna åtskiljs av mängden data bearbetning i virtuella kärnor som kan tillhandahållas, minne per vCore och lagrings teknik som används för att lagra data. Alla resurser har allokerats på PostgreSQL server nivå. En server kan ha en eller flera databaser.

| Resurs/nivå | **Basic** | **Generell användning** | **Minnesoptimerade** |
|:---|:----------|:--------------------|:---------------------|
| Beräknings generation | Gen 4, gen 5 | Gen 4, gen 5 | Gen 5 |
| Virtuella kärnor | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Minne per vCore | 2 GB | 5 GB | 10 GB |
| Lagrings storlek | 5 GB till 1 TB | 5 GB till 16 TB | 5 GB till 16 TB |
| Kvarhållningsperiod för databas säkerhets kopior | 7 till 35 dagar | 7 till 35 dagar | 7 till 35 dagar |

Om du vill välja en pris nivå använder du följande tabell som utgångs punkt.

| Prisnivå | Målbelastningar |
|:-------------|:-----------------|
| Grundläggande | Arbets belastningar som kräver ljus beräkning och I/O-prestanda. Exempel på detta är servrar som används för utveckling eller testning eller småskaliga program som inte används ofta. |
| Generell användning | De flesta företags arbets belastningar som kräver balanserade beräkning och minne med skalbart I/O-genomflöde. Några exempel kan vara servrar som är värd för webb- och mobilappar och andra företagsprogram.|
| Minnesoptimerad | Databas arbets belastningar med höga prestanda som kräver minnes prestanda för snabbare bearbetning av transaktioner och högre samtidighet. Exempel på det är servrar för att bearbeta realtidsdata och transaktionsappar eller analysappar med höga prestanda.|

När du har skapat en server kan du ändra antalet virtuella kärnor, maskin varu generation och pris nivå (förutom till och från Basic) på några sekunder. Du kan också självständigt justera mängden lagrings utrymme och säkerhets kopierings perioden upp eller ned utan avbrott i programmet. Du kan inte ändra lagrings typen för säkerhets kopia när en server har skapats. Mer information finns i avsnittet [Scale Resources](#scale-resources) .

## <a name="compute-generations-and-vcores"></a>Beräkna generationer och virtuella kärnor

Beräknings resurser tillhandahålls som virtuella kärnor, som representerar den underliggande maskin varans logiska processor. Kina, östra 1, Kina, norra 1, US DoD, centrala och US DoD, östra använda generation 4 logiska processorer som baseras på Intel E5-2673 v3-processorer (Haswell) 2,4 GHz. Alla andra regioner använder generation 5 logiska processorer som baseras på Intel E5-2673 v4-processorer (Broadwell) 2,3 GHz.

## <a name="storage"></a>Storage

Lagrings utrymmet du tillhandahåller är mängden lagrings kapacitet som är tillgänglig för din Azure Database for PostgreSQL-Server. Lagrings utrymmet används för databasfilerna, temporära filer, transaktions loggar och PostgreSQL-Server loggarna. Den totala mängden lagrings utrymme som du tillhandahåller definierar också den I/O-kapacitet som är tillgänglig för servern.

| Lagringsattribut | **Basic** | **Generell användning** | **Minnesoptimerade** |
|:---|:----------|:--------------------|:---------------------|
| Lagringstyp | Basic Storage | Generell användning lagring | Generell användning lagring |
| Lagrings storlek | 5 GB till 1 TB | 5 GB till 16 TB | 5 GB till 16 TB |
| Öknings storlek för lagring | 1 GB | 1 GB | 1 GB |
| IOPS | Variabel |3 IOPS/GB<br/>Min 100 IOPS<br/>Max 20 000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 20 000 IOPS |

> [!NOTE]
> Det finns stöd för lagring på upp till 16TB och 20 000 IOPS i följande regioner: östra Australien, sydöstra Australien, södra Brasilien, centrala Kanada, Östra Kanada, centrala USA, Kina, östra 2, Kina, norra 2, Asien, östra, östra USA, östra USA 1, östra USA 2, Östra Japan, västra USA, västra (USA), södra centrala USA, norra Europa, södra centrala USA Schweiz, norra Sydostasien. , Schweiz, västra, US Gov öst, US Gov SouthCentral, US Gov sydvästra, Storbritannien, södra, Storbritannien, västra, Västeuropa, västra centrala USA, västra USA och västra USA 2.
>
> Alla andra regioner stöder upp till 4 TB lagring och 6000 IOPS.
>

Du kan lägga till ytterligare lagrings kapacitet under och efter att servern har skapats och göra det möjligt för systemet att växa lagring automatiskt baserat på lagrings förbrukningen för din arbets belastning.

>[!NOTE]
> Lagringen kan bara skalas upp, inte nedåt.

Basic-nivån ger ingen IOPS-garanti. På den Generell användning och minnesoptimerade pris nivån, skalar IOPS med den tillhandahållna lagrings storleken i ett 3:1-förhållande.

Du kan övervaka i/O-förbrukningen i Azure Portal eller genom att använda Azure CLI-kommandon. De relevanta måtten för övervakning är [lagrings gränser, lagrings utrymme, lagring och i/o procent](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Lagringsgränsen är nådd

Servrar med mindre än lika med 100 GB allokerat lagrings utrymme markeras som skrivskyddade om det lediga lagrings utrymmet är mindre än 512 MB eller 5% av den allokerade lagrings storleken. Servrar med mer än 100 GB allokerat lagringsutrymme markeras bara som skrivskyddade när det lediga lagringsutrymmet är mindre än 5 GB.

Om du till exempel har allokerat 110 GB lagrings utrymme och den faktiska användningen går över 105 GB, är servern markerad som skrivskyddad. Alternativt, om du har allokerat 5 GB lagring, markeras servern som skrivskyddad när den kostnads fria lagrings platsen når mindre än 512 MB.

När servern är inställd på skrivskyddad är alla befintliga sessioner frånkopplade och ej allokerade transaktioner återställs. Eventuella efterföljande Skriv åtgärder och transaktions incheckningar fungerar inte. Alla efterföljande Läs frågor fungerar utan avbrott.  

Du kan antingen öka mängden allokerat lagrings utrymme till servern eller starta en ny session i Read-Write-läge och släppa data för att frigöra ledigt lagrings utrymme. Kör `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` ställer in den aktuella sessionen för att läsa Skriv läge. Undvik att data skadas genom att inte utföra några Skriv åtgärder när servern fortfarande är i skrivskyddat läge.

Vi rekommenderar att du aktiverar automatisk storleks ökning för lagring eller ställer in en avisering som meddelar dig när Server lagringen närmar sig tröskelvärdet, så att du kan undvika att komma in i skrivskyddat läge. Mer information finns i dokumentationen om [hur du konfigurerar en avisering](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Utöka lagring automatiskt

Med automatisk storleks ökning i arkivet förhindrar du att servern kör lagrings utrymme och blir skrivskyddad. Om lagrings funktionen är aktive rad utökas lagringen automatiskt utan att arbets belastningen påverkas. För servrar med mindre än lika med 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5 GB så snart det lediga lagrings utrymmet är lägre än 1 GB eller 10% av det allokerade lagrings utrymmet. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5% när det lediga lagrings utrymmet är lägre än eller lika med 10 GB eller 5% av den allokerade lagrings storleken. De maximala lagrings gränserna som anges ovan gäller.

Om du till exempel har allokerat 1000 GB lagrings utrymme och den faktiska användningen går över 950 GB, ökar server lagrings storleken till 1050 GB. Alternativt, om du har etablerad 10 GB lagrings utrymme, ökar lagrings storleken till 15 GB om mindre än 1 GB lagrings utrymme är kostnads fritt.

Kom ihåg att lagringen bara kan skalas upp, inte nedåt.

## <a name="backup-storage"></a>Lagring av säkerhets kopior

Azure Database for PostgreSQL tillhandahåller upp till 100% av din etablerade Server lagring som säkerhets kopierings lagring utan extra kostnad. Alla säkerhets kopierings lagringar som du använder utöver detta belopp debiteras i GB per månad. Om du till exempel etablerar en server med 250 GB lagrings utrymme, kommer du att ha 250 GB ytterligare lagrings utrymme för Server säkerhets kopior utan kostnad. Lagring för säkerhets kopieringar utöver 250 GB debiteras enligt [pris sättnings modellen](https://azure.microsoft.com/pricing/details/postgresql/). Om du vill förstå faktorer som påverkar lagrings användningen av säkerhets kopior, övervakar och kontrollerar lagrings kostnaden för säkerhets kopiering kan du läsa [dokumentationen för säkerhets kopiering](concepts-backup.md).

## <a name="scale-resources"></a>Skala resurser

När du har skapat din server kan du oberoende ändra virtuella kärnor, maskin varu genereringen, pris nivån (förutom till och från Basic), mängden lagring och kvarhållningsperioden för säkerhets kopior. Du kan inte ändra lagrings typen för säkerhets kopia när en server har skapats. Antalet virtuella kärnor kan skalas upp eller ned. Kvarhållningsperioden för säkerhets kopior kan skalas upp eller ned från 7 till 35 dagar. Det går bara att öka lagrings storleken. Skalning av resurserna kan göras via portalen eller Azure CLI. Ett exempel på skalning med hjälp av Azure CLI finns i [övervaka och skala en Azure Database for postgresql server med hjälp av Azure CLI](scripts/sample-scale-server-up-or-down.md).

> [!NOTE]
> Det går bara att öka lagrings storleken. Du kan inte gå tillbaka till en mindre lagrings storlek efter ökningen.

När du ändrar antalet virtuella kärnor, maskin varu genereringen eller pris nivån skapas en kopia av den ursprungliga servern med den nya beräknings allokeringen. När den nya servern är igång växlar anslutningarna över till den nya servern. Under tiden då systemet växlar över till den nya servern kan inga nya anslutningar upprättas, och transaktioner som inte allokerats återställs. Hur lång tid detta tar varierar, men i de flesta fall tar det mindre än en minut.

Skalning av lagring och ändring av kvarhållning av säkerhets kopior är true online-åtgärder. Det finns ingen nedtid och ditt program påverkas inte. Som IOPS-skala med storleken på den allokerade lagringen kan du öka tillgängligheten för IOPS för servern genom att skala upp lagringen.

## <a name="pricing"></a>Priser

Den senaste pris informationen finns på [sidan med pris](https://azure.microsoft.com/pricing/details/PostgreSQL/)information för tjänsten. Om du vill se kostnaden för den konfiguration du vill ha, visar [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) månads kostnaden på fliken **pris nivå** baserat på de alternativ du väljer. Om du inte har någon Azure-prenumeration kan du använda pris Kalkylatorn för Azure för att få ett uppskattat pris. På webbplatsen för [Azures pris kalkylator](https://azure.microsoft.com/pricing/calculator/) väljer du **Lägg till objekt**, expanderar kategorin **databaser** och väljer **Azure Database for PostgreSQL** för att anpassa alternativen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en postgresql-server i portalen](tutorial-design-database-using-azure-portal.md).
- Läs mer om [tjänst begränsningar](concepts-limits.md).
- Lär dig hur du [skalar ut med Läs repliker](howto-read-replicas-portal.md).
