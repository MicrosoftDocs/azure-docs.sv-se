---
title: Skapa användare – Azure Database for MariaDB
description: I den här artikeln beskrivs hur du kan skapa nya användar konton för att interagera med en Azure Database for MariaDB-Server.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: 28ec060e95d09cb150fc699919dde6cc0e1eaf23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663714"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Skapa användare i Azure Database for MariaDB

I den här artikeln beskrivs hur du kan skapa användare i Azure Database for MariaDB.

När du först skapade din Azure Database for MariaDB angav du ett användar namn och lösen ord för Server Administratörs inloggning. För mer information, kan du följa [snabb](quickstart-create-mariadb-server-database-using-azure-portal.md)starten. Du kan hitta inloggnings användar namnet för Server administratören från Azure Portal.

> [!NOTE]
> Den här artikeln innehåller referenser till termen _slav_, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

Server administratörs användaren får vissa behörigheter för servern enligt listan: Välj, infoga, uppdatera, ta bort, skapa, ta bort, läsa in, bearbeta, REFERERA, INDEXERA, ändra, Visa databaser, skapa TEMPORÄRa tabeller, Lås tabeller, köra, replikering slav, REPLIKERINGSPARTNER, skapa vy, Visa, skapa rutin, ändra rutin, skapa användare, händelse, utlösare

När Azure Database for MariaDB-servern har skapats kan du använda det första server administratörs användar kontot för att skapa fler användare och ge administratörs åtkomst till dem. Server administratörs kontot kan också användas för att skapa mindre privilegierade användare som har åtkomst till enskilda databas scheman.

> [!NOTE]
> Superprivilegiumet och DBA-rollen stöds inte. Granska [privilegierna](concepts-limits.md#privileges--data-manipulation-support) i artikeln begränsningar för att förstå vad som inte stöds i tjänsten.<br><br>
> Lösen ords-plugin-program som "validate_password" och "caching_sha2_password" stöds inte av tjänsten.

## <a name="create-more-admin-users"></a>Skapa fler administratörs användare

1. Hämta anslutnings informationen och administratörs användar namnet.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta server namn och inloggnings information från sidan Server **Översikt** eller sidan **Egenskaper** i Azure Portal.

2. Använd administratörs kontot och lösen ordet för att ansluta till din databas server. Använd önskat klient verktyg, till exempel MySQL Workbench, mysql.exe, HeidiSQL eller andra.
   Om du är osäker på hur du ansluter, se [Använd MySQL Workbench för att ansluta och fråga efter data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt det nya användar namnet med plats hållarens värde `new_master_user` . Den här syntaxen beviljar de angivna behörigheterna för alla databas scheman (*.*) till användar namnet (new_master_user i det här exemplet). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Verifiera bidragen.

   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Skapa databasanvändare

1. Hämta anslutnings informationen och administratörs användar namnet.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta server namn och inloggnings information från sidan Server **Översikt** eller sidan **Egenskaper** i Azure Portal. 

2. Använd administratörs kontot och lösen ordet för att ansluta till din databas server. Använd önskat klient verktyg, till exempel MySQL Workbench, mysql.exe, HeidiSQL eller andra.
   Om du är osäker på hur du ansluter, se [Använd MySQL Workbench för att ansluta och fråga efter data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt placeholder-värdet `db_user` med ditt avsedda nya användar namn och plats hållarens värde `testdb` med ditt eget databas namn.

   Den här SQL-koden skapar en ny databas med namnet testdb i exempel syfte. Sedan skapas en ny användare i Azure Database for MariaDBs tjänsten och alla behörigheter beviljas till det nya databasschemat (testdb \* ) för den användaren. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Verifiera bidragen i databasen.

   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Logga in på servern och ange den angivna databasen med hjälp av det nya användar namnet och lösen ordet. I det här exemplet visas mysql-kommandoraden. Med det här kommandot uppmanas du att ange lösen ordet för användar namnet. Ersätt ditt eget Server namn, databas namn och användar namn.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

   Mer information om hantering av användar konton finns i MariaDB-dokumentationen för [användar konto hantering](https://mariadb.com/kb/en/library/user-account-management/), [beviljande av syntax](https://mariadb.com/kb/en/library/grant/)och [privilegier](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="azure_superuser"></a>azure_superuser

Alla Azure Database for MySQL-servrar skapas med en användare som kallas "azure_superuser". Detta är ett system konto som har skapats av Microsoft för att hantera-servern för att utföra övervakning, säkerhets kopiering och annat regelbundet underhåll. On-Call-tekniker kan också använda det här kontot för att få åtkomst till servern under en incident med certifikatautentisering och måste begära åtkomst med JIT-processer (just-in-Time).

## <a name="next-steps"></a>Nästa steg

Öppna brand väggen för IP-adresserna för de nya användarnas datorer så att de kan ansluta: [skapa och hantera Azure Database for MariaDB brand Väggs regler med hjälp av Azure Portal](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->
