---
title: Skapa ett jobb inmatat från en HTTPS-URL
description: Det här avsnittet visar hur du skapar en Azure Media Services jobb inmatat från en HTTPS-URL.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1073a13f477894e1b35d732fd1cd6191747a62a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955995"
---
# <a name="create-a-job-input-from-an-https-url"></a>Skapa ett jobb inmatat från en HTTPS-URL

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

När du skickar in jobb för att bearbeta videor i Media Services v3 måste du informera Media Services om var indatavideo finns. Ett alternativ är att ange en HTTPS-URL som jobbindata (vilket visas i det här exemplet). Observera att AMS v3 för närvarande inte stöder segmentvis överföringskodning över HTTPS-URL:er. Ett fullständigt exempel finns i det här [GitHub-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Innan du börjar utveckla bör du läsa [utveckla med Media Services v3-API: er](media-services-apis-overview.md) (innehåller information om hur du kommer åt API: er, namngivnings konventioner osv.)

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar ett jobb med en HTTPS URL-inmatare.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nästa steg

[Skapa ett jobb indata från en lokal fil](job-input-from-local-file-how-to.md).
