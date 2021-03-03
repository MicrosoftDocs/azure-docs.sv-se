---
title: Koda och strömma videofiler med Node.JS
description: Strömma videofiler med Node.JS. Följ stegen i den här självstudien för att skapa ett nytt Azure Media Services konto, koda en fil och strömma den till Azure Media Player.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Azure Media Services, Stream, Node.JS
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: 566a7a978a08bf7e37b6c4ab8b0609222b34e549
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724531"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Koda och strömma videofiler med Node.JS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här snabbstarten visar hur lätt det är att koda och börja strömma video på en mängd olika webbläsare och enheter med Azure Media Services. En indatafil kan anges med hjälp av HTTPS-URL: er, SAS-URL: er eller sökvägar till filer som finns i Azure Blob Storage.

I slutet av den här snabb starten får du veta följande:

- Så här kodar du med Node.JS
- Strömma med Node.JS
- Ladda upp en fil från en HTTPS-URL med Node.JS
- Använda en HLS-eller tank strecks klient spelare med Node.JS

Exemplet i den här artikeln kodar innehåll som du gör tillgängliga via en HTTPS-URL. Observera att AMS v3 för närvarande inte stöder segmentvis överföringskodning över HTTPS-URL:er.

![Spela upp videon](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Installera [Node.js](https://nodejs.org/en/download/)
- [Skapa ett Media Services-konto](./create-account-howto.md).<br/>Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot.
- Följ stegen i [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du behöver använda dem för att få åtkomst till API.
- Gå igenom avsnittet [Konfigurera och Anslut med Node.js](./configure-connect-nodejs-howto.md) hur du först ska förstå hur du använder Node.js-klient-SDK: n

## <a name="download-and-configure-the-sample"></a>Ladda ned och konfigurera exemplet

Klona en GitHub-lagringsplats som innehåller det strömmande Node.js exemplet till din dator med hjälp av följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Exemplet finns i mappen [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample)

Öppna [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) i det nedladdade projektet. Uppdatera *exempel-. kuvert* -filen i rotmappen med de värden och autentiseringsuppgifter som du har fått från att [komma åt API: er](./access-api-howto.md). Byt namn på filen *Sample. kuvert* till *. kuvert* (Ja, bara tillägget).

Exemplet utför följande åtgärder:

1. Skapar en **transformering** med en [Content medveten encoding-förinställning](./content-aware-encoding.md). Först kontrol leras om den angivna transformeringen finns.
1. Skapar en utgående **till gång** som används av kodnings **jobbet** för att innehålla utdata
1. Överför valfritt en lokal fil med hjälp av Storage BLOB SDK
1. Skapar **jobb** indata som baseras på en HTTPS-URL eller en uppladdad fil
1. Skickar kodnings **jobbet** med hjälp av indata och utdata som skapades tidigare
1. Kontrollerar jobbets status
1. Laddar ned utdata från kodnings jobbet till en lokal mapp
1. Skapar en **strömmande positionerare** som ska användas i spelaren
1. Skapar strömnings-URL: er för HLS och bindestreck
1. Spelar upp innehållet i ett Player-program – Azure Media Player

## <a name="run-the-sample"></a>Kör exemplet

1. Programmet laddar ned kodade filer. Skapa en mapp där du vill att utdatafilerna ska placeras och uppdatera värdet för variabeln **outputFolder** i filen [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L59) . Den är inställd på "temp" som standard.
1. Öppna en **kommando tolk** och bläddra till exempel katalogen.
1. Ändra katalogen till mappen AMSv3Samples

    ```bash
    cd AMSv3Samples
    ```

1. Installera de paket som används i *packages.jspå* filen.

    ```bash
    npm install 
    ```

1. Ändra katalogen till mappen *StreamFilesSample*

    ```bash
    cd StreamFilesSample
    ```

1. Starta Visual Studio Code från mappen *AMSv3Samples* (Detta krävs för att starta från mappen där *. VSCode* -mappen och *tsconfig.jspå* filer finns.)

    ```bash
    cd ..
    code .
    ```

Öppna mappen för *StreamFilesSample* och öppna *index. TS* -filen i Visual Studio Code Editor.
När du är i *index. TS* -filen trycker du på F5 för att starta fel söknings programmet.

## <a name="test-with-azure-media-player"></a>Testa med Azure Media Player

Använd Azure Media Player för att testa data strömmen. Du kan också använda en HLS-eller tank korts kompatibel spelare, t. ex. Shaka Player, HLS.js, Dash.js eller andra.

Du bör kunna Klicka på länken som genererades i exemplet och starta AMP Player med BINDESTRECKs manifestet som redan har lästs in.

> [!NOTE]
> Om en spelare finns på en HTTPS-webbplats uppdaterar du URL:en till ”HTTPS”.

1. Öppna en webbläsare och gå till [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. I rutan **URL:** klistrar du in en av de strömmande URL-värdena som du fick när du körde programmet. Du kan klistra in URL: en i HLS, tank streck eller smidig formatering och Azure Media Player växlar till ett lämpligt strömnings protokoll för uppspelning på enheten automatiskt.
3. Tryck på **Uppdatera spelare**.

Azure Media Player kan användas vid testning, men bör inte användas i en produktionsmiljö.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen om du inte längre behöver någon av resurserna i resurs gruppen, inklusive Media Services-och lagrings konton som du skapade för den här självstudien.

Kör följande CLI-kommando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Mer dokumentation om utvecklare för Node.js på Azure

- [Azure för Java Script & Node.js-utvecklare](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services käll kod i @azure/azure-sdk-for-js git Hub-lagrings platsen](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentation om Azure-paket för Node.js utvecklare](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="see-also"></a>Se även

- [Felkoder för jobb](/rest/api/media/jobs/get#joberrorcode).
- [NPM-installation @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure för Java Script & Node.js-utvecklare](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services käll kod i @azure/azure-sdk-for-js lagrings platsen](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Nästa steg

> [Media Services begrepp](concepts-overview.md)
