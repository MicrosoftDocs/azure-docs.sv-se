---
title: Välj ett alternativ för VMware-migrering med Azure Migrate Server-migrering
description: Innehåller en översikt över alternativ för att migrera virtuella VMware-datorer till Azure med Azure Migrate Server-migrering
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 130fcaacedc8aaea7790f6aa9ca7463b4e378e02
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070719"
---
# <a name="select-a-vmware-migration-option"></a>Välj ett alternativ för VMware-migrering

Du kan migrera virtuella VMware-datorer till Azure med hjälp av verktyget för migrering av Azure Migrate Server. Det här verktyget erbjuder ett par alternativ för migrering av virtuella VMware-datorer:

- Migrering med hjälp av replikering utan agent. Migrera virtuella datorer utan att behöva installera något på dem.
- Migrering med en agent för replikering. Installera en agent på den virtuella datorn för replikering.


## <a name="compare-migration-methods"></a>Jämför metoder för migrering

Använd de här valda jämförelserna för att bestämma vilken metod som ska användas. Du kan också granska fullständiga support krav för [agent utan agent](migrate-support-matrix-vmware-migration.md#agentless-migration) och [agent-baserad](migrate-support-matrix-vmware-migration.md#agent-based-migration) migrering.

**Inställning** | **Utan agent** | **Agent-baserad**
--- | --- | ---
**Azure-behörigheter** | Du måste ha behörighet att skapa ett Azure Migrate-projekt och för att registrera Azure AD-appar som skapats när du distribuerar Azure Migrate-enheten. | Du behöver deltagar behörighet för Azure-prenumerationen. 
**Replikering** | Högst 300 virtuella datorer kan replikeras samtidigt från en vCenter Server.<br/> Om du har fler än 50 virtuella datorer för migrering skapar du flera batchar för virtuella datorer.<br/> Att replikera mer i taget påverkar prestanda.<br/><br/> I portalen kan du välja upp till 10 datorer på en gång för replikering. Om du vill replikera fler datorer lägger du till i batchar med 10.| Replikeringens kapacitet ökar genom skalning av Replikerings enheten.
**Distribution av utrustning** | [Azure Migrate](migrate-appliance.md) -installationen distribueras lokalt. | [Azure Migrate Replication](migrate-replication-appliance.md) har distribuerats lokalt.
**Site Recovery kompatibilitet** | Överensstämmelse. | Du kan inte replikera med Azure Migrate Server-migrering om du har konfigurerat replikering för en dator med Site Recovery.
**Mål disk** | Hanterade diskar | Hanterade diskar
**Disk gränser** | OS-disk: 2 TB<br/><br/> Datadisk: 32 TB<br/><br/> Maximalt antal diskar: 60 | OS-disk: 2 TB<br/><br/> Datadisk: 32 TB<br/><br/> Maximalt antal diskar: 63
**Genom strömnings diskar** | Stöds inte | Stöds
**UEFI-start** | Stöds. | Stöds.

## <a name="compare-deployment-steps"></a>Jämför distributions steg

När du har granskat begränsningarna kan du med hjälp av de steg som ingår i distributionen av varje lösning hjälpa dig att avgöra vilket alternativ du ska välja.

**Uppgift** | **Information** |**Utan agent** | **Agent-baserad**
--- | --- | --- | ---
**Distribuera Azure Migrate-apparaten** | En förenklad apparat som körs på en virtuell VMware-dator.<br/><br/> Installations programmet används för att identifiera och utvärdera datorer och migrera datorer med hjälp av en agent lös migrering. | Krävs.<br/><br/> Om du redan har konfigurerat installationen för utvärdering kan du använda samma installation för att migrera utan agent. | Krävs inte.<br/><br/> Om du har konfigurerat en installations program för utvärdering kan du lämna den på plats eller ta bort den om du är klar med utvärderingen.
**Använda verktyget Server bedömning** | Utvärdera datorer med verktyget Azure Migrate: Server bedömning. | Du kan utvärdera datorer innan du migrerar dem, men du behöver inte. | Utvärderingen är valfri.
**Använda verktyget Migreringsverktyg** | Lägg till verktyget för migrering av Azure Migrate server i Azure Migrate projektet. | Krävs | Krävs
**Förbereda VMware för migrering** | Konfigurera inställningar på VMware-servrar och virtuella datorer. | Krävs | Krävs
**Installera mobilitets tjänsten på virtuella datorer** | Mobilitets tjänsten körs på varje virtuell dator som du vill replikera | Krävs inte | Krävs
**Distribuera replikerings enheten** | [Replikeringen](migrate-replication-appliance.md) används för agent-baserad migrering. Den ansluter mellan mobilitets tjänsten som körs på virtuella datorer och Server migrering. | Krävs inte | Krävs
**Replikera virtuella datorer**. Aktivera VM-replikering. | Konfigurera replikeringsinställningar och välj virtuella datorer som ska replikeras | Krävs | Krävs
**Kör en testmigrering** | Kör en testmigrering för att se till att allt fungerar som förväntat. | Krävs | Krävs
**Kör en fullständig migrering** | Migrera de virtuella datorerna. | Krävs | Krävs



## <a name="next-steps"></a>Nästa steg

[Migrera virtuella VMware-datorer](tutorial-migrate-vmware.md) med utan agent migrering.



