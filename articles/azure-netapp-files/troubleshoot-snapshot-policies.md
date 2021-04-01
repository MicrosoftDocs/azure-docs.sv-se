---
title: Felsöka ögonblicks bilds principer för Azure NetApp Files | Microsoft Docs
description: Beskriver fel meddelanden och lösningar som kan hjälpa dig att felsöka problem med hantering av ögonblicks bilds principer för Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91651012"
---
# <a name="troubleshoot-snapshot-policies"></a>Felsöka policyer för ögonblicksbilder

Den här artikeln beskriver fel scenarier som du kan stöta på när du hanterar Azure NetApp Files ögonblicks bilds principer. Den innehåller också lösningar som kan hjälpa dig att lösa problemen.

## <a name="error-conditions-and-resolutions"></a>Fel tillstånd och lösningar 

|     Feltillstånd    |     Lösning    |
|-|-|
| Det går inte att skapa ögonblicks bild principen med ett ogiltigt namn på ögonblicks bild | Det uppstår ett fel under skapandet av ögonblicks bild principen om ditt namn på ögonblicks bild principen är ogiltigt. Följande rikt linjer gäller för namn på ögonblicks bilds principer:  <ul><li> Namnet på ögonblicks bilds principen får inte innehålla icke-ASCII-tecken eller specialtecken. </li> <li> Namnet på ögonblicks bilds principen måste börja med en bokstav eller en siffra och får innehålla bokstäver, siffror, under streck (_) och bindestreck (-). </li> <li> Namnet på ögonblicks bilds principen måste innehålla mellan 1 och 64 tecken.  </li></ul> Ändra namnet på ögonblicks bilds principen enligt rikt linjerna.  |
| Det går inte att skapa ögonblicks bild principen med ogiltiga värden. | Azure NetApp Files inte att skapa en ögonblicks bild princip om du anger ett ogiltigt värde för ett fält, till exempel `Number of snapshots to keep` eller `Minute on the hour to take snapshot` . Giltiga värden är följande:  <ul><li>Värdet måste vara ett giltigt tal.</li> <li>Värdet måste vara mellan 0 och 59.</li></ul> Kontrol lera att det finns ett giltigt värde för fälten. | 
| Det gick inte att skapa ögonblicks bild principen. fel meddelande `Total number of snapshots to keep exceeds 255` . | Varje volym kan ha [högst 255 ögonblicks bilder](azure-netapp-files-resource-limits.md). Det högsta antalet inkluderar summan av alla ögonblicks bilder per timme, dag, vecka och månad. <br> Minska `Snapshots to keep` värdet och försök igen. |
| Det går inte att tilldela en princip till en volym med felet `Total snapshot policy is over the max '255'` . | Varje volym kan ha [högst 255 ögonblicks bilder](azure-netapp-files-resource-limits.md). När summan av alla ögonblicks bilder på begäran, varje timme, varje dag, varje vecka och månad överskrider det högsta antalet uppstår ett fel. <br> Minska `snapshots to keep` värdet eller ta bort några ögonblicks bilder på begäran och försök igen. | 

## <a name="next-steps"></a>Nästa steg  

* [Hantera ögonblicks bilds principer](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
