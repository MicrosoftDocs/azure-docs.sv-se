---
title: Svar på vanliga frågor
description: 'Svar på vanliga frågor om: Azure Backup-funktioner inklusive Recovery Services-valvet, vad du kan säkerhetskopiera, hur det fungerar, kryptering och gränser. '
ms.topic: conceptual
ms.date: 07/07/2019
ms.openlocfilehash: f819440001180a3c446f366e61e3ac0f983fa67f
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806651"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup – Vanliga frågor och svar

Den här artikeln innehåller vanliga frågor om Azure Backup-tjänsten.

## <a name="recovery-services-vault"></a>Recovery Services-valv

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Finns det någon gräns för antalet valv som kan skapas i varje Azure-prenumeration?

Ja. Du kan skapa upp till 500 Recovery Services-valv för varje Azure Backup-region som stöds per prenumeration. Om du behöver fler valv skapar du ytterligare en prenumeration.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Finns det några begränsningar för hur många servrar/datorer som kan registreras mot varje valv?

Du kan registrera upp till 1 000 virtuella Azure-datorer per valv. Om du använder Microsoft Azure Backup-agenten kan du registrera upp till 50 MARS-agenter per valv. Och du kan registrera 50 MABS-servrar/DPM-servrar i ett valv.

### <a name="how-many-datasourcesitems-can-be-protected-in-a-vault"></a>Hur många datakällor/objekt kan skyddas i ett valv?

Du kan skydda upp till 2000 data källor/objekt över alla arbets belastningar (till exempel IaaS VM, SQL, AFS) i ett valv.
Om du till exempel redan har skyddat 500 VM och 400 Azure Files resurser i valvet kan du bara skydda upp till 1100 SQL-databaser.

### <a name="how-many-policies-can-i-create-per-vault"></a>Hur många principer kan jag skapa per valv?

Du kan bara ha upp till 200 principer per valv.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Min organisation har ett valv. Hur kan jag isolera data från olika servrar i valvet när jag återställer data?

Serverdata som du vill återställa tillsammans bör använda samma lösenfras när du konfigurerar säkerhetskopiering. Om du vill isolera återställning till en specifik server eller specifika servrar använder du en lösenfras endast för den servern eller de servrarna. HR-servrarna kan till exempel använda en krypteringslösenfras, redovisningsservrarna en annan och lagringsservrar en tredje.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Kan jag flytta mitt valv mellan prenumerationer?

