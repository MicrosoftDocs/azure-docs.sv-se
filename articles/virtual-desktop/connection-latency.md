---
title: Användar anslutnings fördröjning för Windows Virtual Desktop – Azure
description: Anslutnings fördröjning för användare av virtuella Windows-datorer.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 2fa03fa948c7768f5e74cc68b528e847c5351273
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447869"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Fastställa svars tid för användar anslutning i virtuellt Windows-skrivbord

Det virtuella Windows-skrivbordet är globalt tillgängligt. Administratörer kan skapa virtuella datorer i alla Azure-regioner som de vill. Anslutnings fördröjningen varierar beroende på var användarna och de virtuella datorerna finns. Windows Virtual Desktop-tjänster distribueras kontinuerligt till nya geografiska områden för att förbättra svars tiden.

[Verktyget Windows Virtual Desktop Experiences uppskattnings verktyg](https://azure.microsoft.com/services/virtual-desktop/assessment/) kan hjälpa dig att avgöra den bästa platsen för att optimera de virtuella datorernas svars tider. Vi rekommenderar att du använder verktyget varannan till tre månader för att se till att den optimala platsen inte har ändrats när Windows Virtual Desktop samlar in till nya områden.

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Tolka resultat från Windows Virtual Desktop Experiences uppskattnings verktyg

I Windows Virtual Desktop bör svars tiden på upp till 150 ms inte påverka användar upplevelsen som inte omfattar åter givning eller video. Fördröjning mellan 150 ms och 200 MS bör vara bra för text bearbetning. Latens över 200 MS kan påverka användar upplevelsen. 

Dessutom är Windows Virtual Desktop-anslutningen beroende av den dators Internet anslutning som användaren använder tjänsten från. Användare kan förlora anslutning eller uppleva ingångs fördröjning i någon av följande situationer:

 - Användaren har ingen stabil lokal Internet anslutning och svars tiden är över 200 MS.
 - Nätverket är mättad eller Rate-Limited.

Vi rekommenderar att du väljer VM-platser som är så nära dina användare som möjligt. Om användaren till exempel befinner sig i Indien, men den virtuella datorn är i USA, kommer det att finnas en fördröjning som påverkar den övergripande användar upplevelsen. 

## <a name="azure-front-door"></a>Azure Front Door

Windows Virtual Desktop använder [Azures front dörr](https://azure.microsoft.com/services/frontdoor/) för att dirigera om användar anslutningen till närmaste Windows-Gateway för virtuella datorer baserat på käll-IP-adressen. Windows Virtual Desktop använder alltid den Windows-Gateway för virtuella datorer som klienten väljer.

## <a name="next-steps"></a>Nästa steg

- Om du vill kontrol lera den bästa platsen för optimal svars tid kan du läsa [verktyget Windows Virtual Desktop Experience uppskattnings verktyg](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- För pris listor, se [priser för virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Om du vill komma igång med distributionen av virtuella Windows-datorer kan du kolla in [vår självstudie](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
