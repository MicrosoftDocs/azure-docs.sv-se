---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99215375"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>Konfigurera sensorns finger avtrycks leverantör

Vi börjar med att skapa och konfigurera en sensor finger avtrycks leverantör. Sensorns finger avtrycks leverantör tar hand om att läsa de plattformsspecifika sensorer på enheten och konvertera sina läsningar till en gemensam åter givning som används av sessionen för moln rums ankare.

> [!IMPORTANT]
> [Se till att kontrol lera detta](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability) om sensorer som du aktiverar är tillgängliga på din plattform.