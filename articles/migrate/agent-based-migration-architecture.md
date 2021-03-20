---
title: Agent-baserad migrering i Azure Migrate Server-migrering
description: Innehåller en översikt över den agentbaserade virtuella VMware-migreringen i Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: c605c21307cda874f34ae5ea9f4e4959e5e6c183
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861958"
---
# <a name="agent-based-migration-architecture"></a>Agentbaserad migreringsarkitektur

Den här artikeln innehåller en översikt över arkitekturen och processerna som används för agentbaserade replikering av virtuella VMware-datorer med verktyget [Azure Migrate: Migreringsverktyg för Server](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

Med hjälp av Azure Migrate: Server-migrering kan du replikera virtuella VMware-datorer med ett par alternativ:

- Migrera virtuella datorer med agent-baserad replikering enligt beskrivningen i den här artikeln.
- Migrera virtuella VMware-datorer med hjälp av replikering utan agent. Detta migrerar virtuella datorer utan att behöva installera något på dem.

Lär dig mer om [att välja och jämföra](server-migrate-overview.md) migrations metoder för virtuella VMware-datorer. 


## <a name="agent-based-migration"></a>Agent-baserad migrering

Agent-baserad migrering används för att migrera lokala virtuella VMware-datorer och fysiska servrar till Azure. Den kan också användas för att migrera andra lokala virtualiserade servrar, samt privata och offentliga virtuella moln, inklusive AWS-instanser och GCP-VM: ar. Med agent-baserad migrering i Azure Migrate används vissa Server dels funktioner från tjänsten [Azure Site Recovery](../site-recovery/site-recovery-overview.md) .


## <a name="architectural-components"></a>Arkitekturkomponenter

Diagrammet illustrerar de komponenter som ingår i en agent-baserad migrering.

![Diagrammet visar komponenterna för agent-baserad migrering, som förklaras i en tabell.](./media/agent-based-replication-architecture/architecture.png)

Tabellen sammanfattar de komponenter som används för agent-baserad migrering.

**Komponent** | **Information** | **Installation**
--- | --- | ---
**Replikeringsfil** | Replikeringstjänsten (konfigurations Server/processerver) är en lokal dator som fungerar som en brygga mellan den lokala miljön och Server migrering. Enheten identifierar den lokala dator inventeringen så att Server migreringen kan dirigera replikering och migrering. Enheten har två komponenter:<br/><br/> **Konfigurations Server**: ansluter till Server migration och samordnar replikering.<br/> **Processerver**: hanterar datareplikering. Processervern tar emot dator data, komprimerar och krypterar den och skickar dem till Azure. I Azure skriver server migrering data till Managed disks. | Som standard installeras processervern tillsammans med konfigurations servern på replikerings enheten.
**Mobilitetstjänsten** | Mobilitets tjänsten är en agent som är installerad på varje dator som du vill replikera och migrera. Den skickar replikeringsdata från datorn till processervern. | Installationsfiler för olika versioner av mobilitets tjänsten finns på replikerings enheten. Du kan hämta och installera den agent du behöver, i enlighet med operativ systemet och den version av datorn som du vill replikera.

## <a name="mobility-service-installation"></a>Installation av mobilitetstjänsten

Du kan distribuera mobilitets tjänsten på följande sätt:

- **Push-installation**: mobilitets tjänsten installeras av processervern när du aktiverar skydd för en dator. 
- **Installera manuellt**: du kan installera mobilitets tjänsten manuellt på varje dator via UI eller kommando tolken.

Mobilitets tjänsten kommunicerar med replikerings enheten och de replikerade datorerna. Om du har antivirus program som körs på replikerings enheten, bearbetar servrar eller datorer som replikeras, ska följande mappar undantas från genomsökningen:


- C:\Program\Microsoft Azure Recovery Services agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (på Windows-datorer med mobilitets tjänsten installerad)

## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en dator börjar inledande replikering till Azure.
2. Under den inledande replikeringen läser mobilitets tjänsten data från dator diskarna och skickar dem till processervern.
3. Dessa data används för att dirigera en kopia av disken i din Azure-prenumeration. 
4. När den inledande replikeringen har slutförts börjar replikeringen av delta ändringar till Azure. Replikering är block nivå och nästan kontinuerlig.
4. Mobilitets tjänsten fångar upp skrivning till disk minne genom att integrera med underlag rings systemet i operativ systemet. Den här metoden undviker disk-I/O-åtgärder på den replikerande datorn för stegvis replikering. 
5. Spårade ändringar för en dator skickas till processervern på inkommande port HTTPS 9443. Den här porten kan ändras. Processervern komprimerar och krypterar den och skickar den till Azure. 

## <a name="ports"></a>Portar

**Enhet** | **Anslutning**
--- | --- 
**Replikera datorer** | Mobilitets tjänsten som körs på virtuella datorer kommunicerar med den lokala replikeringstjänsten på port HTTPS 443 inkommande, för hantering av replikering.<br/><br/> Datorer skickar replikeringsdata till processervern på port HTTPS 9443 inkommande. Den här porten kan ändras.
**Replikeringsfil** | Replikeringstjänsten dirigerar replikering med Azure över Port HTTPS 443 utgående.
**Processerver** | Processervern tar emot replikeringsdata, optimerar och krypterar den och skickar den till Azure Storage via port 443 utgående.


## <a name="performance-and-scaling"></a>Prestanda och skalning

Som standard distribuerar du en enda replikeringsfil som kör både konfigurations servern och processervern. Om du bara replikerar några få datorer räcker den här distributionen. Men om du replikerar och migrerar hundratals datorer kanske en enskild processerver inte kan hantera all replikeringstrafik. I det här fallet kan du distribuera ytterligare, skalbara process servrar.

### <a name="plan-vmware-deployment"></a>Planera VMware-distribution

Om du replikerar virtuella VMware-datorer kan du använda [Site Recovery distributions planeraren för VMware för](../site-recovery/site-recovery-deployment-planner.md)att fastställa prestanda krav, inklusive daglig data ändrings takt och de process servrar som du behöver.

### <a name="replication-appliance-capacity"></a>Kapacitet för replikerings utrustning

Använd värdena i den här tabellen för att avgöra om du behöver en ytterligare processerver i distributionen.

- Om den dagliga ändrings hastigheten (omsättnings takten) är över 2 TB distribuerar du en ytterligare processerver.
- Om du replikerar fler än 200 datorer kan du distribuera en ytterligare replikeringsfil.

**Processor** | **Minne** | **Ledigt utrymme – cachelagring av data** | **Omsättnings pris** | **Begränsningar för replikering**
--- | --- | --- | --- | ---
8 virtuella processorer (2 Sockets * 4 kärnor \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB eller mindre | < 100-datorer 
12 virtuella processorer (2 Sockets * 6 kärnor \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB till 1 TB | 100-150-datorer.
16 virtuella processorer (2 Sockets * 8 kärnor \@ 2,5 GHz) | 32 GB |  1 TB | 1 TB till 2 TB | 151-200-datorer.

### <a name="sizing-scale-out-process-servers"></a>Storleks skalbara process servrar

Om du behöver distribuera en skalbar processerver använder du den här tabellen för att räkna ut Server storleken.

**Processerver** | **Ledigt utrymme för cachelagring av data** | **Omsättnings pris** | **Begränsningar för replikering**
--- | --- | --- | --- 
4 virtuella processorer (2 Sockets * 2 kärnor \@ 2,5 GHz), 8 GB minne | 300 GB | 250 GB eller mindre | Upp till 85 datorer 
8 virtuella processorer (2 Sockets * 4 kärnor \@ 2,5 GHz), 12 GB minne | 600 GB | 251 GB till 1 TB | 86-150-datorer.
12 virtuella processorer (2 Sockets * 6 kärnor \@ 2,5 GHz), 24 GB minne | 1 TB | 1-2 TB | 151-225-datorer.

## <a name="throttle-upload-bandwidth"></a>Begränsa överförings bandbredden.

VMware-trafik som replikeras till Azure går genom en speciell processerver. Du kan begränsa uppladdnings flödet genom att begränsa bandbredden på datorerna som körs som process servrar. Du kan påverka bandbredden med hjälp av den här register nyckeln:

- Registervärdet HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM anger antalet trådar som används för data överföring (inledande eller delta-replikering) på en disk. Ett högre värde ökar nätverks bandbredden som används för replikering. Standardvärdet är fyra. Det maximala värdet är 32. Övervaka trafiken för att optimera värdet.
- Dessutom kan du begränsa bandbredden på processervern på följande sätt:

    1. Öppna snapin-modulen Azure Backup MMC på processervern. Det finns en genväg på Skriv bordet eller i mappen C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. Välj **ändra egenskaper** i snapin-modulen.
    3. Under **begränsning** väljer du **Aktivera användnings begränsning för Internet bandbredd för säkerhets kopierings åtgärder**. Ange gränserna för arbets tid och ledig tid. Giltiga intervall är mellan 512 och 1 023 Mbit/s.


## <a name="next-steps"></a>Nästa steg

Testa [agent-baserad migrering](tutorial-migrate-vmware-agent.md) för [VMware](tutorial-migrate-vmware-agent.md) eller [fysiska servrar](tutorial-migrate-physical-virtual-machines.md).
