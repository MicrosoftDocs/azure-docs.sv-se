---
title: ta med fil
description: ta med fil
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2f52e8a89ec9dd78a1951836053cb2c698310bbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100645332"
---
Följande begränsningar gäller för Azure Event Grid **ämnen** (system, anpassade och partner ämnen). 

| Resurs | Gräns |
| --- | --- |
| Anpassade ämnen per Azure-prenumeration | 100 |
| Händelse prenumerationer per ämne | 500 |
| Publicerings hastighet för ett anpassat eller ett partner ämne (ingress) | 5 000 händelser/SEK eller 5 MB/SEK (beroende på vad som uppfylls först) |
| Händelse storlek | 1 MB  |
| Anslutningar för privata slut punkter per ämne  | 64 | 
| Regler för IP-brandvägg per ämne | 16 | 

Följande begränsningar gäller för Azure Event Grid **domäner**. 

| Resurs | Gräns |
| --- | --- |
| Ämnen per händelse domän | 100 000 |
| Händelse prenumerationer per ämne i en domän | 500 |
| Händelse prenumerationer för domän omfattning | 50 |
| Publicerings takt för en händelse domän (ingress) | 5 000 händelser/SEK eller 5 MB/SEK (beroende på vad som uppfylls först) |
| Händelse domäner per Azure-prenumeration | 100 |
| Anslutningar för privata slut punkter per domän | 64 | 
| Regler för IP-brandvägg per domän | 16 | 


