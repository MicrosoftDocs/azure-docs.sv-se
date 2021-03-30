---
title: Övervaka Azure Site Recovery processerver
description: Den här artikeln beskriver hur du övervakar Azure Site Recovery processerver som används för haveri beredskap för virtuella VMware-datorer/fysiska servrar
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: eebaa70cee99380ac67b8f6516a5b08ff2832c86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86134738"
---
# <a name="monitor-the-process-server"></a>Övervaka processervern

Den här artikeln beskriver hur du övervakar [Site Recovery](site-recovery-overview.md) processerver.

- Processervern används när du konfigurerar haveri beredskap för lokala virtuella VMware-datorer och fysiska servrar till Azure.
- Som standard körs processervern på konfigurations servern. Den installeras som standard när du distribuerar konfigurations servern.
- Om du vill skala och hantera större antal replikerade datorer och större volymer av replikeringstrafik kan du distribuera ytterligare, skalbara process servrar.

[Lär dig mer](vmware-physical-azure-config-process-server-overview.md) om rollen och distributionen av processervern.

## <a name="monitoring-overview"></a>Övervakningsöversikt

Eftersom processervern har så många roller, särskilt i replikerade datacachelagring, komprimering och överföring till Azure, är det viktigt att övervaka process Server hälsa kontinuerligt.

Det finns ett antal situationer som ofta påverkar process serverns prestanda. Problem som påverkar prestandan har en överlappande effekt på VM-hälsa, och på så sätt kan du skicka både processervern och dess replikerade datorer till ett kritiskt tillstånd. Exempel på situationer:

- Ett stort antal virtuella datorer använder en processerver, närmar sig eller överskrider rekommenderade begränsningar.
- Virtuella datorer som använder processervern har en hög omsättnings taxa.
- Nätverks data flödet mellan virtuella datorer och processervern är inte tillräckligt för att ladda upp replikeringsdata till processervern.
- Nätverks data flöde mellan processervern och Azure räcker inte för att ladda upp replikeringsdata från processervern till Azure.

Alla dessa problem kan påverka återställnings punkt målet för virtuella datorer. 

**Varför?** Eftersom generering av en återställnings punkt för en virtuell dator kräver att alla diskar på den virtuella datorn har en gemensam punkt. Om en disk har hög omsättnings tid är replikeringen långsam eller eftersom processervern inte är optimal, vilket påverkar hur effektivt återställnings punkter skapas.

## <a name="monitor-proactively"></a>Övervaka proaktivt

För att undvika problem med processervern är det viktigt att:

- Förstå särskilda krav för process servrar med hjälp av [kapacitets-och storleks vägledning](site-recovery-plan-capacity-vmware.md#capacity-considerations)och se till att process servrar distribueras och körs enligt rekommendationer.
- Övervaka aviseringar och Felsök problem när de inträffar, för att hålla process servrarna igång effektivt.


## <a name="process-server-alerts"></a>Bearbeta Server aviseringar

Processervern genererar ett antal hälso aviseringar som sammanfattas i följande tabell.

**Aviseringstyp** | **Information**
--- | ---
![Felfri][green] | Processervern är ansluten och felfritt.
![Varning][yellow] | CPU-användning > 80% under de senaste 15 minuterna
![Varning][yellow] | Minnes användning > 80% under de senaste 15 minuterna
![Varning][yellow] | Ledigt utrymme i cache-mappen < 30% under de senaste 15 minuterna
![Varning][yellow] | Site Recovery övervakar väntande/utgående data var femte minut och beräknar att data i processervern inte kan överföras till Azure inom 30 minuter.
![Varning][yellow] | Process Server-tjänsterna körs inte under de senaste 15 minuterna
![Kritiskt][red] | CPU-användning > 95% under de senaste 15 minuterna
![Kritiskt][red] | Minnes användning > 95% under de senaste 15 minuterna
![Kritiskt][red] | Ledigt utrymme i cache-mappen < 25% under de senaste 15 minuterna
![Kritiskt][red] | Site Recovery övervakar väntande/utgående data var femte minut och beräknar att data i processervern inte kan överföras till Azure inom 45 minuter.
![Kritiskt][red] | Inget pulsslag från processervern i 15 minuter.

![Tabell nyckel](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> Process serverns övergripande hälso status är baserad på den värsta varningen som genereras.



## <a name="monitor-process-server-health"></a>Övervaka process Server hälsa

Du kan övervaka hälso tillståndet för dina process servrar på följande sätt: 

1. Om du vill övervaka hälso tillståndet och statusen för en replikerad dator, och dess processerver, i valv > **replikerade objekt**, klickar du på den dator som du vill övervaka.
2. I **replikeringstillståndet** kan du övervaka hälso tillståndet för den virtuella datorn. Klicka på status för att öka detalj nivån för fel information.

    ![Bearbeta Server hälsa på VM-instrumentpanelen](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. I **process Server hälsa** kan du övervaka process serverns status. Öka detalj nivån för mer information.

    ![Information om processerver på VM-instrumentpanelen](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Hälso tillståndet kan också övervakas med hjälp av den grafiska representationen på den virtuella dator sidan.
    - En skalbar processerver markeras i orange om det finns varningar som är kopplade till den, och rött om det har allvarliga problem. 
    - Om processervern körs i standard distributionen på konfigurations servern markeras konfigurations servern i enlighet därmed.
    - Klicka på konfigurations servern eller processervern för att öka detalj nivån. Anteckna eventuella problem och eventuella reparations rekommendationer.

Du kan också övervaka process servrar i valvet under **Site Recovery-infrastruktur**. I **Hantera din Site Recovery-infrastruktur klickar du** på **konfigurations servrar**. Välj den konfigurations server som är kopplad till processervern och öka detalj nivån till process Server.


## <a name="next-steps"></a>Nästa steg

- Om du har problem med process servrar följer du vår [fel söknings vägledning](vmware-physical-azure-troubleshoot-process-server.md)
- Om du behöver mer hjälp kan du publicera din fråga på [sidan Microsoft Q&en fråga för Azure Site Recovery](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
