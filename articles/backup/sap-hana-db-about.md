---
title: Om SAP HANA Database Backup i virtuella Azure-datorer
description: I den här artikeln lär du dig hur du säkerhetskopierar SAP HANA databaser som körs på virtuella Azure-datorer.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: efb9c3f786e429df404e261f053a9c9a9b032e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96296462"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Om SAP HANA Database Backup i virtuella Azure-datorer

SAP HANA-databaser är verksamhets kritiska arbets belastningar som kräver återställnings punkt mål och ett snabbt återställnings tids mål (RTO). Nu kan du [säkerhetskopiera SAP HANA databaser som körs på virtuella Azure-datorer](./tutorial-backup-sap-hana-db.md) med [Azure Backup](./backup-overview.md).

Azure Backup är [Backint certifierat](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) av SAP, för att tillhandahålla ursprungligt stöd för säkerhets kopiering genom att använda SAP HANA inbyggda API: er. Detta erbjudande från Azure Backup justeras med Azure Backups Mantra av **noll-infrastruktur** -säkerhetskopieringar, vilket eliminerar behovet av att distribuera och hantera infrastruktur för säkerhets kopiering. Nu kan du sömlöst säkerhetskopiera och återställa SAP HANA databaser som körs på virtuella Azure-datorer ([M-serien](../virtual-machines/m-series.md) , som också stöds nu!) och utnyttja företags hanterings funktioner som Azure Backup tillhandahåller.

## <a name="added-value"></a>Tillagt värde

Om du använder Azure Backup för att säkerhetskopiera och återställa SAP HANA-databaser ger följande fördelar:

* **15 minuters återställnings punkt mål (återställnings punkt mål)**: det går nu att återställa kritiska data på upp till 15 minuter.
* Återställningar med **ett klick, vid en tidpunkt: det** är enkelt att återställa produktions data till alternativa Hana-servrar. Länkning av säkerhets kopiering och kataloger för att utföra återställningar hanteras allt av Azure bakom kulisserna.
* **Långsiktig kvarhållning**: för rigorösa efterlevnads-och gransknings behov. Behåll dina säkerhets kopior för år, baserat på Retentions tiden, utöver vilken återställnings punkterna rensas automatiskt av den inbyggda funktionen för livs cykel hantering.
* **Säkerhets kopierings hantering från Azure**: Använd Azure backups hanterings-och övervaknings funktioner för förbättrad hanterings upplevelse. Azure CLI stöds också.

