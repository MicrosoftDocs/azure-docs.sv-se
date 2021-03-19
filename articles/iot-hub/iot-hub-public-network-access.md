---
title: Hantera offentlig nätverks åtkomst för Azure IoT Hub
description: Dokumentation om hur du inaktiverar och aktiverar offentlig nätverks åtkomst för IoT Hub
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: b225ad7e12a95b4dcbb8656f54f8a9e9562b2ee8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604703"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Hantera offentlig nätverks åtkomst för IoT Hub

Om du vill begränsa åtkomsten till enbart [privat slut punkt för din IoT Hub i ditt VNet](virtual-network-support.md)inaktiverar du åtkomst till offentliga nätverk. Det gör du genom att använda Azure Portal eller `publicNetworkAccess` API: et. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Inaktivera offentlig nätverks åtkomst med Azure Portal

1. Besök [Azure Portal](https://portal.azure.com)
2. Gå till IoT-hubben.
3. Välj **nätverk** på menyn på den vänstra sidan.
4. Under Tillåt offentligt nätverk åtkomst till väljer du **inaktive rad**
5. Välj **Spara**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Bild som visar Azure Portal där du kan inaktivera offentlig nätverks åtkomst" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Om du vill aktivera offentlig nätverks åtkomst väljer du **alla nätverk** och sedan **Spara**.

## <a name="accessing-the-iot-hub-after-disabling-public-network-access"></a>Åtkomst till IoT Hub efter inaktive ring av offentlig nätverks åtkomst

När offentlig nätverks åtkomst har inaktiverats är IoT Hub bara tillgänglig via [sin privata VNet-slutpunkt med Azures privata länk](virtual-network-support.md).

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>IoT Hub-slutpunkt, IP-adress och portar efter inaktive ring av offentlig nätverks åtkomst

IoT Hub är en PaaS (Platform-as-a-Service) med flera innehavare så att olika kunder delar samma pool med beräknings-, nätverks-och lagrings maskin varu resurser. IoT Hubs värdnamn mappas till en offentlig slut punkt med en offentligt dirigerad IP-adress via Internet. Olika kunder delar den här IoT Hub offentliga slut punkten och IoT-enheter i över WAN-nätverk och lokala nätverk kan komma åt dem. 

Inaktive ring av offentlig nätverks åtkomst tillämpas på en angiven IoT Hub-resurs, vilket säkerställer isolering. Om du vill hålla tjänsten aktiv för andra kund resurser med hjälp av den offentliga sökvägen kan dess offentliga slut punkt fortsätta att matchas, IP-adresser upptäckas och portarna förblir öppna. Detta är inte en orsak till att Microsoft integrerar flera säkerhets nivåer för att säkerställa en fullständig isolering mellan klienter. Mer information finns i [isolera i det offentliga Azure-molnet](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

## <a name="ip-filter"></a>IP-filter 

Om offentlig nätverks åtkomst är inaktive rad ignoreras alla [IP-filter](iot-hub-ip-filtering.md) regler. Detta beror på att alla IP-adresser från det offentliga Internet blockeras. Använd alternativet **valda IP-intervall** om du vill använda IP-filter.

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Fel korrigering med inbyggd Event Hub-kompatibel slut punkt

Det finns ett fel med IoT Hub där den [inbyggda Event Hub-kompatibla slut punkten](iot-hub-devguide-messages-read-builtin.md) fortsätter att vara tillgänglig via offentliga Internet när offentlig nätverks åtkomst till IoT Hub är inaktive rad. Mer information och kontakta oss om detta fel finns i [inaktivera offentlig nätverks åtkomst för IoT Hub inaktiverar åtkomst till den inbyggda slut punkten för Event Hub](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).
