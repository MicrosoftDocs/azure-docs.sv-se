---
title: 'Snabbstart: Din första JavaScript-fråga'
description: I den här snabbstarten följer du stegen för att aktivera Resource Graph för JavaScript och köra din första fråga.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: ef93378acaf2c92cf4f7761345cea326af63d300
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533045"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-javascript"></a>Snabbstart: Kör din första Resource Graph med JavaScript

Den här snabbstarten går igenom processen för att lägga till biblioteken i JavaScript-installationen. Det första steget för att Azure Resource Graph är att initiera ett JavaScript-program med de bibliotek som krävs.

I slutet av den här processen har du lagt till biblioteken i JavaScript-installationen och kört din första Resource Graph fråga.

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration:** Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- **Node.js:** [Node.js](https://nodejs.org/) version 12 eller senare krävs.

## <a name="application-initialization"></a>Programinitiering

Om du vill aktivera JavaScript Azure Resource Graph fråga måste miljön konfigureras. Den här konfigurationen fungerar överallt där JavaScript kan användas, inklusive [bash på Windows 10](/windows/wsl/install-win10).

1. Initiera ett nytt Node.js projekt genom att köra följande kommando.

   ```bash
   npm init -y
   ```

1. Lägg till en referens till yargs-modulen.

   ```bash
   npm install yargs
   ```

1. Lägg till en referens till Azure Resource Graph modulen.

   ```bash
   npm install @azure/arm-resourcegraph
   ```

1. Lägg till en referens till Azure-autentiseringsbiblioteket.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Kontrollera att _package.jspå_ är `@azure/arm-resourcegraph` version **2.0.0** eller senare och `@azure/ms-rest-nodeauth` är version **3.0.3** eller senare.

## <a name="query-the-resource-graph"></a>Fråga Resource Graph

1. Skapa en ny fil _medindex.js_ och ange följande kod.

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

   Ersätt platshållaren med din kommaavgränsade lista över `<YOUR_SUBSCRIPTION_ID_LIST>` Azure-prenumerations-ID:er.

   > [!NOTE]
   > Eftersom det här frågeexempel inte ger någon sorteringsmodifierare som , kommer körning av frågan flera gånger troligen att ge en annan `order by` uppsättning resurser per begäran.

1. Ändra den första parametern `index.js` till och ändra frågan till `order by` **egenskapen** Namn. Ersätt `<YOUR_SUBSCRIPTION_ID_LIST>` med ditt prenumerations-ID:

   ```bash
   node index.js --query "Resources | project name, type | limit 5 | order by name asc" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

   När skriptet försöker autentisera visas ett meddelande som liknar följande meddelande i terminalen:

   > Logga in genom att använda en webbläsare för att öppna sidan och ange koden https://microsoft.com/devicelogin FGB56WJUGK för att autentisera.

   När du har autentiserat i webbläsaren fortsätter skriptet att köras.

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Den här kommandoordningen begränsar först frågeresultatet och beställer dem sedan.

1. Ändra den första parametern `index.js` till och ändra frågan till först egenskapen `order by` **Name** och sedan till de fem `limit` främsta resultaten. Ersätt `<YOUR_SUBSCRIPTION_ID_LIST>` med ditt prenumerations-ID:

   ```bash
   node index.js --query "Resources | project name, type | order by name asc | limit 5" --subs "<YOUR_SUBSCRIPTION_ID_LIST>"
   ```

När den sista frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultatet som returneras konsekventa och sorterade efter egenskapen **Name,** men fortfarande begränsade till de fem främsta resultaten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de installerade biblioteken från programmet kör du följande kommando.

```bash
npm uninstall @azure/arm-resourcegraph @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lagt till Resource Graph i JavaScript-miljön och kört din första fråga. Om du vill veta mer Resource Graph språk fortsätter du till sidan med information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)
