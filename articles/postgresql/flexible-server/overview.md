---
title: Azure Database for PostgreSQL-flexibel Server
description: Ger en översikt över Azure Database for PostgreSQL-flexibel Server.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 03/17/2021
ms.openlocfilehash: 8e5c204709f0eadb6c1a4b6f479d4833f335b264
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594894"
---
# <a name="azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL-flexibel Server

[Azure Database for PostgreSQL](../overview.md) som drivs av postgresql Community Edition finns i tre distributions lägen:

- [Enskild server](../overview-single-server.md)
- Flexibel server (förhandsversion)
- Hyperskala (Citus)

I den här artikeln får du en översikt över och introduktion till grundläggande begrepp för flexibel Server distributions modell.

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

## <a name="overview"></a>Översikt

Azure Database for PostgreSQL-flexibel Server är en fullständigt hanterad databas tjänst som är utformad för att ge mer detaljerad kontroll och flexibilitet för funktioner i databas hantering och konfigurations inställningar. I allmänhet tillhandahåller tjänsten mer flexibilitet och anpassningar av Server konfigurationen baserat på användar kraven. Med den flexibla Server arkitekturen kan användare samordna databas motor med en lägre latens på klient nivå, välja hög tillgänglighet inom en enda tillgänglighets zon och över flera tillgänglighets zoner. Flexibla servrar ger också bättre kostnads optimerings kontroller med möjlighet att stoppa/starta din server och den data bearbetnings nivå som är idealisk för arbets belastningar som inte behöver fullständig beräknings kapacitet kontinuerligt. Tjänsten stöder för närvarande community-versionen av PostgreSQL 11 och 12. Tjänsten är för närvarande en för hands version som är tillgänglig idag i många olika  [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/).

![Flexibel Server – översikt](./media/overview/overview-flexible-server.png)


Flexibla servrar lämpar sig bäst för

- Program utveckling som kräver bättre kontroll och anpassningar.
- Zon redundant hög tillgänglighet
- Hanterade underhålls fönster
  
## <a name="high-availability"></a>Hög tillgänglighet

Den flexibla Server distributions modellen är utformad för att stödja hög tillgänglighet i en zon för enskild tillgänglighet och över flera tillgänglighets zoner. Arkitekturen separerar beräkning och lagring. Databas motorn körs på en virtuell Linux-dator, medan datafiler finns i Azure Storage. Lagringen har tre lokalt redundanta kopior av databasfilerna som garanterar data hållbarhet.

Vid planerade eller oplanerade redundansväxlings händelser, om servern kraschar, behåller tjänsten hög tillgänglighet för servrarna med hjälp av följande automatiserade procedur:

1. En ny virtuell Compute Linux-dator har tillhandahållits.
2. Lagringen med datafiler mappas till den nya virtuella datorn
3. PostgreSQL databas motor tas online på den nya virtuella datorn.

Bilden nedan visar över gången för virtuell dator och lagrings problem.

 :::image type="content" source="./media/overview/overview-azure-postgres-flex-virtualmachine.png" alt-text="Flexibel Server – VM och lagrings problem":::

Om zonens redundanta hög tillgänglighet har kon figurer ATS, etableras tjänsten och upprätthåller en snabb växlings Server mellan tillgänglighets zoner i samma Azure-region. Data ändringarna på käll servern replikeras synkront till vänte läges servern för att säkerställa noll data förlust. Med zon redundant hög tillgänglighet när den planerade eller oplanerade redundansväxlingen utlöses, kommer standby-servern att bli online omedelbart och är tillgänglig för bearbetning av inkommande transaktioner. Detta gör att tjänsten kan återhämtning från tillgänglighets zon felet i en Azure-region som stöder flera tillgänglighets zoner som visas på bilden nedan.

 :::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="Zon redundant hög tillgänglighet":::

 Mer information finns i [dokumentet med hög tillgänglighet](./concepts-high-availability.md) .

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatiserad uppdatering med hanterat underhålls fönster

Tjänsten utför automatisk uppdatering av underliggande maskin vara, operativ system och databas motor. Korrigeringen innehåller säkerhets-och program uppdateringar. För PostgreSQL-motorn ingår lägre versions uppgraderingar också som en del av den planerade underhålls versionen. Användare kan konfigurera uppdaterings schema som ska hanteras av systemet eller definiera deras anpassade schema. Under underhålls planen tillämpas korrigeringen och servern kan behöva startas om som en del av korrigerings processen för att slutföra uppdateringen. Med det anpassade schemat kan användarna göra sin förväntad korrigerings cykel och välja en underhålls period med minimal påverkan på verksamheten. I allmänhet följer tjänsten varje månads versions schema som en del av den kontinuerliga integreringen och versionen.

## <a name="automatic-backups"></a>Automatisk säkerhets kopiering

Den flexibla Server tjänsten skapar automatiskt Server säkerhets kopior och lagrar dem i användar konfigurationen lokalt på den redundanta zonen (ZRS). Säkerhets kopieringar kan användas för att återställa servern till alla tidpunkter inom säkerhets kopie perioden för kvarhållning. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Kvarhållning kan alternativt konfigureras upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering. Se [säkerhets kopieringar](./concepts-backup-restore.md) för mer information.

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder

