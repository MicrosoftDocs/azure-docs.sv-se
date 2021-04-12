---
title: Belastnings utjämning för Windows-adresspoolen för virtuella skriv bord – Azure
description: Lär dig mer om belastnings Utjämnings metoder för värdar för en Windows Virtual Desktop-miljö.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ed0cf568ca8d011beb7150f23c0187bbe262d5f0
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446782"
---
# <a name="host-pool-load-balancing-methods"></a>Belastningsutjämningsmetoder för värdpool

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

Windows Virtual Desktop stöder två metoder för belastnings utjämning. Varje metod bestämmer vilken värddator som ska vara värd för en användares session när de ansluter till en resurs i en adresspool.

Följande belastnings Utjämnings metoder är tillgängliga i Windows Virtual Desktop:

- Med bredd-första belastnings utjämning kan du jämnt distribuera användarsessioner över sessionens värdar i en adresspool.
- Djup – med belastnings utjämning kan du fylla en session-värd med användarsessioner i en adresspool. När den första sessionen når gränsen för antalet sessioner dirigerar belastningsutjämnaren alla nya användar anslutningar till nästa session-värd i adresspoolen tills den når sin gräns, och så vidare.

Varje adresspool kan bara konfigurera en typ av belastnings utjämning som är speciell för den. Både belastnings Utjämnings metoder delar däremot följande beteenden oavsett vilken pool de befinner sig i:

- Om en användare redan har en session i värddatorn och återansluter till den sessionen, kommer belastningsutjämnaren att omdirigera dem till sessionens värd med sin befintliga session. Detta gäller även om den aktuella AllowNewConnections-egenskapen har angetts till false.
- Om en användare inte redan har en session i poolen, kommer belastningsutjämnaren inte att anse ras-värdar vars AllowNewConnections-egenskap är inställd på falskt under belastnings utjämning.

## <a name="breadth-first-load-balancing-method"></a>Bredd – första belastnings Utjämnings metod

Med den bredd-första belastnings Utjämnings metoden kan du distribuera användar anslutningar så att de optimeras för det här scenariot. Den här metoden är idealisk för organisationer som vill ge den bästa upplevelsen av användare som ansluter till sin poolbaserade virtuella Skriv bords miljö.

Metoden vidd-First frågar först efter de sessioner som tillåter nya anslutningar. Metoden väljer sedan en sessions värd slumpmässigt från hälften av antalet sessioner med minst antal sessioner. Om det exempelvis finns nio datorer med 11, 12, 13, 14, 15, 16, 17, 18 och 19 sessioner går en ny session som du skapar inte automatiskt till den första datorn. I stället kan den gå till någon av de första fem datorerna med det lägsta antalet sessioner (11, 12, 13, 14, 15).

## <a name="depth-first-load-balancing-method"></a>Djup-första belastnings Utjämnings metod

Med den första belastnings Utjämnings metoden kan du fylla en session-värd i taget för att optimera för det här scenariot. Den här metoden är idealisk för kostnadsmedvetna organisationer som vill ha mer detaljerad kontroll över antalet virtuella datorer som de har allokerat för en värd pool.

Den första metoden frågar efter de sessioner som är värdar som tillåter nya anslutningar och som inte har varit över gränsen för den högsta tillåtna sessionen. Metoden väljer sedan Session Host med det högsta antalet sessioner. Om det finns en förbindelse väljer metoden den första sessionens värd i frågan.

>[!IMPORTANT]
>Den djupbaserade algoritmen för belastnings utjämning distribuerar sessioner till sessionsbaserade värdar baserat på gränsen för högsta antal sessioner. Den här parametern krävs när du använder algoritmen för djup-första belastnings utjämning. För bästa möjliga användar upplevelse måste du ändra den maximala begränsnings parametern för Session Host till ett tal som passar din miljö bäst.