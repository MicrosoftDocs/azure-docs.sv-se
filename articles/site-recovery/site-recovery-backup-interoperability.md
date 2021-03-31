---
title: Stöd för att använda Azure Site Recovery med Azure Backup
description: Innehåller en översikt över hur Azure Site Recovery och Azure Backup kan användas tillsammans.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: c334eee34eb878135d3d81ab15d03618c6604846
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135178"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Stöd för att använda Site Recovery med Azure Backup

I den här artikeln sammanfattas stödet för att använda tjänsten [Site Recovery](site-recovery-overview.md) tillsammans med [tjänsten Azure Backup](../backup/backup-overview.md).

**Åtgärd** | **Site Recovery-stöd** | **Information**
--- | --- | ---
**Distribuera tjänster tillsammans** | Stöds | Tjänsterna är kompatibla och kan konfigureras tillsammans.
**Säkerhets kopiering/återställning av fil** | Stöds | När säkerhets kopiering och replikering har Aktiver ATS för en virtuell dator och säkerhets kopior görs, finns det inga problem med att återställa filer på virtuella datorer på den virtuella datorn eller en grupp med virtuella datorer. Replikeringen fortsätter som vanligt utan att någon ändring av replikeringstillståndet har ändrats.
**Disk återställning** | Inget aktuellt stöd | Om du återställer en säkerhets kopie rad disk måste du inaktivera och återaktivera replikering för den virtuella datorn igen.
**Återställning av virtuella datorer** | Inget aktuellt stöd | Om du återställer en virtuell dator eller grupp med virtuella datorer måste du inaktivera och återaktivera replikering för den virtuella datorn.  


