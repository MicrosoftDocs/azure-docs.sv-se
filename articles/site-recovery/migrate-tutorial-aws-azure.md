---
title: Migrera virtuella AWS-datorer till Azure med Azure Migrate
description: Den här artikeln beskriver alternativ för att migrera AWS-instanser till Azure och rekommenderar Azure Migrate.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 83d8106b9f206da9095c37b6179bdda8001b439c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009066"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrera virtuella AWS-datorer (Amazon Web Services) till Azure

I den här artikeln beskrivs alternativ för migrering av Amazon Web Services-instanser (AWS) till Azure.

## <a name="migrate-with-azure-migrate"></a>Migrera med Azure Migrate

Vi rekommenderar att du migrerar AWS EC2-instanser till Azure med hjälp av tjänsten [Azure Migrate](../migrate/migrate-services-overview.md) . Azure Migrate är syftet – byggd för Server-migrering. Azure Migrate tillhandahåller en central hubb för identifiering, utvärdering och migrering av lokala datorer till Azure.

[Lär dig hur](../migrate/tutorial-migrate-aws-virtual-machines.md) du MIGRERAr AWS-instanser med Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Migrera med Site Recovery

Site Recovery bör endast användas för haveri beredskap och inte migrering.

Om du redan använder Azure Site Recovery och du vill fortsätta att använda den för AWS-migrering följer du samma steg som du använder för att konfigurera [haveri beredskap för fysiska datorer](physical-azure-disaster-recovery.md).


> [!NOTE]
> När du kör en redundansväxling för haveri beredskap, som ett sista steg, genomför du redundansväxlingen. När du migrerar AWS-instanser är **inchecknings** alternativet inte relevant. I stället väljer du alternativet **fullständig migrering** . 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs vanliga frågor](../migrate/resources-faq.md) om Azure Migrate.