Den flexibla Server tjänsten är tillgänglig i tre beräknings nivåer: Burst, Generell användning och Minnesoptimerade. Den burst-nivån passar bäst för låg kostnads utveckling och låg concurrency-arbetsbelastningar som inte behöver fullständig beräknings kapacitet kontinuerligt. Generell användning och Minnesoptimerade passar bättre för produktions arbets belastningar som kräver hög concurrency, skalning och förutsägbar prestanda. Du kan bygga ditt första program på en liten databas under några dollar i månaden och sedan sömlöst justera skalan så att den uppfyller lösningens behov.

## <a name="stopstart-server-to-lower-tco"></a>Stoppa/starta servern så att den sänker TCO

Med tjänsten flexibel Server kan du stoppa och starta Server på begäran för att sänka TCO. Faktureringen av beräknings nivån stoppas omedelbart när servern stoppas. Detta kan göra att du kan få avsevärda kostnads besparingar under utveckling, testning och tidsbegränsade förutsägbara produktions arbets belastningar. Servern är i stoppat tillstånd i sju dagar om den inte startas om tidigare.

## <a name="enterprise-grade-security"></a>Säkerhet i företagsklass

Den flexibla Server tjänsten använder FIPS 140-2-validerade kryptografisk modul för lagrings kryptering av data i vila. Data, inklusive säkerhets kopieringar och temporära filer som skapas när frågor körs, krypteras. Tjänsten använder AES 256-bit-chiffer som ingår i Azure Storage-kryptering och nycklarna kan hanteras av systemet (standard). Tjänsten krypterar data i rörelse med SSL/TLS (Transport Layer Security) som standard. Tjänsten upprätthåller och stöder endast TLS-versioner 1,2.

Flexibla servrar ger fullständig privat åtkomst till servrarna med Azure Virtual Network (VNet-integrering). Servrar i Azure Virtual Network kan bara nås och anslutas via privata IP-adresser. Med VNet-integrering nekas offentlig åtkomst och servrar kan inte nås med offentliga slut punkter.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Den flexibla Server tjänsten är utrustad med inbyggda funktioner för prestanda övervakning och avisering. Alla Azure-mått har en frekvens på en minut och varje mått ger 30 dagars historik. Du kan konfigurera aviseringar för måtten. Tjänsten exponerar värd Server mått för att övervaka resursernas användning och gör det möjligt att konfigurera långsamma frågemeddelanden. Med dessa verktyg kan du snabbt optimera dina arbets belastningar och konfigurera servern för bästa prestanda.

## <a name="azure-regions"></a>Azure-regioner

En av fördelarna med att köra din arbets belastning i Azure är den globala räckvidden. Den flexibla servern är tillgänglig idag i följande Azure-regioner:

| Region | Tillgänglighet | Zon-redundant HA | 
| --- | --- | --- |
| Europa, västra | :heavy_check_mark: | :heavy_check_mark: |
| Europa, norra | :heavy_check_mark: | :heavy_check_mark: |
| Storbritannien, södra | :heavy_check_mark: | :heavy_check_mark: | 
| USA, östra 2 | :heavy_check_mark: | :heavy_check_mark: |
| USA, västra 2 | :heavy_check_mark: | :heavy_check_mark: |
| Central US | :heavy_check_mark: | :heavy_check_mark: | 
| East US | :heavy_check_mark: | :heavy_check_mark: | 
| Sydostasien | :heavy_check_mark: | :heavy_check_mark: |
| Japan, östra | :heavy_check_mark: | :heavy_check_mark: | 
| Australien, östra | :heavy_check_mark: | :heavy_check_mark: | 
| Kanada, centrala | :heavy_check_mark: | :heavy_check_mark: | 

Vi fortsätter att lägga till fler regioner för flexibel Server.

## <a name="migration"></a>Migrering

Tjänsten kör community-versionen av PostgreSQL. Detta möjliggör fullständig programkompatibilitet och kräver minimal omkostnader för att migrera ett befintligt program som utvecklats på PostgreSQL-motorn till en flexibel Server. 

- **Dumpa och Återställ** – för offline-migrering där användarna kan ge viss nedtid, dumpa och återställa med hjälp av community-verktyg som pg_dump och pg_restore kan ge ett snabbast sätt att migrera. Mer information finns i [migrera med dump och Restore](../howto-migrate-using-dump-and-restore.md) .
- **Azure Database migration service** – för sömlösa och förenklade migreringar till flexibel server med minimal stillestånds tid kan Azure Database migration service utnyttjas. Se [DMS via portal](../../dms/tutorial-postgresql-azure-postgresql-online-portal.md) och [DMS via CLI](../../dms/tutorial-postgresql-azure-postgresql-online.md). Du kan migrera från din Azure Database for PostgreSQL-enskild server till flexibel Server. Mer information finns i den här [DMS-artikeln](../../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) .

## <a name="contacts"></a>Kontakter
Om du har frågor eller förslag som du kan ha på Azure Database for PostgreSQL flexibel Server kan du skicka ett e-postmeddelande till Azure Database for PostgreSQL-teamet ([ @Ask Azure dB för postgresql](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Observera att den här e-postadressen inte är ett alias för teknisk support.

Tänk dessutom på följande kontaktpunkter efter behov:

- Kontakta Azure Support genom att [skicka in ett supportärende från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
  

## <a name="next-steps"></a>Nästa steg

Nu när du har läst en introduktion till Azure Database for PostgreSQL flexibelt Server distributions läge är du redo att skapa din första server: [skapa en Azure Database for PostgreSQL flexibel server med Azure Portal](./quickstart-create-server-portal.md)