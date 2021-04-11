---
title: Migrera lokala datorer med Azure Migrate
description: Den här artikeln sammanfattar hur du migrerar lokala datorer till Azure och rekommenderar Azure Migrate.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 5f5a0f65bfbde5ef8513780933cc570a4b5737df
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581141"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrera lokala datorer till Azure

I den här artikeln beskrivs alternativ för migrering av lokala datorer till Azure. 

## <a name="migrate-with-azure-migrate"></a>Migrera med Azure Migrate

Vi rekommenderar att du migrerar datorer till Azure med hjälp av tjänsten [Azure Migrate](../migrate/migrate-services-overview.md) . Azure Migrate är syftet – byggd för Server-migrering. Azure Migrate tillhandahåller en central hubb för identifiering, utvärdering och migrering av lokala datorer till Azure.

Följ dessa länkar om du vill migrera med Azure Migrate:

- [Lär dig mer om](../migrate/server-migrate-overview.md) migrerings alternativ för virtuella VMware-datorer och migrera sedan virtuella datorer till Azure med [agent](../migrate/tutorial-migrate-vmware.md) lös eller [agent-baserad](../migrate/tutorial-migrate-vmware-agent.md) migrering.
- [Migrera virtuella Hyper-V-datorer](../migrate/tutorial-migrate-hyper-v.md) till Azure.
- Migrera [fysiska servrar eller andra virtuella datorer](../migrate/tutorial-migrate-physical-virtual-machines.md), inklusive [AWS-instanser](../migrate/tutorial-migrate-aws-virtual-machines.md) till Azure.

## <a name="migrate-with-site-recovery"></a>Migrera med Site Recovery
Site Recovery bör endast användas för haveri beredskap och inte migrering.

Om du redan använder Azure Site Recovery och du vill fortsätta att använda den för migrering, följer du samma steg som du använder för haveri beredskap.

- Virtuella VMware-datorer: [Förbered Azure](tutorial-prepare-azure.md) och [VMware](vmware-azure-tutorial-prepare-on-premises.md), starta [replikering av datorer](vmware-azure-tutorial.md), [kontrol lera](tutorial-dr-drill-azure.md) att allt fungerar och [kör en redundansväxling](vmware-azure-tutorial-failover-failback.md).
- Virtuella Hyper-V-datorer: [Förbered Azure](tutorial-prepare-azure-for-hyperv.md) och [Hyper-v](hyper-v-prepare-on-premises-tutorial.md), starta [replikering av datorer](hyper-v-azure-tutorial.md), [kontrol lera](tutorial-dr-drill-azure.md) att allt fungerar och [kör en redundansväxling](hyper-v-azure-failover-failback-tutorial.md).
- Fysiska servrar: [Följ genom gången](physical-azure-disaster-recovery.md) för att förbereda Azure, förbereda datorer för haveri beredskap och konfigurera replikering.

> [!NOTE]
> När du kör en redundansväxling för haveri beredskap, som ett sista steg, genomför du redundansväxlingen. När du migrerar lokala datorer är **inchecknings** alternativet inte relevant. I stället väljer du alternativet **fullständig migrering** . 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs vanliga frågor](../migrate/resources-faq.md) om Azure Migrate.

  
