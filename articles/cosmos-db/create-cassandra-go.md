---
title: Bygg en go-app med Azure Cosmos DB API för Cassandra med gocql-klienten
description: Den här snabb starten visar hur du använder en Go-klient för att interagera med Azure Cosmos DB API för Cassandra
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: 595ec1aaa4aedc3916d1b4d46986dcabae887aaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93076410"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>Snabb start: Bygg en go-app med `gocql` klienten för att hantera Azure Cosmos DB API för Cassandra data
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning. I den här snabb starten börjar du med att skapa ett Azure Cosmos DB API för Cassandra-konto. Sedan kör du ett Go-program för att skapa ett Cassandra-tecken, en tabell och köra några åtgärder. Den här go-appen använder [gocql](https://github.com/gocql/gocql), som är en Cassandra-klient för go-språket. 

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu) utan en Azure-prenumeration.
- [Go](https://golang.org/) installerat på datorn och en fungerande kunskap om go.
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Innan du kan skapa en databas måste du skapa ett Cassandra-konto med Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Börja med att klona programmet från GitHub.

1. Öppna en kommando tolk och skapa en ny mapp med namnet `git-samples` .

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, till exempel git bash. Använd `cd` kommandot för att ändra till den nya mappen och installera exempel appen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du är intresse rad av att lära dig hur koden skapar databas resurserna kan du läsa följande kodfragment. Annars kan du gå vidare till [Kör programmet](#run-the-application)

`GetSession`Funktionen (del av `utils\utils.go` ) returnerar en [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session) som används för att köra kluster åtgärder som Infoga, hitta osv.

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

Den Azure Cosmos DB Cassandra-värden skickas till [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) funktionen för att hämta en [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) struktur som sedan har kon figurer ATS för att använda användar namn, lösen ord, port och lämplig TLS-version ([https/SSL/TLS-kryptering säkerhets krav](./database-security.md?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database))

`GetSession`Funktionen anropas sedan från `main` funktionen ( `main.go` ).

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

Anslutnings informationen och autentiseringsuppgifterna godkänns i form av miljövariabler (löst i `init` -metoden)

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

Den används sedan för att köra olika åtgärder (del av `operations\setup.go` ) på Azure Cosmos DB som börjar med `keyspace` och `table` skapar.

Som namnet antyder, `DropKeySpaceIfExists` släpps funktionen `keyspace` endast om den finns.

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

`CreateKeySpace` funktionen används för att skapa `keyspace` ( `user_profile` )

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

Detta följs av tabell skapande ( `user` ) som är i `CreateUserTable` funktion

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

När du har skapat ett blank steg och en tabell anropar vi CRUD-åtgärder (del av `operations\crud.go` ). 

`InsertUser` används för att skapa en `User` . Den anger användar information (ID, namn och ort) som fråge argumenten med hjälp av [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind)

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser` används för att söka efter en användare ( `model\user.go` ) med hjälp av ett specifikt användar-ID medan [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) användarens attribut (som returneras av Cassandra) binds till enskilda variabler ( `userid` , `name` , `city` ) – det är bara ett av de sätt som du kan använda för att få resultatet från Sök frågan

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers` används för att hämta alla användare. [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) används som en kort skrift för att hämta all användares information i form av en sektor av `map` s. Tänk på varje `map` som nyckel/värde-par där kolumn namn (till exempel `user_id` ) är nyckeln tillsammans med dess respektive värde.

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

Varje `map` användar information konverteras till en `User` användnings `mapToUser` funktion som bara extraherar värdet från dess respektive kolumn och använder den för att skapa en instans av `User` struct

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>Kör programmet

Som tidigare nämnts godkänner programmet anslutningar och autentiseringsuppgifter i form av miljövariablerna. 

1. Välj **anslutnings sträng** i Azure Cosmos DB-kontot i [Azure Portal](https://portal.azure.com/). 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="Visa och kopiera information från sidan anslutnings sträng i Azure Portal":::

Kopiera värdena för följande attribut ( `CONTACT POINT` , `PORT` `USERNAME` och `PRIMARY PASSWORD` ) och ange dem till respektive miljövariabler

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

Ändra till rätt mapp i terminalfönstret. Exempel:

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. Kör följande kommando i terminalen för att starta programmet.

```shell
go run main.go
```

3. Terminalfönstret visar meddelanden för de olika åtgärderna, inklusive återställning av disk utrymme och tabeller, användar skapande osv.

4. I **Datautforskaren** på Azure-portalen kan du fråga, ändra och arbeta med dessa nya data. 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="Visa data i Datautforskaren-Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB-konto med API för Cassandra och kör en go-app som skapar en Cassandra-databas och-behållare. Nu kan du importera ytterligare data till ditt Azure Cosmos DB-konto. 

> [!div class="nextstepaction"]
> [Importera Cassandra-data till Azure Cosmos DB](cassandra-import-data.md)