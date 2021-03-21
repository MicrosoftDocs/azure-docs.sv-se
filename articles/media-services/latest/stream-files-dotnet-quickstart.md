---
title: Strömma videofiler med Azure Media Services-.NET
description: Följ stegen i den här självstudien för att använda .NET för att skapa ett nytt Azure Media Services konto, koda en fil och strömma den till Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, strömma
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: dc6b240a2d97e0b4aa313f858b3965f241dd0b08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898196"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---net"></a>Självstudie: koda en fjärrfil baserat på URL och strömma videon – .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här självstudien visar hur enkelt det är att koda och börja strömma videor på en rad olika webbläsare och enheter med hjälp av Azure Media Services. Ett indatainnehåll kan anges med HTTP-URL:er, SAS-URL:er eller sökvägar till filer i Azure Blob Storage.
Exemplet i det här ämnet kodar innehåll som du gör tillgängligt via en HTTPS-URL. Observera att AMS v3 för närvarande inte stöder segmentvis överföringskodning över HTTPS-URL:er.

I slutet av självstudien kommer du att kunna strömma en video.  

![Spela upp videon](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har Visual Studio installerat kan du hämta [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15).
- [Skapa ett Media Services-konto](./create-account-howto.md).<br/>Se till att komma ihåg de värden som du använde för resursgruppens namn och namnet på Media Services-kontot.
- Följ stegen i [Access Azure Media Services API with the Azure CLI](./access-api-howto.md) (Få åtkomst till Azure Media Services-API med Azure CLI) och spara autentiseringsuppgifterna. Du behöver använda dem för att få åtkomst till API.

## <a name="download-and-configure-the-sample"></a>Ladda ned och konfigurera exemplet

Klona en GitHub-lagringsplats som innehåller det strömmande .NET-exemplet till din dator med följande kommando:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

Exemplet finns i mappen [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles).

Öppna [appsettings.jspå](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) i det nedladdade projektet. Ersätt värdena med autentiseringsuppgifterna som du fick från avsnittet om [åtkomst till API:er](./access-api-howto.md).

Exemplet utför följande åtgärder:

1. Skapar en **transformering** (kontrollerar först om den angivna transformeringen finns). 
2. Skapar en utgående **till gång** som används som kodnings **jobbets** utdata.
3. Skapar **jobbets** indata som baseras på en HTTPS-URL.
4. Skickar **kodningsjobbet** med de indata och utdata som skapades tidigare.
5. Kontrollerar jobbets status.
6. Skapa en **positionerare för direktuppspelning**.
7. Skapar strömnings-URL:er.

Du kan få beskrivningar av varje funktion i exemplet, undersöka koden och titta på kommentarer i [den här källfilen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="run-the-sample-app"></a>Kör exempelappen

När du kör appen visas URL:er som kan användas för uppspelning av video med olika protokoll. 

1. Tryck på Ctrl+F5 för att köra programmet *EncodeAndStreamFiles*.
2. Välj Apples **HLS**-protokoll (slutar med *manifest(format=m3u8-aapl)*) och kopiera strömnings-URL:en från konsolen.

![Skärm bild av utdata från EncodeAndStreamFiles-appen i Visual Studio som visar tre strömnings-URL: er som används i Azure Media Player.](./media/stream-files-tutorial-with-api/output.png)

I exemplets [källkod](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs), kan du se hur URL:en är byggd. För att skapa den, måste du sammanfoga strömningsslutpunktens värdnamn och sökvägen för strömningslokaliseraren.  

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

## <a name="examine-the-code"></a>Granska koden

Du kan få beskrivningar av varje funktion i exemplet, undersöka koden och titta på kommentarer i [den här källfilen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

I självstudien för att [ladda upp, koda och strömma filer](stream-files-tutorial-with-api.md) får du ett mer avancerat strömningsexempel med detaljerade förklaringar. 

### <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Flertrådsteknik

SDK:erna i Azure Media Services v3 är inte trådsäkra. När du arbetar med flertrådade program bör du skapa ett nytt AzureMediaServicesClient-objekt per tråd.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ladda upp, koda och strömma filer](stream-files-tutorial-with-api.md)
