---
title: Hantera hastighet och samtidighet för din kodning med Azure Media Services | Microsoft Docs
description: Den här artikeln ger en kort översikt över hur du kan hantera hastighet och samtidighet för dina kodnings jobb/uppgifter med Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f01235e5b02bf2a61a359fa5571c6e6d575c1232
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014816"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>Hantera hastighet och samtidighet för kodningen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Den här artikeln ger en kort översikt över hur du kan hantera hastighet och samtidighet för dina kodnings jobb/uppgifter.

## <a name="overview"></a>Översikt

I Media Services fastställer en **reserverad enhets typ** den hastighet som dina medie bearbetnings uppgifter bearbetas med. Du kan välja mellan följande typer av reserverade enheter: **S1**, **S2** och **S3**. Samma kodningsjobb körs till exempel snabbare om du använder typen **S2** än om du använder typen **S1**. Avsnittet [skalnings kodnings enheter](media-services-scale-media-processing-overview.md) visar en tabell som hjälper dig att fatta beslut när du väljer mellan olika kodnings hastigheter.

Förutom att ange typ av reserverad enhet kan du ange för att etablera ditt konto med **reserverade enheter**. Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto. Om ditt konto till exempel har fem reserverade enheter körs fem medie uppgifter samtidigt så länge det finns aktiviteter som ska bearbetas. Återstående aktiviteter väntar i kön och hämtas för bearbetning i turordning när en aktivitet som körs slutförs. Om ett konto inte har några reserverade enheter, kommer uppgifter att hämtas sekventiellt. I det här fallet beror vänte tiden mellan en aktivitet och en start beroende på tillgängligheten för resurser i systemet.

Detaljerad information och exempel som visar hur du skalar kodnings enheter finns i [det här](media-services-scale-media-processing-overview.md) avsnittet.

## <a name="next-step"></a>Nästa steg

[Skala kodnings enheter](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

