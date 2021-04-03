---
title: Rubrik karta för Azure Kinect-Body
description: Lär dig hur du frågar en index karta för innehålls spårning i Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, portning, brödtext, spårning, index, segmentering, mappning
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277709"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Index karta för innehålls spårning i Azure Kinect

Rubrik kartan för brödtext innehåller mappningen för instans segment för varje brödtext i djup kameran. Varje pixel mappas till motsvarande pixel i djup-eller IR-bilden. Värdet för varje pixel representerar vilken brödtext som pixeln tillhör. Det kan vara antingen bakgrund (värde `K4ABT_BODY_INDEX_MAP_BACKGROUND` ) eller ett identifierat index `k4abt_body_t` .

![Exempel på karta för innehålls index](./media/concepts/body-index-map.png)

>[!NOTE]
> Bröd text indexet skiljer sig från bröd text-ID: t. Du kan skicka frågor till Body-ID: t från ett angivet innehålls index genom att anropa API: [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92).


## <a name="using-body-index-map"></a>Använda mappning av innehålls index

Bröd text index kartan lagras som en `k4a_image_t` och har samma upplösning som djupet eller IR-bilden. Varje pixel är ett 8-bitars värde. Den kan frågas från a `k4abt_frame_t` genom att anropa `k4abt_frame_get_body_index_map` . Utvecklaren ansvarar för att frisläppa minnet för mappning av Body-index genom att anropa `k4a_image_release()` .

## <a name="next-steps"></a>Nästa steg

[Bygg din första kroppsspårningsapp](build-first-body-app.md)
