---
title: Resurs delning mellan ursprung (CORS) i Azure Cosmos DB
description: Den här artikeln beskriver hur du konfigurerar CORS (Cross-Origin resurs delning) i Azure Cosmos DB med Azure Portal och Azure Resource Manager mallar.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: eba49ff45ba9ab1f5cfaa1d75973d656ac32ca6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339911"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Konfigurera resurs delning mellan ursprung (CORS)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Resurs delning mellan ursprung (CORS) är en HTTP-funktion som gör det möjligt för ett webb program att köras under en domän för att få åtkomst till resurser i en annan domän. Webbläsare implementerar en säkerhets begränsning som kallas samma-Origin-princip som förhindrar att en webb sida anropar API: er i en annan domän. CORS är dock ett säkert sätt att tillåta att ursprungs domänen anropar API: er i en annan domän. Huvud-API: n (SQL) i Azure Cosmos DB stöder nu resurs delning mellan ursprung (CORS) med hjälp av rubriken "allowedOrigins". När du har aktiverat CORS-stöd för ditt Azure Cosmos-konto utvärderas bara autentiserade begär Anden för att avgöra om de är tillåtna enligt de regler som du har angett.

Du kan konfigurera CORS-inställningen (Cross-Origin resurs delning) från Azure Portal eller från en Azure Resource Manager mall. För Cosmos-konton med kärn-API: n (SQL) stöder Azure Cosmos DB ett JavaScript-bibliotek som fungerar i både Node.js och webbläsare-baserade miljöer. Det här biblioteket kan nu dra nytta av CORS-stöd när du använder Gateway-läge. Det finns ingen konfiguration på klient sidan som krävs för att använda den här funktionen. Med CORS-stöd kan resurser från en webbläsare direkt komma åt Azure Cosmos DB via [JavaScript-biblioteket](https://www.npmjs.com/package/@azure/cosmos) eller direkt från [REST API](/rest/api/cosmos-db/) för enkla åtgärder.

> [!NOTE]
> CORS-stöd gäller endast och stöds för API: et för Azure Cosmos DB Core (SQL). Det gäller inte för Azure Cosmos DB-API: erna för Cassandra, Gremlin eller MongoDB, eftersom dessa protokoll inte använder HTTP för kommunikation mellan klienter och server.

## <a name="enable-cors-support-from-azure-portal"></a>Aktivera CORS-stöd från Azure Portal

Använd följande steg för att aktivera resurs delning mellan ursprung genom att använda Azure Portal:

1. Gå till ditt Azure Cosmos DB-konto. Öppna **CORS** -bladet.

2. Ange en kommaavgränsad lista över ursprung som kan göra cross-origin-anrop till ditt Azure Cosmos DB-konto. Till exempel,,, `https://www.mydomain.com` `https://mydomain.com` `https://api.mydomain.com` . Du kan också använda jokertecken \* för att tillåta alla ursprung och välja **Skicka**. 

   > [!NOTE]
   > För närvarande kan du inte använda jokertecken som en del av domän namnet. `https://*.mydomain.net`Formatet stöds till exempel inte ännu. 

   :::image type="content" source="./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png" alt-text="Aktivera resurs delning mellan ursprung med Azure Portal":::

## <a name="enable-cors-support-from-resource-manager-template"></a>Aktivera CORS-stöd från Resource Manager-mall

Om du vill aktivera CORS med hjälp av en Resource Manager-mall lägger du till "CORS"-avsnittet med egenskapen "allowedOrigins" till en befintlig mall. Följande JSON är ett exempel på en mall som skapar ett nytt Azure Cosmos-konto med CORS aktiverat.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Använda Azure Cosmos DB JavaScript-biblioteket från en webbläsare

I dag har Azure Cosmos DB JavaScript-biblioteket bara den CommonJS version av biblioteket som levererades med paketet. Om du vill använda det här biblioteket från webbläsaren måste du använda ett verktyg som till exempel Rollup eller WebPack för att skapa ett webb läsar bibliotek. Vissa Node.js bibliotek bör ha webb läsar modeller för dem. Följande är ett exempel på en WebPack-konfigurationsfil som har de nödvändiga inställningarna för bildfilerna.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Här är ett [kod exempel](https://github.com/christopheranderson/cosmos-browser-sample) som använder typescript och WebPack med Azure Cosmos DB JavaScript SDK Library för att skapa en app som skickar real tids uppdateringar när nya objekt skapas.
Vi rekommenderar att du inte använder den primära nyckeln för att kommunicera med Azure Cosmos DB från webbläsaren. Använd i stället resurs-token för att kommunicera. Mer information om resurs-token finns i [Skydda åtkomsten till Azure Cosmos DB](secure-access-to-data.md#resource-tokens) artikel.

## <a name="next-steps"></a>Nästa steg

Information om andra sätt att skydda ditt Azure Cosmos-konto finns i följande artiklar:

* [Konfigurera en brand vägg för Azure Cosmos DB](how-to-configure-firewall.md) artikel.

* [Konfigurera Virtual Network och Subnet-based Access för ditt Azure Cosmos DB-konto](how-to-configure-vnet-service-endpoint.md)
