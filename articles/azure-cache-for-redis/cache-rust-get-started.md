---
title: Använda Azure cache för Redis med Rust
description: I den här snabb starten får du lära dig hur du interagerar med Azure cache för Redis med hjälp av Rust.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: b55a706aa25b21620226690c172c996fe10a84fa
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203513"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>Snabb start: Använd Azure cache för Redis med Rust

I den här artikeln får du lära dig hur du använder [Rust-programmeringsspråk](https://www.rust-lang.org/) för att interagera med [Azure cache för Redis](./cache-overview.md). Det visar exempel på vanliga Redis-datastrukturer, till exempel [String](https://redis.io/topics/data-types-intro#redis-strings), [hash](https://redis.io/topics/data-types-intro#redis-hashes), [list](https://redis.io/topics/data-types-intro#redis-lists) osv. använda [Redis-RS-](https://github.com/mitsuhiko/redis-rs) biblioteket för Redis. Den här klienten visar både höga och lågnivå-API: er och du ser båda dessa format i åtgärd med hjälp av exempel koden som visas i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Rust](https://www.rust-lang.org/tools/install) (version 1,39 eller senare)
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Skapa en Azure Cache for Redis-instans
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Granska koden (valfritt)

Om du är intresse rad av att lära dig hur koden fungerar kan du läsa följande kodfragment. Annars kan du gå vidare till gå vidare och [köra programmet](#run-the-application).

`connect`Funktionen används för att upprätta en anslutning till Azure cache för Redis. Det förväntar sig att värdnamn och lösen ordet (åtkomst nyckeln) skickas via miljövariabler respektive `REDIS_HOSTNAME` `REDIS_PASSWORD` . Formatet för anslutnings-URL: en är `rediss://<username>:<password>@<hostname>` – Azure cache för Redis accepterar bara säkra anslutningar med [TLS 1,2 som den minsta version som krävs](cache-remove-tls-10-11.md).

Anropet till [Redis:: client:: Open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) utför grundläggande verifiering medan [get_connection ()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection) initierar anslutningen – programmet stoppas om anslutningen Miss lyckas på grund av orsak till ett felaktigt lösen ord.

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

`basics`Funktionen omfattar [set](https://redis.io/commands/set)-, [Get](https://redis.io/commands/get)-och [INCR](https://redis.io/commands/incr) -kommandon. API för låg nivå används för `SET` och `GET` , vilket anger och hämtar värdet för en nyckel med namnet `foo` . `INCRBY`Kommandot körs med ett högnivå-API, d.v.s. [INCR](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) ökar värdet för en nyckel (namngett `counter` ) genom `2` att följa av ett anrop för att hämta det [](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get) .

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

Kodfragmentet nedan visar funktionerna i en Redis- `HASH` datastruktur. [HSET](https://redis.io/commands/hset) anropas med hjälp av lågnivå-API för att lagra information ( `name` , `version` , `repo` ) om Redis-drivrutiner (klienter). Information om Rust-drivrutinen (som används i den här exempel koden!) registreras till exempel i form av en [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) och skickas sedan till API: et på den lägsta nivån. Den hämtas sedan med [HGETALL](https://redis.io/commands/hgetall).

`HSET` kan också utföras med ett högnivå-API som använder [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) som accepterar en matris med tupler. [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) körs sedan för att hämta värdet för ett enskilt attribut ( `repo` i det här fallet).

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

I funktionen nedan kan du se hur du använder en `LIST` data struktur. [LPUSH](https://redis.io/commands/lpush) körs (med LÅGNIVÅ-API) för att lägga till en post i listan och [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) -metoden för hög nivå används för att hämta den från listan. Sedan används metoden [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) för att lägga till ett par poster i listan som sedan hämtas med hjälp av [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) -metoden på den lägsta nivån.

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

Här kan du se några av `SET` åtgärderna. [Sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd) -metoden (High-Level-API) används för att lägga till flera poster i ett `SET` namn `users` . [SISMEMBER](https://redis.io/commands/hset) körs sedan (LÅGNIVÅ-API) för att kontrol lera om det `user1` finns. Slutligen används [smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) för att hämta och iterera över alla angivna poster i form av en Vector ([VEC <String> ](https://doc.rust-lang.org/std/vec/struct.Vec.html)).

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

`sorted_set` funktionen nedan visar data strukturen sorterad uppsättning. [ZADD](https://redis.io/commands/zadd) anropas (med LÅGNIVÅ-API) för att lägga till ett slumpmässigt heltals resultat för en spelare ( `player-1` ). Sedan används [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) -metoden (High-Level-API) för att lägga till fler spelare ( `player-2` till `player-5` ) och deras respektive (slumpmässigt genererade) poäng. Antalet poster i den sorterade uppsättningen visas med [ZCARD](https://redis.io/commands/zcard) och används som gräns för [ZRANGE](https://redis.io/commands/zrange) -kommandot (anropas med lågnivå-API) för att visa en lista över spelarna med sina resultat i stigande ordning.

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
}
```

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Börja med att klona programmet från GitHub.

1. Öppna en kommando tolk och skapa en ny mapp med namnet `git-samples` .

    ```bash
    md "C:\git-samples"
    ```

1. Öppna ett git-terminalfönster, till exempel git bash. Använd `cd` kommandot för att ändra till den nya mappen där du ska klona exempel appen.

    ```bash
    cd "C:\git-samples"
    ```

1. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>Kör programmet

Programmet accepterar anslutningar och autentiseringsuppgifter i form av miljövariabler. 

1. Hämta **värd namnet** och **åtkomst nycklarna** (tillgängliga via åtkomst nycklar) för Azure cache för Redis-instans i [Azure Portal](https://portal.azure.com/).

1. Ange dem till respektive miljövariabler:

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Ändra till rätt mapp i terminalfönstret. Exempel:

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. Kör följande kommando i terminalen för att starta programmet.

    ```shell
    cargo run
    ```
    
    Du kommer att se utdata som sådana:
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    Kommentera ut andra funktioner i funktionen om du vill köra en speciell funktion `main` :
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med den Azure-resurs grupp och de resurser som du skapade i den här snabb starten kan du ta bort dem för att undvika avgifter.

> [!IMPORTANT]
> Att ta bort en resurs grupp går inte att ångra, och resurs gruppen och alla resurser i den tas bort permanent. Om du har skapat Azure cache för Redis-instansen i en befintlig resurs grupp som du vill behålla, kan du ta bort bara cachen genom att välja **ta bort** på sidan cache- **Översikt** . 

Så här tar du bort resurs gruppen och dess Redis Cache för Azure-instansen:

1. Sök efter och välj **resurs grupper** från [Azure Portal](https://portal.azure.com).
1. I text rutan **Filtrera efter namn** anger du namnet på den resurs grupp som innehåller din cache-instans och väljer sedan den från Sök resultaten. 
1. Välj **Ta bort resursgrupp** på din resursgruppssida.
1. Skriv namnet på resurs gruppen och välj sedan **ta bort**.
   
   ![Ta bort din resurs grupp för Azure cache för Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder Rust-drivrutinen för Redis för att ansluta och köra åtgärder i Azure cache för Redis.

> [!div class="nextstepaction"]
> [Skapa en enkel ASP.NET-webbapp som använder Azure Cache for Redis.](./cache-web-app-howto.md)
