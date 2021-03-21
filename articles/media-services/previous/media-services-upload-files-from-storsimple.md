---
title: Ladda upp filer till ett Azure Media Services-konto från Azure StorSimple | Microsoft Docs
description: I den här artikeln finns en kort översikt över Azure StorSimple Data Manager. Artikeln innehåller också länkar till självstudier om hur du kan extrahera data från StorSimple och ladda upp dem som tillgångar till ett Azure Media Services-konto.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0521904f0ed46b4c5309e5f9df980b1cd7d7d858
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009036"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Ladda upp filer till ett Azure Media Services-konto från Azure StorSimple 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)
>
> 
> Azure StorSimple Data Manager finns för närvarande som privat förhandsutgåva. 
> 

## <a name="overview"></a>Översikt

I Media Services överför du dina digitala filer till en tillgång. Till gången kan innehålla video, ljud, bilder, miniatyr samlingar, text spår och filer med dold textning (samt metadata om dessa filer.) När filerna har laddats upp lagras innehållet på ett säkert sätt i molnet för vidare bearbetning och strömning.

[Azure StorSimple](../../storsimple/index.yml) använder lagringsutrymmet i molnet som ett tillägg till den lokala lösningen och fördelar automatiskt data mellan det lokala lagringsutrymmet och lagringsutrymmet i molnet. StorSimple-enheten deduplicerar och komprimerar data innan du skickar dem till molnet, vilket gör det mycket effektivt för att skicka stora filer till molnet. Tjänsten [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md) innehåller API: er som gör det möjligt att extrahera data från StorSimple och presentera dem som AMS-tillgångar.

## <a name="get-started"></a>Kom igång

1. [Skapa ett Media Services-konto](media-services-portal-create-account.md) som du vill överföra tillgångarna till.
2. Registrera dig för förhandsutgåvan av Data Manager på det sätt som beskrivs i artikeln [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md).
3. Skapa ett StorSimple Data Manager-konto.
4. Skapa ett omvandlingsjobb som när det körs hämtar data från en StorSimple-enhet och överför dem till ett AMS-konto som tillgångar. 

    När jobbet börjar köras skapas en lagringskö. Den här kön fylls med meddelanden om transformerade blobbar i takt med att de är klara. Namnet på den här kön är detsamma som namnet på jobbdefinitionen. Du kan använda den här kön för att avgöra när tillgången är klar och anropa önskad Media Services-åtgärd som ska köras på den. Du kan till exempel använda den här kön för att utlösa en Azure-funktion som innehåller den nödvändiga Media Services-koden.

## <a name="see-also"></a>Se även

[Använd .NET SDK för att utlösa jobb i Data Manager](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg

Du kan nu koda överförda tillgångar. Mer information finns i [Koda tillgångar](media-services-portal-encode.md).
