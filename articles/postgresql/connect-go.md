---
title: 'Snabb start: Anslut med go-Azure Database for PostgreSQL – enskild server'
description: I den här snabb starten finns ett exempel på en go-programmeringsspråk som du kan använda för att ansluta och fråga efter data från Azure Database for PostgreSQL-enskild server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: eb844790ac0ae97c281f6d2d7022bee559f545cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93331938"
---
# <a name="quickstart-use-go-language-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Snabb start: Använd go-språk för att ansluta och fråga efter data i Azure Database for PostgreSQL-enskild server

Den här snabbstarten visar hur du ansluter till en Azure Database för PostgreSQL med hjälp av kod som skrivits i språket [Go](https://golang.org/) (golang). Den visar hur du använder SQL-instruktioner för att fråga, infoga, uppdatera och ta bort data i databasen. Den här artikeln förutsätter att du är van att utveckla i Go, men saknar erfarenhet av Azure Database för PostgreSQL.

## <a name="prerequisites"></a>Förutsättningar
I den här snabbstarten används de resurser som skapades i någon av följande guider som utgångspunkt:
- [Skapa DB – Portal](quickstart-create-server-database-portal.md)
- [Skapa DB – Azure CLI](quickstart-create-server-database-azure-cli.md)

## <a name="install-go-and-pq-connector"></a>Installera Go och pq connector
Installera [Go](https://golang.org/doc/install) och [Pure Go Postgres-drivrutinen (pq)](https://github.com/lib/pq) på din egen dator. Följ instruktionerna för din plattform:

### <a name="windows"></a>Windows
1. [Ladda ned](https://golang.org/dl/) och installera Go för Microsoft Windows enligt [installationsanvisningarna](https://golang.org/doc/install).
2. Starta kommandotolken från Start-menyn.
3. Skapa en mapp för ditt projekt, till exempel `mkdir  %USERPROFILE%\go\src\postgresqlgo`.
4. Ändra katalogen till projektmappen, till exempel `cd %USERPROFILE%\go\src\postgresqlgo`.
5. Ange miljövariabeln för GOPATH så att den hänvisar till källkodskatalogen. `set GOPATH=%USERPROFILE%\go`.
6. Installera [Pure Go Postgres-drivrutinen (pq)](https://github.com/lib/pq) genom att köra `go get github.com/lib/pq` kommandot.

   Sammanfattningsvis ska du installera Go och sedan köra dessa kommandon i kommandotolken:
   ```cmd
   mkdir  %USERPROFILE%\go\src\postgresqlgo
   cd %USERPROFILE%\go\src\postgresqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/lib/pq
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Starta Bash-gränssnittet. 
2. Installera Go genom att köra `sudo apt-get install golang-go`.
3. Skapa en mapp för ditt projekt i arbetskatalogen, t.ex `mkdir -p ~/go/src/postgresqlgo/`.
4. ’Ändra katalogen till mappen, till exempel `cd ~/go/src/postgresqlgo/`.
5. Ange miljövariabeln GOPATH så att den hänvisar till en giltig källkatalog, exempelvis den aktuella hemkatalogens go-mapp. Kör `export GOPATH=~/go` i bash-gränssnittet för att lägga till go-katalogen som GOPATH för den aktuella shell-sessionen.
6. Installera [Pure Go Postgres-drivrutinen (pq)](https://github.com/lib/pq) genom att köra `go get github.com/lib/pq` kommandot.

   Sammanfattningsvis ska du köra dessa bash-kommandon:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

### <a name="apple-macos"></a>Apple macOS
1. Hämta och installera Go enligt [installationsinstruktionerna](https://golang.org/doc/install) för din plattform. 
2. Starta Bash-gränssnittet. 
3. Skapa en mapp för ditt projekt i arbetskatalogen, t.ex `mkdir -p ~/go/src/postgresqlgo/`.
4. ’Ändra katalogen till mappen, till exempel `cd ~/go/src/postgresqlgo/`.
5. Ange miljövariabeln GOPATH så att den hänvisar till en giltig källkatalog, exempelvis den aktuella hemkatalogens go-mapp. Kör `export GOPATH=~/go` i bash-gränssnittet för att lägga till go-katalogen som GOPATH för den aktuella shell-sessionen.
6. Installera [Pure Go Postgres-drivrutinen (pq)](https://github.com/lib/pq) genom att köra `go get github.com/lib/pq` kommandot.

   Sammanfattningsvis ska du installera Go och sedan köra dessa bash-kommandon:
   ```bash
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

## <a name="get-connection-information"></a>Hämta anslutningsinformation
Hämta den information som du behöver för att ansluta till Azure Database för PostgreSQL. Du behöver det fullständiga servernamnet och inloggningsuppgifter.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På den vänstra menyn i Azure Portal klickar du på **Alla resurser**. Sök sedan efter den server som du skapade (till exempel **mydemoserver**).
3. Klicka på servernamnet.
4. På serverpanelen **Översikt** antecknar du **Servernamn** och **Inloggningsnamn för serveradministratören**. Om du glömmer lösenordet kan du även återställa det på den här panelen.
 :::image type="content" source="./media/connect-go/1-connection-string.png" alt-text="Azure Database for PostgreSQL-servernamn":::

## <a name="build-and-run-go-code"></a>Skapa och köra Go-kod 
1. Om du vill skriva Golang-kod kan du använda en enkel textredigerare, som Anteckningar i Microsoft Windows, [vi](https://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5)eller [Nano](https://www.nano-editor.org/) i Ubuntu eller TextEdit i macOS. Om du föredrar en mer omfattande IDE (Interactive Development Environment) kan du prova [GoLand](https://www.jetbrains.com/go/) från JetBrains, [Visual Studio Code](https://code.visualstudio.com/) från Microsoft eller [Atom](https://atom.io/).
2. Klistra in Golang-koden från följande avsnitt i textfiler och spara filerna i en projektmapp med filtillägget \*.go, till exempel Windows-sökvägen `%USERPROFILE%\go\src\postgresqlgo\createtable.go` eller Linux-sökvägen `~/go/src/postgresqlgo/createtable.go`.
3. Leta upp konstanterna `HOST`, `DATABASE`, `USER` och `PASSWORD` i koden och ersätt exempelvärdena med dina egna värden.  
4. Starta kommandotolken eller bash-gränssnittet. Ändra katalog till din projektmapp. I Windows kan du till exempel använda `cd %USERPROFILE%\go\src\postgresqlgo\`. I Linux kan du använda `cd ~/go/src/postgresqlgo/`. Några av de IDE-miljöer som nämns erbjuder funktioner för felsökning och körning utan att kräva shell-kommandon.
5. Kör koden genom att skriva kommandot `go run createtable.go` för att kompilera programmet och köra det. 
6. Alternativt bygger du koden till ett internt program, `go build createtable.go`, och kör sedan `createtable.exe` för att köra programmet.

## <a name="connect-and-create-a-table"></a>Ansluta och skapa en tabell
Använd följande kod för att ansluta och skapa en tabell med hjälp av **CREATE TABLE**-SQL-instruktionen följt av **INSERT INTO**-SQL-instruktioner för att lägga till rader i tabellen.

Koden importerar tre paket: [sql-paketet](https://golang.org/pkg/database/sql/), [pq-paketet](https://godoc.org/github.com/lib/pq) (som en drivrutin för att kommunicera med PostgreSQL-servern) och [fmt-paketet](https://golang.org/pkg/fmt/) för skrivna indata och utdata på kommandoraden.

Koden anropar metoden [sql.Open()](https://godoc.org/github.com/lib/pq#Open) för att ansluta till Azure Database for PostgreSQL-databasen. Anslutningen kontrolleras med hjälp av metoden [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). En [databasreferens](https://golang.org/pkg/database/sql/#DB) som håller anslutningspoolen för databasservern används genomgående. I koden anropas metoden [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) flera gånger för att köra flera SQL-kommandon. Varje gång körs en anpassad checkError()-metod för att kontrollera om ett fel har uppstått, och i så fall avslutas körningen.

Ersätt parametrarna `HOST`, `DATABASE`, `USER` och `PASSWORD` med egna värden. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

## <a name="read-data"></a>Läsa data
Använd följande kod för att ansluta och läsa data med en **SELECT**-SQL-instruktion. 

Koden importerar tre paket: [sql-paketet](https://golang.org/pkg/database/sql/), [pq-paketet](https://godoc.org/github.com/lib/pq) (som en drivrutin för att kommunicera med PostgreSQL-servern) och [fmt-paketet](https://golang.org/pkg/fmt/) för skrivna indata och utdata på kommandoraden.

Koden anropar metoden [sql.Open()](https://godoc.org/github.com/lib/pq#Open) för att ansluta till Azure Database for PostgreSQL-databasen. Anslutningen kontrolleras med hjälp av metoden [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). En [databasreferens](https://golang.org/pkg/database/sql/#DB) som håller anslutningspoolen för databasservern används genomgående. SELECT-frågan körs genom att anropa metoden [db.Query()](https://golang.org/pkg/database/sql/#DB.Query), och resulterande rader förvaras i en variabel av typen [rows](https://golang.org/pkg/database/sql/#Rows). Koden läser kolumndatavärden i den aktuella raden med metoden [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) och loopar igenom raderna med iteratorn [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) tills det inte finns fler rader. Varje rads kolumn värden skrivs ut till konsolen. Varje gång används en anpassad checkError ()-metod för att kontrol lera om ett fel har uppstått och panik avslutas om ett fel inträffar.

Ersätt parametrarna `HOST`, `DATABASE`, `USER` och `PASSWORD` med egna värden. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)
    defer rows.Close()

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

## <a name="update-data"></a>Uppdatera data
Använd följande kod för att ansluta och uppdatera data med SQL-instruktionen **UPDATE**.

Koden importerar tre paket: [sql-paketet](https://golang.org/pkg/database/sql/), [pq-paketet](https://godoc.org/github.com/lib/pq) (som en drivrutin för att kommunicera med Postgres-servern) och [fmt-paketet](https://golang.org/pkg/fmt/) för skrivna indata och utdata på kommandoraden.

Koden anropar metoden [sql.Open()](https://godoc.org/github.com/lib/pq#Open) för att ansluta till Azure Database for PostgreSQL-databasen. Anslutningen kontrolleras med hjälp av metoden [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). En [databasreferens](https://golang.org/pkg/database/sql/#DB) som håller anslutningspoolen för databasservern används genomgående. Koden anropar metoden [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) för att köra SQL-instruktionen som uppdaterar tabellen. Varje gång körs en anpassad checkError()-metod för att kontrollera om ett fel har uppstått, och i så fall avslutas körningen.

Ersätt parametrarna `HOST`, `DATABASE`, `USER` och `PASSWORD` med egna värden. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

## <a name="delete-data"></a>Ta bort data
Använd följande kod för att ansluta och läsa data med en **DELETE**-SQL-instruktion. 

Koden importerar tre paket: [sql-paketet](https://golang.org/pkg/database/sql/), [pq-paketet](https://godoc.org/github.com/lib/pq) (som en drivrutin för att kommunicera med Postgres-servern) och [fmt-paketet](https://golang.org/pkg/fmt/) för skrivna indata och utdata på kommandoraden.

Koden anropar metoden [sql.Open()](https://godoc.org/github.com/lib/pq#Open) för att ansluta till Azure Database for PostgreSQL-databasen. Anslutningen kontrolleras med hjälp av metoden [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping). En [databasreferens](https://golang.org/pkg/database/sql/#DB) som håller anslutningspoolen för databasservern används genomgående. Koden anropar metoden [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) för att köra SQL-instruktionen som tar bort en rad från tabellen. Varje gång körs en anpassad checkError()-metod för att kontrollera om ett fel har uppstått, och i så fall avslutas körningen.

Ersätt parametrarna `HOST`, `DATABASE`, `USER` och `PASSWORD` med egna värden. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
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
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
