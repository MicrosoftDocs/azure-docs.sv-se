---
title: Ta bort ett Azure Migrate-projekt
description: I den här artikeln får du lära dig hur du tar bort ett Azure Migrate-projekt med hjälp av Azure Portal.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: 8c94bb23f5d514fef5cdacb855657efdf5219631
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714748"
---
# <a name="delete-an-azure-migrate-project"></a>Ta bort ett Azure Migrate-projekt

I den här artikeln beskrivs hur du tar bort [Azure Migrate](./migrate-services-overview.md) projekt.


## <a name="before-you-start"></a>Innan du börjar

Innan du tar bort ett projekt:

- När du tar bort ett projekt tas projektet och identifierade datormetadata bort.
- Om du har kopplat en Log Analytics-arbetsyta till serverutvärderingsverktyget för beroendeanalys bestämmer du om du vill ta bort arbetsytan. 
    - Arbetsytan tas inte bort automatiskt. Ta bort den manuellt.
    - Kontrollera vad en arbetsyta används för innan du tar bort den. Samma Log Analytics-arbetsyta kan användas för flera scenarier.
    - Innan du tar bort projektet hittar du en länk till arbetsytan **i Azure Migrate - Servers** Azure Migrate - Server  >  **Assessment**, under **OMS-arbetsytan**.
    - Om du vill ta bort en arbetsyta när du har tagit bort ett projekt, hittar du arbetsytan i relevant resursgrupp och följer [dessa anvisningar.](../azure-monitor/logs/delete-workspace.md)


## <a name="delete-a-project"></a>Ta bort ett projekt


1. I Azure Portal du den resursgrupp där projektet skapades.
2. På resursgruppssidan väljer du **Visa dolda typer.**
3. Välj projektet och de associerade resurser som du vill ta bort.
    - Resurstypen för Azure Migrate projekt är **Microsoft.Migrate/migrateprojects**.
    - I nästa avsnitt granskar du de resurser som skapats för identifiering, utvärdering och migrering i ett Azure Migrate projekt.
    - Om resursgruppen bara innehåller Azure Migrate projekt kan du ta bort hela resursgruppen.
    - Om du vill ta bort ett projekt från den tidigare Azure Migrate är stegen desamma. Resurstypen för dessa projekt är **Migreringsprojekt**.


## <a name="created-resources"></a>Skapade resurser

Dessa tabeller sammanfattar de resurser som skapats för identifiering, utvärdering och migrering i ett Azure Migrate projekt.

> [!NOTE]
> Ta bort nyckelvalvet med försiktighet eftersom det kan innehålla säkerhetsnycklar.

### <a name="projects-with-public-endpoint-connectivity"></a>Projekt med offentlig slutpunktsanslutning

#### <a name="vmwarephysical-server"></a>VMware/fysisk server

**Resurs** | **Typ**
--- | ---
"Appliancename"kv | Nyckelvalv
"Appliancename"site | Microsoft.OffAzure/VMwareSites
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName"project | Microsoft.Migrate/assessmentProjects
"ProjectName"rsvault | Recovery Services-valv
"ProjectName"-MigrateVault-* | Recovery Services-valv
migrateappligwsa* | Lagringskonto
migrateapplilsa* | Lagringskonto
migrateapplicsa* | Lagringskonto
migrateapplikv* | Nyckelvalv
migrateapplisbns* | Service Bus-namnområde

#### <a name="hyper-v-vm"></a>Hyper-V VM

**Resurs** | **Typ**
--- | ---
"ProjectName" | Microsoft.Migrate/migrateprojects
"ProjectName"project | Microsoft.Migrate/assessmentProjects
HyperV*kv | Nyckelvalv
HyperV*Plats | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Recovery Services-valv

<br/>
Följande tabeller sammanfattar de resurser som skapats av Azure Migrate för att identifiera, utvärdera och migrera servrar över ett privat nätverk med [hjälp av azure private link](./how-to-use-azure-migrate-with-private-endpoints.md).

### <a name="projects-with-private-endpoint-connectivity"></a>Projekt med anslutning till privat slutpunkt

#### <a name="vmware-vms---agentless-migrations"></a>Virtuella VMware-datorer – agentlösa migreringar

**Typ** | **Resurs** | **Privat slutpunkt <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Identifieringsplats (huvudplats) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | Projekt pe för \* \* "ApplianceName" 
Nyckelvalv | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/VMwareSites | "ApplianceName"*site | NA
Recovery Services-valv | "ApplianceName"*vault | NA
Lagringskonto | "ApplianceName"*usa | "ApplianceName" \* usa \* pe
Recovery Services-valv | "ProjectName"-MigrateVault-* | NA
Lagringskonto | migrateappligwsa* | NA
Lagringskonto | migrateapplilsa* | NA
Nyckelvalv | migrateapplikv* | NA
Service Bus-namnområde | migrateapplisbns* | NA

#### <a name="hyper-v-vms"></a>Hyper-V:s virtuella datorer 

**Typ** | **Resurs** | **Privat slutpunkt <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Identifieringsplats (huvudplats) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | Projekt pe för \* \* "ApplianceName" 
Nyckelvalv | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/HyperVSites | "ApplianceName"*site | NA
Recovery Services-valv | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe

#### <a name="physical-servers--aws-vms--gcp-vms"></a>Fysiska servrar/virtuella AWS-datorer/virtuella GCP-datorer 

**Typ** | **Resurs** | **Privat slutpunkt <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | "ProjectName" | "ProjectName" \* pe 
Identifieringsplats (huvudplats) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | Projekt pe för \* \* "ApplianceName" 
Nyckelvalv | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/serversites | "ApplianceName"*site | NA
Recovery Services-valv | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe


## <a name="next-steps"></a>Nästa steg

Lär dig hur du lägger till [ytterligare verktyg för](how-to-assess.md) utvärdering [och](how-to-migrate.md) migrering. 
