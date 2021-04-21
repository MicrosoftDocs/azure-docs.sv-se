---
title: 'Snabbstart: Ansluta en Node.js MongoDB-app till Azure Cosmos DB'
description: Den här snabbstarten visar hur du ansluter en befintlig MongoDB-app som skrivits i Node.js till Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 588c12addbdbd686038699026bb9dccd8671717c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765270"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Snabbstart: Migrera en befintlig MongoDB-Node.js-webbapp till Azure Cosmos DB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

I den här snabbstarten skapar och hanterar du ett Azure Cosmos DB för Mongo DB API-konto med hjälp av Azure Cloud Shell och med en MEAN-app (MongoDB, Express, Angular och Node.js) klonad från GitHub. Azure Cosmos DB är en databastjänst med flera modeller som gör att du snabbt kan skapa och fråga databaser för dokument, tabeller, nyckelvärden och grafer med global distribution och horisontella skalningsfunktioner.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Eller [prova Azure Cosmos DB utan en](https://azure.microsoft.com/try/cosmosdb/) Azure-prenumeration. Du kan också använda [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator) med anslutningssträngen `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .

- [Node.js](https://nodejs.org/), och arbetskunskaper om Node.js.

- [Git](https://git-scm.com/downloads).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.


## <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Kör följande kommandon för att klona exempellagringsplatsen. Den här exempellagringsplatsen innehåller standard-[MEAN.js](https://meanjs.org/)-programmet.

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Kör programmet

Den här MongoDB-appen som skrivits Node.js ansluter till Azure Cosmos DB databas, som stöder MongoDB-klienten. Med andra ord är det transparent för programmet att data lagras i en Azure Cosmos DB databas.

Installera de nödvändiga paketen och starta programmet.

```bash
cd mean
npm install
npm start
```
Programmet försöker att ansluta till en MongoDB-källa, vilket misslyckas. Gå vidare och avsluta programmet när utdata returnerar ”[MongoError: connect ECONNREFUSED 127.0.0.1:27017]”.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Om du använder ett installerat Azure CLI loggar du in på din Azure-prenumeration med [kommandot az login](/cli/azure/reference-index#az_login) och följer anvisningarna på skärmen. Du kan hoppa över det här steget om du använder Azure Cloud-gränssnittet.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Lägg till Azure Cosmos DB-modulen

Om du använder en installerad Azure CLI, kontrollera om `cosmosdb`-komponenten har installerats genom att köra kommandot `az`. Om `cosmosdb` är i listan över grundläggande kommandon, fortsätter du med nästa kommando. Du kan hoppa över det här steget om du använder Azure Cloud-gränssnittet.

Om `cosmosdb` inte är i listan över grundläggande kommandon, installerar du om [Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [resursgrupp](../azure-resource-manager/management/overview.md) med [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk container som Azure-resurser (t.ex. webbappar, databaser och lagringskonton) distribueras och hanteras i. 

Följande exempel skapar en resursgrupp i regionen västeuropa. Välj ett unikt namn för resursgruppen.

Om du använder Azure Cloud Shell väljer du **Prova,** följer anvisningarna på skärmen för att logga in och kopierar sedan kommandot till kommandotolken.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

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

## <a name="connect-your-nodejs-application-to-the-database"></a>Anslut ditt Node.js-program till databasen

I det här steget ansluter du ditt MEAN.js till det Azure Cosmos DB databaskonto som du nyss skapade. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurera anslutningssträngen i ditt Node.js-program

I din MEAN.js-lagringsplats, öppnar du `config/env/local-development.js`.

Ersätt innehållet i filen med följande kod. Se till att även ersätta de två `<cosmosdb-name>`-platshållarna med namnet på ditt Cosmos-konto.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Hämta nyckeln

För att kunna ansluta till en Cosmos-databas behöver du databasnyckeln. Använd kommandot [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) för att hämta primärnyckeln.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI matar ut information som liknar följande exempel. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Kopiera värdet för `primaryMasterKey`. Klistra in det över `<primary_master_key>` i `local-development.js`.

Spara ändringarna.

### <a name="run-the-application-again"></a>Kör programmet igen.

Kör `npm start` igen. 

```bash
npm start
```

Ett konsolmeddelande borde nu komma upp som säger att utvecklingsmiljön är klar och igång. 

Gå till `http://localhost:3000` i en webbläsare. Välj **Registrera dig** på den översta menyn och försök att skapa två dummy-användare. 

MEAN.js-exempelprogrammet lagrar användardata i databasen. Om du har lyckats och MEAN.js automatiskt loggar in på den skapade användaren så fungerar din Azure Cosmos DB-anslutning. 

:::image type="content" source="./media/create-mongodb-nodejs/mongodb-connect-success.png" alt-text="MEAN.js ansluter till MongoDB":::

## <a name="view-data-in-data-explorer"></a>Visa data i datautforskaren

Data som lagras i en Cosmos-databas är tillgängliga att visa och fråga i Azure-portalen.

Om du vill visa, fråga och arbeta med användardata som skapats i föregående steg, loggar du in på [Azure-portalen](https://portal.azure.com) i din webbläsare.

I den övre sökrutan anger du **Azure Cosmos DB**. När ditt Cosmos-kontoblad öppnas väljer du ditt Cosmos-konto. I det vänstra navigeringsfönstret väljer **du Datautforskaren**. Utöka din samling i samlings-fönstret så kan du visa dokumenten i samlingen, fråga data och skapa och köra lagrade procedurer, utlösare och UDF:er. 

:::image type="content" source="./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png" alt-text="Datautforskaren i Azure Portal":::


## <a name="deploy-the-nodejs-application-to-azure"></a>Distribuera Node.js-programmet till Azure

I det här steget distribuerar du ditt Node.js-program till Cosmos DB.

Du kanske har märkt att konfigurationsfilen du ändrade tidigare är för utvecklingsmiljön (`/config/env/local-development.js`). När du distribuerar ditt program till App Service, körs det i produktionsmiljön som standard. Nu behöver du göra samma ändring till respektive konfigurationsfil.

I din MEAN.js-lagringsplats, öppnar du `config/env/production.js`.

I objektet `db`, ersätter du värdet `uri` som det visas i följande exempel. Se till att ersätta platshållarna som innan.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> Alternativet `ssl=true` är viktigt på grund av Cosmos DB krav. Mer information finns i Krav [på anslutningssträng.](connect-mongodb-account.md#connection-string-requirements)
>
>

Spara dina ändringar till Git i terminalen. Du kan kopiera bägge kommandona för att köra dem tillsammans.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Cosmos DB MongoDB API-konto med hjälp av Azure Cloud Shell och skapar och kör en MEAN.js-app för att lägga till användare i kontot. Du kan nu importera ytterligare data till ditt Azure Cosmos DB-konto.

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)