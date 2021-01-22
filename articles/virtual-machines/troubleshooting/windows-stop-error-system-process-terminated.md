---
title: System processen har avslut ATS för Windows Stop Error-status
description: Den här artikeln innehåller steg för att lösa problem där operativ systemet påträffar stopp felet 0xC000021A, vilket förhindrar start av en virtuell Azure-dator.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: 8af656e00c457dfa3d438f0cb104d85ccc687745
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661246"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Windows Stop-fel-0xC000021A status system process avslutad

Den här artikeln innehåller steg för att lösa problem där operativ systemet (OS) påträffar stopp felet 0xC000021A, vilket gör att en virtuell Azure-dator (VM) startas.

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn, visar skärm bilden meddelandet att operativ systemet påträffade ett fel under starten, med följande meddelande:

**Datorn stötte på ett problem och måste startas om. Vi har bara samlat in fel information och sedan kan du starta om. (# #% Complete) Om du vill veta mer kan du söka online senare efter det här felet: 0xC000021a**.

  ![Bild 1 visar felkoden #0xC000021A med meddelandet "datorn stötte på ett problem och måste startas om. Vi har bara samlat in fel information och sedan kan du starta om. ".](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>Orsak

Fel 0xC000021A betyder **STATUS_SYSTEM_PROCESS_TERMINATED**.

Felet uppstår när en kritisk process, till exempel WinLogon (winlogon.exe) eller klient servern Run-Time under system (csrss.exe) Miss lyckas. När kärnan känner av att någon av dessa tjänster har stoppats genererar den **Stop 0xc000021a** -felet. Det här felet kan ha flera orsaker, inklusive:

- Felmatchade systemfiler har installerats.
- Det gick inte att installera en Service Pack-eller KB-uppdatering.
- Ett säkerhets kopierings program som används för att återställa en hård disk återställde inte korrekt filer som kanske används.
- Ett inkompatibelt program från tredje part har installerats.

## <a name="solution"></a>Lösning

### <a name="collect-the-memory-dump-file"></a>Samla in minnesdumpen

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.


För att lösa det här problemet måste krasch dumpning analyseras. Samla in minnesdumpen för krasch-och kontakta supporten. Samla in dumpfilen genom att följa dessa steg:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Koppla OS-disken till en ny virtuell reparations dator

1.  Använd steg 1-3 i [reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.
2.  Anslut till den virtuella reparations datorn med hjälp av **anslutning till fjärrskrivbord**.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Leta upp dumpfilen och skicka in ett support ärende

1.  På den reparera virtuella datorn går du till Windows-mappen på den anslutna OS-disken. Om den driv rutins beteckning som är kopplad till den anslutna OS-disken är F går du till F:\Windows.
2.  Leta upp filen Memory. dmp och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnesdumpen.
3.  Om du har problem med att hitta Memory. dmp-filen kanske du vill använda [icke-maskbart avbrott (NMI) i serie konsolen i](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) stället. Du kan följa guiden för att [generera en kraschdump-fil med hjälp av NMI-anrop här](/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Nästa steg

- Mer felsöknings information finns i [Felsöka vanliga start fel](./boot-error-troubleshoot.md) eller [Felsöka en virtuell Windows-dator genom att koppla OS-disken till en virtuell dator för återställning](./troubleshoot-recovery-disks-windows.md). Du bör också bekanta dig med [hur du använder startdiagnostik för att felsöka en virtuell dator](./boot-diagnostics.md).
- Mer information om hur du använder Resource Manager finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).
- Om du inte kan ansluta till din virtuella dator kan du läsa [FELSÖKA RDP-anslutningar till en virtuell Azure-dator](./troubleshoot-rdp-connection.md).