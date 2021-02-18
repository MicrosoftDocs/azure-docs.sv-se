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
ms.openlocfilehash: 9628e46281e267bd1c1fd8277a3a975bc338c6dc
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096054"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Ansluta till Media Services v3 API – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln visar hur du ansluter till Azure Media Services v3-node.js SDK med hjälp av inloggnings metoden för tjänstens huvud namn.

## <a name="prerequisites"></a>Förutsättningar

- Installera [Node.js](https://nodejs.org/en/download/).
- Installera [typescript](https://www.typescriptlang.org/download).
- [Skapa ett Media Services-konto](./create-account-howto.md). Glöm inte att komma ihåg resurs gruppens namn och namnet på Media Services kontot.

> [!IMPORTANT]
> Granska Azure Media Services [namngivnings konventioner](media-services-apis-overview.md#naming-conventions) för att förstå de viktiga namngivnings begränsningarna för entiteter. 

## <a name="reference-documentation-for-azurearm-mediaservices"></a>Referens dokumentation för @Azure/arm-mediaservices
- [Referens dokumentation för Azure Media Services-moduler för Node.js](https://docs.microsoft.com/javascript/api/overview/azure/media-services?view=azure-node-latest)

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Mer dokumentation om utvecklare för Node.js på Azure
- [Azure för Java Script & Node.js-utvecklare](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services käll kod i @azure/azure-sdk-for-js git Hub-lagrings platsen](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentation om Azure-paket för Node.js utvecklare](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="install-the-packages"></a>Installera paketen

1. Skapa en package.jspå en fil med hjälp av din favorit redigerare.
1. Öppna filen och klistra in följande kod:

   Se till att hämta den senaste versionen av [AZUREMEDIASERVICES SDK för Java Script](https://www.npmjs.com/package/@azure/arm-mediaservices).

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

Följande paket ska anges:

|Paket|Beskrivning|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK. <br/>Kontrol lera att du använder det senaste Azure Media Servicess paketet genom att kontrol lera [NPM install @azure/arm-mediaservices ](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Krävs för AAD-autentisering med tjänstens huvud namn eller hanterad identitet|
|`@azure/storage-blob`|Storage SDK. Används vid överföring av filer till till gångar.|
|`@azure/ms-rest-js`| Används för att logga in.|
|`@azure/storage-blob` | Används för att ladda upp och ladda ned filer till till gångar i Azure Media Services för kodning.|
|`@azure/abort-controller`| Används tillsammans med lagrings klienten för att ta bort tids krävande nedladdnings åtgärder|


Du kan köra följande kommando för att kontrol lera att du använder det senaste paketet:

### <a name="install-azurearm-mediaservices"></a>Installationer @azure/arm-mediaservices
```
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Installationer @azure/ms-rest-nodeauth

Installera den lägsta versionen av " @azure/ms-rest-nodeauth ": "^ 3.0.0".

```
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Ansluta till Node.js-klienten med TypeScript

1. Skapa en TypeScript. TS-fil med hjälp av din favorit redigerare.
1. Öppna filen och klistra in följande kod.
1. Skapa en. kuvert-fil och fyll i informationen från Azure Portal. Se [API: er för åtkomst](./access-api-howto.md).

### <a name="sample-env-file"></a>Exempel på. kuvert-fil
```
# copy the content of this file to a file named ".env". It should be stored at the root of the repo.
# The values can be obtained from the API Access page for your Media Services account in the portal.
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

## <a name="typescript---hello-world---list-assets"></a>Typescript-Hello World-list till gångar
Det här exemplet visar hur du ansluter till den Media Services klienten med ett huvud namn för tjänsten och list till gångar i kontot. Om du använder ett nytt konto, kommer listan att vara tom. Du kan ladda upp några till gångar i portalen för att se resultatet.

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

## <a name="run-the-sample-application-helloworld-listassets"></a>Kör exempel programmet HelloWorld-ListAssets

Klona lagrings platsen för Node.js exempel

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

Ändra katalog till mappen AMSv3Samples
```bash
cd AMSv3Samples
```

Installera de paket som används i packages.jspå
```
npm install 
```

Ändra katalog till mappen HelloWorld-ListAssets
```bash
cd HelloWorld-ListAssets
```

Starta Visual Studio Code från mappen AMSv3Samples Detta krävs för att starta från mappen där mappen ". VSCode" och tsconfig.jspå filer finns
```dotnetcli
cd ..
code .
```

Öppna mappen för HelloWorld-ListAssets och öppna index. TS-filen i Visual Studio Code Editor.
När du är i index. TS-filen trycker du på F5 för att starta fel söknings programmet. Du bör se en lista över till gångar som visas om du redan har till gångar i kontot. Om kontot är tomt visas en tom lista.  Ladda upp några till gångar i portalen för att se resultatet.

## <a name="more-samples"></a>Fler exempel

Följande exempel är tillgängliga i [lagrings platsen](https://github.com/Azure-Samples/media-services-v3-node-tutorials)

|Projektnamn|Användningsfall|
|---|---|
|Live/index. TS| Exempel på en vanlig direkt uppspelning. **Varning**, kontrol lera att alla resurser har rensats och inte längre faktureras i portalen när du använder Live|
|StreamFilesSample/index. TS| Basic-exempel för att ladda upp en lokal fil eller kodning från en käll-URL. Exempel visar hur du använder Storage SDK för att ladda ned innehåll och visar hur du strömmar till en spelare |
|StreamFilesWithDRMSample/index. TS| Visar hur du kodar och strömmar med Widevine och PlayReady DRM |
|VideoIndexerSample/index. TS| Exempel på hur du använder video-och ljud analys för hands inställningar för att generera metadata och insikter från en video-eller ljudfil |

## <a name="see-also"></a>Se även

- [Media Services begrepp](concepts-overview.md)
- [NPM-installation @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure för Java Script & Node.js-utvecklare](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services käll kod i @azure/azure-sdk-for-js lagrings platsen](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Nästa steg

Utforska Media Services [Node.js-referensdokumentationen](/javascript/api/overview/azure/mediaservices/management) och titta på de [exempel](https://github.com/Azure-Samples/media-services-v3-node-tutorials) som visar hur du använder Media Services API med node.js.
