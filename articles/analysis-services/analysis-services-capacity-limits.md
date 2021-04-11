---
title: Azure Analysis Services resurs-och objekt gränser | Microsoft Docs
description: I den här artikeln beskrivs resurs-och objekt begränsningar för en Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: beb4ca31b65d5de0b81030cdf3222418cb968060
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732014"
---
# <a name="analysis-services-resource-and-object-limits"></a>Analysis Services resurs-och objekt gränser

I den här artikeln beskrivs begränsningar för resurs och modell objekt.

## <a name="tier-limits"></a>Nivå gränser

För QPU-och minnes gränser för nivåerna Developer, Basic och standard, se [sidan Azure Analysis Services prissättning](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Objekt gränser

Dessa gränser är teoretiska. Prestandan minskas med lägre siffror.

|Objekt|Max storlek/siffror|  
|------------|----------------------------|  
|Databaser i en instans|16 000|  
|Kombinerat antal tabeller och kolumner i en databas|16 000|  
|Rader i en tabell|Obegränsat<br /><br /> **Varning:** Med begränsningen ingen enskild kolumn i tabellen kan ha mer än 1 999 999 997 distinkta värden.|  
|Hierarkier i en tabell|15 999|  
|Nivåer i en hierarki|15 999|  
|Relationer|8,000|  
|Nyckel kolumner i alla tabeller|15 999|  
|Mått i tabeller|2 ^ 31-1 = 2 147 483 647|  
|Celler som returneras av en fråga|2 ^ 31-1 = 2 147 483 647|  
|Post storlek för käll frågan|64 KB|  
|Längd på objekt namn|512 tecken|  


