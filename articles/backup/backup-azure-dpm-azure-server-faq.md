---
title: Vanliga frågor och svar om Azure Backup Server och DPM
description: I den här artikeln hittar du svar på vanliga frågor om Microsoft Azure Backup Server (MABS) och DPM (Data Protection Manager).
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: b2994e250c37d707eee5428e83c151728f83a1ab
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88522937"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server och DPM – vanliga frågor och svar

## <a name="general-questions"></a>Allmänna frågor

I den här artikeln får du svar på vanliga frågor om Azure Backup Server och DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Kan jag använda Azure Backup Server för att skapa en BMR-säkerhetskopia (Bare Metal Recovery) för en fysisk server?

Ja.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Kan jag registrera servern på flera valv?

Nej. En DPM-eller Azure Backup Server kan bara registreras till ett valv.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Kan jag använda DPM för att säkerhetskopiera appar i Azure Stack?

Nej. Du kan använda Azure Backup för att skydda Azure Stack, Azure Backup inte stöder användning av DPM för att säkerhetskopiera appar i Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Kan jag installera System Center DPM för att säkerhetskopiera lokala arbets belastningar till Azure om jag har installerat Azure Backup Agent för att skydda mina filer och mappar?

Ja. Men du bör konfigurera DPM först och sedan installera Azure Backup agenten.  Genom att installera komponenter i den här ordningen ser du till att Azure Backup-agenten fungerar med DPM. Att installera agenten innan du installerar DPM är inte underrättad eller stöds inte.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Varför kan jag inte lägga till en extern DPM-server efter att ha installerat UR7 och den senaste Azure Backup agenten?

För DPM-servrar med data källor som är skyddade i molnet (genom att använda en samlad uppdatering som är tidigare än Samlad uppdatering 7) måste du vänta minst en dag efter att du har installerat UR7 och den senaste Azure Backup agenten för att starta **Lägg till extern DPM-server**. Tids perioden för en dag krävs för att ladda upp metadata för DPM-skyddsagenten till Azure. Skydds gruppens metadata överförs första gången till ett natt jobb.

### <a name="are-there-recommendations-for-configuring-exclusions-for-antivirus-software"></a>Finns det några rekommendationer för att konfigurera undantag för antivirus program?

Ja, vi rekommenderar att du konfigurerar Antivirus uteslutning. Undantag för DPM finns i [köra antivirus program på DPM-servern](https://docs.microsoft.com/system-center/dpm/run-antivirus-server?view=sc-dpm-2019). Undantag för MABS finns i [Konfigurera antivirus program för Mabs-servern](backup-azure-mabs-troubleshoot.md#configure-antivirus-for-mabs-server).

## <a name="vmware-and-hyper-v-backup"></a>VMware och Hyper-V-säkerhetskopiering

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Kan jag säkerhetskopiera VMware vCenter-servrar till Azure?

Ja. Du kan använda Azure Backup Server för att säkerhetskopiera VMware vCenter Server-och ESXi-värdar till Azure.

- [Läs mer](backup-mabs-protection-matrix.md) om versioner som stöds.
- [Följ dessa steg](backup-azure-backup-server-vmware.md) om du vill säkerhetskopiera en VMware-Server.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Behöver jag en separat licens för att återställa ett fullständigt lokalt VMware/Hyper-V-kluster?

Du behöver inte separat licensiering för VMware/Hyper-V-skydd.

- Om du är System Center-kund använder du System Center Data Protection Manager (DPM) för att skydda virtuella VMware-datorer.
- Om du inte är System Center-kund kan du använda Azure Backup Server (betala per användning) för att skydda virtuella VMware-datorer.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Kan jag återställa ett SharePoint-objekt till den ursprungliga platsen om SharePoint har kon figurer ATS med hjälp av SQL AlwaysOn (med skydd på disk)?

Ja, objektet kan återställas till den ursprungliga SharePoint-webbplatsen.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Kan jag återställa en SharePoint-databas till den ursprungliga platsen om SharePoint konfigureras med hjälp av SQL AlwaysOn?

Eftersom SharePoint-databaser är konfigurerade i SQL AlwaysOn, kan de inte ändras om inte tillgänglighets gruppen tas bort. Det innebär att DPM inte kan återställa en databas till den ursprungliga platsen. Du kan återställa en SQL Server-databas till en annan SQL Server-instans.

## <a name="next-steps"></a>Nästa steg

Läs andra vanliga frågor och svar:

- [Läs mer](backup-support-matrix-mabs-dpm.md) om Azure Backup Server och DPM-stödmatrisen.
- [Läs mer](backup-azure-mabs-troubleshoot.md) om rikt linjerna för Azure Backup Server och fel sökning av DPM.
