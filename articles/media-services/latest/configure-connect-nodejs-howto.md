---
title: Ansluta till Azure Media Services v3 API – Node.js
description: Den här artikeln visar hur du ansluter till Media Services v3-API med Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 33d84ca86ac3cd4696dce3797b015b861884182a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216436"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Ansluta till Media Services v3 API – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln visar hur du ansluter till Azure Media Services v3-node.js SDK med hjälp av inloggnings metoden för tjänstens huvud namn. Du kommer att arbeta med filer i exempel databasen *Media-Services-v3-Node-självstudier* . Exemplet *HelloWorld-ListAssets* innehåller koden för att ansluta och sedan lista till gångar i kontot.

## <a name="prerequisites"></a>Förutsättningar

- En installation av Visual Studio Code.
- Installera [Node.js](https://nodejs.org/en/download/).
- Installera [typescript](https://www.typescriptlang.org/download).
- [Skapa ett Media Services-konto](./create-account-howto.md). Glöm inte att komma ihåg resurs gruppens namn och namnet på Media Services kontot.
- Skapa ett huvud namn för tjänsten för ditt program. Se [API: er för åtkomst](./access-api-howto.md).<br/>**Pro-tips!** Behåll fönstret öppet eller kopiera allt på JSON-fliken till anteckningar. 
- Se till att hämta den senaste versionen av [AZUREMEDIASERVICES SDK för Java Script](https://www.npmjs.com/package/@azure/arm-mediaservices).

> [!IMPORTANT]
> Granska Azure Media Services [namngivnings konventioner](media-services-apis-overview.md#naming-conventions) för att förstå de viktiga namngivnings begränsningarna för entiteter.

## <a name="clone-the-nodejs-samples-repo"></a>Klona Node.JS-exempel lagrings platsen

Du kommer att arbeta med några filer i Azure-exempel. Klona lagrings platsen för Node.JS-exempel.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-packages"></a>Installera paketen

### <a name="install-azurearm-mediaservices"></a>Installationer @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Installationer @azure/ms-rest-nodeauth

Installera den lägsta versionen av " @azure/ms-rest-nodeauth ": "^ 3.0.0".

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

I det här exemplet ska du använda följande paket i `package.json` filen.

|Paket|Beskrivning|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK. <br/>Kontrol lera att du använder det senaste Azure Media Servicess paketet genom att kontrol lera [NPM install @azure/arm-mediaservices ](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Krävs för AAD-autentisering med tjänstens huvud namn eller hanterad identitet|
|`@azure/storage-blob`|Storage SDK. Används vid överföring av filer till till gångar.|
|`@azure/ms-rest-js`| Används för att logga in.|
|`@azure/storage-blob` | Används för att ladda upp och ladda ned filer till till gångar i Azure Media Services för kodning.|
|`@azure/abort-controller`| Används tillsammans med lagrings klienten för att ta bort tids krävande nedladdnings åtgärder|

### <a name="create-the-packagejson-file"></a>Skapa package.jsfilen

1. Skapa en `package.json` fil med hjälp av din favorit redigerare.
1. Öppna filen och klistra in följande kod:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Ansluta till Node.js-klienten med TypeScript



### <a name="sample-env-file"></a>Exempel på *. kuvert* -fil

Kopiera innehållet i den här filen till en fil med namnet *. kuvert*. Den ska lagras i roten på din arbets plats. Detta är de värden som du fick från sidan API-åtkomst för ditt Media Services-konto i portalen.

När du har skapat *. kuvert* -filen kan du börja arbeta med exemplen.

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>Kör exempel programmet *HelloWorld-ListAssets*

1. Ändra katalog till mappen *AMSv3Samples*

```bash
cd AMSv3Samples
```

2. Installera de paket som används i *packages.jspå* filen.

```
npm install 
```

3. Ändra katalogen till mappen *HelloWorld-ListAssets* .

```bash
cd HelloWorld-ListAssets
```

4. Starta Visual Studio Code från mappen AMSv3Samples Detta krävs för att starta från mappen där mappen ". VSCode" och tsconfig.jspå filer finns

```dotnetcli
cd ..
code .
```

Öppna mappen för *HelloWorld-ListAssets* och öppna *index. TS* -filen i Visual Studio Code Editor.

När du är i *index. TS* -filen trycker du på F5 för att starta fel söknings programmet. Du bör se en lista över till gångar som visas om du redan har till gångar i kontot. Om kontot är tomt visas en tom lista.  

Om du snabbt vill se de till gångar som visas använder du portalen för att ladda upp några videofiler. Till gångar skapas automatiskt var och en när du kör skriptet igen och returnerar sedan deras namn.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>Titta närmare på exemplet *HelloWorld-ListAssets*

Exemplet *HelloWorld-ListAssets* visar hur du ansluter till den Media Services klienten med ett huvud namn för tjänsten och en list till gångar i kontot. Se kommentarerna i koden om du vill ha en detaljerad förklaring av vad det gör.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>Fler exempel

Följande exempel är tillgängliga i [lagrings platsen](https://github.com/Azure-Samples/media-services-v3-node-tutorials)

|Projektnamn|Användningsfall|
|---|---|
|Live/index. TS| Exempel på en vanlig direkt uppspelning. **Varning**, kontrol lera att alla resurser har rensats och inte längre faktureras i portalen när du använder Live|
|StreamFilesSample/index. TS| Basic-exempel för att ladda upp en lokal fil eller kodning från en käll-URL. Exempel visar hur du använder Storage SDK för att ladda ned innehåll och visar hur du strömmar till en spelare |
|StreamFilesWithDRMSample/index. TS| Visar hur du kodar och strömmar med Widevine och PlayReady DRM |
|VideoIndexerSample/index. TS| Exempel på hur du använder video-och ljud analys för hands inställningar för att generera metadata och insikter från en video-eller ljudfil |

## <a name="see-also"></a>Se även

- [Referens dokumentation för Azure Media Services-moduler för Node.js](/javascript/api/overview/azure/media-services)
- [Azure för Java Script & Node.js-utvecklare](/azure/developer/javascript/)
- [Media Services käll kod i @azure/azure-sdk-for-js git Hub-lagrings platsen](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentation om Azure-paket för Node.js utvecklare](/javascript/api/overview/azure/)
- [Media Services begrepp](concepts-overview.md)
- [NPM-installation @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure för Java Script & Node.js-utvecklare](/azure/developer/javascript/)
- [Media Services käll kod i @azure/azure-sdk-for-js lagrings platsen](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Nästa steg

Utforska Media Services [Node.js-referensdokumentationen](/javascript/api/overview/azure/mediaservices/management) och titta på de [exempel](https://github.com/Azure-Samples/media-services-v3-node-tutorials) som visar hur du använder Media Services API med node.js.
