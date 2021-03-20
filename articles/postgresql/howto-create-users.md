---
title: Skapa användare – Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs hur du kan skapa nya användar konton för att interagera med en Azure Database for PostgreSQL-enskild server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2019
ms.openlocfilehash: 1dbbdde03d1c24882be298d8c81362744debeecf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91704947"
---
# <a name="create-users-in-azure-database-for-postgresql---single-server"></a>Skapa användare i Azure Database for PostgreSQL-enskild server

I den här artikeln beskrivs hur du kan skapa användare i en Azure Database for PostgreSQL-Server.

Om du vill veta mer om hur du skapar och hanterar användare av Azure-prenumerationer och deras behörigheter kan du gå till [artikeln Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/built-in-roles.md) eller granska [hur du anpassar roller](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Serveradministratörskontot

När du först skapade din Azure Database for PostgreSQL angav du ett användar namn och lösen ord för Server administratören. För mer information, kan du följa [snabb](quickstart-create-server-database-portal.md) starten för att se steg för steg-metoden. Eftersom Server administratörens användar namn är ett anpassat namn, kan du hitta det valda Server administratörs användar namnet från Azure Portal.

Azure Database for PostgreSQL servern skapas med de tre definierade standard rollerna. Du kan se dessa roller genom att köra kommandot: `SELECT rolname FROM pg_roles;`

- azure_pg_admin
- azure_superuser
- din server administratörs användare

Din server administratörs användare är medlem i azure_pg_admins rollen. Men Server administratörs kontot ingår inte i azure_superusers rollen. Eftersom den här tjänsten är en hanterad PaaS-tjänst ingår endast Microsoft i superanvändarens roll.

PostgreSQL-motorn använder behörigheter för att kontrol lera åtkomsten till databas objekt, enligt beskrivningen i [produkt dokumentationen för postgresql](https://www.postgresql.org/docs/current/static/sql-createrole.html). I Azure Database for PostgreSQL beviljas Server administratörs användaren följande behörigheter: inloggning, superanvändare, Ärv, CREATEDB, CREATEROLE, noreplikering

Användar kontot för Server administratör kan användas för att skapa ytterligare användare och ge dessa användare till azure_pg_admin-rollen. Server administratörs kontot kan också användas för att skapa mindre privilegierade användare och roller som har åtkomst till enskilda databaser och scheman.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-postgresql"></a>Så här skapar du ytterligare administratörs användare i Azure Database for PostgreSQL

1. Hämta anslutnings informationen och administratörs användar namnet.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta server namn och inloggnings information från sidan Server **Översikt** eller sidan **Egenskaper** i Azure Portal.

2. Använd administratörs kontot och lösen ordet för att ansluta till din databas server. Använd önskat klient verktyg, till exempel pgAdmin eller psql.
   Om du är osäker på hur du ansluter, se [snabb](./quickstart-create-server-database-portal.md) starten

3. Redigera och kör följande SQL-kod. Ersätt det nya användar namnet för plats hållarens värde <new_user> och ersätt plats hållarens lösen ord med ditt eget starka lösen ord. 

   ```sql
   CREATE ROLE <new_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB CREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';

   GRANT azure_pg_admin TO <new_user>;
   ```

## <a name="how-to-create-database-users-in-azure-database-for-postgresql"></a>Så här skapar du databas användare i Azure Database for PostgreSQL

1. Hämta anslutnings informationen och administratörs användar namnet.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta server namn och inloggnings information från sidan Server **Översikt** eller sidan **Egenskaper** i Azure Portal.

2. Använd administratörs kontot och lösen ordet för att ansluta till din databas server. Använd önskat klient verktyg, till exempel pgAdmin eller psql.

3. Redigera och kör följande SQL-kod. Ersätt placeholder-värdet `<db_user>` med ditt avsedda nya användar namn och plats hållarens värde `<newdb>` med ditt eget databas namn. Ersätt plats hållarens lösen ord med ditt eget starka lösen ord.

   Den här SQL-koden skapar en ny databas med namnet testdb, till exempel. Sedan skapar den en ny användare i PostgreSQL-tjänsten och tilldelar Connect-behörigheter till den nya databasen för den användaren.

   ```sql
   CREATE DATABASE <newdb>;
   
   CREATE ROLE <db_user> WITH LOGIN NOSUPERUSER INHERIT CREATEDB NOCREATEROLE NOREPLICATION PASSWORD '<StrongPassword!>';
   
   GRANT CONNECT ON DATABASE <newdb> TO <db_user>;
   ```

4. Med ett administratörs konto kan du behöva ge ytterligare behörighet för att skydda objekten i databasen. Mer information om databas roller och behörigheter finns i [postgresql-dokumentationen](https://www.postgresql.org/docs/current/static/ddl-priv.html) . Exempel:

   ```sql
   GRANT ALL PRIVILEGES ON DATABASE <newdb> TO <db_user>;
   ```

5. Logga in på servern och ange den angivna databasen med hjälp av det nya användar namnet och lösen ordet. I det här exemplet visas kommando raden psql. Med det här kommandot uppmanas du att ange lösen ordet för användar namnet. Ersätt ditt eget Server namn, databas namn och användar namn.

   ```shell
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=db_user@mydemoserver --dbname=newdb
   ```

## <a name="next-steps"></a>Nästa steg

Öppna brand väggen för IP-adresserna för de nya användarnas datorer så att de kan ansluta: [skapa och hantera Azure Database for PostgreSQL brand Väggs regler med hjälp av Azure Portal](howto-manage-firewall-using-portal.md) eller [Azure CLI](howto-manage-firewall-using-cli.md).

Mer information om hantering av användar konton finns i produkt dokumentation för PostgreSQL för [databas roller och behörigheter](https://www.postgresql.org/docs/current/static/user-manag.html), [beviljande av syntax](https://www.postgresql.org/docs/current/static/sql-grant.html)och [behörigheter](https://www.postgresql.org/docs/current/static/ddl-priv.html).
