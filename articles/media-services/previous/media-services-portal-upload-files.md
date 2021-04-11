---
title: Ladda upp filer till ett Media Services-konto på Azure-portalen | Microsoft Docs
description: Den här självstudiekursen beskriver steg för steg hur du laddar upp filer till ett Media Services-konto på Azure-portalen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a0e4b7e759e27dafb1d847dc5d7ce464b440e98c
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960681"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Ladda upp filer till ett Media Services-konto på Azure-portalen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. Information om hur du överför filer med portalen finns i [använda portalen för att ladda upp, koda och strömma innehåll](../latest/asset-create-asset-upload-portal-quickstart.md).<br/>Kolla också: [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

I Azure Media Services laddar du upp digitala filer till en tillgång. Tillgången kan innehålla video, ljud, bilder, samlingar med miniatyrbilder, textspår och filer med dold textning (samt metadata för dessa filer). När filerna har laddats upp lagras innehållet på ett säkert sätt i molnet för vidare bearbetning och strömning.

Media Services har en maximal filstorlek för bearbetning av filer. Mer information om storleksgränser för filer finns i avsnittet om [kvoter och begränsningar för Media Services](media-services-quotas-and-limitations.md).

Du behöver ett Azure-konto för att genomföra kursen. Mer information finns i [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="upload-files"></a>Ladda upp filer
1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. Välj **Inställningar**  >  **till gångar**. Välj sedan knappen **Ladda upp**.
   
    ![Ladda upp filer](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Fönstret **Överför en videotillgång** visas.
   
   > [!NOTE]
   > Media Services begränsar inte filstorleken för uppladdning av videor.
 
3. På datorn går du till den video som du vill ladda upp. Välj videon och klicka på **OK**.  
   
    Uppladdningen startar. Du kan följa förloppet under filnamnet.  

När uppladdningen är klar visas den nya tillgången i fönstret **Tillgångar**. 

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [kodar dina uppladdade tillgångar](media-services-portal-encode.md).

* Du kan också använda Azure Functions om du vill att ett kodningsjobb ska köras automatiskt när den konfigurerade containern tar emot en fil. Mer information finns i exemplet i [Media Services: Integrera Azure Media Services med Azure Functions och Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/).
