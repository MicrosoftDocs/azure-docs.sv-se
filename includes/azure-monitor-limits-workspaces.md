---
title: ta med fil
description: ta med fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 56afc81794bb18bfb7c9146eeb4ed61a0a75ea0f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587021"
---
**Data insamlings volym och kvarhållning** 

| Nivå | Gräns per dag | Datakvarhållning | Kommentar |
|:---|:---|:---|:---|
| Aktuell pris nivå per GB<br>(lanserades april 2018) | Obegränsad | 30-730 dagar | Data kvarhållning utöver 31 dagar är tillgängligt för ytterligare kostnader. Läs mer om Azure Monitor prissättning. |
| Äldre kostnads fria nivåer<br>(lanserades april 2016) | 500 MB | 7 dagar | När din arbets yta når gränsen på 500 MB per dag stoppas data inmatningen och återupptas i början av nästa dag. En dag baseras på UTC. Observera att data som samlas in av Azure Security Center inte ingår i den här gränsen på 500 MB per dag och fortsätter att samlas in över den här gränsen.  |
| Äldre fristående per GB-nivå<br>(lanserades april 2016) | Obegränsad | 30 till 730 dagar | Data kvarhållning utöver 31 dagar är tillgängligt för ytterligare kostnader. Läs mer om Azure Monitor prissättning. |
| Bakåtkompatibelt per nod (OMS)<br>(lanserades april 2016) | Obegränsad | 30 till 730 dagar | Data kvarhållning utöver 31 dagar är tillgängligt för ytterligare kostnader. Läs mer om Azure Monitor prissättning. |
| Äldre standard nivå | Obegränsad | 30 dagar  | Det går inte att justera kvarhållning |
| Legacy Premium-nivå | Obegränsad | 365 dagar  | Det går inte att justera kvarhållning |

**Antal arbets ytor per prenumeration.**

| Prisnivå    | Arbets ytans gräns | Kommentarer
|:---|:---|:---|
| Kostnadsfri nivå  | 10 | Det går inte att öka den här gränsen. |
| Alla andra nivåer | Obegränsad | Du är begränsad till antalet resurser i en resurs grupp och antalet resurs grupper per prenumeration. |

**Azure-portalen**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Maximalt antal poster som returneras av en logg fråga | 10 000 | Minska resultatet med fråge omfånget, tidsintervallet och filtren i frågan. |


**API för data insamling**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Maximal storlek för ett enskilt inlägg | 30 MB | Dela upp större volymer i flera inlägg. |
| Maximal storlek för fält värden  | 32 KB | Fält som är längre än 32 kB trunkeras. |

**Sök API**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Maximalt antal poster som returneras i en enskild fråga | 500 000 | |
| Maximal storlek för returnerade data | 64 000 000 byte (~ 61 MiB)| |
| Maximal kör tid för fråga | 10 minuter | Se [timeout](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) för mer information.  |
| Högsta begär ande frekvens | 200 förfrågningar per 30 sekunder per Azure AD-användare eller klient-IP-adress | Mer information finns i avsnittet om [hastighets begränsningar](https://dev.loganalytics.io/documentation/Using-the-API/Limits) . |

**Anslutningsprogram för Azure Monitor-loggar**
| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Högsta antal poster | 500 000 | |
| Maximal tids gräns för fråga | 110 sekund | |
| Diagram | | Visualiseringen på sidan loggar och kopplingen använder olika diagram bibliotek och vissa funktioner är inte tillgängliga i anslutningen för tillfället. |

**Gränser för allmän arbets yta**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Maximalt antal kolumner i en tabell         | 500 | |
| Maximalt antal tecken för kolumn namn | 500 | |

**<a name="data-ingestion-volume-rate">Volym pris för data inmatning</a>**

Azure Monitor är en hög skalbar data tjänst som tjänar tusentals kunder som skickar terabyte data varje månad i en växande takt. Gränsen för volym hastighet avser att isolera Azure Monitor kunder från plötsliga inmatnings toppar i en miljö med flera organisationer. Ett tröskelvärde för standard inläsnings volym på 500 MB (komprimerat) har definierats i arbets ytor, detta översätts till cirka **6 GB/min** okomprimerad – den faktiska storleken kan variera mellan data typerna beroende på logg längd och dess komprimerings förhållande. Gränsen för volym hastighet gäller för data som matas in från Azure-resurser via [diagnostikinställningar](../articles/azure-monitor/platform/diagnostic-settings.md). När volym hastighets gränsen uppnås försöker en mekanism för återförsök att mata in data 4 gånger under en period på 30 minuter och släppa den om åtgärden Miss lyckas. Den gäller inte för data som matas in från [agenter](../articles/azure-monitor/agents/agents-overview.md) eller [API för data insamling](../articles/azure-monitor/platform/data-collector-api.md).

När data som skickas till din arbets yta har en volym hastighet som är högre än 80% av tröskelvärdet som kon figurer ATS i din arbets yta, skickas en händelse till *Åtgärds* tabellen i arbets ytan var 6: e timme medan tröskelvärdet fortsätter att överskridas. När inmatad volym taxa är högre än tröskelvärdet släpps vissa data och en händelse skickas till *Åtgärds* tabellen i arbets ytan var 6: e timme medan tröskelvärdet fortsätter att överskridas. Om din inmatnings volym överskrider tröskelvärdet eller om du förväntar dig att få en stund snart, kan du begära att öka den i genom att öppna en support förfrågan. 

Se [övervaka hälsan för Log Analytics arbets ytan i Azure Monitor](../articles/azure-monitor/platform/monitor-workspace.md) för att skapa varnings regler som ska meddelas proaktivt när du når eventuella inmatnings gränser.

>[!NOTE]
>Beroende på hur länge du har använt Log Analytics kan du ha åtkomst till äldre pris nivåer. Läs mer om [Log Analytics äldre pris nivåer](../articles/azure-monitor/platform/manage-cost-storage.md#legacy-pricing-tiers).