---
title: Ansluta till Azure Database for MySQL med dbForge Studio för MySQL
description: Artikeln visar hur du ansluter till Azure Database for MySQL Server via dbForge Studio för MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 942651aadf4113c1aca32e4e1d2c558b0d764421
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377233"
---
# <a name="connect-to-azure-database-for-mysql-using-dbforge-studio-for-mysql"></a>Ansluta till Azure Database for MySQL med dbForge Studio för MySQL

Så här ansluter du Azure Database for MySQL med [dbForge Studio för MySQL:](https://www.devart.com/dbforge/mysql/studio/)

1. Välj Ny anslutning på databasmenyn.

2. Ange ett värdnamn och inloggningsuppgifter.

3. Välj knappen Testa anslutning för att kontrollera konfigurationen.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Azure-anslutning":::

## <a name="migrate-a-database-using-the-backup-and-restore-functionality"></a>Migrera en databas med hjälp av funktionen säkerhetskopiering och återställning

Studio tillåter migrering av databaser till Azure på många sätt, och valet beror helt på dina behov. Om du behöver flytta hela databasen är det bäst att använda funktionen Säkerhetskopiering och återställning. I det här exemplet migrerar vi *sakila-databasen* som finns på MySQL-servern till Azure Database for MySQL. Logiken bakom migreringsprocessen med funktionerna säkerhetskopiering och återställning i dbForge Studio för MySQL är att skapa en säkerhetskopia av MySQL-databasen och sedan återställa den i Azure Database for MySQL.

### <a name="back-up-the-database"></a>Back up the database

1. På menyn Databas pekar du på Säkerhetskopiera och återställ och väljer sedan Säkerhetskopieringsdatabas. Guiden Säkerhetskopiering av databas visas.

2. På fliken Säkerhetskopieringsinnehåll i guiden Säkerhetskopiering av databas väljer du databasobjekt som du vill säkerhetskopiera.

3. På fliken Alternativ konfigurerar du säkerhetskopieringen så att den passar dina behov.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Alternativ för guiden Back up":::

4. Ange sedan fel för bearbetningsbeteende och loggningsalternativ.

5. Välj Säkerhetskopiera.

### <a name="restore-the-database"></a>Återställa databasen

1. Anslut till Azure for Database for MySQL enligt beskrivningen ovan.

2. Högerklicka på den Databasutforskare, peka på Återställning och Återställ och välj sedan Återställ databas.

3. I guiden Databasåterställning som öppnas väljer du en fil med en säkerhetskopia av databasen.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Återställningssteg":::

4. Välj Återställ.

5. Kontrollera resultatet.

## <a name="migrate-a-database-using-the-copy-databases-functionality"></a>Migrera en databas med funktionen Kopiera databaser

Funktionen Kopiera databaser liknar säkerhetskopiering och återställning, förutom att du inte behöver två steg för att migrera en databas. Dessutom tillåter funktionen överföring av två eller flera databaser på en gång. Funktionen Kopiera databaser är endast tillgänglig i Enterprise-versionen av dbForge Studio för MySQL.
I det här exemplet migrerar vi *world_x* från MySQL-servern till Azure Database for MySQL.
Så här migrerar du en databas med funktionen Kopiera databaser:

1. På menyn Databas väljer du Kopiera databaser. 

2. På fliken Kopiera databaser som visas anger du käll- och målanslutningen och väljer de databaser som ska migreras. Vi anger Azure MySQL-anslutning och väljer *world_x* databas. Välj den gröna pilen för att initiera processen.

3. Kontrollera resultatet.

Som ett resultat av vårt databasmigreringsarbete *har world_x* databasen visats i Azure MySQL.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Kopiera databasresultat":::

## <a name="migrate-a-database-using-schema-and-data-compare-tools"></a>Migrera en databas med verktyg för schema- och datajämföring

dbForge Studio för MySQL innehåller några verktyg som gör det möjligt att migrera MySQL-databaser, MySQL-scheman och\eller data till Azure. Valet av funktion beror på dina behov och kraven för ditt projekt. Om du behöver flytta en databas selektivt, det vill säga migrera vissa MySQL-tabeller till Azure, är det bäst att använda funktionerna Schema och DataJämföring.
I det här exemplet migrerar vi *den världsdatabas* som finns på MySQL-servern till Azure Database for MySQL. Logiken bakom migreringsprocessen med funktionerna Schema och Datajämföring i dbForge Studio för MySQL är att skapa en tom databas i Azure Database for MySQL, synkronisera den med den mySQL-databas som krävs först med verktyget Schemajämföring och sedan använda verktyget Datajämföring. På så sätt flyttas MySQL-scheman och data korrekt till Azure.

### <a name="step-1-connect-to-azure-database-for-mysql-and-create-an-empty-database"></a>Steg 1. Ansluta till Azure Database for MySQL och skapa en tom databas

### <a name="step-2-schema-synchronization"></a>Steg 2. Schemasynkronisering

1. På jämförelsemenyn väljer du Ny schemajämförelse.
Guiden Ny schemajämförelse visas.

2. Välj Källa och Mål och ange sedan alternativen för schemajämförelse. Välj Jämför.

3. I rutnätet med jämförelseresultat som visas väljer du objekt för synkronisering. Välj den gröna pilen för att öppna guiden Schemasynkronisering.

4. Gå igenom stegen i guiden för att konfigurera synkronisering. Välj Synkronisera för att distribuera ändringarna.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Guiden Schemasynkronisering":::

### <a name="step-3-data-comparison"></a>Steg 3. Datajämförelse

1. På jämförelsemenyn väljer du Ny datajämförelse. Guiden Ny datajämförelse visas.

2. Välj Källa och Mål och ange sedan alternativ för datajämförelse och ändra mappningar om det behövs. Välj Jämför.

3. I rutnätet med jämförelseresultat som visas väljer du objekt för synkronisering. Välj den gröna pilknappen för att öppna guiden Datasynkronisering.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Resultat av datakomplicera":::

4. Gå igenom stegen i guiden för att konfigurera synkronisering. Välj Synkronisera för att distribuera ändringarna.

5. Kontrollera resultatet.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Resultat av datasynkronisering":::

## <a name="summary"></a>Sammanfattning

Numera flyttar fler företag sina databaser Azure Database for MySQL, eftersom den här databastjänsten är enkel att konfigurera, hantera och skala. Migreringen behöver inte vara svårt. dbForge Studio för MySQL har obefläckade migreringsverktyg som kan underlätta processen avsevärt. Studio gör att databasöverföringen enkelt kan konfigureras, sparas, redigeras, automatiseras och schemaläggas.

## <a name="next-steps"></a>Nästa steg
- [Översikt över MySQL](overview.md)
