---
title: Översikt – Azure Database for MySQL enskild server
description: Lär dig mer om Azure Database for MySQL enskild server, en Relations databas tjänst i Microsoft-molnet baserat på MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: b0f8c5806ba22708db6dc537d391c1f1b1a183ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92543822"
---
# <a name="azure-database-for-mysql-single-server"></a>Azure Database for MySQL-server – enskild server

[Azure Database for MySQL](overview.md) som drivs av MySQL Community Edition finns i två distributions lägen:
- Enskild server 
- Flexibel server (förhandsversion)

I den här artikeln får du en översikt över och introduktion till grundläggande begrepp för distributions modellen för en enskild server. Mer information om flexibelt Server distributions läge finns i [Översikt över flexibla servrar](flexible-server/index.yml). Information om hur du bestämmer vilka distributions alternativ som passar din arbets belastning finns i [välja rätt MySQL Server-alternativ i Azure](select-right-deployment-type.md).

## <a name="overview"></a>Översikt

En enda server är en fullständigt hanterad databas tjänst med minimala krav på anpassningar av databasen. Plattformen för enskild server är utformad för att hantera de flesta av funktionerna för databas hantering, till exempel korrigering, säkerhets kopiering, hög tillgänglighet, säkerhet med minimal användar konfiguration och kontroll. Arkitekturen är optimerad för att ge 99,99% tillgänglighet för enskild tillgänglighets zon. Den stöder community-versionen av MySQL 5,6, 5,7 och 8,0. Tjänsten är allmänt tillgänglig idag i många olika Azure- [regioner](https://azure.microsoft.com/global-infrastructure/services/). 

Enkla servrar lämpar sig bäst för inbyggda Cloud-program som är utformade för att hantera automatiserad uppdatering utan att det krävs någon detaljerad kontroll över uppdaterings schemats och anpassade MySQL-konfigurationsinställningar. 

## <a name="high-availability"></a>Hög tillgänglighet

Distributions modellen för en enskild server är optimerad för inbyggd hög tillgänglighet och elastiskhet till minskad kostnad. Arkitekturen separerar beräkning och lagring. Databas motorn körs på en patentskyddad beräknings behållare, medan datafiler finns i Azure Storage. Lagringen har tre lokalt redundanta kopior av databasfilerna som garanterar data hållbarhet. 

Vid planerade eller oplanerade redundansväxlings händelser, om servern kraschar, behåller tjänsten hög tillgänglighet för servrarna med hjälp av följande automatiserade procedur:

1. En ny beräknings behållare har allokerats
2. Lagringen med datafiler mappas till den nya behållaren 
3. MySQL Database Engine är online i den nya beräknings behållaren
4. Gateway-tjänsten säkerställer transparent redundans som garanterar att inga ändringar i program sidan kräver. 
  
Den normala tids växlings tiden sträcker sig från 60-120 sekunder. Den inbyggda moln designen i den enskilda Server tjänsten gör det möjligt att stödja 99,99% av tillgängligheten och eliminera kostnaden för passivt snabb växling.

Azures branschledande service avtal (SLA) med 99,99% tillgänglighet, som drivs av ett globalt nätverk av Microsoft-hanterade data Center, hjälper till att hålla programmen igång 24/7.

:::image type="content" source="media/single-server-overview/1-singleserverarchitecture.png" alt-text="Konceptuellt diagram över Azure Database for MySQL enskild server arkitektur"::: 

## <a name="automated-patching"></a>Automatisk korrigering 

Tjänsten utför automatisk uppdatering av underliggande maskin vara, operativ system och databas motor. Korrigeringen innehåller säkerhets-och program uppdateringar. För MySQL-motorn är lägre versions uppgraderingar automatiska och ingår som en del av korrigerings cykeln. Det finns inga användar åtgärder eller konfigurations inställningar som krävs för korrigering. Uppdaterings frekvensen är tjänst som hanteras baserat på nytto lastens allvarlighets grad. I allmänhet följer tjänsten varje månads versions schema som en del av den kontinuerliga integreringen och versionen. Användare kan prenumerera på ett [planerat underhålls meddelande](concepts-monitoring.md) för att få ett meddelande om kommande underhåll 72 timmar före händelsen.

## <a name="automatic-backups"></a>Automatiska säkerhetskopieringar

Tjänsten för enskild server skapar automatiskt Server säkerhets kopior och lagrar dem i användare som har konfigurerat lokalt redundant eller Geo-redundant lagring. Säkerhets kopieringar kan användas för att återställa servern till alla tidpunkter inom säkerhets kopie perioden för kvarhållning. Standard kvarhållningsperioden för säkerhets kopiering är sju dagar. Kvarhållning kan alternativt konfigureras upp till 35 dagar. Alla säkerhetskopior krypteras med AES 256-bitars kryptering. Mer information finns i [säkerhets kopior](concepts-backup.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Justera prestanda och skalning på några sekunder

Tjänsten enskild server är tillgänglig på tre SKU-nivåer: Basic, Generell användning och Minnesoptimerade. Basic-nivån passar bäst för låg kostnads utveckling och låg concurrency-arbetsbelastningar. Generell användning och Minnesoptimerade passar bättre för produktions arbets belastningar som kräver hög concurrency, skalning och förutsägbar prestanda. Du kan skapa din första app i en liten databas för några kronor i månaden och sedan justera skalan för att bemöta lösningens behov. Lagrings skalningen är online och har stöd för automatisk utökning av lagring. Dynamisk skalbarhet gör att databasen reagerar transparent på resurskrav som ändras snabbt. Du betalar bara för de resurser du förbrukar. Mer information finns i [Prisnivåer](./concepts-pricing-tiers.md).

## <a name="enterprise-grade-security-compliance-and-governance"></a>Säkerhet, efterlevnad och styrning i företags klass

Tjänsten enskild server använder FIPS 140-2-validerade kryptografisk modul för lagrings kryptering av data i vila. Data, inklusive säkerhets kopieringar och temporära filer som skapas när frågor körs, krypteras. Tjänsten använder AES 256-bit-chiffer som ingår i Azure Storage-kryptering och nycklarna kan hanteras av systemet (standard) eller [kund hantering](concepts-data-encryption-mysql.md). Tjänsten krypterar data i rörelse med SSL/TLS (Transport Layer Security) som standard. Tjänsten stöder TLS-versionerna 1,2, 1,1 och 1,0 med möjlighet att framtvinga [lägsta TLS-version](concepts-ssl-connection-security.md). 

Tjänsten tillåter privat åtkomst till servrarna med hjälp av en [privat länk](concepts-data-access-security-private-link.md) och tillhandahåller funktionen [Avancerat skydd](concepts-data-access-and-security-threat-protection.md) . Avancerat skydd identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Förutom den interna autentiseringen har tjänsten för enskild server stöd för  [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) autentisering. Azure AD-autentisering är en mekanism för att ansluta till MySQL-servrarna med identiteter definierade och hanterade i Azure AD. Med Azure AD-autentisering kan du hantera databasens användar identiteter och andra Azure-tjänster på en central plats, vilket fören klar och centraliserar åtkomst kontrollen.

[Gransknings loggning](concepts-audit-logs.md) är tillgängligt för att spåra alla aktiviteter på databas nivå. 

Tjänsten för enskild server är en framställning av alla branschledande certifieringar som FedRAMP, HIPAA, PCI DSS. Besök [Azure Säkerhetscenter](https://www.microsoft.com/trustcenter/security) för information om Azures plattformssäkerhet. 

Mer information om Azure Database for MySQL säkerhetsfunktioner finns i [säkerhets översikten](concepts-security.md).

## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Tjänsten enskild server är utrustad med inbyggda funktioner för prestanda övervakning och avisering. Alla Azure-mått har en frekvens på en minut och varje mått ger 30 dagars historik. Du kan konfigurera aviseringar för måtten. Tjänsten gör det möjligt att konfigurera långsamma Query-loggar och levereras med en differentierad [query Store](concepts-query-store.md) -funktion. Query Store fören klar prestanda fel sökningen genom att hjälpa dig att snabbt hitta de allra som körs och de flesta resurs intensiva frågor. Med dessa verktyg kan du snabbt optimera dina arbets belastningar och konfigurera servern för bästa prestanda. Se [övervakning](concepts-monitoring.md) för mer information.

## <a name="migration"></a>Migrering

Tjänsten kör community-versionen av MySQL. Detta möjliggör fullständig programkompatibilitet och kräver minimal omplanering av kostnader för att migrera befintliga program som utvecklats i MySQL-motorn till en enskild server-tjänst. Migreringen till den enskilda servern kan utföras med hjälp av något av följande alternativ:

- **Dumpa och Återställ** – för offline-migrering, där användarna kan få viss nedtid, dumpa och återställa med hjälp av community-verktyg som mysqldump/dumper kan ge ett snabbast sätt att migrera. Mer information finns i [migrera med dump och Restore](concepts-migrate-dump-restore.md) . 
- **Azure Database migration service** – för sömlösa och förenklade migreringar till en enskild server med minimal nedtid kan [Azure Database migration service](../dms/tutorial-mysql-azure-mysql-online.md) utnyttjas. 
- **Datareplikering** – för minimala nedtid-migreringar kan datareplikering, som förlitar sig på BinLog-baserad replikering, också användas. Datareplikering är att föredra för minimala stillestånds migreringar genom praktiska experter som söker efter mer kontroll över migreringen. Mer information finns i [data-i-replikering](concepts-data-in-replication.md) .

## <a name="contacts"></a>Kontakter
Om du har frågor eller förslag som du kan behöva för att arbeta med Azure Database for MySQL skickar du ett e-postmeddelande till Azure Database for MySQL-teamet ([ @Ask Azure dB för MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Den här e-postadressen är inte ett alias för teknisk support.

Tänk dessutom på följande kontaktpunkter efter behov:

- Kontakta Azure Support genom att [skicka in ett supportärende från Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.
- Om du vill ge feedback eller begära nya funktioner, skapar du ett inlägg via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till Azure Database for MySQL distributions läge för enskild server är du redo att:

- Skapa din första server. 
  - [Skapa en Azure Database för MySQL med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
  - [Skapa en Azure Database för MySQL-server med Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)
  - [Azure CLI-exempel för Azure Database for MySQL](sample-scripts-azure-cli.md)

- Bygg din första app med önskat språk:
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Kör](./connect-go.md)
