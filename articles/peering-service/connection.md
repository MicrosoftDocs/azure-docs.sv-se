---
title: Anslutning till Azure peering-tjänst
description: Läs mer om anslutning till Microsoft Azure peering-tjänsten
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0540fa9c7a29456aaf343adce83509b2d0e390e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91398928"
---
# <a name="peering-service-connection"></a>Peering service-anslutning

En anslutning refererar vanligt vis till en logisk informations uppsättning som identifierar en peering-tjänst. Den definieras genom att ange följande attribut:

- Logiskt namn
- Anslutnings partner
- Kundens fysiska plats
- IP-prefix

Kunden kan upprätta en enskild anslutning eller flera anslutningar som enligt kravet. En anslutning används också som en samlings enhet för telemetri. Om du till exempel vill välja aviseringar för telemetri måste kunden definiera den anslutning som ska övervakas.

> [!Note]
> När du registrerar dig för peering-tjänsten, analyseras Windows och Microsoft 365 telemetri för att ge dig svars tider för dina valda prefix.
>Mer information om telemetri för anslutningar finns i [anslutnings telemetri för peering-tjänsten](connection-telemetry.md).
>

>## <a name="how-to-register-a-connection"></a>Hur registrerar jag en anslutning?

**Scenario** – anta att ett avdelnings kontor sprids mellan olika geografiska platser som visas i bilden nedan. Här måste kunden tillhandahålla ett logiskt namn, ett namn på en tjänst leverantör (SP), kundens fysiska plats och IP-prefix som ägs av kunden eller allokeras av tjänst leverantören som är associerad med en enda anslutning. Den här processen måste upprepas för att registrera peering-tjänsten för separata geo-redundanta anslutningar.

![Geo-redundanta anslutningar](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> Tillstånds nivå – filtreringen beaktas för kundens fysiska plats när anslutningen är geo-placerad i USA.
>

## <a name="next-steps"></a>Nästa steg

Information om steg för steg hur du registrerar peering service-anslutning finns i [Registrera peering-tjänsten med hjälp av Azure Portal](azure-portal.md).

Om du vill veta mer om telemetri för peering service-anslutning läser du [telemetri för peering service-anslutning](connection-telemetry.md).

För att mäta telemetri, se [mått på telemetri för anslutning](measure-connection-telemetry.md).
