---
title: Lös skadad databas – Azure Database for MySQL
description: I den här artikeln får du lära dig hur du åtgärdar problem med databas skada i Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91766892"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Felsöka databasfel i Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Skadan av databasen kan orsaka drift stopp för ditt program. Det är också viktigt att lösa skadade problem i tid för att undvika data förlust. När databasen skadas visas det här felet i Server loggarna: `InnoDB: Database page corruption on disk or a failed.`

I den här artikeln får du lära dig hur du löser problem med databas-eller tabell skador. Azure Database for MySQL använder InnoDB-motorn. Den innehåller automatisk kontroll av skador och reparations åtgärder. InnoDB söker efter skadade sidor genom att köra kontroll summor på varje sida som läses. Om den hittar en avvikelse i kontroll summan stoppas automatiskt MySQL-servern.

Prova följande alternativ för att snabbt minimera problem med databasens skada.

## <a name="restart-your-mysql-server"></a>Starta om MySQL-servern

Du ser vanligt vis att en databas eller tabell är skadad när programmet har åtkomst till tabellen eller databasen. InnoDB innehåller en funktion för krasch återställning som kan lösa de flesta problem när servern startas om. Att starta om servern kan hjälpa servern att återställa från en krasch som gjorde att databasen är i ett felaktigt tillstånd.

## <a name="use-the-dump-and-restore-method"></a>Använd dump-och Restore-metoden

Vi rekommenderar att du löser problem med skadan genom att använda en *dump-och Restore* -metod. Den här metoden innebär följande:
1. Åtkomst till den skadade tabellen.
1. Använd mysqldump-verktyget för att skapa en logisk säkerhets kopia av tabellen. Säkerhets kopian kommer att behålla tabell strukturen och data i den.
1. Läser in tabellen i databasen igen.

### <a name="back-up-your-database-or-tables"></a>Säkerhetskopiera databasen eller tabellerna

> [!Important]
> - Kontrol lera att du har konfigurerat en brand Väggs regel för att få åtkomst till servern från klient datorn. Mer information finns i [Konfigurera en brand Väggs regel på en enskild server](howto-manage-firewall-using-portal.md) och [Konfigurera en brand Väggs regel på en flexibel Server](flexible-server/how-to-connect-tls-ssl.md).
> - Använd SSL-alternativet `--ssl-cert` för mysqldump om du har aktiverat SSL.

Skapa en säkerhets kopierings fil från kommando raden med hjälp av mysqldump. Använd det här kommandot:

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parameter beskrivningar:
- `[ssl-cert=/path/to/pem]`: Sökvägen till SSL-certifikatet. Hämta SSL-certifikatet på klient datorn och ange sökvägen i kommandot. Använd inte den här parametern om SSL är inaktiverat.
- `[host]`: Din Azure Database for MySQL-server.
- `[uname]`: Ditt användar namn för Server administratören.
- `[pass]`: Lösen ordet för din administratörs användare.
- `[dbname]`: Namnet på din databas.
- `[backupfile.sql]`: Fil namnet för säkerhets kopian av databasen.

> [!Important]
> - För en enskild server använder du det format `admin-user@servername` som ska ersättas `myserveradmin` i följande kommandon.
> - För flexibel Server använder du det format `admin-user` som ska ersättas `myserveradmin` i följande kommandon.

Om en enskild tabell är skadad väljer du de tabeller i databasen som ska säkerhets kopie ras:
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Om du vill säkerhetskopiera en eller flera databaser använder du `--database` växeln och listar databas namnen, avgränsade med blank steg:

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>Återställa databasen eller tabellerna

Följande steg visar hur du återställer din databas eller dina tabeller. När du har skapat säkerhets kopierings filen kan du återställa tabellerna eller databaserna med hjälp av MySQL-verktyget. Kör följande kommando:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Här är ett exempel som återställer `testdb` från en säkerhets kopia som skapats med mysqldump: 

> [!Important]
> - För en enskild server använder du det format `admin-user@servername` som ska ersättas `myserveradmin` i följande kommando.
> - För flexibel Server använder du det format ```admin-user``` som ska ersättas `myserveradmin` i följande kommando. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Nästa steg
Om föregående steg inte löser problemet kan du alltid återställa hela servern:
- [Återställa servern i Azure Database for MySQL-enskild server](howto-restore-server-portal.md)
- [Återställa servern i Azure Database for MySQL-flexibel Server](flexible-server/how-to-restore-server-portal.md)



