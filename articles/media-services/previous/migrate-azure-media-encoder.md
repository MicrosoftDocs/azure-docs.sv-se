---
title: Migrera från Azure Media Encoder till Media Encoder Standard | Microsoft Docs
description: I det här avsnittet beskrivs hur du migrerar från Azure Media Encoder till Media Encoder Standard medie processor.
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
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f528cea9b475c0158524ad9b46623a78df5761d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016213"
---
# <a name="migrate-from-azure-media-encoder-to-media-encoder-standard"></a>Migrera från Azure Media Encoder till Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Den här artikeln beskriver stegen för att migrera från den äldre Azure Media Encoder (amn) medie processorn (som dras tillbaka) till Media Encoder Standard medie processor. Se det här avsnittet om [äldre komponenter](legacy-components.md) för datum för indragningen.

När du kodar filer med amn använder kunderna vanligt vis en namngiven förinställd sträng, till exempel `H264 Adaptive Bitrate MP4 Set 1080p` . För att du ska kunna migrera måste koden uppdateras för att använda **Media Encoder Standard** medie processor i stället för amn och en av motsvarande [system för inställningar](media-services-mes-presets-overview.md) som `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Migrera till Media Encoder Standard

Här är ett typiskt C#-kod exempel som använder den äldre medie processorn. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("AME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    " H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Här är den uppdaterade versionen som använder Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Avancerade scenarier 

Om du har skapat din egen kodnings för inställning för amn med dess schema, finns det ett [motsvarande schema för Media Encoder Standard](media-services-mes-schema.md). Om du har frågor om hur du mappar de äldre inställningarna till den nya kodaren kan du kontakta oss via mailto:amshelp@microsoft.com  
## <a name="known-differences"></a>Kända skillnader 

Media Encoder Standard är stabilare, tillförlitlig, har bättre prestanda och ger bättre kvalitet på utdata än den äldre AMNs koden. Dessutom gäller följande: 

* Media Encoder Standard producerar utdatafiler med en annan namngivnings konvention än amn.
* Media Encoder Standard skapar artefakter, till exempel filer som innehåller metadata för [indatafilen](media-services-input-metadata-schema.md) och [metadata för utdatafilen](media-services-output-metadata-schema.md).

## <a name="next-steps"></a>Nästa steg

* [Äldre komponenter](legacy-components.md)
* [Prissättningssidan](https://azure.microsoft.com/pricing/details/media-services/#encoding)
