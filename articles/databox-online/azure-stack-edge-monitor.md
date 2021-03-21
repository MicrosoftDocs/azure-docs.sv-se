---
title: Övervaka din Azure Stack Edge Pro-enhet | Microsoft Docs
description: Beskriver hur du använder det Azure Portal och lokala webb gränssnittet för att övervaka Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: aae64cad3603725a4062d5afb42df974bbf8ac40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438802"
---
# <a name="monitor-your-azure-stack-edge-pro"></a>Övervaka Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

Den här artikeln beskriver hur du övervakar Azure Stack Edge Pro. Du kan övervaka din enhet genom att använda Azure Portal eller det lokala webb gränssnittet. Använd Azure Portal för att Visa enhets händelser, konfigurera och hantera aviseringar och visa mått. Använd det lokala webb gränssnittet på din fysiska enhet för att Visa maskin varu status för de olika enhets komponenterna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Visa enhets händelser och motsvarande aviseringar
> * Visa maskin varu status för enhets komponenter
> * Visa kapacitets-och transaktions mått för din enhet

## <a name="view-device-events"></a>Visa enhets händelser

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Visa maskin varu status

Utför följande steg i det lokala webbgränssnittet för att visa maskinvarustatusen för dina enhetskomponenter.

1. Anslut till enhetens lokala webbgränssnitt.
2. Gå till **underhålls > maskin varu status**. Du kan visa hälsotillståndet för de olika enhetskomponenterna.

    ![Visa maskin varu status](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Visa mått

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Mått på enheten

I det här avsnittet beskrivs övervaknings måtten på enheten. Måtten kan vara:

* Kapacitets mått. Kapacitets måtten är relaterade till enhetens kapacitet.

* Transaktions mått. Transaktions måtten är relaterade till Läs-och skriv åtgärder för att Azure Storage.

* Kant beräknings mått. Kant beräknings måtten är relaterade till användningen av Edge-beräkningen på enheten.

En fullständig lista över måtten visas i följande tabell:

|Kapacitetsmått                     |Beskrivning  |
|-------------------------------------|-------------|
|**Tillgänglig kapacitet**               | Avser storleken på de data som kan skrivas till enheten. Detta mått är med andra ord den kapacitet som kan göras tillgänglig på enheten. <br></br>Du kan frigöra enhets kapaciteten genom att ta bort den lokala kopian av filer som har en kopia på både enheten och molnet.        |
|**Total kapacitet**                   | Avser det totala antalet byte på enheten för att skriva data till, vilket även kallas för den totala storleken på det lokala cacheminnet. <br></br> Du kan nu öka kapaciteten för en befintlig virtuell enhet genom att lägga till en datadisk. Lägg till en datadisk via hypervisor-hantering för den virtuella datorn och starta sedan om den virtuella datorn. Den lokala lagringspoolen för gateway-enheten kommer att utökas för att hantera den nyligen tillagda data disken. <br></br>Mer information finns i lägga till [en hård disk för virtuell Hyper-V-dator](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Transaktionsmått              | Beskrivning         |
|-------------------------------------|---------|
|**Överförda moln byte (enhet)**    | Summan av alla byte som laddats upp över alla resurser på enheten        |
|**Överförda moln byte (resurs)**     | Överförda byte per resurs. Detta mått kan vara: <br></br> AVG, som är (summan av alla byte som överförs per resurs/antal resurser),  <br></br>Max, vilket är det maximala antalet byte som överförs från en resurs <br></br>Min, som är det minsta antalet byte som överförs från en resurs      |
|**Data flöde för moln hämtning (resurs)**| Hämtade byte per resurs. Detta mått kan vara: <br></br> AVG, som är (summan av alla byte som lästs eller laddats ned till resurs/antal resurser) <br></br> Max, vilket är det maximala antalet byte som hämtas från en resurs<br></br> och min, som är det minsta antalet byte som hämtas från en resurs  |
|**Moln läsnings data flöde**            | Summan av alla byte som lästs från molnet över alla resurser på enheten     |
|**Moln överförings data flöde**          | Summan av alla byte som skrivits till molnet över alla resurser på enheten     |
|**Moln överförings data flöde (resurs)**  | Summan av alla byte som skrivs till molnet från resurs/antal resurser är genomsnitt, max och min per resurs      |
|**Läs data flöde (nätverk)**           | Innehåller systemets nätverks data flöde för alla byte som läses från molnet. Den här vyn kan innehålla data som inte är begränsade till resurser. <br></br>Vid delning visas trafiken över alla nätverkskort på enheten, inklusive kort som inte är anslutna eller aktiverade.      |
|**Skriv data flöde (nätverk)**       | Innehåller systemets nätverks data flöde för alla byte som skrivs till molnet. Den här vyn kan innehålla data som inte är begränsade till resurser. <br></br>Vid delning visas trafiken över alla nätverkskort på enheten, inklusive kort som inte är anslutna eller aktiverade.          |

| Kant beräknings mått              | Beskrivning         |
|-------------------------------------|---------|
|**Edge Compute-minnes användning**      |           |
|**Edge Compute-procent CPU**    |         |

## <a name="next-steps"></a>Nästa steg

Lär dig att [hantera bandbredd](azure-stack-edge-manage-bandwidth-schedules.md).
Lär dig [Hantera aviseringar om enhets händelser](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).
