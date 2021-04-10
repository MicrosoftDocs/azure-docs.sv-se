---
title: Ta bort ett Azure Migrate-projekt
description: I den här artikeln lär du dig hur du kan ta bort ett Azure Migrate-projekt med hjälp av Azure Portal.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: bfb4db5d3ebf69f9c7f552c175d33a8b817d1562
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595141"
---
# <a name="delete-an-azure-migrate-project"></a>Ta bort ett Azure Migrate-projekt

I den här artikeln beskrivs hur du tar bort ett [Azure Migrate](./migrate-services-overview.md) -projekt.


## <a name="before-you-start"></a>Innan du börjar

Innan du tar bort ett projekt:

- När du tar bort ett projekt raderas projektet och identifierade datorns metadata.
- Om du har kopplat en Log Analytics arbets yta till verktyget för Server utvärdering för beroende analys bestämmer du om du vill ta bort arbets ytan. 
    - Arbets ytan tas inte bort automatiskt. Ta bort den manuellt.
    - Kontrol lera vilken arbets yta som används innan du tar bort den. Samma Log Analytics arbets yta kan användas för flera scenarier.
    - Innan du tar bort projektet hittar du en länk till arbets ytan i **Azure Migrate-servrar**  >  **Azure Migrate-Server-utvärdering**, under **OMS-arbetsyta**.
    - Om du vill ta bort en arbets yta efter att du har tagit bort ett projekt söker du efter arbets ytan i relevant resurs grupp och följer [de här anvisningarna](../azure-monitor/logs/delete-workspace.md).


## <a name="delete-a-project"></a>Ta bort ett projekt


1. Öppna resurs gruppen där projektet skapades i Azure Portal.
2. På sidan resurs grupp väljer du **Visa dolda typer**.
3. Markera projektet och de associerade resurserna som du vill ta bort.
    - Resurs typen för Azure Migrate projekt är **Microsoft. Migrate/migrateprojects**.
    - I nästa avsnitt granskar du de resurser som har skapats för identifiering, utvärdering och migrering i ett Azure Migrate projekt.
    - Om resurs gruppen bara innehåller Azure Migrate projektet kan du ta bort hela resurs gruppen.
    - Om du vill ta bort ett projekt från den tidigare versionen av Azure Migrate, är stegen desamma. Resurs typen för dessa projekt är ett **migreringsjobb**.


## <a name="created-resources"></a>Skapade resurser

Dessa tabeller sammanfattar resurserna som skapats för identifiering, utvärdering och migrering i ett Azure Migrate projekt.

> [!NOTE]
> Ta bort nyckel valvet med försiktighet eftersom det kan innehålla säkerhets nycklar.

### <a name="vmwarephysical-server"></a>VMware/fysisk server

**Resurs** | **Typ**
--- | ---
"Appliancename" kv | Nyckelvalv
"Appliancename"-webbplats | Microsoft. OffAzure/VMwareSites
ProjectName | Microsoft. Migrate/migrateprojects
"ProjectName"-projekt | Microsoft. Migrate/assessmentProjects
"ProjectName" rsvault | Recovery Services-valv
"ProjectName"-MigrateVault-* | Recovery Services-valv
migrateappligwsa* | Lagringskonto
migrateapplilsa* | Lagringskonto
migrateapplicsa* | Lagringskonto
migrateapplikv* | Nyckelvalv
migrateapplisbns16041 | Service Bus-namnområde

### <a name="hyper-v-vm"></a>Hyper-V VM 

**Resurs** | **Typ**
--- | ---
ProjectName | Microsoft. Migrate/migrateprojects
"ProjectName"-projekt | Microsoft. Migrate/assessmentProjects
HyperV * kv | Nyckelvalv
HyperV *-webbplats | Microsoft. OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Recovery Services-valv


## <a name="next-steps"></a>Nästa steg

Lär dig hur du lägger till ytterligare verktyg för [bedömning](how-to-assess.md) och [migrering](how-to-migrate.md) . 
