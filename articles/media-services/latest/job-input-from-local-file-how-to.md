---
title: Skapa ett jobb indata från en lokal fil
description: Den här artikeln visar hur du skapar en Azure Media Services jobb indata från en lokal fil.
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
ms.openlocfilehash: 6fe1274c6f659f97e528e788116949fce60e6020
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960077"
---
# <a name="create-a-job-input-from-a-local-file"></a>Skapa ett jobb indata från en lokal fil

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

När du skickar in jobb för att bearbeta videor i Media Services v3 måste du informera Media Services om var indatavideo finns. Indataporten kan lagras som en medie tjänst till gång, i vilket fall du skapar en indata-till gång baserat på en fil (lagras lokalt eller i Azure Blob Storage). I det här avsnittet visas hur du skapar ett jobb indata från en lokal fil. Ett fullständigt exempel finns i det här [GitHub-exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Förutsättningar

* [Skapa ett Media Services-konto](./account-create-how-to.md).

## <a name="net-sample"></a>.NET-exempel

Följande kod visar hur du skapar en inmatad till gång och använder den som inmatad för jobbet. Funktionen CreateInputAsset utför följande åtgärder:

* Skapar till gången
* Hämtar en skrivbar [SAS-URL](../../storage/common/storage-sas-overview.md) till till gångens [behållare i lagringen](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)
* Laddar upp filen till containern i lagringen med hjälp av SAS-URL:en

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Följande kodfragment skapar en output-till gång om det inte redan finns:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Följande kodfragment skickar ett kodnings jobb:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Jobbfelkoder

Se [Felkoder](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nästa steg

[Skapa ett jobb inmatat från en HTTPS-URL](job-input-from-http-how-to.md).
