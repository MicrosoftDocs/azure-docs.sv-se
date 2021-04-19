---
title: Välj ett VMware-migreringsalternativ med Azure Migrate Server Migration
description: Innehåller en översikt över alternativ för att migrera virtuella VMware-datorer till Azure med Azure Migrate Server Migration
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 22a0629d50ee8181ffcbfe7dad32ab76fb3e68fd
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714172"
---
# <a name="select-a-vmware-migration-option"></a>Välj ett alternativ för VMware-migrering

Du kan migrera virtuella VMware-datorer till Azure med Azure Migrate servermigrering. Det här verktyget erbjuder ett par alternativ för migrering av virtuella VMware-datorer:

- Migrering med agentlös replikering. Migrera virtuella datorer utan att behöva installera något på dem.
- Migrering med en agent för replikering. Installera en agent på den virtuella datorn för replikering.


## <a name="compare-migration-methods"></a>Jämför migreringsmetoder

Använd dessa valda jämförelser för att avgöra vilken metod du ska använda. Du kan också granska fullständiga supportkrav för [agentlös](migrate-support-matrix-vmware-migration.md#agentless-migration) [och agentbaserad](migrate-support-matrix-vmware-migration.md#agent-based-migration) migrering.

**Inställning** | **Utan agent** | **Agentbaserad**
--- | --- | ---
**Azure-behörigheter** | Du behöver behörighet att skapa ett Azure Migrate-projekt och registrera Azure AD-appar som skapas när du distribuerar Azure Migrate-installationen. | Du behöver deltagarbehörighet för Azure-prenumerationen. 
**Replikering** | Högst 500 virtuella datorer kan replikeras samtidigt från en vCenter Server. I portalen kan du välja upp till 10 datorer samtidigt för replikering. Om du vill replikera fler datorer lägger du till batchar om 10.| Replikeringskapaciteten ökar genom att replikeringsinstallationen skalas.
**Distribution av installation** | Den [Azure Migrate-installationen](migrate-appliance.md) distribueras lokalt. | Den [Azure Migrate replikeringsinstallationen](migrate-replication-appliance.md) distribueras lokalt.
**Site Recovery kompatibilitet** | Kompatibel. | Du kan inte replikera med Azure Migrate Server Migration om du har ställt in replikering för en dator med hjälp av Site Recovery.
**Måldisk** | Hanterade diskar | Hanterade diskar
**Diskgränser** | OS-disk: 2 TB<br/><br/> Datadisk: 32 TB<br/><br/> Maximalt antal diskar: 60 | OS-disk: 2 TB<br/><br/> Datadisk: 32 TB<br/><br/> Maximalt antal diskar: 63
**Genomströmningsdiskar** | Stöds inte | Stöds
**UEFI-start** | Stöds. | Stöds. 
**Anslutningsmöjligheter** | Offentligt Internet <br/> ExpressRoute med Microsoft-peering <br/> <br/> [Lär dig](./replicate-using-expressroute.md) hur du använder privata slutpunkter för replikering via en privat ExpressRoute-peering eller en S2S VPN-anslutning. |Offentligt Internet <br/> ExpressRoute med privat peering <br/> ExpressRoute med Microsoft-peering <br/> Plats-till-plats-VPN

## <a name="compare-deployment-steps"></a>Jämföra distributionssteg

När du har granskat begränsningarna kan det hjälpa dig att avgöra vilket alternativ du ska välja genom att förstå stegen som ingår i distributionen av varje lösning.

**Uppgift** | **Information** |**Utan agent** | **Agentbaserad**
--- | --- | --- | ---
**Distribuera Azure Migrate-apparaten** | En förenklad installation som körs på en virtuell VMware-dator.<br/><br/> Installationen används för att identifiera och utvärdera datorer och för att migrera datorer med agentlös migrering. | Krävs.<br/><br/> Om du redan har ställt in apparaten för utvärdering kan du använda samma installation för agentlös migrering. | Krävs inte.<br/><br/> Om du har skapat en apparat för utvärdering kan du lämna den på plats eller ta bort den om du är klar med utvärderingen.
**Använda serverutvärderingsverktyget** | Utvärdera datorer med verktyget Azure Migrate:Server Assessment. | Utvärdering är valfritt. | Utvärdering är valfritt.
**Använda servermigreringsverktyget** | Lägg till Azure Migrate Server Migration i Azure Migrate projekt. | Obligatorisk | Obligatorisk
**Förbereda VMware för migrering** | Konfigurera inställningar på VMware-servrar och virtuella datorer. | Obligatorisk | Obligatorisk
**Installera tjänsten Mobility virtuella datorer** | tjänsten Mobility körs på varje virtuell dator som du vill replikera | Krävs inte | Obligatorisk
**Distribuera replikeringsinstallationen** | [Replikeringsinstallationen](migrate-replication-appliance.md) används för agentbaserad migrering. Den ansluter mellan tjänsten Mobility körs på virtuella datorer och Server Migration. | Krävs inte | Obligatorisk
**Replikera virtuella datorer**. Aktivera VM-replikering. | Konfigurera replikeringsinställningar och välj virtuella datorer som ska replikeras | Obligatorisk | Obligatorisk
**Kör en testmigrering** | Kör en testmigrering för att se till att allt fungerar som förväntat. | Obligatorisk | Obligatorisk
**Köra en fullständig migrering** | Migrera de virtuella datorerna. | Obligatorisk | Obligatorisk



## <a name="next-steps"></a>Nästa steg

[Migrera virtuella VMware-datorer](tutorial-migrate-vmware.md) med agentlös migrering.



