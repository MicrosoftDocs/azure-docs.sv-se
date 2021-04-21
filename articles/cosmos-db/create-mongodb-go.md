---
title: Ansluta ett Go-program Azure Cosmos DB till api:et för MongoDB
description: Den här snabbstarten visar hur du ansluter ett befintligt Go-program till Azure Cosmos DB:s API för MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: c9829b49662c90df685388691c04b201a7010eb8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765223"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Snabbstart: Ansluta ett Go-program Azure Cosmos DB till api:et för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckelvärdes- och grafdatabaser med global distribution och horisontella skalningsfunktioner. I den här snabbstarten skapar och hanterar du ett Azure Cosmos DB-konto med hjälp av Azure Cloud Shell, klonar ett befintligt exempelprogram från GitHub och konfigurerar det så att det fungerar med Azure Cosmos DB. 

Exempelprogrammet är ett kommandoradsbaserat `todo` hanteringsverktyg som skrivits i Go. Azure Cosmos DB API för MongoDB är kompatibelt med [MongoDB-trådprotokollet,](./mongodb-introduction.md#wire-protocol-compatibility)vilket gör det möjligt för alla MongoDB-klientdrivrutiner att ansluta till den. Det här programmet använder [Go-drivrutinen för MongoDB](https://github.com/mongodb/mongo-go-driver) på ett sätt som är transparent för programmet att data lagras i en Azure Cosmos DB databas.

## <a name="prerequisites"></a>Förutsättningar
- Ett Azure-konto med en aktiv prenumeration. [Skapa en utan kostnad.](https://azure.microsoft.com/free) Eller [prova Azure Cosmos DB utan en](https://azure.microsoft.com/try/cosmosdb/) Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med anslutningssträngen `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Go](https://golang.org/) installerat på datorn och kunskaper om Go.
- [Git](https://git-scm.com/downloads).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Kör följande kommandon för att klona exempellagringsplatsen.

1. Öppna en kommandotolk, skapa en ny mapp med `git-samples` namnet och stäng sedan kommandotolken.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>Granska koden

Det här är valfritt. Om du vill lära dig hur programmet fungerar kan du granska följande kodavsnitt. Annars kan du gå vidare till [Kör programmet](#run-the-application). Programlayouten är följande:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

Följande kodavsnitt är alla hämtade från filen `todo.go`.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Ansluta Go-appen till Azure Cosmos DB

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) kapslar in anslutningssträngen för Azure Cosmos DB, som skickas med hjälp av en miljövariabel (information i det kommande avsnittet). Anslutningen initieras med hjälp [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) av vilken `clientOptions` instansen skickas. [ `Ping` funktionen](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) anropas för att bekräfta en lyckad anslutning (det är en strategi som går snabbt att redundansväxa)

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> Det är [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) viktigt att använda konfigurationen, utan vilken du får följande anslutningsfel: `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Skapa ett `todo` objekt

För att skapa `todo` en får vi en referens till en och [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) anropar funktionen . 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

Vi skickar en `Todo` struct som innehåller beskrivningen och statusen (som ursprungligen är inställd på `pending` )

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>`todo`Listobjekt

Vi kan lista TODOs baserat på kriterier. En [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) skapas för att kapsla in filtervillkoren

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) används för att söka efter dokument baserat på filtret och resultatet konverteras till ett segment av `Todo`

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

Slutligen återges informationen i tabellformat

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>Uppdatera ett `todo` objekt

En `todo` kan uppdateras baserat på dess `_id` . Ett filter skapas baserat på och ett annat skapas för den uppdaterade informationen, vilket i det här fallet är en [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) ny status ( eller `_id` `completed` `pending` ). Slutligen [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) anropas funktionen med filtret och det uppdaterade dokumentet

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>Ta bort en `todo`

En `todo` tas bort baserat på dess och `_id` kapslas in i form av en [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) -instans. [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) anropas för att ta bort dokumentet.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>Skapa programmet

Ändra till den katalog där du klonade programmet och skapa det (med hjälp av `go build` ).

```bash
cd monogdb-go-quickstart
go build -o todo
```

För att bekräfta att programmet har skapats korrekt.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Konfigurera Azure Cosmos DB

### <a name="sign-in-to-azure"></a>Logga in på Azure

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du gå till [Installera Azure CLI]. 

Om du använder ett installerat Azure CLI loggar du in på din Azure-prenumeration med [kommandot az login](/cli/azure/reference-index#az_login) och följer anvisningarna på skärmen. Du kan hoppa över det här steget om du använder Azure Cloud-gränssnittet.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Lägg till Azure Cosmos DB-modulen

Om du använder en installerad Azure CLI, kontrollera om `cosmosdb`-komponenten har installerats genom att köra kommandot `az`. Om `cosmosdb` är i listan över grundläggande kommandon, fortsätter du med nästa kommando. Du kan hoppa över det här steget om du använder Azure Cloud-gränssnittet.

Om `cosmosdb` inte är i listan över grundläggande kommandon, installerar du om [Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [resursgrupp](../azure-resource-manager/management/overview.md) med [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk container som Azure-resurser (t.ex. webbappar, databaser och lagringskonton) distribueras och hanteras i. 

Följande exempel skapar en resursgrupp i regionen västeuropa. Välj ett unikt namn för resursgruppen.

Om du använder Azure Cloud Shell väljer du **Prova,** följer anvisningarna på skärmen för att logga in och kopierar sedan kommandot till kommandotolken.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Skapa ett Cosmos-konto med kommandot [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create).

I följande kommando ersätter du ditt eget unika Cosmos-kontonamn där du ser platshållaren `<cosmosdb-name>`. Den här unika namnet kommer att användas som en del av din Cosmos DB-slutpunkt (`https://<cosmosdb-name>.documents.azure.com/`) så namnet måste vara unikt för alla Cosmos-konton i Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Parametern `--kind MongoDB` aktiverar MongoDB-klientanslutningar.

När Azure Cosmos DB-kontot har skapats, visar Azure CLI information liknande följande exempel. 

> [!NOTE]
> I det här exemplet används JSON som standardalternativ för Azure CLI-utdataformat. Om du vill använda andra format läser du [Utdataformat för Azure CLI-kommandon](/cli/azure/format-output-azure-cli). 

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>Hämta databasnyckeln

För att kunna ansluta till en Cosmos-databas behöver du databasnyckeln. Använd kommandot [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) för att hämta primärnyckeln.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI matar ut information som liknar följande exempel. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>Konfigurera programmet 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Exportera anslutningssträngen, MongoDB-databasen och samlingsnamnen som miljövariabler. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> Alternativet `ssl=true` är viktigt på grund av Cosmos DB krav. Mer information finns i Krav [på anslutningssträng.](connect-mongodb-account.md#connection-string-requirements)
>

För `MONGODB_CONNECTION_STRING` miljövariabeln ersätter du platshållarna för `<COSMOSDB_ACCOUNT_NAME>` och `<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: Namnet på det Azure Cosmos DB-konto som du skapade
2. `<COSMOSDB_PASSWORD>`: Databasnyckeln som extraherades i föregående steg

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

Du kan välja dina önskade värden för `MONGODB_DATABASE` och eller lämna dem som de `MONGODB_COLLECTION` är.

## <a name="run-the-application"></a>Kör programmet

Så här skapar du en `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

Om det lyckas bör du se utdata med MongoDB för `_id` det nya dokumentet:

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Skapa en till `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Visa en lista över `todo` alla

```bash
./todo --list all
```

Du bör se dem som du just lade till i tabellformat som sådana

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

Om du vill uppdatera statusen `todo` för en (t.ex. ändra den till `completed` status) använder du `todo` ID:t

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Lista endast `todo` slutförda s

```bash
./todo --list completed
```

Du bör se den som du precis har uppdaterat

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Visa data i datautforskaren

Data som lagras Azure Cosmos DB är tillgängliga för att visa och fråga i Azure Portal.

Om du vill visa, fråga och arbeta med användardata som skapats i föregående steg, loggar du in på [Azure-portalen](https://portal.azure.com) i din webbläsare.

I den övre sökrutan anger du **Azure Cosmos DB**. När ditt Cosmos-kontoblad öppnas väljer du ditt Cosmos-konto. I det vänstra navigeringsfönstret väljer **du Datautforskaren**. Utöka din samling i samlings-fönstret så kan du visa dokumenten i samlingen, fråga data och skapa och köra lagrade procedurer, utlösare och UDF:er. 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="Det nyligen skapade dokumentet visas i Datautforskaren":::


Ta bort `todo` en med hjälp av id

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Visa en lista `todo` med s för att bekräfta

```bash
./todo --list all
```

Den `todo` som du nyss tog bort bör inte finnas

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB MongoDB API-konto med hjälp av Azure Cloud Shell och hur du skapar och kör en Go-kommandoradsapp för att `todo` hantera er. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto.

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)