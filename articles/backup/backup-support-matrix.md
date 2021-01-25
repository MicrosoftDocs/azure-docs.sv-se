---
title: Stödmatris för Azure Backup
description: Innehåller en sammanfattning av stödinställningar och begränsningar för Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 02/17/2019
ms.custom: references_regions
ms.openlocfilehash: 833837c317d22bf031aee4608ff4c93775736a34
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757448"
---
# <a name="support-matrix-for-azure-backup"></a>Support mat ris för Azure Backup

Du kan använda [Azure Backup](backup-overview.md) för att säkerhetskopiera data till Microsoft Azure moln plattform. Den här artikeln sammanfattar allmänna support inställningar och begränsningar för Azure Backup scenarier och distributioner.

Andra support-matriser är tillgängliga:

- Support mat ris för [säkerhets kopiering av virtuella Azure-datorer](backup-support-matrix-iaas.md)
- Support mat ris för säkerhets kopiering med [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (Mabs)](backup-support-matrix-mabs-dpm.md)
- Support mat ris för säkerhets kopiering med hjälp av [Microsoft Azure Recovery Services (mars)-agenten](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Stöd för valv

Azure Backup använder Recovery Services valv för att dirigera och hantera säkerhets kopior för följande arbets belastnings typer – virtuella Azure-datorer, SQL på virtuella Azure-datorer, SAP HANA i virtuella Azure-datorer, Azure-filresurser och lokala arbets belastningar som använder Azure Backup Agent, Azure Backup Server och System Center DPM. Den använder också Recovery Services valv för att lagra säkerhetskopierade data för dessa arbets belastningar.

I följande tabell beskrivs funktionerna i Recovery Services-valv:

**Funktion** | **Information**
--- | ---
**Valv i prenumerationen** | Upp till 500 Recovery Services-valv i en enstaka prenumeration.
**Datorer i ett valv** | Upp till 2000 data källor över alla arbets belastningar (som virtuella Azure-datorer, SQL Server VM, MABS-servrar och så vidare) kan skyddas i ett enda valv.<br><br>Upp till 1 000 virtuella Azure-datorer i ett enda valv.<br/><br/> Upp till 50 MABS-servrar kan registreras i ett enda valv.
**Datakällor** | Den maximala storleken för en enskild [data källa](./backup-azure-backup-faq.md#how-is-the-data-source-size-determined) är 54 400 GB. Den här gränsen gäller inte för virtuella Azure-säkerhetskopieringar. Inga gränser gäller för den totala mängden data som du kan säkerhetskopiera till valvet.
**Säkerhetskopieringar till valv** | **Virtuella Azure-datorer:** En gång om dagen.<br/><br/>**Datorer som skyddas av DPM/Mabs:** Två gånger om dagen.<br/><br/> **Datorer som har säkerhetskopierats direkt med hjälp av mars-agenten:** Tre gånger per dag.
**Säkerhets kopieringar mellan valv** | Säkerhets kopiering är inom en region.<br/><br/> Du behöver ett valv i varje Azure-region som innehåller de virtuella datorer som du vill säkerhetskopiera. Du kan inte säkerhetskopiera till en annan region.
**Flytta valv** | Du kan [Flytta valv](./backup-azure-move-recovery-services-vault.md) mellan prenumerationer eller mellan resurs grupper i samma prenumeration. Det finns dock inte stöd för att flytta valv mellan regioner.
**Flytta data mellan valv** | Det finns inte stöd för att flytta säkerhetskopierade data mellan valv.
**Ändra lagrings typ för valv** | Du kan ändra typen av lagrings replik (antingen Geo-redundant lagring eller lokalt redundant lagring) för ett valv innan säkerhets kopiorna lagras. När säkerhetskopiering börjar i valvet går det inte att ändra replikeringstypen.
**Zonredundant lagring (ZRS)** | Tillgängligt i regionerna Storbritannien, södra (UKS) och Asien, sydöstra (SEA).

## <a name="on-premises-backup-support"></a>Stöd för lokal säkerhetskopiering

Här är what's som stöds om du vill säkerhetskopiera lokala datorer:

**Dator** | **Vad har säkerhetskopierats** | **Plats** | **Funktioner**
--- | --- | --- | ---
**Direkt säkerhets kopiering av Windows-dator med MARS-agent** | Filer, mappar, systemtillstånd | Säkerhetskopiera till Recovery Services Vault. | Säkerhetskopiera tre gånger per dag<br/><br/> Ingen app-medveten säkerhets kopiering<br/><br/> Återställa fil, mapp, volym
**Direkt säkerhets kopiering av Linux-datorer med MARS-agent** | Säkerhets kopiering stöds inte
**Säkerhetskopiera till DPM** | Filer, mappar, volymer, system tillstånd, AppData | Säkerhetskopiera till den lokala DPM-lagringen. DPM säkerhetskopieras sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhets kopiering och återställning<br/><br/> Linux som stöds för virtuella datorer (Hyper-V/VMware)<br/><br/> Oracle stöds inte
**Säkerhetskopiera till MABS** | Filer, mappar, volymer, system tillstånd, AppData | Säkerhetskopiera till MABS lokal lagring. MABS säkerhetskopierar sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig granularitet för säkerhets kopiering och återställning<br/><br/> Linux som stöds för virtuella datorer (Hyper-V/VMware)<br/><br/> Oracle stöds inte

## <a name="azure-vm-backup-support"></a>Support för Azure VM-säkerhetskopiering

### <a name="azure-vm-limits"></a>Begränsningar för virtuella Azure-datorer

**Gränserna** | **Information**
--- | ---
**Datadiskar för virtuella Azure-datorer** | Se [support mat ris för säkerhets kopiering av virtuella Azure-datorer](./backup-support-matrix-iaas.md#vm-storage-support).
**Datadiskstorlek för virtuella Azure-datorer** | Enskild disk storlek kan vara upp till 32 TB och högst 256 TB kombinerat för alla diskar i en virtuell dator.

### <a name="azure-vm-backup-options"></a>Alternativ för säkerhetskopiering av virtuella Azure-datorer

Här är what's som stöds om du vill säkerhetskopiera virtuella Azure-datorer:

**Dator** | **Vad har säkerhetskopierats** | **Plats** | **Funktioner**
--- | --- | --- | ---
**VM-säkerhetskopiering i Azure med hjälp av VM-tillägget** | Hela VM | Säkerhetskopiera till valvet. | Tillägg som installeras när du aktiverar säkerhets kopiering för en virtuell dator.<br/><br/> Säkerhetskopiera en gång om dagen.<br/><br/> App-medveten säkerhets kopiering för virtuella Windows-datorer; filkonsekvent säkerhets kopiering för virtuella Linux-datorer. Du kan konfigurera program konsekvens för Linux-datorer med hjälp av anpassade skript.<br/><br/> Återställ virtuell dator eller disk.<br/><br/>[Säkerhets kopiering och återställning av Active Directory domänkontrollanter](active-directory-backup-restore.md) stöds.<br><br> Det går inte att säkerhetskopiera en virtuell Azure-dator till en lokal plats.
**Virtuell Azure VM-säkerhetskopiering med MARS-agenten** | Filer, mappar, systemtillstånd | Säkerhetskopiera till valvet. | Säkerhetskopiera tre gånger per dag.<br/><br/> Om du vill säkerhetskopiera vissa filer eller mappar i stället för hela den virtuella datorn, kan MARS-agenten köras tillsammans med VM-tillägget.
**Virtuell Azure-dator med DPM** | Filer, mappar, volymer, system tillstånd, AppData | Säkerhetskopiera till lokal lagring av virtuell Azure-dator som kör DPM. DPM säkerhetskopieras sedan till valvet. | App-medvetna ögonblicks bilder.<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/> Oracle stöds inte.
**Virtuell Azure-dator med MABS** | Filer, mappar, volymer, system tillstånd, AppData | Säkerhetskopiera till lokal lagring av virtuell Azure-dator som kör MABS. MABS säkerhetskopierar sedan till valvet. | App-medvetna ögonblicks bilder.<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/> Oracle stöds inte.

## <a name="linux-backup-support"></a>Stöd för Linux-säkerhetskopiering

Här är what's som stöds om du vill säkerhetskopiera Linux-datorer:

**Typ av säkerhets kopiering** | **Linux (Azure-godkänt)**
--- | ---
**Direkt säkerhets kopiering av lokal dator som kör Linux** | Stöds inte. MARS-agenten kan bara installeras på Windows-datorer.
**Använda agent tillägget för att säkerhetskopiera virtuell Azure-dator som kör Linux** | Programkonsekvent säkerhets kopiering med hjälp av [anpassade skript](backup-azure-linux-app-consistent.md).<br/><br/> Återställning på filnivå.<br/><br/> Återställ genom att skapa en virtuell dator från en återställningspunkt eller disk.
**Använda DPM för att säkerhetskopiera lokala datorer som kör Linux** | Filkonsekvent säkerhets kopiering av virtuella Linux-gäst datorer i Hyper-V och VMware.<br/><br/> VM-återställning av virtuella Hyper-V-och VMware Linux-gäst datorer.
**Använda MABS för att säkerhetskopiera lokala datorer som kör Linux** | Filkonsekvent säkerhets kopiering av virtuella Linux-gäst datorer i Hyper-V och VMware.<br/><br/> VM-återställning av virtuella Hyper-V-och VMware Linux-gäst datorer.
**Använda MABS eller DPM för att säkerhetskopiera virtuella Linux Azure-datorer** | Stöds inte.

## <a name="daylight-saving-time-support"></a>Stöd för sommar tid

Azure Backup stöder inte automatisk klock justering för sommar tid för virtuella Azure-säkerhetskopieringar. Den skiftar inte timmen för säkerhets kopieringen framåt eller bakåt. För att säkerställa att säkerhets kopieringen körs vid önskad tidpunkt ändrar du säkerhets kopierings principerna manuellt efter behov.

## <a name="disk-deduplication-support"></a>Stöd för disk deduplicering

Stöd för diskdeduplicering är följande:

- Disk deduplicering stöds lokalt när du använder DPM eller MABS för att säkerhetskopiera virtuella Hyper-V-datorer som kör Windows. Windows Server utför datadeduplicering (på värdnivå) på virtuella hård diskar (VHD: er) som är kopplade till den virtuella datorn som säkerhets kopierings lagring.
- Deduplicering stöds inte i Azure för säkerhetskopieringskomponenter. När DPM och MABS distribueras i Azure går det inte att deduplicera lagrings diskarna som är anslutna till den virtuella datorn.

## <a name="security-and-encryption-support"></a>Säkerhet och krypterings stöd

Azure Backup stöder kryptering för överförings-och rest-data.

### <a name="network-traffic-to-azure"></a>Nätverks trafik till Azure

- Säkerhets kopierings trafik från servrar till Recovery Services-valvet krypteras med hjälp av Advanced Encryption Standard 256.
- Säkerhetskopierade data skickas via en säker HTTPS-anslutning.

### <a name="data-security"></a>Datasäkerhet

- Säkerhetskopierade data lagras i Recovery Services valvet i krypterad form.
- När data säkerhets kopie ras från lokala servrar med MARS-agenten krypteras data med en lösen fras innan överföring till Azure Backup och dekrypteras först efter att den har laddats ned från Azure Backup.
- När du säkerhetskopierar virtuella Azure-datorer måste du konfigurera kryptering *i* den virtuella datorn.
- Azure Backup stöder Azure Disk Encryption, som använder BitLocker på virtuella Windows-datorer och **dm-crypt** på virtuella Linux-datorer.
- På Server sidan använder Azure Backup [Azure Storage tjänst kryptering](../storage/common/storage-service-encryption.md)som skyddar data i vila.

**Dator** | **Under överföring** | **I vila**
--- | --- | ---
**Lokala Windows-datorer utan DPM/MABS** | ![Ja][green] | ![Ja][green]
**Virtuella Azure-datorer** | ![Ja][green] | ![Ja][green]
**Lokala Windows-datorer eller virtuella Azure-datorer med DPM** | ![Ja][green] | ![Ja][green]
**Lokala Windows-datorer eller virtuella Azure-datorer med MABS** | ![Ja][green] | ![Ja][green]

## <a name="compression-support"></a>Stöd för komprimering

Säkerhets kopiering stöder komprimering av säkerhets kopierings trafik, som sammanfattas i följande tabell.

- För virtuella Azure-datorer läser VM-tillägget data direkt från Azure Storage-kontot över lagrings nätverket, så det är inte nödvändigt att komprimera den här trafiken.
- Om du använder DPM eller MABS kan du spara bandbredd genom att komprimera data innan de säkerhets kopie ras.

**Dator** | **Komprimera till MABS/DPM (TCP)** | **Komprimera till valv (HTTPS)**
--- | --- | ---
**Direkt säkerhets kopiering av lokala Windows-datorer** | Ej tillämpligt | ![Ja][green]
**Säkerhets kopiering av virtuella Azure-datorer med hjälp av VM-tillägg** | NA | NA
**Säkerhetskopiera lokalt/Azure-datorer med hjälp av MABS/DPM** | ![Ja][green] | ![Ja][green]

## <a name="retention-limits"></a>Gräns för kvarhållning

**Inställning** | **Gränser**
--- | ---
**Högsta antal återställnings punkter per skyddad instans (dator eller arbets belastning)** | 9 999
**Maximal förfallo tid för en återställnings punkt** | Obegränsad
**Högsta säkerhetskopieringsfrekvens till DPM/MABS** | Varje kvart för SQL Server<br/><br/> En gång i timmen för andra arbets belastningar
**Högsta säkerhetskopieringsfrekvens till valv** | **Lokala Windows-datorer eller virtuella Azure-datorer som kör mars:** Tre per dag<br/><br/> **DPM/Mabs:** Två per dag<br/><br/> **Azure VM-säkerhetskopiering:** En per dag
**Kvarhållning av återställnings punkt** | Varje dag, varje vecka, varje månad, varje år
**Högsta kvarhållningsperiod** | Beror på säkerhetskopieringsfrekvensen
**Återställningspunkter på DPM-/MABS-disk** | 64 för fil servrar; 448 för App-servrar <br/><br/>Obegränsade band återställnings punkter för lokal DPM

## <a name="cross-region-restore"></a>Återställning mellan regioner

Azure Backup har lagt till funktionen för återställning av kors region för att förstärka data tillgänglighet och återhämtnings kapacitet, vilket ger dig fullständig kontroll för att återställa data till en sekundär region. Om du vill konfigurera den här funktionen går [du till artikeln ange återställning mellan regioner.](backup-create-rs-vault.md#set-cross-region-restore).. Den här funktionen stöds för följande hanterings typer:

| Typ av säkerhets kopierings hantering | Stöds                                                    | Regioner som stöds |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Stöds för virtuella Azure-datorer med både hanterade och ohanterade diskar. Stöds inte för klassiska virtuella datorer. | Alla offentliga Azure-regioner.  |
| SQL-/SAP HANA | Ja                                                          | Alla offentliga regioner utom Frankrike |
| MARS-agent/lokalt  | Nej                                                           | Ej tillämpligt               |
| AFS (Azure-filresurser)                 | Nej                                                           | Ej tillämpligt               |

## <a name="next-steps"></a>Nästa steg

- [Granska support mat ris](backup-support-matrix-iaas.md) för säkerhets kopiering av virtuella Azure-datorer.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
