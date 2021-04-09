---
title: 'Snabb start: Anslut till ruby – Azure Database for PostgreSQL-enskild server'
description: Den här snabb starten innehåller ett ruby-kod exempel som du kan använda för att ansluta och fråga efter data från Azure Database for PostgreSQL-enskild server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 20e45454284af230da74896d5b3f5e9da676dbb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831711"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Snabb start: Använd ruby för att ansluta och fråga efter data i Azure Database for PostgreSQL-enskild server

Den här snabbstarten visar hur du ansluter till en Azure Database för PostgreSQL med hjälp av ett [Ruby](https://www.ruby-lang.org)-program. Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. I den här artikeln förutsätter vi att du har kunskaper om Ruby och att du inte har arbetat med Azure Database for PostgreSQL tidigare.

## <a name="prerequisites"></a>Förutsättningar
I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa DB – Portal](quickstart-create-server-database-portal.md)
- [Skapa DB – Azure CLI](quickstart-create-server-database-azure-cli.md)

Du måste också ha installerat:
- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Ruby PG](https://rubygems.org/gems/pg/), postgresql-modulen för ruby

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Hämta den information som du behöver för att ansluta till Azure Database för PostgreSQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal klickar du på **Alla resurser**. Sök sedan efter den server som du skapade (till exempel **mydemoserver**).
3. Klicka på servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 :::image type="content" source="./media/connect-ruby/1-connection-string.png" alt-text="Azure Database for PostgreSQL-servernamn":::

> [!NOTE]
> `@`Symbolen i Azure postgres-användarnamnet har varit URL-kodad som `%40` i alla anslutnings strängar.

## <a name="connect-and-create-a-table"></a>Ansluta och skapa en tabell
Använd följande kod för att ansluta och skapa en tabell med hjälp av **CREATE TABLE**-SQL-instruktionen följt av **INSERT INTO**-SQL-instruktioner för att lägga till rader i tabellen.

I koden används ett ```PG::Connection``` objekt med en konstruktor ```new``` för att ansluta till Azure Database for PostgreSQL. Sedan anropas metoden ```exec()``` för att köra kommandona Drop, CREATE TABLE och INSERT INTO. Koden söker efter fel med hjälp av ```PG::Error``` klassen. Sedan anropas metoden ```close()``` för att stänga anslutningen innan den avslutas. Mer information om dessa klasser och metoder finns i ruby PG Reference-dokumentation.

Ersätt strängarna `host`, `database`, `user` och `password` med egna värden.


```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="read-data"></a>Läsa data
Använd följande kod för att ansluta och läsa data med en **SELECT**-SQL-instruktion.

I koden används ett  ```PG::Connection``` objekt med en konstruktor ```new``` för att ansluta till Azure Database for PostgreSQL. Sedan anropar den metoden ```exec()``` för att köra kommandot SELECT och behåller resultatet i en resultat uppsättning. Resultatuppsättningen itereras igenom med loopen `resultSet.each do` som sparar de aktuella radvärdena i variabeln `row`. Koden söker efter fel med hjälp av ```PG::Error``` klassen. Sedan anropas metoden ```close()``` för att stänga anslutningen innan den avslutas. Mer information om dessa klasser och metoder finns i ruby PG Reference-dokumentation.

Ersätt strängarna `host`, `database`, `user` och `password` med egna värden.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="update-data"></a>Uppdatera data
Använd följande kod för att ansluta och uppdatera data med en **UPDATE**-SQL-instruktion.

I koden används ett  ```PG::Connection``` objekt med en konstruktor ```new``` för att ansluta till Azure Database for PostgreSQL. Sedan anropas metoden ```exec()``` för att köra kommandot Update. Koden söker efter fel med hjälp av ```PG::Error``` klassen. Sedan anropas metoden ```close()``` för att stänga anslutningen innan den avslutas. Mer information om dessa klasser och metoder finns i [ruby PG Reference-dokumentation](https://rubygems.org/gems/pg) .

Ersätt strängarna `host`, `database`, `user` och `password` med egna värden.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>Ta bort data
Använd följande kod för att ansluta och läsa data med en **DELETE**-SQL-instruktion.

I koden används ett  ```PG::Connection``` objekt med en konstruktor ```new``` för att ansluta till Azure Database for PostgreSQL. Sedan anropas metoden ```exec()``` för att köra kommandot Update. Koden söker efter fel med hjälp av ```PG::Error``` klassen. Sedan anropas metoden ```close()``` för att stänga anslutningen innan den avslutas.

Ersätt strängarna `host`, `database`, `user` och `password` med egna värden.

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message

ensure
    connection.close if connection
end
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa alla resurser som används under den här snabb starten tar du bort resurs gruppen med hjälp av följande kommando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md) <br/>
> [!div class="nextstepaction"]
> [Referens dokumentation för ruby PG](https://rubygems.org/gems/pg)
