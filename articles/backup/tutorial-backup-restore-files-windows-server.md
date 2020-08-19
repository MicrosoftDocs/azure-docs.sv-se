---
title: 'Självstudie: återställa objekt till Windows Server'
description: I den här självstudien får du lära dig hur du använder MARS-agenten (Microsoft Azure Recovery Services agent) för att återställa objekt från Azure till en Windows-Server.
ms.topic: tutorial
ms.date: 02/14/2018
ms.custom: mvc
ms.openlocfilehash: 746c901747cf1c0b87612a31fbabcb657d5c4a0c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263120"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Återställa filer från Azure till Windows Server

Med Azure Backup kan du återställa enskilda objekt från säkerhetskopior av Windows Server. Det är praktiskt att återställa enskilda filer om du snabbt måste återställa filer som du har råkat ta bort av misstag. I den här självstudien beskrivs hur du kan använda MARS-agenten (Microsoft Azure Recovery Services Agent) för att återställa objekt från säkerhetskopior som du redan har skapat i Azure. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
>
> * Initiera återställning av enskilda objekt
> * Välja en återställningspunkt
> * Återställa objekt från en återställningspunkt

Den här självstudien förutsätter att du redan har utfört stegen för att [säkerhetskopiera en Windows-Server till Azure](backup-windows-with-mars-agent.md) och ha minst en säkerhets kopia av dina Windows Server-filer i Azure.

## <a name="initiate-recovery-of-individual-items"></a>Initiera återställning av enskilda objekt

En praktisk användargränssnittsguide som heter Microsoft Azure Backup är installerad med MARS-agenten (Microsoft Azure Recovery Services). Microsoft Azure Backup-guiden fungerar tillsammans med MARS-agenten (Microsoft Azure Recovery Services) för att hämta säkerhetskopierade data från återställningspunkter som lagras i Azure. Använd Microsoft Azure Backup-guiden för att identifiera de filer eller mappar du vill återställa till Windows Server.

1. Öppna snapin-modulen **Microsoft Azure Backup**. Du hittar den genom att söka efter **Microsoft Azure Backup** på datorn.

    ![Microsoft Azure Backup snapin-modul](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. I guiden väljer du **Återställ data** i **Åtgärds fönstret** i agent konsolen för att starta guiden **Återställ data** .

    ![Välj Återställ data](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. På sidan **komma igång** väljer du **den här servern (Server namn)** och väljer **Nästa**.

4. På sidan **Välj återställnings läge** väljer du **enskilda filer och mappar** och väljer sedan **Nästa** för att påbörja urvals processen för återställnings punkten.

5. På sidan **Välj volym och datum** väljer du den volym som innehåller de filer eller mappar som du vill återställa och väljer **montera**. Välj ett datum och en tid på den nedrullningsbara menyn som motsvarar en återställningspunkt. Datum i **fetstil** anger tillgängligheten för minst en återställningspunkt för den dagen.

    ![Välj volym och datum](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)

    När du väljer **montera**gör Azure Backup återställnings punkten tillgänglig som en disk. Bläddra till och återställ filer från disken.

## <a name="restore-items-from-a-recovery-point"></a>Återställa objekt från en återställningspunkt

1. När återställnings volymen har monterats väljer du **Bläddra** för att öppna Utforskaren och hitta de filer och mappar som du vill återställa.

    ![Välj Bläddra](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Du kan öppna filerna direkt från återställningsvolymen och verifiera filerna.

2. I Utforskaren i Windows kopierar du de filer och mappar som du vill återställa och klistrar in dem på önskad plats på servern.

    ![Kopiera filer och mappar](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. När du är klar med att återställa filerna och mapparna går du till sidan **Bläddra och återställa filer** i guiden **Återställ data** och väljer **demontera**.

    ![Välj demontera](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4. Välj **Ja** för att bekräfta att du vill demontera volymen.

    När ögonblicksbilden är demonterad visas **Jobbet har slutförts** i rutan **Jobb** i agentkonsolen.

## <a name="next-steps"></a>Nästa steg

Det avslutar självstudien om at säkerhetskopiera och återställa Windows Server-data till Azure. Om du vill läsa mer om Azure Backup kan du ta en titt på PowerShell-exemplet för att säkerhetskopiera krypterade virtuella datorer.

> [!div class="nextstepaction"]
> [Säkerhetskopiera krypterade virtuella datorer](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
