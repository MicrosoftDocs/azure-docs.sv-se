---
title: 'Snabb start: Anslut med Rust-Azure Database for PostgreSQL-Single Server'
description: Den här snabb starten innehåller Rust kod exempel som du kan använda för att ansluta och fråga efter data från Azure Database for PostgreSQL-enskild server.
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.openlocfilehash: 00adc50ac14e627eb356a3e62ce0faa5a9716aa3
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505860"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Snabb start: Använd Rust för att ansluta och fråga efter data i Azure Database for PostgreSQL-enskild server

I den här artikeln får du lära dig hur du använder [postgresql-drivrutinen för Rust](https://github.com/sfackler/rust-postgres) för att interagera med Azure Database for PostgreSQL genom att utforska CRUD (skapa, läsa, uppdatera, ta bort) åtgärder som implementerats i exempel koden. Slutligen kan du köra programmet lokalt för att se hur det fungerar.

## <a name="prerequisites"></a>Förutsättningar
För den här snabb starten behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free).
- En nyare version av [Rust](https://www.rust-lang.org/tools/install) är installerad.
- En Azure Database for PostgreSQL enskild server – skapa en med hjälp av [Azure Portal](./quickstart-create-server-database-portal.md) <br/> eller [Azure CLI](./quickstart-create-server-database-azure-cli.md).
- Baserat på om du använder offentlig eller privat åtkomst utför du **en** av åtgärderna nedan för att aktivera anslutningen.

  |Action| Anslutningsmetod|Instruktionsguide|
  |:--------- |:--------- |:--------- |
  | **Konfigurera brandväggsregler** | Offentliga | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Konfigurera tjänstens slut punkt** | Offentliga | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Konfigurera privat länk** | Privat | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Git](https://git-scm.com/downloads) installerat.

## <a name="get-database-connection-information"></a>Hämta information om databas anslutning
Att ansluta till en Azure Database for PostgreSQL-databas kräver det fullständigt kvalificerade Server namnet och inloggnings uppgifterna. Du kan hämta den här informationen från Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)söker du efter och väljer Azure Database for postgresql server namnet.
1. På sidan **Översikt** för servern kopierar du det fullständigt kvalificerade **Server namnet** och **administratörens användar namn**. Det fullständigt kvalificerade **Server namnet** är alltid av formatet *\<my-server-name> . postgres.Database.Azure.com* och **administratörens användar namn** är alltid i formatet *\<my-admin-username>@\<my-server-name>* .

## <a name="review-the-code-optional"></a>Granska koden (valfritt)

Om du är intresse rad av att lära dig hur koden fungerar kan du läsa följande kodfragment. Annars kan du gå vidare till gå vidare och [köra programmet](#run-the-application).

### <a name="connect"></a>Ansluta

`main`Funktionen startar genom att ansluta till Azure Database for PostgreSQL och den är beroende av följande miljövariabler för anslutnings `POSTGRES_HOST` information `POSTGRES_USER` , `POSTGRES_PASSWORD` och `POSTGRES_DBNAME` . Som standard är PostgreSQL-databas tjänsten konfigurerad för att kräva `TLS` anslutning. Du kan välja att inaktivera kravet `TLS` om klient programmet inte stöder `TLS` anslutning. Mer information finns [i Konfigurera TLS-anslutningar i Azure Database for PostgreSQL-enskild server](./concepts-ssl-connection-security.md).

Exempel programmet i den här artikeln använder TLS med [postgres-openssl-häcken](https://crates.io/crates/postgres-openssl/). [postgres:: klient:: funktionen Connect](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) används för att initiera anslutningen och programmet avslutas om detta Miss lyckas.

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>Släpp och skapa tabell

Exempel programmet använder en enkel `inventory` tabell för att demonstrera åtgärderna CRUD (skapa, läsa, uppdatera, ta bort).

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

`drop_create_table`Funktionen försöker från början till `DROP` `inventory` tabellen innan du skapar en ny. Detta gör det enklare för inlärning/experiment, eftersom du alltid börjar med en känd (ren) status. Metoden [execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) används för att skapa och släppa-åtgärder. 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>Infoga data

`insert_data` lägger till poster i `inventory` tabellen. Den skapar ett för [berett uttryck](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html) med [prepisk](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare) funktion. 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

Observera också användningen av [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed) -metoden som tillåter att olika typer av frågeparametrar anges explicit.

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

Slutligen används en `for` slinga för att lägga till `item-3` `item-4` och, `item-5` med slumpmässigt genererad kvantitet för var och en.

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>Söka i data

`query_data` funktionen visar hur du hämtar data från `inventory` tabellen. Metoden [query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) används för att hämta ett objekt `id` . 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

Alla rader i inventerings tabellen hämtas med hjälp av en `select * from` fråga med [fråge](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query) metoden. De returnerade raderna upprepas för att extrahera värdet för varje kolumn med [Get](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get). 

> [!TIP]
> Observera hur `get` gör det möjligt att ange kolumnen med ett numeriskt index på raden eller med dess kolumn namn.

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>Uppdatera data

`update_date`Funktionen uppdaterar slumpmässigt antalet för alla objekt. Eftersom `insert_data` funktionen hade lagt till `5` rader, beaktas samma konto i `for` slingan- `for n in 1..=5`

> [!TIP]
> Observera att vi använder `query` i stället för `execute` eftersom vi planerar att komma tillbaka `id` och den nyligen genererade `quantity` (med [RETUR satsen](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>Ta bort data

Slutligen `delete` visar funktionen hur du tar bort ett objekt från `inventory` tabellen med dess `id` . `id`Väljs slumpmässigt – det är ett slumpmässigt heltal mellan `1` till `5` ( `5` inklusive) eftersom `insert_data` funktionen hade lagt till `5` rader att börja med. 

> [!TIP]
> Observera att vi använder `query` i stället för `execute` eftersom vi planerar att få tillbaka informationen om objektet som vi nyss tog bort (med hjälp av [RETUR satsen](https://www.postgresql.org/docs/current/dml-returning.html)).

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>Kör programmet

1. Börja med genom att köra följande kommando för att klona exempel lagrings platsen:

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Ange de miljövariabler som krävs med de värden som du kopierade från Azure Portal:

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. Om du vill köra programmet ändrar du till den katalog där du klonade det och kör `cargo run` :

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    Du bör se utdata som liknar detta:

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. För att bekräfta kan du också ansluta till Azure Database for PostgreSQL [med psql](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) och köra frågor mot databasen, till exempel:

    ```sql
    select * from inventory;
    ```

[Har du problem? Berätta för oss](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa alla resurser som används under den här snabb starten tar du bort resurs gruppen med hjälp av följande kommando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Hantera Azure Database for PostgreSQL server med hjälp av portalen](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Hantera Azure Database for PostgreSQL server med CLI](./how-to-manage-server-cli.md)<br/>

[Hittar du inte det du letar efter? Berätta för oss.](https://aka.ms/postgres-doc-feedback)
