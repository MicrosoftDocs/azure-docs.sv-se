---
title: Inbyggda Azure Maps kart format som stöds
description: Lär dig mer om de inbyggda kart formaten som Azure Maps stöder, till exempel väg, blank_accessible, satellit, satellite_road_labels, road_shaded_relief och natt.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3404c02dc159309ed1e9fcd6f9f6bb593fc7552
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896947"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps inbyggda kart format som stöds

Azure Maps stöder flera olika inbyggda kart format enligt beskrivningen nedan.

## <a name="road"></a>utanför

En **väg** karta är en standard karta som visar vägar. Den visar också naturliga och artificiella funktioner och etiketter för dessa funktioner.

![kart stil för vägar](./media/supported-map-styles/road.png)

**Tillämpliga API: er:**

* [Kart bild](/rest/api/maps/render/getmapimage)
* [Kart panel](/rest/api/maps/render/getmaptile)
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="blank-and-blank_accessible"></a>Tom och blank_accessible

De **tomma** och **blank_accessible** kart formaten innehåller en tom arbets yta för visualisering av data. **Blank_accessible** formatet fortsätter att tillhandahålla skärm läsar uppdateringar med kartans plats information, även om bas kartan inte visas.

> [!Note]
> I Web SDK kan du ändra bakgrunds färgen för kartan genom att ange CSS- `background-color` formatet för Map DIV-elementet.

**Tillämpliga API: er:**

* Kart kontroll för webb-SDK

## <a name="satellite"></a>satellite

**Satellit** stilen är en kombination av satellit-och antenn bilder.

![kart stil för satellit panel](./media/supported-map-styles/satellite.png)

**Tillämpliga API: er:**

* [Satellit panel](/rest/api/maps/render/getmapimagerytilepreview)
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="satellite_road_labels"></a>satellite_road_labels

Den här kart stilen är en hybrid av vägar och etiketter som finns ovanpå satellit-och antenn bilder.

![satellite_road_labels kart stil](./media/supported-map-styles/satellite-road-labels.png)

**Tillämpliga API: er:**

* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="grayscale_dark"></a>grayscale_dark

**gråskala** är en mörk version av vägens kart stil.

![gray_scale kart stil](./media/supported-map-styles/grayscale-dark.png)

**Tillämpliga API: er:**

* [Kart bild](/rest/api/maps/render/getmapimage)
* [Kart panel](/rest/api/maps/render/getmaptile)
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="grayscale_light"></a>grayscale_light

**grå Skale lampor** är en låg version av vägens kart stil.

![format för grå tons indikator](./media/supported-map-styles/grayscale-light.png)

**Tillämpliga API: er:**
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="night"></a>natt

**natt** är en mörk version av kart formatet för vägar med färgade vägar och symboler.

![stil för natt karta](./media/supported-map-styles/night.png)

**Tillämpliga API: er:**

* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="road_shaded_relief"></a>road_shaded_relief

en **nedtonad** avlastning är ett Azure Maps huvud format som har slutförts med hjälp av jordens profiler.

![skuggad avbilds stil](./media/supported-map-styles/shaded-relief.png)

**Tillämpliga API: er:**

* [Kart panel](/rest/api/maps/render/getmaptile)
* Kart kontroll för webb-SDK
* Kontroll av Android-kartning
* Visuellt Power BI-objekt

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** är ett mörkt kart format med en högre kontrast än andra format.

![mörkt kart format med hög kontrast](./media/supported-map-styles/high-contrast-dark.png)

**Tillämpliga API: er:**

* Kart kontroll för webb-SDK
* Visuellt Power BI-objekt

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du anger ett kart format i Azure Maps:

[Välj ett kartformat](./choose-map-style.md)