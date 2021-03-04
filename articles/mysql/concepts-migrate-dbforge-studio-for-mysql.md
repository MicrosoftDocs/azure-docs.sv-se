---
title: Ansluta till Azure Database for MySQL med dbForge Studio för MySQL
description: Artikeln visar hur du ansluter till Azure Database for MySQL server via dbForge Studio för MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 5138e542d1cc744a23fa1017dfec1f24b2ec1a5b
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107890"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Ansluta till Azure Database for MySQL med dbForge Studio för MySQL

Så här ansluter du till Azure Database for MySQL med dbForge Studio för MySQL:

1. På menyn databas väljer du ny anslutning.

2. Ange ett värdnamn och inloggnings uppgifter.

3. Välj knappen Testa anslutning för att kontrol lera konfigurationen.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure-anslutning":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Migrera en databas med funktionen för säkerhets kopiering och återställning

Med Studio kan du migrera databaser till Azure på många olika sätt, vilket du väljer beror bara på dina behov. Om du behöver flytta hela databasen är det bäst att använda säkerhets kopierings-och återställnings funktionen. I det här exemplet migrerar vi *Sakila* -databasen som finns på MySQL-servern till Azure Database for MySQL. Logiken bakom migreringsprocessen med säkerhets kopierings-och återställnings funktionen i dbForge Studio för MySQL är att skapa en säkerhets kopia av MySQL-databasen och sedan återställa den i Azure Database for MySQL.

### <a name="back-up-the-database"></a>Säkerhetskopiera databasen

1. Peka på Säkerhetskopiera och Återställ på databas-menyn och välj sedan säkerhets kopierings databas. Guiden säkerhets kopiering av databas visas.

2. På fliken Säkerhetskopiera innehåll i guiden säkerhets kopiering av databas väljer du de databas objekt som du vill säkerhetskopiera.

3. På fliken Alternativ konfigurerar du säkerhets kopierings processen så att den passar dina behov.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Guide alternativ för säkerhets kopiering":::

4. Ange sedan fel bearbetnings beteende och loggnings alternativ.

5. Välj säkerhets kopiering.

### <a name="restore-the-database"></a>Återställa databasen

1. Anslut till Azure för Database för MySQL enligt beskrivningen ovan.

2. Högerklicka på Databasutforskare bröd texten, peka på Säkerhetskopiera och Återställ och välj sedan Återställ databas.

3. I guiden databas återställning som öppnas väljer du en fil med en databas säkerhets kopia.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Återställ steg":::

4. Välj Återställ.

5. Kontrol lera resultatet.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Migrera en databas med funktionen Kopiera databaser

Funktionen Kopiera databaser liknar säkerhets kopieringen och återställningen, förutom att du inte behöver två steg för att migrera en databas. Och vad som är mer kan funktionen överföra två eller flera databaser i en enda go. Funktionen Kopiera databaser är bara tillgänglig i Enterprise-versionen av dbForge Studio för MySQL.
I det här exemplet migrerar vi *world_x* databasen från MySQL-servern till Azure Database for MySQL.
Så här migrerar du en databas med funktionen Kopiera databaser:

1. Välj Kopiera databaser på menyn databas. 

2. På fliken Kopiera databaser som visas anger du källa och mål anslutning och väljer databas (er) som ska migreras. Vi anger Azure MySQL-anslutning och väljer den *world_x* databasen. Välj den gröna pilen för att starta processen.

3. Kontrol lera resultatet.

På grund av vårt arbete med migreringen av databasen har *world_x* databasen visats i Azure MySQL.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Resultat av kopiera databaser":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Migrera en databas med hjälp av schema och data jämförelse verktyg

dbForge Studio för MySQL införlivar några verktyg som tillåter migrering av MySQL-databaser, MySQL-scheman och/eller data till Azure. Valet av funktioner beror på dina behov och kraven för ditt projekt. Om du behöver flytta en databas selektivt, det vill säga migrera vissa MySQL-tabeller till Azure, är det bäst att använda schema-och data jämförelse funktioner.
I det här exemplet migrerar vi den *världs* databas som finns på MySQL-servern till Azure Database for MySQL. Logiken bakom migreringsprocessen med schema-och data jämförelse funktionen i dbForge Studio för MySQL är att skapa en tom databas i Azure Database for MySQL, synkronisera den med den nödvändiga MySQL-databasen först med schema jämförelse verktyget och sedan använda verktyget data jämförelse. På så sätt flyttas MySQL-scheman och data korrekt till Azure.

### <a name="connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Anslut till Azure Database for MySQL och skapa en tom databas

Anslut till en Azure Database for MySQL och skapa en tom databas.

### <a name="step-2-schema-synchronization"></a>Steg 2. Schema-synkronisering

1. På menyn jämförelse väljer du ny schema jämförelse.
Guiden ny schema jämförelse visas.

2. Välj källa och mål och ange sedan schema jämförelse alternativen. Välj jämför.

3. I rutnätet jämförelse resultat som visas väljer du objekt för synkronisering. Klicka på den gröna pilen för att öppna guiden schema synkronisering.

4. Gå igenom stegen i guiden Konfigurera synkronisering. Välj Synkronisera för att distribuera ändringarna.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Guiden synkronisera schema":::

### <a name="data-comparison"></a>Data jämförelse

1. På menyn jämförelse väljer du ny data jämförelse. Guiden ny data jämförelse visas.

2. Välj källa och mål och ange sedan data jämförelse alternativen och ändrings mappningar om det behövs. Välj jämför.

3. I rutnätet jämförelse resultat som visas väljer du objekt för synkronisering. Klicka på den gröna pilen för att öppna guiden datasynkronisering.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Resultat av data komposition":::

4. Gå igenom stegen i guiden Konfigurera synkronisering. Välj Synkronisera för att distribuera ändringarna.

5. Kontrol lera resultatet.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Resultat av data synkronisering":::

## <a name="summary"></a>Sammanfattning

Nuförtiden flera företag flyttar sina databaser till Azure Database for MySQL eftersom den här databas tjänsten är enkel att konfigurera, hantera och skala. Migreringen behöver inte vara smärtsamt. dbForge Studio för MySQL har Immaculate Migreringsverktyg som avsevärt underlättar processen. Med Studio kan databas överföring enkelt konfigureras, sparas, redige ras, automatiseras och schemaläggas.

## <a name="next-steps"></a>Nästa steg
- [Översikt över MySQL](overview.md)