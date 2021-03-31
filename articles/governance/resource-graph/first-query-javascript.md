---
title: 'Snabb start: din första JavaScript-fråga'
description: I den här snabb starten följer du stegen för att aktivera resurs biblioteks biblioteket för Java Script och köra din första fråga.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 97cbd9ddf65a4135f55304f6dd67c704c6fcac5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917529"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Snabb start: kör din första resurs diagram fråga med hjälp av Java Script

Den här snabb starten vägleder dig genom processen att lägga till biblioteken i JavaScript-installationen. Det första steget i att använda Azure Resource Graph är att initiera ett JavaScript-program med de bibliotek som krävs.

I slutet av den här processen har du lagt till biblioteken i JavaScript-installationen och kört din första resurs diagram fråga.

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration**: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt](https://azure.microsoft.com/free/) konto innan du börjar.

- **Node.js**: [Node.js](https://nodejs.org/) version 12 eller högre krävs.

## <a name="application-initialization"></a>Programinitiering

Om du vill aktivera Java Script för att fråga Azure Resource Graph måste miljön konfigureras. Den här installationen fungerar där Java Script kan användas, inklusive [bash i Windows 10](/windows/wsl/install-win10).

1. Initiera ett nytt Node.js-projekt genom att köra följande kommando.

   ```bash
   npm init -y
   ```

1. Lägg till en referens till yargs-modulen.

   ```bash
   npm install yargs
   ```

1. Lägg till en referens till Azure-resursens diagram-modul.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Lägg till en referens till Azure Authentication-biblioteket.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Kontrol lera att _package.jspå_ `@azure/arm-resourcegraph` är version **2.0.0** eller högre och att den `@azure/ms-rest-nodeauth` är version **3.0.3** eller högre.

## <a name="query-the-resource-graph"></a>Fråga resurs diagrammet

1. Skapa en ny fil med namnet _index.js_ och ange följande kod.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const resourceGraph = require("@azure/arm-resourcegraph");

   if (argv.query && argv.subs) {
       const subscriptionList = argv.subs.split(",");

       const query = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new resourceGraph.ResourceGraphClient(credentials);
          const result = await client.resources(
             {
                 query: argv.query,
                 subscriptions: subscriptionList,
             },
             { resultFormat: "table" }
          );
          console.log("Records: " + result.totalRecords);
          console.log(result.data);
       };

       query();
   }
   ```

1. Ange följande kommando i terminalen:

   ```bash
   node index.js --query "Resources | project name, type | limit 5" --subs <YOUR_SUBSCRIPTION_ID_LIST>
   ```

   Se till att ersätta `<YOUR_SUBSCRIPTION_ID_LIST>` plats hållaren med en kommaavgränsad lista över Azures prenumerations-ID.

   > [!NOTE]
   > Eftersom den här frågan inte tillhandahåller en sorterings modifierare, till exempel `order by` att köra den här frågan flera gånger, är det troligt att en annan uppsättning resurser skapas per begäran.

1. Ändra den första parametern till `index.js` och ändra frågan till `order by` egenskapen **namn** . Ersätt `<YOUR_SUBSCRIPTION_ID_LIST>` med ditt prenumerations-ID:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   När skriptet försöker autentisera visas ett meddelande som liknar följande meddelande i terminalen:

   > Logga in genom att använda en webbläsare för att öppna sidan https://microsoft.com/devicelogin och ange koden FGB56WJUGK för att autentisera.

   När du har autentiserat i webbläsaren fortsätter skriptet att köras.

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommando ordningen begränsar först frågeresultaten och beställer dem.

1. Ändra den första parametern till `index.js` och ändra frågan till första `order by` egenskapen **namn** och sedan `limit` till de fem översta resultaten. Ersätt `<YOUR_SUBSCRIPTION_ID_LIST>` med ditt prenumerations-ID:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

När den slutliga frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultatet som returneras konsekvent och beställt av egenskapen **namn** , men fortfarande begränsat till de fem främsta resultaten.

## <a name="clean-up-resources"></a>Rensa resurser

Kör följande kommando om du vill ta bort de installerade biblioteken från programmet.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lagt till resurs biblioteks biblioteken i JavaScript-miljön och kör din första fråga. Om du vill veta mer om det här resurs språket fortsätter du till sidan information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)