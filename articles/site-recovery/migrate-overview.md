---
title: Jämför Azure Migrate och Site Recovery för migrering till Azure
description: Sammanfattar fördelarna med att använda Azure Migrate för migrering i stället för Site Recovery.
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: c4d30b8b21bf3a0ea27467d2120b04a107f5a73f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581405"
---
# <a name="migrating-to-azure"></a>Migrera till Azure

För migrering rekommenderar vi att du använder tjänsten Azure Migrate för att migrera virtuella datorer och servrar till Azure, i stället för tjänsten Azure Site Recovery. [Läs mer](../migrate/migrate-services-overview.md) om Azure Migrate.


## <a name="why-use-azure-migrate"></a>Varför ska jag använda Azure Migrate?

Att använda Azure Migrate för migrering ger ett antal fördelar:
 
 
- Azure Migrate tillhandahåller en centraliserad hubb för identifiering, utvärdering och migrering till Azure.
- Användning av Azure Migrate ger möjlighet till samverkan och framtida utöknings barhet med Azure Migrate verktyg, andra Azure-tjänster och verktyg från tredje part.
- Verktyget Azure Migrate: Migreringsverktyg för Server är syftet-byggt för Server migrering till Azure. Den är optimerad för migrering. Du behöver inte lära dig mer om begrepp och scenarier som inte är direkt relevanta för migrering. 
- Det finns inga avgifter för verktygs användning för migrering i 180 dagar, från tidpunkten då replikeringen startades för en virtuell dator. Det ger dig tid att slutföra migreringen. Du betalar bara för de lagrings-och nätverks resurser som används i replikeringen och för de beräknings avgifter som förbrukas under testmigreringar.
- Azure Migrate stöder alla migrerings scenarier som stöds av Site Recovery. För virtuella VMware-datorer tillhandahåller Azure Migrate dessutom ett alternativ för att migrera utan agent.
- Vi prioriterar nya funktioner för migrering för Azure Migrate: Migreringsverktyg för Server. Dessa funktioner är inte riktade för Site Recovery.

## <a name="when-to-use-site-recovery"></a>När ska jag använda Site Recovery?

Site Recovery ska användas:

- För haveri beredskap för lokala datorer till Azure.
- För haveri beredskap för virtuella Azure-datorer mellan Azure-regioner.

Även om vi rekommenderar att du använder Azure Migrate för att migrera lokala servrar till Azure, kan du fortsätta att använda den för att slutföra migreringen om du redan har startat migreringen med Site Recovery.  

## <a name="next-steps"></a>Nästa steg

> [Läs vanliga frågor](../migrate/resources-faq.md) om Azure Migrate.
