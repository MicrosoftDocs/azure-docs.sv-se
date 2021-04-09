---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95992871"
---
- Det går för närvarande inte att flytta stegvisa ögonblicks bilder mellan prenumerationer.
- För närvarande kan du bara skapa SAS-URI: er på upp till fem ögonblicks bilder av en viss ögonblicks bild serie vid en given tidpunkt.
- Du kan inte skapa en stegvis ögonblicks bild för en viss disk utanför diskens prenumeration.
- Upp till sju stegvisa ögonblicks bilder per disk kan skapas var femte minut.
- Totalt 200 stegvisa ögonblicks bilder kan skapas för en enskild disk.
- Du kan inte hämta ändringarna mellan ögonblicks bilder som togs innan och efter att du ändrat storleken på den överordnade disken över 4 TB-gränser. Du tog till exempel en stegvis ögonblicks bild av ögonblicks bilder – a när storleken på en disk var 2 TB. Nu har du ökat storleken på disken till 6 TB och sedan tog en ytterligare ögonblicks bild av ögonblicks bilder-b. Du kan inte hämta ändringarna mellan Snapshot-a och Snapshot-b. Du måste återigen Ladda ned den fullständiga kopian av Snapshot-b som skapats efter storleken. Därefter kan du hämta ändringarna mellan ögonblicks bilder och b och ögonblicks bilder som skapats efter Snapshot-b. 
