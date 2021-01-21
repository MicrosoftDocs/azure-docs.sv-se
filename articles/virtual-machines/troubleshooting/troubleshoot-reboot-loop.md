---
title: Loop för Windows-omstart på en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du felsöker omstart av Windows-loop | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: ad0ed7e9619f0b789bf8949fe398aa27bc36b9e0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629648"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Loop för Windows-omstart på en virtuell Azure-dator
I den här artikeln beskrivs den omstart-loop som du kan uppleva på en virtuell Windows-dator (VM) i Microsoft Azure.

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](./boot-diagnostics.md) för att hämta skärm bilderna för en virtuell dator, hittar du en virtuell dator, men start processen avbryts och processen börjar om.

![Start skärm 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Orsak

Loopen för omstart inträffar på grund av följande orsaker:

### <a name="cause-1"></a>Orsak 1

Det finns en tjänst från tredje part som flaggats som kritisk och inte kan startas. Detta gör att operativ systemet startas om.

### <a name="cause-2"></a>Orsak 2

Vissa ändringar har gjorts i operativ systemet. Dessa är vanligt vis relaterade till en uppdaterings installation, program installation eller en ny princip. Du kan behöva kontrol lera följande loggar för ytterligare information:

- Händelseloggar
- CBS. logWindows
- Uppdatera. log

### <a name="cause-3"></a>Orsak 3

Skadat fil system kan orsaka detta. Det är dock svårt att diagnostisera och identifiera den ändring som orsakar att operativ systemet skadas.

## <a name="solution"></a>Lösning

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

Lös problemet genom att [säkerhetskopiera OS-disken](../windows/snapshot-copy-managed-disk.md)och [koppla OS-disken till en virtuell dator för räddning](./troubleshoot-recovery-disks-portal-windows.md)och följ sedan lösnings alternativen i enlighet med detta, eller prova lösningarna en i taget.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

1. När OS-disken är ansluten till en fungerande virtuell dator kontrollerar du att disken är flaggad som **online** i disk hanterings konsolen och noterar enhets beteckningen för den partition som innehåller mappen **\Windows** .

2. Om disken är **offline** anger du den till **online**.

3. Skapa en kopia av **\Windows\System32\config** -mappen om det behövs en återställning av ändringarna.

4. På den rädda virtuella datorn öppnar du Windows Registereditorn (regedit).

5. Välj **HKEY_LOCAL_MACHINE** nyckel och välj sedan **fil**  >  **inläsning Hive** på menyn.

6. Bläddra till SYSTEM filen i mappen **\Windows\System32\config**

7. Välj **Öppna**, Skriv **BROKENSYSTEM** som namn, expandera **HKEY_LOCAL_MACHINE** nyckeln och se ytterligare en nyckel med namnet **BROKENSYSTEM**.

8. Kontrol lera vilken ControlSet datorn startar från. Nyckel numret visas i följande register nyckel.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Kontrol lera vilken allvarlighets grad tjänsten för VM-agenten har genom följande register nyckel.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Om värdet för register nyckeln inte är inställt på **2**, går du vidare till nästa minskning.

11. Om värdet för register nyckeln är inställt på **2**, ändra värdet från **2** till **1**.

12. Om någon av följande nycklar finns och har värdet **2** eller **3**, och ändra sedan värdena till **1** :

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Välj nyckeln **BROKENSYSTEM** och välj sedan **Arkiv**  >  **ta bort Hive** från menyn.

14. Koppla bort OS-disken från den virtuella fel söknings datorn.

15. Ta bort disken från den virtuella fel söknings datorn och vänta ungefär 2 minuter på att Azure ska frigöra disken.

16. [Skapa en ny virtuell dator från OS-disken](../windows/create-vm-specialized.md).

17. Om problemet är löst kan du behöva installera om [RDAgent](/archive/blogs/mast/install-the-vm-agent-on-an-existing-azure-vm) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Återställ den virtuella datorn till den senast fungerande konfigurationen, Följ stegen i [så här startar du en virtuell Azure Windows-dator med senast fungerande konfiguration](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Lösning för orsak 3
>[!NOTE]
>Följande procedur bör endast användas som sista resurs. När återställning från Regback kan återställa åtkomst till datorn anses inte operativ systemet vara stabilt eftersom data går förlorade i registret mellan tidsstämpeln för Hive och den aktuella dagen. Du måste skapa en ny virtuell dator och göra planer på att migrera data.

1. När disken är ansluten till en fel söknings dator kontrollerar du att disken är flaggad som **online** i disk hanterings konsolen.

2. Skapa en kopia av **\Windows\System32\config** -mappen om det behövs en återställning av ändringarna.

3. Kopiera filerna i mappen **\Windows\System32\config\regback** och ersätt filerna i mappen **\Windows\System32\config** .

4. Ta bort disken från den virtuella fel söknings datorn och vänta ungefär 2 minuter på att Azure ska frigöra disken.

5. [Skapa en ny virtuell dator från OS-disken](../windows/create-vm-specialized.md).
