---
title: Skala media reserverade enheter (MRUs) CLI
description: Det här avsnittet visar hur du använder CLI för att skala medie bearbetning med Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 06c0c6333b84697415ef598d4c5e853d5c006f08
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870168"
---
# <a name="how-to-scale-media-reserved-units"></a>Skala enhets reserverade enheter

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln visar hur du skalar MRSs (Media reservered units) för snabbare kodning.

> [!WARNING]
> Kommandot fungerar inte längre för Media Services konton som skapats med 2020-05-01-versionen av API: et eller senare. För de här kontoren behövs inte längre enheter som systemet skalar upp och ned automatiskt baserat på belastningen. Om du inte ser alternativet för att hantera MRUs i Azure Portal använder du ett konto som har skapats med 2020-05-01-API: et eller senare.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett Media Services-konto](./create-account-howto.md).

Förstå [reserverade enheter för media](concept-media-reserved-units.md).

## <a name="scale-media-reserved-units-with-cli"></a>Skala medie reserverade enheter med CLI

Kör kommandot `mru`.

Följande [AZ AMS konto MRU](/cli/azure/ams/account/mru) -kommando anger medie reserverade enheter på "amsaccount"-kontot med hjälp av **Count** -och **Type** -parametrarna.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet minuter som de reserverade enheterna är etablerade i ditt konto. Detta inträffar oberoende av om det finns jobb som körs i ditt konto. En detaljerad förklaring finns i avsnittet Vanliga frågor och svar på sidan [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/) .   

## <a name="next-step"></a>Nästa steg

[Analysera videor](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Se även

* [Kvoter och begränsningar](limits-quotas-constraints.md)
