---
title: Ansluta ett Go-program till Azure Cosmos DB s API för MongoDB
description: Den här snabb starten visar hur du ansluter ett befintligt Go-program till Azure Cosmos DB s API för MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: 92edfa148268db5a5458b2af4000bc9ffd9ecc83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101659961"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Snabb start: ansluta ett Go-program till Azure Cosmos DB s API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB är en databas tjänst med flera modeller som gör att du snabbt kan skapa och fråga dokument-, tabell-, nyckel värdes-och Graf-databaser med globala funktioner för distribution och horisontell skalning. I den här snabb starten skapar du och hanterar ett Azure Cosmos DB konto genom att använda Azure Cloud Shell, klona ett befintligt exempel program från GitHub och konfigurera det så att det fungerar med Azure Cosmos DB. 

Exempel programmet är ett kommando rads baserat `todo` hanterings verktyg som skrivs i go. Azure Cosmos DBs API för MongoDB är [kompatibelt med MongoDB-Wire-protokollet](./mongodb-introduction.md#wire-protocol-compatibility), vilket gör det möjligt för alla MongoDB klient driv rutiner att ansluta till den. I det här programmet används [driv rutinen Go för MongoDB](https://github.com/mongodb/mongo-go-driver) på ett sätt som är transparent för programmet att data lagras i en Azure Cosmos DB databas.

## <a name="prerequisites"></a>Förutsättningar
- Ett Azure-konto med en aktiv prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free). Eller [prova Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med anslutnings strängen `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- [Go](https://golang.org/) installerat på datorn och en fungerande kunskap om go.
- [Git](https://git-scm.com/downloads).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Kör följande kommandon för att klona exempellagringsplatsen.

1. Öppna en kommando tolk, skapa en ny mapp med namnet `git-samples` och stäng sedan kommando tolken.

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

Det här är valfritt. Om du är intresse rad av att lära dig hur programmet fungerar kan du läsa följande kodfragment. Annars kan du gå vidare till [Kör programmet](#run-the-application). Programlayouten är följande:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

Följande kodavsnitt är alla hämtade från filen `todo.go`.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Ansluta Go-appen till Azure Cosmos DB

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) kapslar in anslutnings strängen för Azure Cosmos DB, som skickas med hjälp av en miljö variabel (information i avsnittet kommande). Anslutningen initieras med hjälp av [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) vilken `clientOptions` instansen skickas. [ `Ping` funktionen](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) anropas för att bekräfta att anslutningen lyckades (det är en snabb strategi)

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
> Att använda [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) konfigurationen är viktigt, utan att du får följande anslutnings fel: `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Skapa ett `todo` objekt

För att skapa en `todo` , får vi en referens till en [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) och anropar [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) funktionen. 

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

Vi skickar i en `Todo` struktur som innehåller beskrivningen och statusen (som ursprungligen ställs in på `pending` )

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>List `todo` objekt

Vi kan visa TODOs baserat på kriterier. En [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) har skapats för att kapsla filter villkoren

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

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) används för att söka efter dokument baserat på filtret och resultatet konverteras till en sektor av `Todo`

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

Slutligen återges informationen i tabell format

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

En `todo` kan uppdateras baserat på dess `_id` . Ett [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) filter skapas baserat på och ett `_id` annat skapas för den uppdaterade informationen, vilket är en ny status ( `completed` eller `pending` ) i det här fallet. Slutligen [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) anropas funktionen med filtret och det uppdaterade dokumentet

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

A `todo` tas bort baserat på dess `_id` och den kapslas i form av en [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) instans. [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) anropas för att ta bort dokumentet.

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

Ändra till den katalog där du klonade programmet och bygg det (med `go build` ).

```bash
cd monogdb-go-quickstart
go build -o todo
```

För att bekräfta att programmet har skapats korrekt.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Installations Azure Cosmos DB

### <a name="sign-in-to-azure"></a>Logga in på Azure

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [installera Azure CLI]. 

