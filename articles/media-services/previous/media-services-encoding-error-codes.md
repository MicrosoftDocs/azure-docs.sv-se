---
title: Fel koder för Azure Media Services kodning | Microsoft Docs
description: Det här avsnittet innehåller felkoder som kan returneras om ett fel påträffades under kodnings körningen..
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 7e6848fb49dd63fa67a639d09754a28dd5953a32
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013490"
---
# <a name="encoding-error-codes"></a>Felkoder vid kodning

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

I följande tabell visas felkoder som kan returneras om ett fel påträffades under kodnings körningen.  Om du vill ha fel information i .NET-koden använder du klassen [ErrorDetails](/previous-versions/azure/jj126075(v=azure.100)) . Använd [ErrorDetail](/rest/api/media/operations/errordetail) -REST API för att få fel information i din rest-kod.

| ErrorDetail. Code | Möjliga orsaker till felet |
| --- | --- |
| Okänt |Ett okänt fel uppstod när uppgiften kördes |
| ErrorDownloadingInputAssetMalformedContent |Kategori av fel som täcker fel vid hämtning av indata till gångar, t. ex. Felaktiga fil namn, filer med nollängd, felaktiga format och så vidare. |
| ErrorDownloadingInputAssetServiceFailure |Kategori av fel som täcker problem på tjänst sidan, t. ex. nätverks-eller lagrings fel vid hämtning. |
| ErrorParsingConfiguration |Kategori av fel där Task \<see cref="MediaTask.PrivateData"/> (konfiguration) inte är giltig, till exempel om konfigurationen inte är en giltig system för inställning eller om den innehåller ogiltig XML. |
| ErrorExecutingTaskMalformedContent |Fel kategori under körningen av aktiviteten där problem i indatafiler orsakar fel. |
| ErrorExecutingTaskUnsupportedFormat |Kategori av fel där medie processorn inte kan bearbeta de angivna filerna, medie formatet stöds inte eller matchar inte konfigurationen. Du kan till exempel försöka skapa en ljud signal från en till gång som bara har video |
| ErrorProcessingTask |Kategori av andra fel som medie processorn påträffar under bearbetningen av uppgiften som inte är relaterade till innehåll. |
| ErrorUploadingOutputAsset |Kategori av fel vid uppladdning av utgående till gång |
| ErrorCancelingTask |Kategori av fel som ska gälla fel vid försök att avbryta uppgiften |
| TransientError |Kategori av fel som ska gälla för tillfälliga problem (t. ex. tillfälligt nätverks problem med Azure Storage) |

Öppna ett [support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)för att få hjälp från **Media Servicess** teamet.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Relaterade artiklar
* [Utföra avancerade kodnings uppgifter genom att anpassa Media Encoder Standard för inställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Kvoter och begränsningar](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
