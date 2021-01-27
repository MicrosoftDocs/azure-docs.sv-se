---
title: Strömma videofiler med Azure Media Services-Node.js
description: Följ stegen i den här självstudien för att skapa ett nytt Azure Media Services konto, koda en fil och strömma den till Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, strömma
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 711db5135345fd9b69da9aaea5bc4dbb929d27c9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895143"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Självstudie: koda en fjärrfil baserat på URL och strömma video-Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här självstudien visar hur enkelt det är att koda och börja strömma videor på en rad olika webbläsare och enheter med hjälp av Azure Media Services. Ett indatainnehåll kan anges med HTTP-URL:er, SAS-URL:er eller sökvägar till filer i Azure Blob Storage.

Exemplet i den här artikeln kodar innehåll som du gör tillgängliga via en HTTPS-URL. Observera att AMS v3 för närvarande inte stöder segmentvis överföringskodning över HTTPS-URL:er.

I slutet av självstudien kommer du att kunna strömma en video.  

![Spela upp videon](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Installera [Node.js](https://nodejs.org/en/download/)
- [Skapa ett Media Services-konto](./create-account-howto.md).<br/>Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot.
- Följ stegen i [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du behöver använda dem för att få åtkomst till API.

## <a name="download-and-configure-the-sample"></a>Ladda ned och konfigurera exemplet

Klona en GitHub-lagringsplats som innehåller det strömmande Node.js exemplet till din dator med hjälp av följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

Exemplet finns i mappen [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample)

Öppna [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) i det nedladdade projektet. Ersätt `endpoint config` värdena med autentiseringsuppgifter som du har fått från att [komma åt API: er](./access-api-howto.md).

Exemplet utför följande åtgärder:

1. Skapar en **transformering** (kontrollerar först om den angivna transformeringen finns). 
2. Skapar en utgående **till gång** som används som kodnings **jobbets** utdata.
3. Skapar **jobbets** indata som baseras på en HTTPS-URL.
4. Skickar **kodningsjobbet** med de indata och utdata som skapades tidigare.
5. Kontrollerar jobbets status.
6. Skapa en **positionerare för direktuppspelning**.
7. Skapar strömnings-URL:er.

## <a name="run-the-sample-app"></a>Kör exempelappen

1. Appen laddar ned kodade filer. Skapa en mapp där du vill att utdatafilerna ska placeras och uppdatera värdet för variabeln **outputFolder** i [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) -filen.
1. Öppna **kommando tolken**, bläddra till exempel katalogen och kör följande kommandon.

    ```
    npm install 
    node index.js
    ```

När den är igång bör du se liknande utdata:

![Skärm bild av ett kommando fönster med utdata från StreamFileSample-exempel appen som visar URL: er för tre filer som hämtats till den lokala katalogen.](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Testa med Azure Media Player

I den här artikeln används Azure Media Player till att testa strömningen. 

> [!NOTE]
> Om en spelare finns på en HTTPS-webbplats uppdaterar du URL:en till ”HTTPS”.

1. Öppna en webbläsare och gå till [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. I rutan **URL:** klistrar du in ett av de strömmande URL-värden som du fick när du körde programmet. 
 
     Du kan klistra in URL:en i formatet HLS, Dash eller Smooth så växlar Azure Media Player automatiskt till ett lämpligt strömningsprotokoll för uppspelning på din enhet.
3. Tryck på **Uppdatera spelare**.

Azure Media Player kan användas vid testning, men bör inte användas i en produktionsmiljö. 

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen om du inte längre behöver någon av resurserna i resurs gruppen, inklusive Media Services-och lagrings konton som du skapade för den här självstudien.

Kör följande CLI-kommando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Se även

[Felkoder för jobb](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Media Services begrepp](concepts-overview.md)
