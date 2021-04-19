---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716757"
---
1. För virtuella Azure-datorer som har valts för säkerhetskopiering Azure Backup startar ett säkerhetskopieringsjobb enligt det säkerhetskopieringsschema som du anger.
1. Under den första säkerhetskopieringen installeras ett säkerhetskopieringstillägg på den virtuella datorn om den virtuella datorn körs.
    - För virtuella Windows-datorer [installeras tillägget VMSnapshot.](../articles/virtual-machines/extensions/vmsnapshot-windows.md)
    - För virtuella Linux-datorer [installeras tillägget VMSnapshotLinux.](../articles/virtual-machines/extensions/vmsnapshot-linux.md)
1. För virtuella Windows-datorer som kör samordnar Säkerhetskopiering med Windows tjänsten Volume Shadow Copy (VSS) för att ta en app-konsekvent ögonblicksbild av den virtuella datorn.
    - Som standard tar Säkerhetskopiering fullständiga VSS-säkerhetskopior.
    - Om Backup inte kan ta en app-konsekvent ögonblicksbild tar den en fil konsekvent ögonblicksbild av den underliggande lagringen (eftersom inga program skriver medan den virtuella datorn stoppas).
1. För virtuella Linux-datorer tar Backup en fil konsekvent säkerhetskopia. För app-konsekventa ögonblicksbilder måste du manuellt anpassa för-/efterskript.
1. När Backup tar ögonblicksbilden överförs data till valvet.
    - Säkerhetskopieringen optimeras genom att varje VM-disk säkerhetskopieras parallellt.
    - För varje disk som ska säkerhetskopieras läser Azure Backup blocken på disken samt identifierar och överför endast de datablock som har ändrats (delta) sedan den tidigare säkerhetskopieringen.
    - Ögonblicksbilddata kopieras kanske inte direkt till valvet. Det kan ta några timmar vid hög belastning. Den totala säkerhetskopieringstiden för en virtuell dator blir mindre än 24 timmar för dagliga säkerhetskopieringsprinciper.
1. Ändringar som görs i en virtuell Windows-Azure Backup har aktiverats på den är:
    - Microsoft Visual C++ 2013 Redistributable(x64) – 12.0.40660 är installerat på den virtuella datorn
    - Starttypen för tjänsten Volume Shadow Copy (VSS) har ändrats till automatisk från manuell
    - Windows-tjänsten IaaSVmProvider har lagts till

1. När dataöverföringen är klar tas ögonblicksbilden bort och en återställningspunkt skapas.

![Arkitektur för säkerhetskopiering av virtuella Azure-datorer](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
