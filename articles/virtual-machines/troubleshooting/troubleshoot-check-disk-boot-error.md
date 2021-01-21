---
title: Kontrollerar fil systemet vid start av en virtuell Azure-dator | Microsoft Docs
description: Lär dig hur du löser problemet som den virtuella datorn visar kontrollerar fil systemet vid start av | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 196f49a72932906e0a21b3c6c534c79d291a845f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633001"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows visar "kontrollerar fil system" när en virtuell Azure-dator startas

I den här artikeln beskrivs fel meddelandet "kontrollerar fil systemet" som kan uppstå när du startar en virtuell Windows-dator (VM) i Microsoft Azure.


## <a name="symptom"></a>Symptom 

En virtuell Windows-dator startar inte. När du kontrollerar start skärmarna i [startdiagnostik](boot-diagnostics.md), ser du att processen för att kontrol lera disken (chkdsk.exe) körs med något av följande meddelanden:

- Genomsöker och reparerar enhet (C:)
- Kontrollerar fil system på C:

## <a name="cause"></a>Orsak

Om ett NTFS-fel påträffas i fil systemet kontrollerar Windows och reparerar konsekvensen för disken vid nästa omstart. Detta inträffar vanligt vis om den virtuella datorn hade oväntad omstart eller om den virtuella dator stängnings processen avbröts plötsligt.

## <a name="solution"></a>Lösning 

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

Windows startar normalt när processen för att kontrol lera disken har slutförts. Om den virtuella datorn har fastnat i kontrol lera disk-processen försöker du köra check disken på den virtuella datorn offline:
1. Ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).
2. [Koppla OS-disken till en virtuell dator för återställning](troubleshoot-recovery-disks-portal-windows.md).  
3. Kör check disk på den anslutna OS-disken på den virtuella återställnings datorn. I följande exempel är driv rutins beteckningen för den anslutna OS-disken E: 

    ```console
    chkdsk E: /f
    ```

4. När kontrollen är klar kopplar du bort disken från den virtuella återställnings datorn och kopplar sedan disken till den berörda virtuella datorn som en operativ system disk. Mer information finns i [Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell dator för återställning](troubleshoot-recovery-disks-portal-windows.md).
