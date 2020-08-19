---
title: inkludera fil
description: inkludera fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: c8868cd6f5c50b84f263155518ee553145afcfa9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602421"
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

**Gränser för allmän arbets yta**

| Kategori | Gräns | Kommentarer |
|:---|:---|:---|
| Maximalt antal kolumner i en tabell         | 500 | |
| Maximalt antal tecken för kolumn namn | 500 | |
| Dataexport | Inte tillgänglig för närvarande | Använd Azure Function eller Logic app för att aggregera och exportera data. | 

**<a name="data-ingestion-volume-rate">Volym pris för data inmatning</a>**

Azure Monitor är en hög skalbar data tjänst som tjänar tusentals kunder som skickar terabyte data varje månad i en växande takt. Gränsen för volym hastighet avser att isolera Azure Monitor kunder från plötsliga inmatnings toppar i en miljö med flera organisationer. Ett tröskelvärde för standard inläsnings volym på 500 MB (komprimerat) har definierats i arbets ytor, detta översätts till cirka **6 GB/min** okomprimerad – den faktiska storleken kan variera mellan data typerna beroende på logg längd och dess komprimerings förhållande. Volym frekvensen gäller för alla inmatade data oavsett om de skickas från Azure-resurser med hjälp av [diagnostikinställningar](../articles/azure-monitor/platform/diagnostic-settings.md), [data insamlings-API](../articles/azure-monitor/platform/data-collector-api.md) eller agenter.

När du skickar data till en arbets yta med en volym hastighet som är högre än 80% av tröskelvärdet som kon figurer ATS i din arbets yta, skickas en händelse till *Åtgärds* tabellen i arbets ytan var 6: e timme medan tröskelvärdet fortsätter att överskridas. När inmatad volym taxa är högre än tröskelvärdet släpps vissa data och en händelse skickas till *Åtgärds* tabellen i arbets ytan var 6: e timme medan tröskelvärdet fortsätter att överskridas. Om din inmatnings volym överskrider tröskelvärdet eller om du förväntar dig att få en stund snart, kan du begära att öka den i genom att öppna en support förfrågan. 

Om du vill få ett meddelande på approching eller nått gränsen för inläsnings volym på din arbets yta skapar du en [logg aviserings regel](../articles/azure-monitor/platform/alerts-log.md) med hjälp av följande fråga med aviserings logik Base om antalet resultat som är högre än noll, utvärderings perioden på 5 minuter och frekvensen 5 minuter.

Inläsnings volymens hastighet nådde 80% av tröskelvärdet:
```Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The data ingestion volume rate crossed 80% of the threshold"
```

Inläsnings volymens hastighet nådde tröskel:
```Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The data ingestion volume rate crossed the threshold"
```

>[!NOTE]
>Beroende på hur länge du har använt Log Analytics kan du ha åtkomst till äldre pris nivåer. Läs mer om [Log Analytics äldre pris nivåer](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
