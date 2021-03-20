---
title: Kom igång med Azure CDN SDK för Node.js | Microsoft Docs
description: Lär dig hur du skapar ett enkelt Node.js konsol program som visar hur du automatiserar skapandet och hanteringen av Azure CDN profiler och slut punkter.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: f5d5c7a6e1f6993b19f38db2ae846b213a1d553e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993364"
---
# <a name="get-started-with-azure-cdn-development"></a>Kom igång med Azure CDN-utveckling
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Du kan använda [Azure CDN SDK för Node.js](https://www.npmjs.com/package/azure-arm-cdn) för att automatisera skapandet och hanteringen av CDN-profiler och slut punkter.  Den här självstudien vägleder dig genom skapandet av ett enkelt Node.js konsol program som visar flera av de tillgängliga åtgärderna.  Den här självstudien är inte avsedd att beskriva alla aspekter av Azure CDN SDK för Node.js i detalj.

För att slutföra den här självstudien bör du redan ha [Node.js](https://www.nodejs.org) **4. x.** x eller högre installerat och konfigurerat.  Du kan använda en text redigerare som du vill skapa ett Node.jss program.  För att skriva den här självstudien har jag använt [Visual Studio Code](https://code.visualstudio.com).  

> [!TIP]
> Det [färdiga projektet från den här självstudien](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) är tillgängligt för hämtning på MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Skapa ditt projekt och Lägg till NPM-beroenden
Nu när vi har skapat en resurs grupp för våra CDN-profiler och gett vår Azure AD-program behörighet att hantera CDN-profiler och slut punkter inom gruppen kan vi börja skapa vårt program.

Skapa en mapp för att lagra ditt program.  Från en-konsol med Node.js verktyg i din aktuella sökväg anger du den aktuella platsen till den nya mappen och initierar projektet genom att köra:

```console
npm init
```

Sedan visas en serie frågor om att initiera projektet.  I den här självstudien används *app.js* för **Start punkten**.  Du kan se mina andra alternativ i följande exempel.

![NPM init-utdata](./media/cdn-app-dev-node/cdn-npm-init.png)

Nu initieras projektet med en *packages.jspå* filen.  Vårt projekt kommer att använda vissa Azure-bibliotek som finns i NPM-paket.  Vi använder Azure client runtime för Node.js (MS-rest-Azure) och Azure CDN klient biblioteket för Node.js (Azure-arm-CD).  Nu ska vi lägga till dem i projektet som beroenden.

```console
npm install --save ms-rest-azure
npm install --save azure-arm-cdn
```

När paketen har installerats bör *package.js* filen se ut ungefär som i det här exemplet (versions nummer kan skilja sig):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Slutligen kan du använda text redigeraren för att skapa en tom textfil och spara den i roten i vår projektmapp som *app.js*.  Nu är det dags att börja skriva kod.

## <a name="requires-constants-authentication-and-structure"></a>Kräver, konstanter, autentisering och struktur
Med *app.js* öppna i vårt redigerings program, ska vi hämta den grundläggande strukturen i vårt program.

1. Lägg till "kräver" för våra NPM-paket överst med följande:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Vi måste definiera vissa konstanter som metoderna kommer att använda.  Lägg till följande.  Se till att ersätta plats hållarna, inklusive **&lt; vinkel paren tes &gt;**, med dina egna värden efter behov.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Härnäst ska vi instansiera CDN Management-klienten och ge den våra autentiseringsuppgifter.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Om du använder individuell användarautentisering ser dessa två rader lite annorlunda ut.
   
   > [!IMPORTANT]
   > Använd bara det här kod exemplet om du väljer att ha individuell användarautentisering i stället för ett huvud namn för tjänsten.  Var noga med att skydda dina enskilda användarautentiseringsuppgifter och hålla dem hemliga.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Se till att ersätta objekten i **&lt; vinkelparenteser &gt;** med rätt information.  För `<redirect URI>` använder du den omdirigerings-URI som du angav när du registrerade programmet i Azure AD.
4. Vårt Node.js konsol program kommer att ta några kommando rads parametrar.  Vi kontrollerar att minst en parameter skickades.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Vi kommer till huvud delen av vårt program, där vi förgrenar till andra funktioner baserat på vilka parametrar som skickades.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. På flera platser i vårt program måste vi kontrol lera att rätt antal parametrar skickades och visa lite hjälp om de inte ser korrekt ut.  Nu ska vi skapa funktioner för att göra det.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Slutligen är de funktioner som vi kommer att använda på CDN-hanterings klienten asynkrona, så de behöver en metod för att anropa tillbaka när de är klara.  Låt oss göra det som kan visa utdata från CDN Management-klienten (om det finns några) och avsluta programmet på ett smidigt sätt.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Nu när den grundläggande strukturen i vårt program är skriven bör vi skapa funktioner som kallas baserade på våra parametrar.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN-profiler och slut punkter
Vi börjar med kod för att lista våra befintliga profiler och slut punkter.  Mina kod kommentarer ger den förväntade syntaxen så att vi vet var varje parameter går.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Skapa CDN-profiler och slut punkter
Nu ska vi skriva funktionerna för att skapa profiler och slut punkter.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Rensa en slut punkt
Förutsatt att slut punkten har skapats, en vanlig uppgift som vi kanske vill utföra i vårt program, tar vi bort innehåll i vår slut punkt.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Ta bort CDN-profiler och slut punkter
Den sista funktionen kommer att inkludera borttagnings slut punkter och profiler.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Köra programmet
Vi kan nu köra vårt Node.js program med vår favorit fel sökare eller i-konsolen.

> [!TIP]
> Om du använder Visual Studio Code som fel sökare måste du konfigurera din miljö för att skicka in kommando rads parametrarna.  Visual Studio Code gör detta i **launch.js** i filen.  Leta efter en egenskap med namnet **args** och Lägg till en matris med sträng värden för parametrarna, så att den ser ut ungefär så här:  `"args": ["list", "profiles"]` .
> 
> 

Låt oss börja med att lista våra profiler.

![Lista profiler](./media/cdn-app-dev-node/cdn-list-profiles.png)

Vi har tillbaka en tom matris.  Eftersom vi inte har några profiler i vår resurs grupp förväntas det.  Nu ska vi skapa en profil nu.

![Skapa profil](./media/cdn-app-dev-node/cdn-create-profile.png)

Nu ska vi lägga till en slut punkt.

![Skapa slut punkt](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Slutligen tar vi bort vår profil.

![Ta bort profil](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Nästa steg
[Hämta exemplet](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74)för att se det slutförda projektet från den här genom gången.

Om du vill se referensen för Azure CDN SDK för Node.js, se [referensen](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/).

Om du vill ha mer dokumentation om Azure SDK för Node.js, se den [fullständiga referensen](https://azure.github.io/azure-sdk-for-node/).

Hantera dina CDN-resurser med [PowerShell](cdn-manage-powershell.md).

