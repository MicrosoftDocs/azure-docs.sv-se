---
title: Använd Azure cache för Redis med go
description: I den här snabb starten får du lära dig hur du skapar en go-app som använder Azure cache för Redis.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: de7bdc22b4bfdf9ef5865b1b601b32672eb868d1
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165135"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>Snabb start: Använd Azure cache för Redis med go

I den här artikeln får du lära dig hur du skapar en REST API i go som lagrar och hämtar användar information som backas upp av en [hash](https://redis.io/topics/data-types-intro#redis-hashes) -datastruktur i [Azure cache för Redis](./cache-overview.md). 

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Go](https://golang.org/doc/install) (helst version 1,13 eller senare)
- [Git](https://git-scm.com/downloads)
- En HTTP-klient, sådan [sväng](https://curl.se/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Skapa en Azure Cache for Redis-instans
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Granska koden (valfritt)

Om du är intresse rad av att lära dig hur koden fungerar kan du läsa följande kodfragment. Annars kan du gå vidare till gå vidare och [köra programmet](#run-the-application).

[Redis-](https://github.com/go-redis/redis) biblioteket med öppen källkod används för att interagera med Azure cache för Redis.

`main`Funktionen börjar stängas av genom att läsa värd namnet och lösen ordet (åtkomst nyckeln) för Azure-cachen för Redis-instansen.

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

Sedan upprättar vi anslutning med Azure cache för Redis. Observera att [tls.Config](https://golang.org/pkg/crypto/tls/#Config) används – Azure cache för Redis accepterar bara säkra anslutningar med [TLS 1,2 som den minsta version som krävs](cache-remove-tls-10-11.md).

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

Om anslutningen lyckas konfigureras [http-hanterare](https://golang.org/pkg/net/http/#HandleFunc) för hantering `POST` och `GET` åtgärder och HTTP-servern startas. 

> [!NOTE] 
> [Gorilla MUX-biblioteket](https://github.com/gorilla/mux) används för routning (även om det inte är absolut nödvändigt och vi kunde ha gjort det med standard biblioteket för det här exempel programmet).
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

`userHandler` struct kapslar in en [Redis. -Klienten](https://pkg.go.dev/github.com/go-redis/redis/v8#Client), som används av `createUser` , `getUser` metod koden för dessa metoder har inte inkluderats för det kortfattat. 

- `createUser`: accepterar en JSON-nyttolast (som innehåller användar information) och sparar den som en `HASH` i Azure cache för Redis.
- `getUser`: hämtar användar information från `HASH` eller returnerar ett HTTP- `404` svar om det inte hittas.

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
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
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>Kör programmet

Programmet accepterar anslutningar och autentiseringsuppgifter i form av miljövariabler. 

1. Hämta **värd namnet** och **åtkomst nycklarna** (tillgängliga via åtkomst nycklar) för Azure cache för Redis-instans i [Azure Portal](https://portal.azure.com/)

1. Ange dem till respektive miljövariabler:

    ```shell
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Ändra till rätt mapp i terminalfönstret. Exempel:

    ```shell
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. Kör följande kommando i terminalen för att starta programmet.

    ```shell
    go run main.go
    ```

HTTP-servern kommer att starta på porten `8080` .

## <a name="test-the-application"></a>Testa programmet

1. Skapa några användar poster. I exemplet nedan används sväng:

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. Hämta en befintlig användare med dess `id` :

    ```bash
    curl -i localhost:8080/users/1
    ```

    Du bör få JSON-svar så här:
    
    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. Om du försöker hämta en användare som inte finns får du ett HTTP-svar `404` . Exempel:

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
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

I den här snabb starten har du lärt dig hur du kommer igång med go med Azure cache för Redis. Du har konfigurerat och kört ett enkelt REST API baserat program för att skapa och hämta användar information som backas upp av en Redis `HASH` data struktur.

> [!div class="nextstepaction"]
> [Skapa en enkel ASP.NET-webbapp som använder Azure Cache for Redis.](./cache-web-app-howto.md)