För att Visa säkerhets kopierings-och återställnings scenarier som vi stöder idag, se [support mat ris för SAP HANAs scenario](./sap-hana-backup-support-matrix.md#scenario-support).

## <a name="backup-architecture"></a>Säkerhetskopieringsarkitektur

![Diagram över säkerhets kopierings arkitektur](./media/sap-hana-db-about/backup-architecture.png)

* Säkerhets kopierings processen börjar genom att [skapa ett Recovery Services-valv](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) i Azure. Det här valvet kommer att användas för att lagra säkerhets kopior och återställnings punkter som skapats med tiden.
* Den virtuella Azure-datorn som kör SAP HANA Server registreras med valvet och databaserna som ska säkerhets kopie ras [identifieras](./tutorial-backup-sap-hana-db.md#discover-the-databases). För att Azure Backup tjänsten ska kunna identifiera databaser måste ett för [registrerings skript](https://aka.ms/scriptforpermsonhana) köras på Hana-servern som en rot användare.
* Det här skriptet skapar **AZUREWLBACKUPHANAUSER** DB-användare och en motsvarande nyckel med samma namn i **hdbuserstore**. Mer information om vad skriptet gör finns i avsnittet  [Vad skriptet gör för registrering](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .
* Azure Backups tjänsten installerar nu **Azure Backup-plugin-programmet för Hana** på den registrerade SAP HANA servern.
* Den **AZUREWLBACKUPHANAUSER** DB-användare som skapats av för registrerings skriptet används av **Azure Backup plugin-programmet för Hana** för att utföra alla säkerhets kopierings-och återställnings åtgärder. Om du försöker konfigurera säkerhets kopiering för SAP HANA databaser utan att köra skriptet kan du få följande fel meddelande: **UserErrorHanaScriptNotRun**.
* Om du vill [Konfigurera säkerhets kopiering](./tutorial-backup-sap-hana-db.md#configure-backup) av de databaser som identifieras väljer du den säkerhets kopierings princip som krävs och aktiverar säkerhets kopieringar.

* När säkerhets kopieringen har kon figurer ATS konfigurerar Azure Backup Service följande Backint-parametrar på databas nivån på den skyddade SAP HANA-servern:
  * [catalog_backup_using_backint: sant]
  * [enable_accumulated_catalog_backup: falskt]
  * [parallel_data_backup_backint_channels: 1]
  * [log_backup_timeout_s: 900)]
  * [backint_response_timeout: 7200]

>[!NOTE]
>Se till att dessa parametrar *inte* finns på värdnivå. Parametrar på värdnivå åsidosätter dessa parametrar och kan orsaka oväntade beteenden.
>

* **Azure Backup-plugin-programmet för Hana** upprätthåller alla säkerhets kopierings scheman och princip information. Den utlöser de schemalagda säkerhets kopieringarna och kommunicerar med **Hana-Säkerhetskopieraren** via Backint-API: er.
* **Säkerhets kopierings motorn Hana** returnerar en Backint-dataström med de data som ska säkerhets kopie ras.
* Alla schemalagda säkerhets kopieringar och säkerhets kopieringar på begäran (utlöses från Azure Portal) som är antingen fullständiga eller differentiella initieras av **Azure Backup plugin-programmet för Hana**. Logg säkerhets kopior hanteras dock och aktive ras av **Hana-Säkerhetsmotorn** .
* Azure Backup för SAP HANA, som är en BackInt-certifierad lösning, är inte beroende av underliggande disk-eller VM-typer. Säkerhets kopieringen utförs av data strömmar som genereras av HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Använda Azure VM backup med Azure SAP HANA backup

Förutom att använda den SAP HANA säkerhets kopian i Azure som tillhandahåller säkerhets kopiering och återställning på databas nivå kan du använda Azures lösning för säkerhets kopiering av virtuella datorer för att säkerhetskopiera OS-och icke-databasens diskar.

[Backint Certified Azure SAP HANA backup-lösningen](#backup-architecture) kan användas för säkerhets kopiering och återställning av databasen.

[Azure VM backup](backup-azure-vms-introduction.md) kan användas för att säkerhetskopiera operativ systemet och andra diskar som inte är databas. VM-säkerhetskopieringen tas en gång varje dag och säkerhets kopie ras alla diskar (utom **Skrivningsaccelerator-OS-diskar** och **Ultra disks**). Eftersom databasen säkerhets kopie ras med hjälp av Azure SAP HANA backup-lösningen kan du ta en filkonsekvent säkerhets kopia av bara operativ systemen OS och icke-databas med hjälp av funktionen [för säkerhets kopiering och återställning av selektiva diskar för virtuella Azure-datorer](selective-disk-backup-restore.md) .

Följ dessa steg om du vill återställa en virtuell dator som kör SAP HANA:

* [Återställ en ny virtuell dator från Azure VM backup](backup-azure-arm-restore-vms.md) från den senaste återställnings punkten. Eller skapa en ny tom virtuell dator och koppla diskarna från den senaste återställnings punkten.
* Om WA-diskar undantas återställs de inte. I det här fallet skapar du tomma WA-diskar och logg områden.
* När alla andra konfigurationer (t. ex. IP, system namn och så vidare) har angetts är den virtuella datorn inställd på att ta emot databas data från Azure Backup.
* Återställ nu databasen till den virtuella datorn från [Azure SAP HANA DB-säkerhetskopiering](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) till önskad tidpunkt.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer en SAP HANA databas som körs på en virtuell Azure-dator](./sap-hana-db-restore.md)
* Lär dig hur du [hanterar SAP HANA databaser som säkerhets kopie ras med Azure Backup](./sap-hana-db-manage.md)