Ja. Information om hur du flyttar ett Recovery Services-valv finns i den här [artikeln](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>Kan jag flytta säkerhetskopierade data till ett annat valv?

Nej. Säkerhetskopierade data som lagras i ett valv kan inte flyttas till ett annat valv.

### <a name="can-i-change-the-storage-redundancy-setting-after-a-backup"></a>Kan jag ändra inställningen för redundans efter en säkerhets kopiering?

Typen av lagrings replikering är som standard inställd på Geo-redundant lagring (GRS). När du har konfigurerat säkerhets kopieringen är alternativet att ändra inaktiverat och kan inte ändras.

![Lagringsreplikeringstyp](./media/backup-azure-backup-faq/storage-replication-type.png)

Om du redan har konfigurerat säkerhets kopian och måste gå från GRS till LRS, se [hur du ändrar från GRS till LRS efter att du har konfigurerat säkerhets kopiering](backup-create-rs-vault.md#how-to-change-from-grs-to-lrs-after-configuring-backup).

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Kan jag göra en återställning på objektnivå (ILR) för virtuella datorer som har säkerhetskopierats till ett Recovery Services-valv?

- ILR stöds för virtuella Azure-datorer som säkerhetskopieras av säkerhetskopiering av virtuella Azure-datorer. Mer information finns i [artikeln](backup-azure-restore-files-from-vm.md)
- ILR stöds inte för online-återställnings punkter för lokala virtuella datorer som säkerhets kopie ras av Azure Backup Server (MABS) eller System Center DPM.

### <a name="how-can-i-move-data-from-the-recovery-services-vault-to-on-premises"></a>Hur kan jag flytta data från Recovery Services-valvet till en lokal plats?

Export av data direkt från Recovery Services-valvet till lokalt med hjälp av Data Box-enhet stöds inte. Data måste återställas till ett lagrings konto och kan sedan flyttas till lokalt via [data Box-enhet](../databox/data-box-overview.md) eller [Importera/exportera](../import-export/storage-import-export-service.md).

### <a name="what-is-the-difference-between-a-geo-redundant-storage-grs-vault-with-and-without-the-cross-region-restore-crr-capability-enabled"></a>Vad är skillnaden mellan ett GRS-valv (Geo-redundant Storage) med och utan CRR-funktionen (Cross-region Restore) aktive rad?

Om det finns ett [GRS](azure-backup-glossary.md#grs) -valv utan [CRR](azure-backup-glossary.md#cross-region-restore-crr) -funktionen aktive rad kan inte data i den sekundära regionen nås förrän Azure deklarerar en katastrof i den primära regionen. I ett sådant scenario sker återställningen från den sekundära regionen. När CRR är aktiverat, även om den primära regionen är igång, kan du utlösa en återställning i den sekundära regionen.

## <a name="azure-backup-agent"></a>Azure Backup-agent

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Var hittar jag vanliga frågor om Azure Backup-agenten för säkerhetskopiering av virtuella Azure-datorer?

- För den agent som körs på virtuella Azure-datorer läser du dessa [Vanliga frågor och svar](backup-azure-vm-backup-faq.yml).
- För den agent som används för att säkerhetskopiera Azure-filmappar läser du dessa [Vanliga frågor och svar](backup-azure-file-folder-backup-faq.md).

## <a name="general-backup"></a>Allmän säkerhetskopiering

### <a name="are-there-limits-on-backup-scheduling"></a>Finns det begränsningar för schemaläggning av säkerhetskopiering?

Ja.

- Du kan säkerhetskopiera Windows Server- eller Windows-datorer upp till tre gånger per dag. Du kan ange schemaläggningsprincipen till dagliga eller veckovisa scheman.
- Du kan säkerhetskopiera DPM upp till två gånger per dag. Du kan ange schemaläggningsprincipen till daglig, veckovis, månatlig och årlig.
- Du säkerhetskopierar virtuella Azure-datorer en gång om dagen.

### <a name="what-operating-systems-are-supported-for-backup"></a>Vilka operativsystem stöds för säkerhetskopiering?

Azure Backup stöder dessa operativsystem för säkerhetskopiering av filer och mappar samt appar som skyddas av Azure Backup Server och SCDPM.

**Operativsystem** | **SKU** | **Information**
--- | --- | ---
Arbetsstation | |
Windows 10 64-bitars | Enterprise, Pro, Home | Datorerna ska köra de senaste Service Packs och uppdateringarna.
Windows 8.1 64-bitars | Enterprise, Pro | Datorerna ska köra de senaste Service Packs och uppdateringarna.
Windows 8 64-bitars | Enterprise, Pro | Datorerna ska köra de senaste Service Packs och uppdateringarna.
Windows 7 64-bitars | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Datorerna ska köra de senaste Service Packs och uppdateringarna.
Server | |
Windows Server 2019 64-bitars | Standard, Datacenter, Essentials | Med de senaste Service Packs/uppdateringarna.
Windows Server 2016 64-bitars | Standard, Datacenter, Essentials | Med de senaste Service Packs/uppdateringarna.
Windows Server 2012 R2 64-bitars | Standard, Datacenter, Foundation | Med de senaste Service Packs/uppdateringarna.
Windows Server 2012 64-bitars | Datacenter, Foundation, Standard | Med de senaste Service Packs/uppdateringarna.
Windows Storage Server 2016 64-bitars | Standard, Workgroup | Med de senaste Service Packs/uppdateringarna.
Windows Storage Server 2012 R2 64-bitars | Standard, Workgroup, Essential | Med de senaste Service Packs/uppdateringarna.
Windows Storage Server 2012 64-bitars | Standard, Workgroup | Med de senaste Service Packs/uppdateringarna.
Windows Server 2008 R2 SP1 64-bitars | Standard, Enterprise, Datacenter, Foundation | Med de senaste uppdateringarna.
Windows Server 2008 64-bitars | Standard, Enterprise, Datacenter | Med de senaste uppdateringarna.

Azure Backup stöder inte 32-bitars operativsystem.

För säkerhetskopieringar av virtuella Azure Linux-datorer stöder Azure Backup [listan över distributioner som är godkända av Azure](../virtual-machines/linux/endorsed-distros.md), förutom Core OS Linux och 32-bitars operativsystem. Andra Bring-Your-Own-Linux-distributioner kan också fungera förutsatt att den virtuella datoragenten är tillgänglig på den virtuella datorn och att det finns stöd för Python.

### <a name="are-there-size-limits-for-data-backup"></a>Finns det storleksgränser för säkerhetskopiering av data?

Storleksgränserna är följande:

OS/dator | Storleksgräns för datakälla
--- | ---
Windows 8 eller senare | 54 400 GB
Windows 7 |1 700 GB
Windows Server 2012 eller senare | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1 700 GB
Azure VM | Se [support mat ris för säkerhets kopiering av virtuella Azure-datorer](./backup-support-matrix-iaas.md#vm-storage-support)

### <a name="how-is-the-data-source-size-determined"></a>Hur bestäms datakällstorleken?

Följande tabell beskriver hur datakällans storlek bestäms.

**Datakälla** | **Information**
--- | ---
Volym |Den mängd data som säkerhetskopieras från en virtuell dator med en enskild volym som säkerhetskopieras.
SQL Server-databas |Storlek på den enkla databas storleken som säkerhets kopie ras.
SharePoint | Summan av innehållet och konfigurationsdatabaserna i en SharePoint-servergrupp som säkerhetskopieras.
Exchange |Summan av alla Exchange-databaser på en Exchange-server som säkerhetskopieras.
BMR/systemtillstånd |Varje enskild kopia av BMR eller systemtillstånd på den dator som säkerhetskopieras.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Finns det en gräns för den mängd data som säkerhetskopieras med ett Recovery Services-valv?

Det finns ingen gräns för den totala mängden data som du kan säkerhetskopiera med ett Recovery Services-valv. Enskilda data källor (andra än virtuella Azure-datorer) kan vara högst 54 400 GB stora. Mer information om gränser finns i [avsnittet valv gränser i support mat ris](./backup-support-matrix.md#vault-support).

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Varför är storleken på data som överförs till Recovery Services-valvet mindre än de data som valts för säkerhetskopiering?

Data som säkerhetskopieras från Azure Backup Agent, DPM och Azure Backup Server komprimeras och krypteras innan de överförs. När komprimering och kryptering tillämpats är storleken på data i valvet 30–40 % mindre.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Kan jag ta bort enskilda filer från en återställningspunkt i valvet?

Nej, Azure Backup har inte stöd för borttagning eller rensning av enskilda objekt från lagrade säkerhetskopior.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Tas säkerhetskopierade data som har överförts bort om jag avbryter ett säkerhetskopieringsjobb som redan har startat?

Nej. Alla data som har överförts till valvet innan säkerhetskopieringen avbröts finns kvar i valvet.

- Azure Backup använder en kontrollpunktsmekanism för att då och då lägga till kontrollpunkter till säkerhetskopierade data under säkerhetskopieringen.
- Eftersom det finns kontrollpunkter i säkerhetskopian kan nästa säkerhetskopiering validera filernas integritet.
- Nästa säkerhetskopieringsjobb är en inkrementell säkerhetskopiering mot tidigare säkerhetskopierade data. Vid inkrementella säkerhetskopieringar överförs bara nya eller ändrade data, vilket innebär att bandbredden utnyttjas bättre.

Om du avbryter ett säkerhetskopieringsjobb för en virtuella Azure-dator ignoreras alla överförda data. Nästa säkerhetskopieringsjobb överför inkrementella data från det senaste lyckade säkerhetskopieringsjobbet.

## <a name="retention-and-recovery"></a>Bevarande och återställning

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Är bevarandeprinciperna för DPM- och Windows-datorer utan DPM desamma?

Ja, de har båda dagliga, veckovisa, månatliga och årliga bevarandeprinciper.

### <a name="can-i-customize-retention-policies"></a>Kan jag anpassa bevarandeprinciper?

Ja, du kan anpassa principer. Du kan till exempel konfigurera veckovisa och dagliga bevarandekrav, men inte årliga eller månatliga.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Kan jag använda olika tider för säkerhetskopieringsscheman och bevarandeprinciper?

Nej. Bevarandeprinciper kan bara användas med säkerhetskopieringspunkter. Den här bilden visar till exempel en bevarandeprincip för säkerhetskopior som tas kl. 00:00 och 18:00.

![Schemalägga säkerhetskopiering och kvarhållning](./media/backup-azure-backup-faq/Schedule.png)

### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point"></a>Tar det längre tid att återställa en äldre datapunkt om en säkerhetskopia behålls under lång tid?

Nej. Det tar lika lång tid att återställa den äldsta och den senaste punkten. Varje återställningspunkt beter sig som en fullständig punkt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Om varje återställningspunkt fungerar som en fullständig punkt, påverkas i så fall den totalt fakturerbara lagringen av säkerhetskopior?

Typiska produkter för långsiktiga kvarhållningspunkter lagrar säkerhetskopierade data som fullständiga punkter.

- De fullständiga punkterna är *ineffektiva* ur lagringssynvinkel men är enklare och snabbare att återställa.
- Inkrementella kopior är *lagringseffektiva* men kräver att du återställer en kedja med data, vilket påverkar återställningstiden

Azure Backup-lagringsarkitekturen ger dig det bästa av två världar genom att lagra data för snabb återställning till låga lagringskostnader. Detta säkerställer att den ingående och utgående bandbredden används effektivt. Mängden datalagring och den tid som krävs för att återställa data hålls till ett minimum. Läs mer om [inkrementella säkerhetskopieringar](backup-architecture.md#backup-types).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Finns det någon gräns för antalet återställningspunkter som kan skapas?

Du kan skapa upp till 9999 återställningspunkter per skyddad instans. En skyddad instans är en dator, server (fysisk eller virtuell) eller arbetsbelastning som säkerhetskopierar data till Azure.

- Läs mer om [säkerhetskopiering och bevarande](./backup-support-matrix.md).

### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Hur många gånger kan jag återställa data som har säkerhetskopierats till Azure?

Det finns ingen gräns för antalet återställningar från Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Betalar jag för den utgående trafiken från Azure när jag återställer data?

Nej. Återställning är kostnadsfritt och du debiteras inte för den utgående trafiken.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Vad händer om jag ändrar en säkerhetskopieringsprincip?

När en ny princip tillämpas följs schemat och kvarhållningstiden för den nya principen.

- Om kvarhållning är utökad markeras befintliga återställnings punkter för att behålla dem enligt den nya principen.
- Om kvarhållningen minskar markeras de för rensning under nästa rensningsjobb och tas sedan bort.

### <a name="how-long-is-data-retained-when-stopping-backups-but-selecting-the-option-to-retain-backup-data"></a>Hur länge sparas data när säkerhets kopieringen stoppas, men om du väljer alternativet att behålla säkerhetskopierade data?

När säkerhets kopieringen stoppas och data bevaras, kommer befintliga princip regler för data rensning att upphöra att gälla utan att de kommer att ha initierats av administratören för borttagning.

## <a name="encryption"></a>Kryptering

### <a name="is-the-data-sent-to-azure-encrypted"></a>Krypteras informationen som skickas till Azure?

Ja. Data krypteras på den lokala datorn med hjälp av AES256. Data skickas via en säker HTTPS-anslutning. De data som överförs i molnet skyddas av en HTTPS-exklusiv länk mellan lagringen och återställningstjänsten. iSCSI-protokollet skyddar de data som överförs mellan återställningstjänsten och användardatorn. En säker tunnelanslutning används för att skydda iSCSI-kanalen.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Är säkerhetskopierade data i Azure också krypterade?

Ja. Data i Azure krypteras i vila.

- Vid lokal säkerhetskopiering tillhandahålls kryptering i vila med hjälp av den lösenfras som du anger när du säkerhetskopierar till Azure.
- För virtuella Azure-datorer krypteras data i vila med hjälp av kryptering för lagringstjänst (SSE).

Microsoft dekrypterar inte säkerhets kopierings data när som helst.

### <a name="what-is-the-minimum-length-of-the-encryption-key-used-to-encrypt-backup-data"></a>Vilken är den minsta längden på krypterings nyckeln som används för att kryptera säkerhetskopierade data?

Krypterings nyckeln som används av Microsoft Azure Recovery Services (MARS)-agenten härleds från en lösen fras som måste vara minst 16 tecken lång. För virtuella Azure-datorer finns det ingen gräns för längden på nycklar som används av Azure-valv.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Vad händer om jag tappar bort krypteringsnyckeln? Kan jag återställa data? Kan Microsoft återställa data?

Den nyckel som används för att kryptera säkerhetskopierade data finns endast hos dig. Microsoft underhåller inte en kopia i Azure och har ingen åtkomst till nyckeln. Om du tappar bort nyckeln kan Microsoft inte återställa säkerhetskopierade data.

## <a name="next-steps"></a>Nästa steg

Läs andra vanliga frågor och svar:

- [Vanliga frågor](backup-azure-vm-backup-faq.yml) om säkerhetskopiering av virtuella Azure-datorer.
- [Vanliga frågor](backup-azure-file-folder-backup-faq.md) om Azure Backup-agenten
