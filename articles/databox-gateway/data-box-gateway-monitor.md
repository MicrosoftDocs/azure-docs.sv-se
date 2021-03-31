---
title: Övervaka Azure Data Box Gateway-enheten | Microsoft Docs
description: Beskriver hur du använder Azure Portal och det lokala webb gränssnittet för att övervaka din Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: ed05f3d60f8ba4fbb06327136c7a117ae1d1d2db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582486"
---
# <a name="monitor-your-azure-data-box-gateway"></a>Övervaka dina Azure Data Box Gateway

I den här artikeln beskrivs hur du övervakar Azure Data Box Gateway. Du kan övervaka din enhet genom att använda Azure Portal eller det lokala webb gränssnittet. Använd Azure Portal för att Visa enhets händelser, konfigurera och hantera aviseringar och visa mått.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Visa enhets händelser och motsvarande aviseringar
> * Visa kapacitets-och transaktions mått för din enhet
> * Konfigurera och hantera aviseringar

## <a name="view-device-events"></a>Visa enhets händelser

[!INCLUDE [data-box-gateway-view-device-events](../../includes/data-box-gateway-view-device-events.md)]

## <a name="view-metrics"></a>Visa mått

[!INCLUDE [data-box-gateway-view-metrics](../../includes/data-box-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Mått på enheten

I det här avsnittet beskrivs övervaknings måtten på enheten. Måtten kan vara:

* Kapacitets mått. Kapacitets måtten är relaterade till enhetens kapacitet.

* Transaktions mått. Transaktions måtten är relaterade till Läs-och skriv åtgärder för att Azure Storage.

En fullständig lista över måtten visas i följande tabell:

|Kapacitetsmått                     |Beskrivning  |
|-------------------------------------|-------------|
|**Tillgänglig kapacitet**               | Avser storleken på de data som kan skrivas till enheten. Med andra ord är detta den kapacitet som kan göras tillgänglig på enheten. <br></br>Du kan frigöra enhets kapaciteten genom att ta bort den lokala kopian av filer som har en kopia på både enheten och molnet.        |
|**Total kapacitet**                   | Avser det totala antalet byte på enheten som data ska skrivas till. Detta kallas även för den totala storleken på det lokala cacheminnet. <br></br> Du kan nu öka kapaciteten för en befintlig virtuell enhet genom att lägga till en datadisk. Lägg till en datadisk via hypervisor-hantering för den virtuella datorn och starta sedan om den virtuella datorn. Den lokala lagringspoolen för gateway-enheten kommer att utökas för att hantera den nyligen tillagda data disken. <br></br>Mer information finns i lägga till [en hård disk för virtuell Hyper-V-dator](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Transaktionsmått              | Beskrivning         |
|-------------------------------------|---------|
|**Överförda moln byte (enhet)**    | Summan av alla byte som laddats upp över alla resurser på enheten        |
|**Överförda moln byte (resurs)**     | Överförda byte per resurs. Detta kan vara: <br></br> AVG, som är (summan av alla byte som överförs per resurs/antal resurser),  <br></br>Max, vilket är det maximala antalet byte som överförs från en resurs <br></br>Min, som är det minsta antalet byte som överförs från en resurs      |
|**Data flöde för moln hämtning (resurs)**| Hämtade byte per resurs. Detta kan vara: <br></br> AVG, som är (summan av alla byte som lästs eller laddats ned till resurs/antal resurser) <br></br> Max, vilket är det maximala antalet byte som hämtas från en resurs<br></br> och min, som är det minsta antalet byte som hämtas från en resurs  |
|**Moln läsnings data flöde**            | Summan av alla byte som lästs från molnet över alla resurser på enheten     |
|**Moln överförings data flöde**          | Summan av alla byte som skrivits till molnet över alla resurser på enheten     |
|**Moln överförings data flöde (resurs)**  | Summan av alla byte som skrivs till molnet från resurs/antal resurser är genomsnitt, max och min per resurs      |
|**Läs data flöde (nätverk)**           | Innehåller systemets nätverks data flöde för alla byte som läses från molnet. Den här vyn kan innehålla data som inte är begränsade till resurser. <br></br>Vid delning visas trafiken över alla nätverkskort på enheten. Detta inkluderar kort som inte är anslutna eller aktiverade.      |
|**Skriv data flöde (nätverk)**       | Innehåller systemets nätverks data flöde för alla byte som skrivs till molnet. Den här vyn kan innehålla data som inte är begränsade till resurser. <br></br>Vid delning visas trafiken över alla nätverkskort på enheten. Detta inkluderar kort som inte är anslutna eller aktiverade.          |

## <a name="manage-alerts"></a>Hantera aviseringar

[!INCLUDE [data-box-gateway-manage-alerts](../../includes/data-box-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig att [hantera bandbredd](data-box-gateway-manage-bandwidth-schedules.md).
