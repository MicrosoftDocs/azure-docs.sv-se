---
title: Konfigurera PostMan för Azure Media Services REST API-anrop
description: I den här artikeln beskrivs hur du konfigurerar Postman för Media Services REST API samtal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: ef92e772085b1b89388c3f85fb3fdb91df0f6a75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012215"
---
# <a name="configure-postman-for-media-services-v2-rest-api-calls"></a>Konfigurera PostMan för Media Services v2 REST API anrop

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Den här självstudien visar hur du konfigurerar **Postman** så att den kan användas för att anropa Azure Media Services REST-API: er för (AMS). I självstudien visas hur du importerar miljö-och samlings filer till **Postman**. Samlingen innehåller grupperade definitioner av HTTP-begäranden som anropar Azure Media Services (AMS) REST-API: er. Miljöfilen innehåller variabler som används av samlingen.

Den här miljön och samlingen används i artiklar som visar hur du uppnår olika uppgifter med Azure Media Services REST-API: er.

## <a name="prerequisites"></a>Förutsättningar

- Installera [Postman](https://www.getpostman.com/) REST-klienten för att köra REST API:er som visas i några AMS REST-självstudierna. 

    Vi använder **Postman** men ett REST-verktyg skulle vara lämpligt. Andra alternativ är: **Visual Studio Code** med rest-plugin-programmet eller **Telerik-Fiddler**. 

## <a name="configure-the-environment"></a>Konfigurera miljön 

1. Skapa en. JSON-fil som innehåller miljövariabler som används i AMS-självstudier. Namnge filen (till exempel **AzureMediaServices.postman_environment.jspå**). Öppna filen och klistra in koden som definierar Postman-miljön från [den här kod listan](postman-environment.md). 
2. Öppna **Postman**.
3. På höger sida om skärmen, väljer du alternativet **Hantera miljö**.

    ![Skärm bild som visar alternativet hantera miljö valt.](./media/media-services-rest-upload-files/postman-create-env.png)
4. Från dialogrutan **Hantera miljö**, klickar du på **Importera**.
5. Bläddra och välj **AzureMediaServices.postman_environment.jspå** filen.
6. **AzureMedia** -miljön har lagts till.
7. Stäng dialogrutan.
8. Välj **AzureMedia** -miljön.

    ![Skärm bild som visar AzureMedia-miljön som valts.](./media/media-services-rest-upload-files/postman-choose-env.png)

## <a name="configure-the-collection"></a>Konfigurera samlingen

1. Skapa en. JSON-fil som innehåller **Postman** -samlingen med alla åtgärder som behövs för att ladda upp en fil till Media Services. Namnge filen (till exempel **AzureMediaServicesOperations.postman_collection.jspå**). Öppna filen och klistra in koden som definierar **Postman** -samlingen från [den här kod listan](postman-collection.md).
2. Klicka på **Importera** för att importera samlingsfilen.
3. Välj **AzureMediaServicesOperations.postman_collection.jspå** filen.

    ![Skärm bild som visar dialog rutan importera med Välj valda filer.](./media/media-services-rest-upload-files/postman-import-collection.png)

## <a name="next-steps"></a>Nästa steg

Ta en titt på artikeln [upload assets](media-services-rest-upload-files.md) .  
