---
title: Säkerhets rekommendationer för IoT Hub
description: Lär dig mer om begreppet säkerhets rekommendationer och hur de används i Defender för IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: 4ae479fcff0514b42d46aef455d8a32d6ebfaf23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636551"
---
# <a name="security-recommendations-for-iot-hub"></a>Säkerhets rekommendationer för IoT Hub

Defender för IoT genomsöker dina Azure-resurser och IoT-enheter och ger säkerhets rekommendationer för att minska risken för attacker.
Säkerhets rekommendationer är åtgärds bara och syftar till att hjälpa kunder att följa rekommenderade säkerhets metoder.

I den här artikeln hittar du en lista över rekommendationer som kan utlösas på IoT Hub.

## <a name="built-in-recommendations-in-iot-hub"></a>Inbyggda rekommendationer i IoT Hub

Rekommendations aviseringar ger insikter och förslag på åtgärder för att förbättra position säkerhets miljö.

| Allvarlighetsgrad | Name | Datakälla | Beskrivning |
|--|--|--|--|
| Högt | Identiska autentiseringsuppgifter för autentisering används av flera enheter | IoT Hub | IoT Hub autentiseringsuppgifter för autentisering används av flera enheter. Den här processen kan tyda på en Illegitimate-enhet som personifierar en legitim enhet. Dubblett av autentiseringsuppgifter ökar risken för personifiering av enheter av en skadlig aktör. |
| Medel | Standard princip för IP-filter ska nekas | IoT Hub | IP-filterlistan måste ha definierade regler för tillåten trafik och ska som standard neka all annan trafik som standard. |
| Medel | IP-filterlistan innehåller stora IP-intervall | IoT Hub | En Tillåt källa för IP-filterlistan är för stor. Överskrivna regler kan exponera IoT-hubben för skadliga aktörer. |
| Låg | Aktivera diagnostikloggar i IoT Hub | IoT Hub | Aktivera loggar och behåll dem i upp till ett år. Om du behåller loggarna kan du återskapa aktivitets spårningar i utrednings syfte när en säkerhets incident inträffar eller nätverket komprometteras. |

## <a name="next-steps"></a>Nästa steg

- [Översikt över](overview.md) Defender for IoT-tjänsten
- Lär dig hur du [kommer åt dina säkerhets data](how-to-security-data-access.md)
- Lär dig mer om att [undersöka en enhet](how-to-investigate-device.md)
