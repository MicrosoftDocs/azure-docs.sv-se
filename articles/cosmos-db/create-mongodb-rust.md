---
title: Anslut ett Rust-program till Azure Cosmos DB s API för MongoDB
description: Den här snabb starten visar hur du skapar ett Rust-program som backas upp av Azure Cosmos DBs API för MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/12/2021
ms.openlocfilehash: 91e7bafe98b1aceaf8fe27b07029291a48a31351
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555660"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Snabb start: ansluta ett Rust-program till Azure Cosmos DB s API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning. Exemplet som presenteras i den här artikeln är ett enkelt kommando rads baserat program som använder [Rust-drivrutinen för MongoDB](https://github.com/mongodb/mongo-rust-driver). Eftersom Azure Cosmos DBs API för MongoDB är [kompatibelt med MongoDB-kabel-protokollet](./mongodb-introduction.md#wire-protocol-compatibility)är det möjligt att en MongoDB klient driv rutin ansluter till den.

Du får lära dig hur du använder MongoDB Rust-drivrutinen för att interagera med Azure Cosmos DB s API för MongoDB genom att utforska CRUD (skapa, läsa, uppdatera, ta bort) åtgärder som implementerats i exempel koden. Slutligen kan du köra programmet lokalt för att se hur det fungerar.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med anslutnings strängen `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Rust](https://www.rust-lang.org/tools/install) (version 1,39 eller senare)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Konfigurera Azure Cosmos DB

Om du vill konfigurera ett Azure Cosmos DB konto följer du [instruktionerna här](create-mongodb-dotnet.md). Programmet kommer att behöva MongoDB-anslutningssträngen som du kan hämta med hjälp av Azure Portal. Mer information finns i [Hämta anslutnings strängen MongoDB för att anpassa](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize).

## <a name="run-the-application"></a>Kör programmet

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Kör följande kommandon för att klona exempellagringsplatsen.

1. Öppna en kommando tolk, skapa en ny mapp med namnet `git-samples` och stäng sedan kommando tolken.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>Skapa programmet

Så här skapar du en binärfil:

```bash
cargo build --release
```

### <a name="configure-the-application"></a>Konfigurera programmet 

Exportera anslutnings strängen, MongoDB-databasen och samlings namnen som miljövariabler. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> `ssl=true`Alternativet är viktigt på grund av Cosmos DB krav. Mer information finns i [krav på anslutnings sträng](connect-mongodb-account.md#connection-string-requirements).
>

För `MONGODB_URL` miljövariabeln ersätter du plats hållarna för `<COSMOSDB_ACCOUNT_NAME>` och `<COSMOSDB_PASSWORD>`

- `<COSMOSDB_ACCOUNT_NAME>`: Namnet på det Azure Cosmos DB konto som du har skapat
- `<COSMOSDB_PASSWORD>`: Databas nyckeln som extraherades i föregående steg

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

Du kan välja önskade värden för `MONGODB_DATABASE` och `MONGODB_COLLECTION` eller lämna dem som de är.

Om du vill köra programmet ändrar du till rätt mapp (där programmets binärfil finns):

```bash
cd target/release
```

För att skapa en `todo`

```bash
./todo create "Create an Azure Cosmos DB database account"
```

Om det lyckas bör du se utdata med MongoDB `_id` för det nyligen skapade dokumentet:

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

Skapa ett annat `todo`

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

Visa alla `todo` s

```bash
./todo list all
```

Du bör se de som du nyss lade till:

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

Om du vill uppdatera status för en `todo` (till exempel ändra till `completed` status) använder du `todo` ID: t:

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

Visa endast de slutförda `todo` s

```bash
./todo list completed
```

Du bör se den som du precis har uppdaterat

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

Ta bort ett `todo` using-ID

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

Lista `todo` s att bekräfta

```bash
./todo list all
```

Det `todo` du nyss tog bort bör inte finnas.

### <a name="view-data-in-data-explorer"></a>Visa data i datautforskaren

Data som lagras i Azure Cosmos DB kan visas och fråga i Azure Portal.

Om du vill visa, fråga och arbeta med användardata som skapats i föregående steg, loggar du in på [Azure-portalen](https://portal.azure.com) i din webbläsare.

I den översta sökrutan anger du **Azure Cosmos DB**. När ditt Cosmos-kontoblad öppnas väljer du ditt Cosmos-konto. I det vänstra navigerings fönstret väljer du **datautforskaren**. Utöka din samling i samlings-fönstret så kan du visa dokumenten i samlingen, fråga data och skapa och köra lagrade procedurer, utlösare och UDF:er.

## <a name="review-the-code-optional"></a>Granska koden (valfritt)

Om du är intresse rad av att lära dig hur programmet fungerar kan du granska kodfragmenten i det här avsnittet. Följande kodfragment tas från `src/main.rs` filen.

`main`Funktionen är start punkten för `todo` programmet. URL: en för anslutning förväntas för Azure Cosmos DBens API för MongoDB som ska tillhandahållas av `MONGODB_URL` miljö variabeln. En ny instans av `TodoManager` skapas, följt av ett [ `match` uttryck](https://doc.rust-lang.org/book/ch06-02-match.html) som delegerar till lämplig `TodoManager` metod baserat på den åtgärd som valts av användaren `create` , `update` , `list` eller `delete` .

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager` är en `struct` som kapslar in en [MongoDB:: Sync:](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html):-samling. När du försöker instansiera en `TodoManager` med hjälp av `new` funktionen initierar den en anslutning till Azure Cosmos DB s API för MongoDB.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

Det viktigaste är `TodoManager` att ha metoder för att hantera `todo` s. Låt oss gå över dem en i taget.

`add_todo`Metoden tar med en `todo` Beskrivning från användaren och skapar en instans av `Todo` struct, som ser ut som nedan. [Serde](https://github.com/serde-rs/serde) Framework används för att mappa (serialisera/deserialisera) bson data till instanser av `Todo` structs. Observera hur fältattribut `serde` används för att anpassa serialzation-processen för serialisering. `todo_id`Fält i att göra är till exempel `struct` en `ObjectId` och lagras i MongoDB som `_id` .

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one) accepterar ett [dokument](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html) som representerar den `todo` information som ska läggas till. Observera att konverteringen från `Todo` till en `Document` är en två stegs process som uppnås med hjälp av en kombination av [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) och [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document).

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[Collection. find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find) används för att hämta *alla* `todo` eller filtrera dem baserat på den angivna användarens status ( `pending` eller `completed` ). Observera hur i `while` slingan `Document` som erhålls till följd av sökningen, konverteras till en `Todo` struct med [bson:: from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html). Detta är motsatsen till vad som skett i- `add_todo` metoden.

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

En `todo` status kan uppdateras (från `pending` till `completed` eller vice versa). `todo`Konverteras till en [bson:: OID:: ObjectID](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html) som sedan används av metoden[Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) för att hitta det dokument som behöver uppdateras.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

`todo`Det är enkelt att ta bort en i [Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one) metoden.


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB MongoDB API-konto med hjälp av Azure Cloud Shell och skapar och kör en Rust kommando rads app för att hantera `todo` s. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto.

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
