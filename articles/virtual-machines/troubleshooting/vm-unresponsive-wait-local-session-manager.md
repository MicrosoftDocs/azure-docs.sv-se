---
title: Den virtuella datorn svarar inte under väntan på att tjänsten Local session Manager
description: Den här artikeln innehåller steg för att lösa problem där gäst operativ systemet fastnar i väntan på att den lokala sessionshanteraren ska slutföra bearbetningen när den startar en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: fc3bd5d2590e969db07e9dffa61b4902ea4604c3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632554"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager-service"></a>Den virtuella datorn svarar inte under väntan på att tjänsten Local session Manager

Den här artikeln innehåller steg för att lösa problem där gäst operativ systemet (gäst operativ system) är fastnat i väntan på att den lokala sessionshanteraren ska slutföra bearbetningen när en virtuell Azure-dator startas.

## <a name="symptoms"></a>Symtom

När du använder [startdiagnostik](./boot-diagnostics.md) för att visa en skärm bild av den virtuella datorns utdata, ser du att skärm bilden visar en prompt med meddelandet "vänta på den lokala sessionshanteraren".

![Skärm bild som visar det låsta gäst operativ systemet i Windows Server 2012 R2 med meddelandet "vänta på den lokala sessionshanteraren".](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Orsak

Det kan finnas flera orsaker till att en virtuell dator har fastnat i väntan på hanteraren för lokala sessioner. Om det här problemet kvarstår måste du samla in en minnesdump för analys.

## <a name="solution"></a>Lösning

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

I vissa fall behöver du bara vänta tills processen har slutförts för att lösa problemet. Om den virtuella datorn inte svarar och finns kvar på skärmen vänta i mer än en timme, ska du samla in en minnesdump och sedan kontakta Microsoft support.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Koppla OS-disken till en ny virtuell reparations dator

1. Om du vill förbereda en virtuell reparations dator följer du steg 1-3 i [reparations kommandon för virtuella datorer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md).
1. Anslut till den virtuella reparations datorn med hjälp av Anslutning till fjärrskrivbord.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Leta upp dumpfilen och skicka in ett support ärende

1. På den virtuella reparations datorn går du till Windows-mappen på den anslutna OS-disken. Till exempel, om enhets beteckningen som är tilldelad till den anslutna OS-disken heter *F*, går du till `F:\Windows` .
1. Leta efter *Memory. dmp* -filen och skicka sedan [ett support ärende](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) med minnes dumpnings filen ansluten.
1. Om du har problem med att hitta *Memory. dmp* -filen följer du guiden för att [Generera en kraschdumpfil med ett icke-maskbart avbrotts anrop (NMI)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Mer information om NMI-anrop finns i NMI-anrop i användar handboken för [Azures serie konsol](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Felsöka start fel för virtuella Azure-datorer](boot-error-troubleshoot.md)