---
title: Fordons förbruknings modeller för routning | Microsoft Azure Maps
description: 'Lär dig mer om de förbruknings modeller som Azure Maps stöder: förbränning och elektrisk. Se vilka parametrar varje modell använder och Visa parameter begränsningar.'
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013147"
---
# <a name="consumption-model"></a>Förbrukningsmodell

Routningstjänsten innehåller en uppsättning parametrar för en detaljerad beskrivning av den fordonsbaserade förbruknings modellen.
Beroende på värdet för **vehicleEngineType** stöds två huvudsakliga förbruknings modeller: _förbränning_ och _elektrisk_. Det är fel att ange parametrar som tillhör olika modeller i samma begäran. Dessutom kan inte förbruknings modell parametrar användas med följande **travelMode** -värden: _cykel_ och _gående_.

## <a name="parameter-constraints-for-consumption-model"></a>Parameter begränsningar för förbruknings modell

I båda förbruknings modellerna finns det vissa beroenden när du anger parametrar. Det innebär att det kan krävas några andra parametrar för att explicit ange vissa parametrar. Här är dessa beroenden för att vara medvetna om:

* Alla parametrar kräver att **constantSpeedConsumption** anges av användaren. Det går inte att ange någon annan förbruknings modell parameter om **constantSpeedConsumption** inte har angetts. **VehicleWeight** -parametern är ett undantag för det här kravet.
* **accelerationEfficiency** och **decelerationEfficiency** måste alltid anges som ett par (dvs. båda eller inga).
* Om **accelerationEfficiency** och **decelerationEfficiency** har angetts får produkten av deras värden inte vara större än 1 (för att förhindra beständig rörelse).
* **uphillEfficiency** och **downhillEfficiency** måste alltid anges som ett par (det vill säga båda eller inga).
* Om **uphillEfficiency** och **downhillEfficiency** har angetts får produkten av deras värden inte vara större än 1 (för att förhindra beständig rörelse).
* Om \* __effektivitets__ parametrarna anges av användaren måste även **vehicleWeight** anges. När **vehicleEngineType** är _förbränning_ måste även **fuelEnergyDensityInMJoulesPerLiter** anges.
* **maxChargeInkWh** och **currentChargeInkWh** måste alltid anges som ett par (dvs. båda eller inga).

> [!NOTE]
> Om endast **constantSpeedConsumption** anges används inga andra användnings aspekter som lutning och fordons acceleration i beräkningen av förbruknings beräkningar.

## <a name="combustion-consumption-model"></a>Förbrännings förbruknings modell

Förbrännings förbruknings modellen används när **vehicleEngineType** är inställd på _förbränning_.
Listan över parametrar som tillhör den här modellen är nedan. Se avsnittet parametrar för detaljerad beskrivning.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Elektrisk förbruknings modell

Den elektriska förbruknings modellen används när **vehicleEngineType** är inställt på _elektrisk_.
Listan över parametrar som tillhör den här modellen är nedan. Se avsnittet parametrar för detaljerad beskrivning.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Lämpliga värden för förbruknings parametrar

En viss uppsättning förbruknings parametrar kan avvisas även om mängden kan uppfylla alla specifika krav. Det inträffar när värdet för en speciell parameter, eller en kombination av värden för flera parametrar, anses leda till orimliga värden för förbruknings värden. Om detta inträffar indikerar det förmodligen ett indatafel, eftersom det är så viktigt att ta hänsyn till alla lämpliga-värden för förbruknings parametrar. Om en viss uppsättning förbruknings parametrar avvisas, innehåller det medföljande fel meddelandet en text förklaring av anledningen (erna).
De detaljerade beskrivningarna av parametrarna innehåller exempel på lämpliga-värden för båda modellerna.
