---
title: Skala media reserverade enheter (MRUs) CLI
description: Det här avsnittet visar hur du använder CLI för att skala medie bearbetning med Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: 98e87cf9d1f46ddb8ee1d433bd0b0ba8806fac89
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091926"
---
# <a name="how-to-scale-media-reserved-units"></a>Skala enhets reserverade enheter

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln visar hur du skalar MRSs (Media reservered units) för snabbare kodning.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett Media Services-konto](./create-account-howto.md).

Förstå [reserverade enheter för media](concept-media-reserved-units.md).

## <a name="scale-media-reserved-units-with-cli"></a>Skala medie reserverade enheter med CLI

Kör kommandot `mru`.

Följande [AZ AMS konto MRU](/cli/azure/ams/account/mru?view=azure-cli-latest) -kommando anger medie reserverade enheter på "amsaccount"-kontot med hjälp av **Count** -och **Type** -parametrarna.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet minuter som de reserverade enheterna är etablerade i ditt konto. Detta inträffar oberoende av om det finns jobb som körs i ditt konto. En detaljerad förklaring finns i avsnittet Vanliga frågor och svar på sidan [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-step"></a>Nästa steg

[Analysera videor](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Se även

* [Kvoter och begränsningar](limits-quotas-constraints.md)
