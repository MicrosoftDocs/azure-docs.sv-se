---
title: Skapa databaser och användare – Azure Database for MySQL
description: Den här artikeln beskriver hur du skapar nya användar konton för att interagera med en Azure Database for MySQL-server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: 9a6346a2b62c81dd74cf0ebe9a85df12d3488679
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251258"
---
# <a name="create-databases-and-users-in-azure-database-for-mysql"></a>Skapa databaser och användare i Azure Database for MySQL

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

I den här artikeln beskrivs hur du skapar användare i Azure Database for MySQL.

> [!NOTE]
> Den här artikeln innehåller referenser till termen _slav_, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.
>

När du först skapade Azure Database for MySQL-servern angav du ett användar namn och lösen ord för Server administratören. Mer information finns i den här [snabb](quickstart-create-mysql-server-database-using-azure-portal.md)starten. Du kan kontrol lera ditt användar namn för Server administratören i Azure Portal.

Server administratörs användaren har följande behörigheter:

   VÄLJ, INFOGA, UPPDATERA, TA BORT, SKAPA, SLÄPPA, LÄSA IN, BEARBETA, REFERERA, INDEXERA, ÄNDRA, VISA DATABASER, SKAPA TEMPORÄRA TABELLER, LÅS TABELLER, KÖRA, REPLIKERING SLAVAR, REPLIKERINGSPARTNER, SKAPA, VISA, VISA, SKAPA RUTIN, ÄNDRA RUTIN, SKAPA ANVÄNDARE, HÄNDELSE, UTLÖSARE

När du har skapat en Azure Database for MySQL-server kan du använda det första server administratörs kontot för att skapa fler användare och ge administratörs åtkomst till dem. Du kan också använda Server administratörs kontot för att skapa mindre privilegierade användare som har åtkomst till enskilda databas scheman.

> [!NOTE]
> Rollen SUPER Privilege och DBA stöds inte. Granska [privilegierna](concepts-limits.md#privileges--data-manipulation-support) i artikeln begränsningar för att förstå vad som inte stöds i tjänsten.
>
> Lösen ords-plugin-program som `validate_password` och `caching_sha2_password` stöds inte av tjänsten.

## <a name="to-create-a-database-with-a-non-admin-user-in-azure-database-for-mysql"></a>Så här skapar du en databas med en icke-administratörs användare i Azure Database for MySQL

1. Hämta anslutnings informationen och administratörs användar namnet.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta Server namnet och inloggnings informationen på sidan Server **Översikt** eller på sidan **Egenskaper** i Azure Portal.

2. Använd administratörs kontot och lösen ordet för att ansluta till din databas server. Använd önskat klient verktyg, till exempel MySQL Workbench, mysql.exe eller HeidiSQL.

   Om du inte är säker på hur du ansluter, se [Anslut och fråga efter data för en enskild server](./connect-workbench.md) eller [Anslut och fråga efter data för flexibel Server](./flexible-server/connect-workbench.md).

3. Redigera och kör följande SQL-kod. Ersätt placeholder-värdet `db_user` med ditt avsedda nya användar namn. Ersätt placeholder-värdet `testdb` med ditt databas namn.

   Den här SQL-koden skapar en ny databas med namnet testdb. Den skapar sedan en ny användare i MySQL-tjänsten och beviljar alla behörigheter för det nya databasschemat (testdb \* ) till den användaren.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Verifiera bidragen i databasen:

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Logga in på servern, ange den angivna databasen och Använd det nya användar namnet och lösen ordet. I det här exemplet visas mysql-kommandoraden. När du använder det här kommandot uppmanas du att ange användarens lösen ord. Använd ditt eget Server namn, databas namn och användar namn.

   ### <a name="single-server"></a>[Enskild server](#tab/single-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

   ### <a name="flexible-server"></a>[Flexibel Server](#tab/flexible-server)

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user -p
   ```
 ---

## <a name="to-create-more-admin-users-in-azure-database-for-mysql"></a>Så här skapar du fler administratörs användare i Azure Database for MySQL

1. Hämta anslutnings informationen och administratörs användar namnet.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta Server namnet och inloggnings informationen på sidan Server **Översikt** eller på sidan **Egenskaper** i Azure Portal.

2. Använd administratörs kontot och lösen ordet för att ansluta till din databas server. Använd önskat klient verktyg, till exempel MySQL Workbench, mysql.exe eller HeidiSQL.

   Om du inte är säker på hur du ansluter, se [Använd MySQL Workbench för att ansluta och fråga efter data](./connect-workbench.md).

3. Redigera och kör följande SQL-kod. Ersätt placeholder-värdet `new_master_user` med ditt nya användar namn. Den här syntaxen beviljar de angivna behörigheterna för alla databas scheman (*.*) till användaren ( `new_master_user` i det här exemplet).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Verifiera bidragen:

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="azure_superuser"></a>azure_superuser

Alla Azure Database for MySQL-servrar skapas med en användare som kallas "azure_superuser". Detta är ett system konto som har skapats av Microsoft för att hantera-servern för att utföra övervakning, säkerhets kopiering och annat regelbundet underhåll. On-Call-tekniker kan också använda det här kontot för att få åtkomst till servern under en incident med certifikatautentisering och måste begära åtkomst med JIT-processer (just-in-Time).

## <a name="next-steps"></a>Nästa steg

Öppna brand väggen för IP-adresserna för de nya användarnas datorer så att de kan ansluta:

* [Skapa och hantera brand Väggs regler på en enskild server](howto-manage-firewall-using-portal.md)
* [Skapa och hantera brand Väggs regler på en flexibel Server](flexible-server/how-to-connect-tls-ssl.md)

Mer information om hantering av användar konton finns i produkt dokumentationen för MySQL för [användar konto hantering](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [beviljande syntax](https://dev.mysql.com/doc/refman/5.7/en/grant.html)och [behörigheter](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).