Om du använder en installerad Azure CLI loggar du in på Azure-prenumerationen med kommandot [AZ login](/cli/azure/reference-index#az-login) och följer anvisningarna på skärmen. Du kan hoppa över det här steget om du använder Azure Cloud-gränssnittet.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Lägg till Azure Cosmos DB-modulen

Om du använder en installerad Azure CLI, kontrollera om `cosmosdb`-komponenten har installerats genom att köra kommandot `az`. Om `cosmosdb` är i listan över grundläggande kommandon, fortsätter du med nästa kommando. Du kan hoppa över det här steget om du använder Azure Cloud-gränssnittet.

Om `cosmosdb` inte är i listan över grundläggande kommandon, installerar du om [Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [resurs grupp](../azure-resource-manager/management/overview.md) med [AZ-gruppen Create](/cli/azure/group#az-group-create). En Azure-resursgrupp är en logisk container som Azure-resurser (t.ex. webbappar, databaser och lagringskonton) distribueras och hanteras i. 

Följande exempel skapar en resursgrupp i regionen västeuropa. Välj ett unikt namn för resursgruppen.

Om du använder Azure Cloud Shell väljer du **prova**, följer anvisningarna på skärmen för att logga in och kopierar sedan kommandot till kommando tolken.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Skapa ett Cosmos-konto med kommandot [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create).

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

För att kunna ansluta till en Cosmos-databas behöver du databasnyckeln. Använd kommandot [AZ cosmosdb Key List](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) för att hämta den primära nyckeln.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI matar ut information som liknar följande exempel. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>Konfigurera programmet 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Exportera anslutnings strängen, MongoDB-databasen och samlings namnen som miljövariabler. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> `ssl=true`Alternativet är viktigt på grund av Cosmos DB krav. Mer information finns i [krav på anslutnings sträng](connect-mongodb-account.md#connection-string-requirements).
>

För `MONGODB_CONNECTION_STRING` miljövariabeln ersätter du plats hållarna för `<COSMOSDB_ACCOUNT_NAME>` och `<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: Namnet på det Azure Cosmos DB konto som du har skapat
2. `<COSMOSDB_PASSWORD>`: Databas nyckeln som extraherades i föregående steg

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

Du kan välja önskade värden för `MONGODB_DATABASE` och `MONGODB_COLLECTION` eller lämna dem som de är.

## <a name="run-the-application"></a>Kör programmet

För att skapa en `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

Om det lyckas bör du se utdata med MongoDB `_id` för det nyligen skapade dokumentet:

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Skapa ett annat `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Visa alla `todo` s

```bash
./todo --list all
```

Du bör se de som du nyss lade till i tabell format som sådana

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

Om du vill uppdatera status för en `todo` (t. ex. ändra till- `completed` status) använder du `todo` ID: t

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Visa endast de slutförda `todo` s

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

Data som lagras i Azure Cosmos DB kan visas och fråga i Azure Portal.

Om du vill visa, fråga och arbeta med användardata som skapats i föregående steg, loggar du in på [Azure-portalen](https://portal.azure.com) i din webbläsare.

I den översta sökrutan anger du **Azure Cosmos DB**. När ditt Cosmos-kontoblad öppnas väljer du ditt Cosmos-konto. I det vänstra navigerings fönstret väljer du **datautforskaren**. Utöka din samling i samlings-fönstret så kan du visa dokumenten i samlingen, fråga data och skapa och köra lagrade procedurer, utlösare och UDF:er. 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="Det nyligen skapade dokumentet visas i Datautforskaren":::


Ta bort ett `todo` using-ID

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Lista `todo` s att bekräfta

```bash
./todo --list all
```

Det `todo` du nyss tog bort bör inte finnas

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

I den här snabb starten har du lärt dig hur du skapar ett Azure Cosmos DB MongoDB API-konto med hjälp av Azure Cloud Shell och skapar och kör en go-kommandoraden för att hantera `todo` s. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto.

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)