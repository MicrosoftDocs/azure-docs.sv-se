---
title: 'Snabb start: Anslut med go-Azure Database for MySQL'
description: I den här snabbstarten finns flera kodexempel i Go som du kan använda för att ansluta till och fråga efter data från Azure Database för MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 0da32fb404972556a1c4c964eec692bad20a4db1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94535766"
---
# <a name="quickstart-use-go-language-to-connect-and-query-data-in-azure-database-for-mysql"></a>Snabb start: Använd go-språk för att ansluta och fråga efter data i Azure Database for MySQL

Den här snabbstarten visar hur du ansluter till en Azure Database for MySQL från Windows, Ubuntu Linux och Apple macOS-plattformar med hjälp av kod som har skrivits på språket [Go](https://golang.org/). Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. Det här avsnittet förutsätter att du är van att utveckla i Go och att du saknar erfarenhet av Azure Database for MySQL.

## <a name="prerequisites"></a>Förutsättningar
I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa en Azure Database för MySQL med Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Skapa en Azure Database för MySQL-server med Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> Se till att den IP-adress som du ansluter från har lagts till i serverns brand Väggs regler med hjälp av [Azure Portal](./howto-manage-firewall-using-portal.md) eller [Azure CLI](./howto-manage-firewall-using-cli.md)

## <a name="install-go-and-mysql-connector"></a>Installera en anslutningsapp för Go och MySQL
Installera [Go](https://golang.org/doc/install) och [go-sql-driver for MySQL](https://github.com/go-sql-driver/mysql#installation) på din egen dator. Följ instruktionerna för din plattform:

### <a name="windows"></a>Windows
1. [Ladda ned](https://golang.org/dl/) och installera Go för Microsoft Windows enligt [installationsanvisningarna](https://golang.org/doc/install).
2. Starta kommandotolken från Start-menyn.
3. Skapa en mapp för ditt projekt, till exempel. `mkdir  %USERPROFILE%\go\src\mysqlgo`.
4. Ändra katalogen till projektmappen, till exempel `cd %USERPROFILE%\go\src\mysqlgo`.
5. Ange miljövariabeln för GOPATH så att den hänvisar till källkodskatalogen. `set GOPATH=%USERPROFILE%\go`.
6. Installera [go-sql-driver for mysql](https://github.com/go-sql-driver/mysql#installation) genom att köra kommandot `go get github.com/go-sql-driver/mysql`.

   Sammanfattningsvis ska du installera Go och sedan köra dessa kommandon i kommandotolken:
   ```cmd
   mkdir  %USERPROFILE%\go\src\mysqlgo
   cd %USERPROFILE%\go\src\mysqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/go-sql-driver/mysql
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Starta Bash-gränssnittet. 
2. Installera Go genom att köra `sudo apt-get install golang-go`.
3. Skapa en mapp för ditt projekt i arbetskatalogen, t.ex `mkdir -p ~/go/src/mysqlgo/`.
4. ’Ändra katalogen till mappen, till exempel `cd ~/go/src/mysqlgo/`.
5. Ange miljövariabeln GOPATH så att den hänvisar till en giltig källkatalog, exempelvis den aktuella hemkatalogens go-mapp. Kör `export GOPATH=~/go` i Bash-gränssnittet och lägg till go-katalogen som GOPATH för den aktuella shell-sessionen.
6. Installera [go-sql-driver for mysql](https://github.com/go-sql-driver/mysql#installation) genom att köra kommandot `go get github.com/go-sql-driver/mysql`.

   Sammanfattningsvis ska du köra dessa bash-kommandon:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

### <a name="apple-macos"></a>Apple macOS
1. Hämta och installera go enligt [installationsinstruktionerna](https://golang.org/doc/install) som matchar din plattform. 
2. Starta Bash-gränssnittet.
3. Skapa en mapp för ditt projekt i arbetskatalogen, t.ex `mkdir -p ~/go/src/mysqlgo/`.
4. ’Ändra katalogen till mappen, till exempel `cd ~/go/src/mysqlgo/`.
5. Ange miljövariabeln GOPATH så att den hänvisar till en giltig källkatalog, exempelvis den aktuella hemkatalogens go-mapp. Kör `export GOPATH=~/go` i Bash-gränssnittet och lägg till go-katalogen som GOPATH för den aktuella shell-sessionen.
6. Installera [go-sql-driver for mysql](https://github.com/go-sql-driver/mysql#installation) genom att köra kommandot `go get github.com/go-sql-driver/mysql`.

   Sammanfattningsvis ska du installera Go och sedan köra dessa bash-kommandon:
   ```bash
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Skaffa den information som du behöver för att ansluta till Azure Database för MySQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal klickar du på **Alla resurser**. Sök sedan efter den server som du skapade (till exempel **mydemoserver**).
3. Klicka på servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 :::image type="content" source="./media/connect-go/1_server-overview-name-login.png" alt-text="Azure Database för MySQL-servernamn":::
   

## <a name="build-and-run-go-code"></a>Skapa och köra Go-kod 
1. Om du vill skriva Golang-kod kan du använda en enkel textredigerare, som Anteckningar i Microsoft Windows, [vi](https://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5)eller [Nano](https://www.nano-editor.org/) i Ubuntu eller TextEdit i macOS. Om du föredrar en mer omfattande IDE (Interactive Development Environment) kan du prova [Gogland](https://www.jetbrains.com/go/) från JetBrains, [Visual Studio Code](https://code.visualstudio.com/) från Microsoft eller [Atom](https://atom.io/).
2. Klistra in Go-koden från avsnitten nedan i textfilerna och spara dem sedan i en projektmapp med filtillägget \*.go (till exempel Windows-sökvägen `%USERPROFILE%\go\src\mysqlgo\createtable.go` eller Linux-sökvägen `~/go/src/mysqlgo/createtable.go`).
3. Leta upp konstanterna `HOST`, `DATABASE`, `USER` och `PASSWORD` i koden och ersätt sedan exempelvärdena med dina egna värden. 
4. Starta kommandotolken eller Bash-gränssnittet. Ändra katalog till din projektmapp. I Windows kan du till exempel använda `cd %USERPROFILE%\go\src\mysqlgo\`. I Linux kan du använda `cd ~/go/src/mysqlgo/`.  Några av de IDE-redigerare som nämns erbjuder funktioner för felsökning och körning utan att kräva shell-kommandon.
5. Kör koden genom att skriva kommandot `go run createtable.go` för att kompilera programmet och köra det. 
6. Alternativt bygger du koden till ett internt program, `go build createtable.go`, och kör sedan `createtable.exe` för att köra programmet.

## <a name="connect-create-table-and-insert-data"></a>Ansluta, skapa tabell och infoga data
Använd följande kod för att ansluta till servern, skapa en tabell och läsa in data med hjälp av SQL-instruktionen **INSERT**. 

Koden importerar tre paket: [sql-paketet](https://golang.org/pkg/database/sql/), [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) som en drivrutin för att kommunicera med Azure Database för MySQL och [fmt-paketet](https://golang.org/pkg/fmt/) för skrivna indata och utdata på kommandoraden.

I koden anropas metoden [sql.Open()](http://go-database-sql.org/accessing.html) för att ansluta till Azure Database for MySQL, och anslutningen kontrolleras med hjälp av metoden [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). En [databasreferens](https://golang.org/pkg/database/sql/#DB) som håller anslutningspoolen för databasservern används genomgående. I koden anropas metoden [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) flera gånger för att köra flera DDL-kommandon. I koden används också [Prepare()](http://go-database-sql.org/prepared.html) och Exec() som kör förberedda instruktioner med olika parametrar för att infoga tre rader. Varje gång används en anpassad checkError()-metod för att kontrollera om ett fel har uppstått, och i så fall avslutas körningen direkt.

Ersätt konstanterna `host`, `database`, `user` och `password` med egna värden. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed).")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table.")

    // Insert some data into table.
    sqlStatement, err := db.Prepare("INSERT INTO inventory (name, quantity) VALUES (?, ?);")
    res, err := sqlStatement.Exec("banana", 150)
    checkError(err)
    rowCount, err := res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("orange", 154)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("apple", 100)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}

```

## <a name="read-data"></a>Läsa data
Använd följande kod för att ansluta och läsa data med en **SELECT**-SQL-instruktion. 

Koden importerar tre paket: [sql-paketet](https://golang.org/pkg/database/sql/), [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) som en drivrutin för att kommunicera med Azure Database för MySQL och [fmt-paketet](https://golang.org/pkg/fmt/) för skrivna indata och utdata på kommandoraden.

I koden anropas metoden [sql.Open()](http://go-database-sql.org/accessing.html) för att ansluta till Azure Database för MySQL. Anslutningen kontrolleras med hjälp av metoden [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). En [databasreferens](https://golang.org/pkg/database/sql/#DB) som håller anslutningspoolen för databasservern används genomgående. Koden anropar [Query()](https://golang.org/pkg/database/sql/#DB.Query)-metoden för att köra det valda kommandot. Sedan kör den [Next()](https://golang.org/pkg/database/sql/#Rows.Next) att gå igenom resultatuppsättningen och [Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) för att parsa kolumnvärdena och sparar värdet i variabler. Varje gång använda en anpassad checkError()-metod för att kontrollera om ett fel har uppstått, och i så fall avslutas körningen direkt.

Ersätt konstanterna `host`, `database`, `user` och `password` med egna värden. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Variables for printing column data when scanned.
    var (
        id       int
        name     string
        quantity int
    )

    // Read some data from the table.
    rows, err := db.Query("SELECT id, name, quantity from inventory;")
    checkError(err)
    defer rows.Close()
    fmt.Println("Reading data:")
    for rows.Next() {
        err := rows.Scan(&id, &name, &quantity)
        checkError(err)
        fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
    }
    err = rows.Err()
    checkError(err)
    fmt.Println("Done.")
}
```

## <a name="update-data"></a>Uppdatera data
Använd följande kod för att ansluta och uppdatera data med en **UPDATE**-SQL-instruktion. 

Koden importerar tre paket: [sql-paketet](https://golang.org/pkg/database/sql/), [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) som en drivrutin för att kommunicera med Azure Database för MySQL och [fmt-paketet](https://golang.org/pkg/fmt/) för skrivna indata och utdata på kommandoraden.

I koden anropas metoden [sql.Open()](http://go-database-sql.org/accessing.html) för att ansluta till Azure Database för MySQL. Anslutningen kontrolleras med hjälp av metoden [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). En [databasreferens](https://golang.org/pkg/database/sql/#DB) som håller anslutningspoolen för databasservern används genomgående. Koden anropar [Exec()](https://golang.org/pkg/database/sql/#DB.Exec)-metoden för att köra uppdateringskommandot. Varje gång använda en anpassad checkError()-metod för att kontrollera om ett fel har uppstått, och i så fall avslutas körningen direkt.

Ersätt konstanterna `host`, `database`, `user` och `password` med egna värden. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("UPDATE inventory SET quantity = ? WHERE name = ?", 200, "banana")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Updated %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
```

## <a name="delete-data"></a>Ta bort data
Använd följande kod för att ansluta och ta bort data med en **DELETE**-SQL-instruktion. 

Koden importerar tre paket: [sql-paketet](https://golang.org/pkg/database/sql/), [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) som en drivrutin för att kommunicera med Azure Database för MySQL och [fmt-paketet](https://golang.org/pkg/fmt/) för skrivna indata och utdata på kommandoraden.

I koden anropas metoden [sql.Open()](http://go-database-sql.org/accessing.html) för att ansluta till Azure Database för MySQL. Anslutningen kontrolleras med hjälp av metoden [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). En [databasreferens](https://golang.org/pkg/database/sql/#DB) som håller anslutningspoolen för databasservern används genomgående. Koden anropar den [Exec()](https://golang.org/pkg/database/sql/#DB.Exec)-metod som används för att köra borttagningskommandot. Varje gång använda en anpassad checkError()-metod för att kontrollera om ett fel har uppstått, och i så fall avslutas körningen direkt.

Ersätt konstanterna `host`, `database`, `user` och `password` med egna värden. 

```Go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "mydemoserver.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@mydemoserver"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("DELETE FROM inventory WHERE name = ?", "orange")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Deleted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
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
> [Migrera din databas med Exportera och importera](./concepts-migrate-import-export.md)
