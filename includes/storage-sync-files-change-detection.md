---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: 1387933dc82c07e73b7715d6593238ea8c993e93
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005353"
---
Ändringar som görs i Azure-filresursen med hjälp av Azure Portal eller SMB identifieras inte omedelbart och replikeras som ändringar i Server slut punkten. Azure Files har ännu inte ändrings aviseringar eller journaler, så det finns inget sätt att automatiskt initiera en Sync-session när filerna ändras. På Windows Server använder Azure File Sync [Windows USN-journalering](/windows/win32/fileio/change-journals) för att automatiskt initiera en Sync-session när filer ändras.

För att kunna identifiera ändringar i Azure-filresursen har Azure File Sync ett schemalagt jobb som kallas *ändrings identifierings jobb*. Ett ändrings identifierings jobb räknar upp varje fil i fil resursen och jämför det sedan med Sync-versionen av filen. När ändringen av ändrings identifieringen avgör att filerna har ändrats initierar Azure File Sync en Sync-session. Ändrings identifierings jobbet initieras var 24: e timme. Eftersom ändrings identifierings jobbet fungerar genom att räkna upp varje fil i Azure-filresursen tar ändrings identifieringen längre upp i större namn områden än i mindre namn områden. För stora namn rymder kan det ta längre tid än en gång var 24: e timme att avgöra vilka filer som har ändrats.

Om du vill synkronisera filer som har ändrats i Azure-filresursen direkt kan du använda PowerShell-cmdleten **Invoke-AzStorageSyncChangeDetection** för att manuellt initiera identifieringen av ändringar i Azure-filresursen. Denna cmdlet är avsedd för scenarier där någon typ av automatiserad process gör ändringar i Azure-filresursen eller ändringarna utförs av en administratör (t. ex. att flytta filer och kataloger till resursen). För att slutanvändarna ska ändras, är rekommendationen att installera Azure File Sync agenten i en virtuell IaaS-dator och har slutanvändarna åtkomst till fil resursen via den virtuella IaaS-datorn. På så sätt synkroniseras alla ändringar snabbt till andra agenter utan att du behöver använda Invoke-AzStorageSyncChangeDetection-cmdleten. Mer information finns i [Invoke-AzStorageSyncChangeDetection-](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) dokumentationen.

>[!NOTE]
>Ändringar som görs i en Azure-filresurs med REST uppdaterar inte SMB senast ändrad-tiden och visas inte som en ändring genom synkronisering.

Vi undersöker hur du lägger till ändrings identifiering för en Azure-filresurs som liknar USN för volymer i Windows Server. Hjälp oss att prioritera den här funktionen för framtida utveckling genom att rösta på den på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).