---
title: 'Snabb start: Anslut med ruby-Azure Database for MySQL'
description: I den här snabbstarten finns ett kodexempel i Ruby som du kan använda för att ansluta till och fråga efter data från Azure Database för MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 4eba3fabee50e0011d5a63297c726a9647dd84c0
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831541"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-mysql"></a>Snabb start: Använd ruby för att ansluta och fråga efter data i Azure Database for MySQL

Den här snabbstarten visar hur du ansluter till en Azure Database för MySQL med hjälp av ett [Ruby](https://www.ruby-lang.org)-program och en [mysql2](https://rubygems.org/gems/mysql2)-gem från plattformar med Windows, Ubuntu Linux och Mac. Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. Det här avsnittet förutsätter att du är van att utveckla i Ruby och att du saknar erfarenhet av Azure Database for MySQL.

## <a name="prerequisites"></a>Förutsättningar

I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa en Azure Database för MySQL med Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT]
> Se till att den IP-adress som du ansluter från har lagts till i serverns brand Väggs regler med hjälp av [Azure Portal](./howto-manage-firewall-using-portal.md) eller [Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="install-ruby"></a>Installera Ruby
Installera Ruby, Gem och MySQL2-biblioteket på din egen dator.

### <a name="windows"></a>Windows
1. Hämta och installera den version 2.3 av [Ruby](https://rubyinstaller.org/downloads/).
2. Starta en ny kommandotolk (cmd) från Start-menyn.
3. Ändra katalog till katalogen Ruby för version 2.3. `cd c:\Ruby23-x64\bin`
4. Kontrollera Ruby-installationen genom att köra kommandot `ruby -v` och se den installerade versionen.
5. Kontrollera Gem-installationen genom att köra kommandot `gem -v` och se den installerade versionen.
6. Skapa Mysql2-modulen för Ruby med Gem genom att köra kommandot `gem install mysql2`.

### <a name="macos"></a>macOS
1. Installera Ruby med Homebrew genom att köra kommandot `brew install ruby`. Fler installationsalternativ finns i [installationsdokumentationen](https://www.ruby-lang.org/en/documentation/installation/#homebrew) för Ruby.
2. Kontrollera Ruby-installationen genom att köra kommandot `ruby -v` och se den installerade versionen.
3. Kontrollera Gem-installationen genom att köra kommandot `gem -v` och se den installerade versionen.
4. Skapa Mysql2-modulen för Ruby med Gem genom att köra kommandot `gem install mysql2`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Installera Ruby genom att köra kommandot `sudo apt-get install ruby-full`. Fler installationsalternativ finns i [installationsdokumentationen](https://www.ruby-lang.org/en/documentation/installation/) för Ruby.
2. Kontrollera Ruby-installationen genom att köra kommandot `ruby -v` och se den installerade versionen.
3. Installera de senaste uppdateringarna för Gem genom att köra kommandot `sudo gem update --system`.
4. Kontrollera Gem-installationen genom att köra kommandot `gem -v` och se den installerade versionen.
5. Installera gcc, make och andra genereringsverktyg genom att köra kommandot `sudo apt-get install build-essential`.
6. Installera MySQL-klientbibliotek för utvecklare genom att köra kommandot `sudo apt-get install libmysqlclient-dev`.
7. Skapa mysql2-modulen för Ruby med Gem genom att köra kommandot `sudo gem install mysql2`.

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Skaffa den information som du behöver för att ansluta till Azure Database för MySQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal klickar du på **Alla resurser**. Sök sedan efter den server som du skapade (till exempel **mydemoserver**).
3. Klicka på servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 :::image type="content" source="./media/connect-ruby/1_server-overview-name-login.png" alt-text="Azure Database för MySQL-servernamn":::

## <a name="run-ruby-code"></a>Köra Ruby-kod
1. Klistra in Ruby-koden nedan i textfiler och spara sedan filerna till en projektmapp med filtillägget .rb (till exempel `C:\rubymysql\createtable.rb` eller `/home/username/rubymysql/createtable.rb`).
2. För att köra koden startar du kommandotolken eller bash-gränssnittet. Ändra katalog till din projektmapp, till exempel `cd rubymysql`
3. Skriv Ruby-kommandot följt av filnamnet, till exempel `ruby createtable.rb`, för att köra programmet.
4. I Windows, om Ruby-programmet inte finns på sökvägen för miljövariabeln, kan du behöva använda den fullständiga sökvägen för att starta nodprogrammet, till exempel `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>Ansluta och skapa en tabell
Använd följande kod för att ansluta och skapa en tabell med hjälp av **CREATE TABLE**-SQL-instruktionen följt av **INSERT INTO**-SQL-instruktioner för att lägga till rader i tabellen.

Koden använder en mysql2:: client-klass för att ansluta till MySQL-servern. Sedan anropas metoden ```query()``` för att köra kommandona Drop, CREATE TABLE och INSERT INTO. Anropa slutligen ```close()``` för att stänga anslutningen innan du avslutar.

Ersätt strängarna `host`, `database`, `username` och `password` med egna värden.
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Läsa data
Använd följande kod för att ansluta och läsa data med en **SELECT**-SQL-instruktion.

Koden använder en mysql2:: client-klass för att ansluta till Azure Database for MySQL med- ```new()``` metoden. Sedan anropas metoden ```query()``` för att köra SELECT-kommandona. Sedan anropas metoden ```close()``` för att stänga anslutningen innan den avslutas.

Ersätt strängarna `host`, `database`, `username` och `password` med egna värden.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Uppdatera data
Använd följande kod för att ansluta och uppdatera data med en **UPDATE**-SQL-instruktion.

Koden använder en [mysql2::client](https://rubygems.org/gems/mysql2-client-general_log)-klass .new() metod för att ansluta till Azure-databas för MySQL. Sedan anropas metoden ```query()```  för att köra uppdaterings kommandona. Sedan anropas metoden ```close()``` för att stänga anslutningen innan den avslutas.

Ersätt strängarna `host`, `database`, `username` och `password` med egna värden.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Ta bort data
Använd följande kod för att ansluta och läsa data med en **DELETE**-SQL-instruktion.

Koden använder en [mysql2:: client](https://rubygems.org/gems/mysql2/) -klass för att ansluta till MySQL-servern, kör kommandot DELETE och stäng sedan anslutningen till servern.

Ersätt strängarna `host`, `database`, `username` och `password` med egna värden.

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
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
> [Migrera din databas med Exportera och importera](./concepts-migrate-import-export.md) <br/>

> [!div class="nextstepaction"]
> [Läs mer om MySQL2-klienten](https://rubygems.org/gems/mysql2-client-general_log) <br/>

