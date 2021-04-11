---
title: Vanliga frågor om Azure Site Recovery övervakning
description: Få svar på vanliga frågor om Azure Site Recovery övervakning med inbyggd övervakning och Azure Monitor (Log Analytics)
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.openlocfilehash: 715fd3a30dcc37a26b07f2ca1c6a0f2ce168a23a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581709"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Vanliga frågor om Site Recovery övervakning

I den här artikeln besvaras vanliga frågor om övervakning av Azure- [Site Recovery](site-recovery-overview.md), med hjälp av inbyggd Site Recovery övervakning och Azure Monitor (Log Analytics).

## <a name="general"></a>Allmänt

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Hur loggas återställnings värden annorlunda än den senaste tillgängliga återställnings punkten?

Site Recovery använder en asynkron process med flera steg för att replikera datorer till Azure.

- I penultimate-steget i replikering kopieras de senaste ändringarna på datorn tillsammans med metadata till ett lagrings konto för logg/cache.
- Dessa ändringar, tillsammans med taggen som identifierar en återställnings punkt, skrivs till lagrings kontot/den hanterade disken i mål regionen.
- Site Recovery kan nu skapa en återställnings bar punkt för datorn.
- I det här läget har återställningen uppfyllts för de ändringar som har överförts till lagrings kontot hittills. Med andra ord är datorn återställnings bar till den tid som har förflutit från den tidstämpel som motsvarar återställnings punkten.
- Nu Site Recovery väljer överförda data från lagrings kontot och tillämpar dem på replik diskarna som har skapats för datorn.
- Site Recovery genererar sedan en återställnings punkt och gör den här punkten tillgänglig för återställning vid fel.
- Den senaste tillgängliga återställnings punkten visar alltså den tidstämpel som motsvarar den senaste återställnings punkten som redan har bearbetats och tillämpats på replik diskarna.


En felaktig system tid på den replikerande käll datorn eller på lokala infrastruktur servrar kommer att skeva det beräknade återställnings värdet. Kontrol lera att system klockan är korrekt på alla servrar och datorer för korrekt återställnings rapportering.



## <a name="inbuilt-site-recovery-logging"></a>Inbyggd Site Recovery loggning


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Varför är antalet virtuella datorer i valv infrastruktur vyn annorlunda än det totala antalet som visas i replikerade objekt?

Vyn valv infrastruktur är begränsad av scenarier med replikering. Endast datorer i det valda scenariot för replikering ingår i antalet för vyn. Dessutom räknar vi bara virtuella datorer som är konfigurerade att replikera till Azure. Misslyckade över datorer eller datorer som replikeras tillbaka till en lokal plats räknas inte i vyn.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Varför är antalet replikerade objekt i Essentials annorlunda än det totala antalet replikerade objekt på instrument panelen?

Endast datorer där den inledande replikeringen har slutförts ingår i det antal som visas i Essentials. Det totala antalet replikerade objekt innehåller alla datorer i valvet, inklusive de som den inledande replikeringen pågår för närvarande.

## <a name="azure-monitor-logging"></a>Azure Monitor-loggning


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Hur ofta Site Recovery skicka resurs loggar till Azure Monitor loggen? 

- AzureSiteRecoveryReplicationStats och AzureSiteRecoveryRecoveryPoints skickas var 15: e minut.  
- AzureSiteRecoveryReplicationDataUploadRate och AzureSiteRecoveryProtectedDiskDataChurn skickas var femte minut. 
- AzureSiteRecoveryJobs skickas vid utlösaren och slutförandet av ett jobb.
- AzureSiteRecoveryEvents skickas när en händelse genereras. 
- AzureSiteRecoveryReplicatedItems skickas när någon miljö ändras. Data uppdaterings tiden är vanligt vis 15 minuter efter en ändring. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Hur länge sparas data i Azure Monitor loggar? 

Som standard är kvarhållning i 31 dagar. Du kan öka perioden i avsnittet **användning och uppskattad kostnad** i Log Analytics arbets ytan. Klicka på **data kvarhållning** och välj intervall.

### <a name="whats-the-size-of-the-resource-logs"></a>Vad är storleken på resurs loggarna? 

Normalt är storleken på en logg 15-20 KB. 


## <a name="next-steps"></a>Nästa steg

Lär dig hur du övervakar med [Site Recovery inbyggd övervakning](site-recovery-monitor-and-troubleshoot.md)eller [Azure Monitor](monitor-log-analytics.md).


