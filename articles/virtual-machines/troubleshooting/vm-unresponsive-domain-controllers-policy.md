---
title: Den virtuella datorn svarar inte när standardpolicyn för domänkontrollanter tillämpas
titlesuffix: Azure Virtual Machines
description: Den här artikeln innehåller steg för att lösa problem där standard principen för domänkontrollanter förhindrar start av en virtuell Azure-dator.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 6c139398182ca9d875de0d3b21c58afe503bd8a5
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632282"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>Den virtuella datorn svarar inte när standardpolicyn för domänkontrollanter tillämpas

Den här artikeln innehåller steg för att lösa problem där standard principen för domänkontrollanter förhindrar start av en virtuell Azure-dator (VM).

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar att operativ systemet slutar svara vid start med **standard principen för domänkontrollanter**.

  ![Bild 1 visar det operativ system som fastnat med meddelandet standard princip för domänkontrollanter](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>Orsak

Det här problemet kan bero på att nya ändringar har gjorts i standard principen för domänkontrollanter. Annars måste en fil analys av minnesdumpen utföras för att fastställa rotor saken.

## <a name="solution"></a>Lösning

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

Om du nyligen har gjort ändringar i standard principen för domänkontrollanter, kanske du vill ångra ändringarna för att åtgärda problemet. Om du inte är säker på vad som orsakar problemet kan du samla in en minnesdump och sedan skicka ett support ärende.

### <a name="collect-the-memory-dump-file"></a>Samla in minnesdumpen

För att lösa det här problemet bör du först samla in minnesdumpen för kraschen och sedan kontakta supporten med minnesdumpen. Samla in dumpfilen genom att följa dessa steg:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Koppla OS-disken till en ny virtuell reparations dator

1. Använd steg 1-3 i [reparations kommandona för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) för att förbereda en reparations-VM.

1. Använda Anslutning till fjärrskrivbord ansluta till den virtuella reparations datorn.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Leta upp dumpfilen och skicka in ett support ärende

1. På den virtuella reparations datorn går du till Windows-mappen på den anslutna OS-disken. Om operativsystemdisken har enhetsbeteckningen `F` går du till `F:\Windows`.

1. Leta upp filen Memory. dmp och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnesdumpen.

1. Om du har problem med att hitta Memory. dmp-filen kanske du vill använda [icke-maskbart avbrott (NMI) i serie konsolen i](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) stället. Följ guiden för att [Generera en kraschdump-fil med NMI-anrop](/windows/client-management/generate-kernel-or-complete-crash-dump).