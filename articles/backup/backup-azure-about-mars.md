---
title: Om MARS-agenten
description: Lär dig hur MARS-agenten stöder säkerhetskopieringsscenarier
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9e01694aca386482f9ff7ba52593c88326ba3d62
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517750"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Om MICROSOFT AZURE Recovery Services-agenten (MARS)

I den här artikeln beskrivs hur Azure Backup-tjänsten använder MARS-agenten (Microsoft Azure Recovery Services) för att backa upp och återställa filer, mappar och volym- eller systemtillståndet från en lokal dator till Azure.

## <a name="backup-scenarios"></a>Säkerhetskopieringsscenarier

MARS-agenten stöder följande säkerhetskopieringsscenarier:

![MARS-säkerhetskopieringsscenarier](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Filer och mappar:** Skydda Windows-filer och -mappar selektivt.
- **Volymnivå:** Skydda en hel Windows-volym på datorn.
- **Systemnivå:** Skydda ett helt Windows-systemtillstånd.

### <a name="additional-scenarios"></a>Fler scenarier

- **Back up specific files and folders within Azure virtual machines**(Bladet innehåller specifika filer och mappar på virtuella Azure-datorer): Den primära metoden för att servera virtuella Azure-datorer (VM) är att använda ett Azure Backup-tillägg på den virtuella datorn. Tillägget backar upp hela den virtuella datorn. Om du vill backa upp specifika filer och mappar på en virtuell dator kan du installera MARS-agenten på de virtuella Azure-datorerna. Mer information finns i [Architecture: Built-in Azure VM Backup (Arkitektur: Inbyggd säkerhetskopiering av virtuella Azure-datorer).](./backup-architecture.md#architecture-built-in-azure-vm-backup)

- **Offline-seeding:** Inledande fullständiga säkerhetskopior av data till Azure överför vanligtvis stora mängder data och kräver mer nätverksbandbredd. Efterföljande säkerhetskopieringar överför endast delta, eller inkrementell, mängd data. Azure Backup komprimerar de första säkerhetskopiorna. Med *offline-seeding kan* Azure Backup använda diskar för att ladda upp komprimerade inledande säkerhetskopierade data offline till Azure. Mer information finns i Azure Backup [offlinesäkerhetskopiering med hjälp av Azure Data Box](offline-backup-azure-data-box.md).

## <a name="restore-scenarios"></a>Återställningsscenarier

MARS-agenten stöder följande återställningsscenarier:

![MARS-återställningsscenarier](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Samma server:** Den server där säkerhetskopian ursprungligen skapades.
  - **Filer och mappar:** Välj de enskilda filer och mappar som du vill återställa.
  - **Volymnivå:** Välj den volym och återställningspunkt som du vill återställa. Återställ den sedan till samma plats eller en alternativ plats på samma dator.  Skapa en kopia av befintliga filer, skriva över befintliga filer eller hoppa över återställning av befintliga filer.
  - **Systemnivå:** Välj det systemtillstånd och den återställningspunkt som ska återställas till samma dator på en angiven plats.

- **Alternativ server:** En annan server än den server där säkerhetskopian gjordes.
  - **Filer och mappar:** Välj de enskilda filer och mappar vars återställningspunkt du vill återställa till en måldator.
  - **Volymnivå:** Välj den volym och återställningspunkt som du vill återställa till en annan plats. Skapa en kopia av befintliga filer, skriva över befintliga filer eller hoppa över återställning av befintliga filer.
  - **Systemnivå:** Välj det systemtillstånd och den återställningspunkt som ska återställas som en systemtillståndsfil till en annan dator.

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

1. Från Azure Portal du ett [Recovery Services-valv](install-mars-agent.md#create-a-recovery-services-vault)och väljer filer, mappar och systemtillståndet från **Säkerhetskopieringsmål.**
2. [Ladda ned autentiseringsuppgifterna för Recovery Services-valvet och agentinstallationsprogrammet](./install-mars-agent.md#download-the-mars-agent) till en lokal dator.

3. [Installera agenten och](./install-mars-agent.md#install-and-register-the-agent) använd de nedladdade valvautentiseringsuppgifterna för att registrera datorn till Recovery Services-valvet.
4. Från agentkonsolen på [](./backup-windows-with-mars-agent.md#create-a-backup-policy) klienten konfigurerar du säkerhetskopieringen för att ange vad som ska säkerhetskopieras, när du ska säkerhetskopiera (schemat), hur länge säkerhetskopiorna ska behållas i Azure (bevarandeprincipen) och börja skydda.

![Azure Backup agentdiagram](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>Ytterligare information

- Den **första säkerhetskopieringen** (första säkerhetskopieringen) körs enligt dina inställningar för säkerhetskopiering.  MARS-agenten använder VSS för att ta en ögonblicksbild av de volymer som valts för säkerhetskopiering. Agenten använder bara Windows System Writer-åtgärden för att ta ögonblicksbilden. Den använder inte några VSS-skrivare för program och samlar inte in app-konsekventa ögonblicksbilder. När DU har tagit ögonblicksbilden med VSS skapar MARS-agenten en virtuell hårddisk (VHD) i den cachemapp som du angav när du konfigurerade säkerhetskopieringen. Agenten lagrar också kontrollsumor för varje datablock.

- **Inkrementella** säkerhetskopieringar (efterföljande säkerhetskopior) körs enligt det schema som du anger. Under inkrementella säkerhetskopieringar identifieras ändrade filer och en ny virtuell hårddisk skapas. Den virtuella hårddisken komprimeras och krypteras och skickas sedan till valvet. När den inkrementella säkerhetskopieringen är klar sammanfogas den nya virtuella hårddisken med den virtuella hårddisken som skapades efter den inledande replikeringen. Den här sammanslagna virtuella hårddisken ger det senaste tillståndet som ska användas för jämförelse av pågående säkerhetskopiering.

- MARS-agenten kan köra säkerhetskopieringsjobbet i **optimerat** läge med hjälp av USN-ändringsjournalen (uppdateringssekvensnummer) eller i **icke-optimerat läge** genom att söka efter ändringar i kataloger eller filer via genomsökning av hela volymen. Det optimerade läget går långsammare eftersom agenten måste genomsöka varje fil på volymen och jämföra den med metadata för att fastställa de ändrade filerna.  Den **första säkerhetskopieringen** körs alltid i icke-optimerat läge. Om den tidigare säkerhetskopieringen misslyckades körs nästa schemalagda säkerhetskopieringsjobb i icke-optimerat läge. Mer information om dessa lägen och hur du verifierar dem finns i den [här artikeln.](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode)

## <a name="next-steps"></a>Nästa steg

[Stödmatris för MARS-agenten](./backup-support-matrix-mars-agent.md)

[Vanliga frågor och svar om MARS-agenten](./backup-azure-file-folder-backup-faq.yml)
