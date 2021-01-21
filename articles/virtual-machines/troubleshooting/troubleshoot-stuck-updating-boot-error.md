---
title: Starten av virtuella Azure-datorer har fastnat på Windows Update | Microsoft Docs
description: Lär dig hur du felsöker problemet när en Azure VM-start fastnar på Windows Update.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 3090b7b889d914fc0cdb598b8bf29a73c81f50cb
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632011"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Starten av virtuella Azure-datorer fastnar på Windows Update

Den här artikeln hjälper till att lösa problemet när den virtuella datorn (VM) fastnar i Windows Update fasen under start. 


## <a name="symptom"></a>Symptom

 En virtuell Windows-dator startar inte. När du kontrollerar skärm bilderna i fönstret [Boot Diagnostics](../troubleshooting/boot-diagnostics.md) ser du att Start programmet fastnar i uppdaterings processen. Följande är exempel på meddelanden som du kan få:

- Om du installerar Windows # #% stängs inte datorn av. Det tar en stund att starta om datorn flera gånger
- Håll datorn kvar tills det är färdigt. Installerar uppdatering # av #... 
- Det gick inte att slutföra uppdateringar som ångrar ändringar Stäng inte av datorn
- Det gick inte att konfigurera Windows-uppdateringar återställnings ändringar Stäng inte av datorn
- Fel < felkod > tillämpa uppdaterings åtgärder # # # # # av # # (\Regist...)
- Allvarligt fel < felkod > att tillämpa uppdaterings åtgärder # # # # # av # #...)


## <a name="solution"></a>Lösning
> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

Uppdaterings processen kan ta en stund beroende på hur många uppdateringar som installeras eller återställs. Lämna den virtuella datorn i det här läget i 8 timmar. Om den virtuella datorn fortfarande är i det här läget efter den perioden startar du om den virtuella datorn från Azure Portal och ser om den kan starta normalt. Om det här steget inte fungerar kan du prova följande lösning.

### <a name="remove-the-update-that-causes-the-problem"></a>Ta bort uppdateringen som orsakar problemet

1. Ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md). 
2. [Koppla OS-disken till en virtuell dator för återställning](troubleshoot-recovery-disks-portal-windows.md).
3. När OS-disken är ansluten till den virtuella återställnings datorn kör du **diskmgmt. msc** för att öppna disk hantering och kontrollerar att den anslutna disken är **online**. Anteckna enhets beteckningen som är kopplad till den anslutna OS-disken som innehåller mappen \Windows. Om disken är krypterad dekrypterar du disken innan du fortsätter med nästa steg i det här dokumentet.

4. Öppna en upphöjd kommando tolks instans (kör som administratör). Kör följande kommando för att hämta en lista över de uppdaterings paket som finns på den anslutna OS-disken:

    ```console
    dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt
    ```

    Om den anslutna OS-disken till exempel är enhet F kör du följande kommando:

    ```console
    dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
    ```

5. Öppna C:\temp\Patch_level.txt-filen och Läs den sedan längst ned. Leta upp uppdateringen som **väntar** eller väntar på att **avinstalleras** .  Följande är ett exempel på uppdaterings status:

    ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Ta bort uppdateringen som orsakade problemet:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Exempel: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > Beroende på paketets storlek tar DISM-verktyget en stund att bearbeta avinstallationen. Normalt utförs processen inom 16 minuter.

7. [Koppla från OS-disken och återskapa den virtuella datorn](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-the-original-virtual-hard-disk). Kontrol lera sedan om problemet är löst.
