---
title: Azure Database for PostgreSQL enskild server
description: Ger en översikt över Azure Database for PostgreSQL enskild server.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 18d944cd2b3bfde1a0ee5fc53e83e4016b11b01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100379224"
---
# <a name="azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL enskild server

[Azure Database for PostgreSQL](./overview.md) som drivs av postgresql Community Edition finns i tre distributions lägen:

- Enskild server
- Flexibel server (förhandsversion)
- Hyperskala (Citus)

I den här artikeln får du en översikt över och introduktion till grundläggande begrepp för distributions modellen för en enskild server. Mer information om flexibelt Server distributions läge finns i Översikt över [flexibla Server översikter](./flexible-server/overview.md) och citus-översikt.

## <a name="overview"></a>Översikt

En enda server är en fullständigt hanterad databas tjänst med minimala krav på anpassningar av databasen. Plattformen för enskild server är utformad för att hantera de flesta av funktionerna för databas hantering, till exempel korrigering, säkerhets kopiering, hög tillgänglighet, säkerhet med minimal användar konfiguration och kontroll. Arkitekturen är optimerad för att ge 99,99% tillgänglighet för enskild tillgänglighets zon. Det stöder community-versionen av PostgreSQL 9,5, 9,6, 10 och 11. Tjänsten är allmänt tillgänglig idag i många olika Azure- [regioner](https://azure.microsoft.com/global-infrastructure/services/).

Enkla servrar lämpar sig bäst för molnbaserade program som är utformade för att hantera automatiserad uppdatering utan att behöva detaljerad kontroll över uppdaterings schema och anpassade konfigurations inställningar för PostgreSQL.

## <a name="high-availability"></a>Hög tillgänglighet

Distributions modellen för en enskild server är optimerad för inbyggd hög tillgänglighet och elastiskhet till minskad kostnad. Arkitekturen separerar beräkning och lagring. Databas motorn körs på en patentskyddad beräknings behållare, medan datafiler finns i Azure Storage. Lagringen har tre lokalt redundanta kopior av databasfilerna som garanterar data hållbarhet.

Vid planerade eller oplanerade redundansväxlings händelser, om servern kraschar, behåller tjänsten hög tillgänglighet för servrarna med hjälp av följande automatiserade procedur:

1. En ny beräknings behållare har tillhandahållits.
2. Lagringen med datafiler mappas till den nya behållaren.
3. PostgreSQL-databasmotorn är online i den nya Compute-behållaren.
4. Gateway-tjänsten säkerställer transparent redundans som garanterar att inga ändringar i program sidan kräver.
   
 :::image type="content" source="./media/overview/overview-azure-postgres-single-server.png" alt-text="Azure Database for PostgreSQL enskild server":::

Den normala tids växlings tiden sträcker sig från 60-120 sekunder. Den inbyggda moln designen i den enskilda Server tjänsten gör det möjligt att stödja 99,99% av tillgängligheten och eliminera kostnaden för passivt snabb växling.

Azures branschledande service avtal (SLA) med 99,99% tillgänglighet, som drivs av ett globalt nätverk av Microsoft-hanterade data Center, hjälper till att hålla programmen igång 24/7.

## <a name="automated-patching"></a>Automatisk uppdatering

Tjänsten utför automatisk uppdatering av underliggande maskin vara, operativ system och databas motor. Korrigeringen innehåller säkerhets-och program uppdateringar. För PostgreSQL-motorn är lägre versions uppgraderingar automatiska och ingår som en del av korrigerings cykeln. Det finns inga användar åtgärder eller konfigurations inställningar som krävs för korrigering. Uppdaterings frekvensen är tjänst som hanteras baserat på nytto lastens allvarlighets grad. I allmänhet följer tjänsten varje månads versions schema som en del av den kontinuerliga integreringen och versionen. Användare kan prenumerera på ett [planerat underhålls meddelande]() för att få ett meddelande om kommande underhåll 72 timmar före händelsen.

## <a name="automatic-backups"></a>Automatisk säkerhets kopiering

Tjänsten för enskild server skapar automatiskt Server säkerhets kopior och lagrar dem i användar konfigurationen lokalt redundant (LRS) eller Geo-redundant lagring. Säkerhets kopieringar kan användas för att återställa servern till alla tidpunkter inom säkerhets kopie perioden för kvarhållning. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Kvarhållning kan alternativt konfigureras upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering. Se [säkerhets kopior](./concepts-backup.md) för mer information.

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder

Tjänsten enskild server är tillgänglig på tre SKU-nivåer: Basic, Generell användning och Minnesoptimerade. Basic-nivån passar bäst för låg kostnads utveckling och låg concurrency-arbetsbelastningar. Generell användning och Minnesoptimerade passar bättre för produktions arbets belastningar som kräver hög concurrency, skalning och förutsägbar prestanda. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Lagrings skalningen är online och har stöd för automatisk utökning av lagring. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du förbrukar. Mer information finns i [Prisnivåer]().

## <a name="enterprise-grade-security-compliance-and-governance"></a>Säkerhet, efterlevnad och styrning i företags klass

Tjänsten enskild server använder FIPS 140-2-validerade kryptografisk modul för lagrings kryptering av data i vila. Data, inklusive säkerhets kopieringar och temporära filer som skapas när frågor körs, krypteras. Tjänsten använder AES 256-bit-chiffer som ingår i Azure Storage-kryptering och nycklarna kan hanteras av systemet (standard) eller [kund hantering](). Tjänsten krypterar data i rörelse med SSL/TLS (Transport Layer Security) som standard. Tjänsten stöder TLS-versionerna 1,2, 1,1 och 1,0 med möjlighet att framtvinga [lägsta TLS-version]().

Tjänsten tillåter privat åtkomst till servrarna med hjälp av en privat länk och tillhandahåller funktionen Avancerat skydd. Avancerat skydd identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Förutom den interna autentiseringen har tjänsten för enskild server stöd för Azure Active Directory autentisering. Azure AD-autentisering är en mekanism för att ansluta till PostgreSQL-servrarna med identiteter definierade och hanterade i Azure AD. Med Azure AD-autentisering kan du hantera databasens användar identiteter och andra Azure-tjänster på en central plats, vilket fören klar och centraliserar åtkomst kontrollen.

[Gransknings loggning]() (i för hands version) är tillgänglig för att spåra alla aktiviteter på databas nivå.

Tjänsten enskild server är kompatibel med alla branschledande certifieringar som FedRAMP, HIPAA, PCI DSS. Besök [Azure Säkerhetscenter]() för information om Azures plattformssäkerhet.

Mer information om Azure Database for PostgreSQL säkerhetsfunktioner finns i [säkerhets översikten]().

## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Tjänsten enskild server är utrustad med inbyggda funktioner för prestanda övervakning och avisering. Alla Azure-mått har en frekvens på en minut och varje mått ger 30 dagars historik. Du kan konfigurera aviseringar för måtten. Tjänsten gör det möjligt att konfigurera långsamma Query-loggar och levereras med en differentierad [query Store](./concepts-query-store.md) -funktion. Query Store fören klar prestanda fel sökningen genom att hjälpa dig att snabbt hitta de allra som körs och de flesta resurs intensiva frågor. Med dessa verktyg kan du snabbt optimera dina arbets belastningar och konfigurera servern för bästa prestanda. Se [övervakning](./concepts-monitoring.md) för mer information.

## <a name="migration"></a>Migrering

Tjänsten kör community-versionen av PostgreSQL. Detta möjliggör fullständig programkompatibilitet och kräver minimal omkostnader för att migrera befintliga program som utvecklats på PostgreSQL-motorn till en enskild server-tjänst. Migreringen till den enskilda servern kan utföras med hjälp av något av följande alternativ:

- **Dumpa och Återställ** – för offline-migrering där användarna kan ge viss nedtid, dumpa och återställa med hjälp av community-verktyg som Pg_dump och Pg_restore kan ge ett snabbast sätt att migrera. Mer information finns i [migrera med dump och Restore](./howto-migrate-using-dump-and-restore.md) .
- **Azure Database migration service** – för sömlösa och förenklade migreringar till en enskild server med minimal nedtid kan Azure Database migration service utnyttjas. Se [DMS via portal](../dms/tutorial-postgresql-azure-postgresql-online-portal.md) och [DMS via CLI](../dms/tutorial-postgresql-azure-postgresql-online.md).

## <a name="contacts"></a>Kontakter

Om du har frågor eller förslag som du kan behöva för att arbeta med Azure Database for PostgreSQL skickar du ett e-postmeddelande till Azure Database for PostgreSQL-teamet ([ @Ask Azure dB för postgresql](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Den här e-postadressen är inte ett alias för teknisk support.

Tänk dessutom på följande kontaktpunkter efter behov:

- Kontakta Azure Support genom att [skicka in ett supportärende från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Nästa steg

Nu när du har läst en introduktion till Azure Database for PostgreSQL distributions läge för enskild server är du redo att:
- Skapa din första server.
