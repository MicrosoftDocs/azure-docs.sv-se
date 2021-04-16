---
title: Stödmatris för Azure Backup
description: Innehåller en sammanfattning av stödinställningar och begränsningar för Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 04/14/2021
ms.custom: references_regions
ms.openlocfilehash: 5c74a34efe8075ab7a34fab4570d9513900b3f81
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517427"
---
# <a name="support-matrix-for-azure-backup"></a>Stödmatris för Azure Backup

Du kan använda [Azure Backup](backup-overview.md) för att backa upp data Microsoft Azure molnplattformen. Den här artikeln sammanfattar allmänna supportinställningar och begränsningar för Azure Backup och distributioner.

Andra supportmatriser är tillgängliga:

- Stödmatris för [säkerhetskopiering av virtuella Azure-datorer](backup-support-matrix-iaas.md)
- Stödmatris för säkerhetskopiering [med hjälp System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md)
- Stödmatris för säkerhetskopiering med hjälp [Microsoft Azure MARS-agenten (Recovery Services)](backup-support-matrix-mars-agent.md)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>Stöd för valv

Azure Backup använder Recovery Services-valv för att dirigera och hantera säkerhetskopieringar för följande typer av arbetsbelastningar – virtuella Azure-datorer, SQL i virtuella Azure-datorer, SAP HANA på virtuella Azure-datorer, Azure-filresurser och lokala arbetsbelastningar med Azure Backup Agent, Azure Backup Server och System Center DPM. Den använder också Recovery Services-valv för att lagra säkerhetskopierade data för dessa arbetsbelastningar.

I följande tabell beskrivs funktionerna i Recovery Services-valv:

**Funktion** | **Information**
--- | ---
**Valv i prenumeration** | Upp till 500 Recovery Services-valv i en enstaka prenumeration.
**Datorer i ett valv** | Upp till 2 000 datakällor för alla arbetsbelastningar (som virtuella Azure-datorer, SQL Server VM, MABS-servrar och så vidare) kan skyddas i ett enda valv.<br><br>Upp till 1 000 virtuella Azure-datorer i ett enda valv.<br/><br/> Upp till 50 MABS-servrar kan registreras i ett enda valv.
**Datakällor** | Den maximala storleken för en [enskild datakälla](./backup-azure-backup-faq.yml#how-is-the-data-source-size-determined-) är 54 400 GB. Den här gränsen gäller inte för säkerhetskopiering av virtuella Azure-datorer. Inga begränsningar gäller för den totala mängden data som du kan valva upp till.
**Säkerhetskopieringar till valv** | **Virtuella Azure-datorer:** En gång om dagen.<br/><br/>**Datorer som skyddas av DPM/MABS:** Två gånger om dagen.<br/><br/> **Datorer som säkerhetskopieras direkt med hjälp av MARS-agenten:** Tre gånger om dagen.
**Säkerhetskopieringar mellan valv** | Säkerhetskopieringen är inom en region.<br/><br/> Du behöver ett valv i varje Azure-region som innehåller virtuella datorer som du vill valva upp. Du kan inte backa upp till en annan region.
**Flytta valv** | Du kan [flytta valv](./backup-azure-move-recovery-services-vault.md) mellan prenumerationer eller mellan resursgrupper i samma prenumeration. Det finns dock inte stöd för att flytta valv mellan regioner.
**Flytta data mellan valv** | Det går inte att flytta säkerhetskopierade data mellan valv.
**Ändra lagringstyp för valv** | Du kan ändra lagringsreplikeringstypen (geo-redundant lagring eller lokalt redundant lagring) för ett valv innan säkerhetskopiorna lagras. När säkerhetskopiering börjar i valvet går det inte att ändra replikeringstypen.
**Zonredundant lagring (ZRS)** | Tillgängligt i Storbritannien, södra (UKS) och Asien, sydöstra (SEA).
**Privata slutpunkter** | I [det här avsnittet](./private-endpoints.md#before-you-start) finns krav för att skapa privata slutpunkter för ett Recovery Service-valv.  

## <a name="on-premises-backup-support"></a>Stöd för lokal säkerhetskopiering

Här är vad som stöds om du vill backa upp lokala datorer:

**Dator** | **Vad som säkerhetskopieras** | **Plats** | **Funktioner**
--- | --- | --- | ---
**Direkt säkerhetskopiering av Windows-dator med MARS-agent** | Filer, mappar, systemtillstånd | Back up to Recovery Services vault ( Valv för valv i Recovery Services). | Back up three times a day ( 3 gånger per dag)<br/><br/> Ingen appmedveten säkerhetskopiering<br/><br/> Återställa fil, mapp, volym
**Direkt säkerhetskopiering av Linux-dator med MARS-agent** | Säkerhetskopiering stöds inte
**Back up to DPM** | Filer, mappar, volymer, systemtillstånd, appdata | Tillbaka till lokal DPM-lagring. DPM säkerhetskopieras sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig kornighet för säkerhetskopiering och återställning<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware)<br/><br/> Oracle stöds inte
**Tillbaka till MABS** | Filer, mappar, volymer, systemtillstånd, appdata | Back up to MABS local storage ( Backa upp till mabs lokal lagring). MABS säkerhetskopierar sedan till valvet. | Appmedvetna ögonblicksbilder<br/><br/> Fullständig kornighet för säkerhetskopiering och återställning<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware)<br/><br/> Oracle stöds inte

## <a name="azure-vm-backup-support"></a>Stöd för säkerhetskopiering av virtuella Azure-datorer

### <a name="azure-vm-limits"></a>Begränsningar för virtuella Azure-datorer

**Gräns** | **Information**
--- | ---
**Datadiskar för virtuella Azure-datorer** | Se [supportmatrisen för säkerhetskopiering av virtuella Azure-datorer.](./backup-support-matrix-iaas.md#vm-storage-support)
**Datadiskstorlek för virtuella Azure-datorer** | Den enskilda diskstorleken kan vara upp till 32 TB och högst 256 TB kombinerat för alla diskar på en virtuell dator.

### <a name="azure-vm-backup-options"></a>Alternativ för säkerhetskopiering av virtuella Azure-datorer

Här är vad som stöds om du vill backa upp virtuella Azure-datorer:

**Dator** | **Vad som säkerhetskopieras** | **Plats** | **Funktioner**
--- | --- | --- | ---
**Säkerhetskopiering av virtuella Azure-datorer med hjälp av VM-tillägg** | Hela den virtuella datorn | Valv igen. | Tillägget installeras när du aktiverar säkerhetskopiering för en virtuell dator.<br/><br/> Back up en gång om dagen.<br/><br/> Appmedveten säkerhetskopiering för virtuella Windows-datorer; fil-konsekvent säkerhetskopiering för virtuella Linux-datorer. Du kan konfigurera appkonsekvens för Linux-datorer med hjälp av anpassade skript.<br/><br/> Återställ virtuell dator eller disk.<br/><br/>[Säkerhetskopiering och återställning av Active Directory-domänkontrollanter](active-directory-backup-restore.md) stöds.<br><br> Det går inte att backa upp en virtuell Azure-dator till en lokal plats.
**Säkerhetskopiering av virtuella Azure-datorer med MARS-agenten** | Filer, mappar, systemtillstånd | Back up to vault ( Valv Valv). | Back up three times a day ( 3 gånger per dag).<br/><br/> Om du vill backa upp specifika filer eller mappar i stället för hela den virtuella datorn kan MARS-agenten köras tillsammans med VM-tillägget.
**Virtuell Azure-dator med DPM** | Filer, mappar, volymer, systemtillstånd, appdata | Backa upp till lokal lagring av virtuella Azure-datorer som kör DPM. DPM säkerhetskopieras sedan till valvet. | Appmedvetna ögonblicksbilder.<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/> Oracle stöds inte.
**Virtuell Azure-dator med MABS** | Filer, mappar, volymer, systemtillstånd, appdata | Backa upp till lokal lagring av virtuella Azure-datorer som kör MABS. MABS säkerhetskopierar sedan till valvet. | Appmedvetna ögonblicksbilder.<br/><br/> Fullständig granularitet för säkerhetskopiering och återställning.<br/><br/> Linux stöds för virtuella datorer (Hyper-V/VMware).<br/><br/> Oracle stöds inte.

## <a name="linux-backup-support"></a>Stöd för Linux-säkerhetskopiering

Här är vad som stöds om du vill backa upp Linux-datorer:

**Typ av säkerhetskopiering** | **Linux (Azure-godkänt)**
--- | ---
**Direkt säkerhetskopiering av en lokal dator som kör Linux** | Stöds inte. MARS-agenten kan bara installeras på Windows-datorer.
**Använda agenttillägget för att backa upp virtuella Azure-datorer som kör Linux** | App-konsekvent säkerhetskopiering med [hjälp av anpassade skript](backup-azure-linux-app-consistent.md).<br/><br/> Återställning på filnivå.<br/><br/> Återställ genom att skapa en virtuell dator från en återställningspunkt eller disk.
**Använda DPM för att backa upp lokala datorer som kör Linux** | Fil-konsekvent säkerhetskopiering av virtuella Linux-gäst-VM:ar på Hyper-V och VMware.<br/><br/> VM-återställning av virtuella Hyper-V- och VMware Linux-gäst-VM:ar.
**Använda MABS för att backa upp lokala datorer som kör Linux** | Fil-konsekvent säkerhetskopiering av virtuella Linux-gäst-VM:ar på Hyper-V och VMware.<br/><br/> VM-återställning av virtuella Hyper-V- och VMware Linux-gäst-VM:ar.
**Använda MABS eller DPM för att operativsystemets virtuella Linux Azure-datorer** | Stöds inte.

## <a name="daylight-saving-time-support"></a>Stöd för sommartid

Azure Backup stöder inte automatisk klockjustering för sommartid för säkerhetskopiering av virtuella Azure-datorer. Den flyttar inte timmen för säkerhetskopieringen framåt eller bakåt. För att säkerställa att säkerhetskopieringen körs vid önskad tidpunkt ändrar du säkerhetskopieringsprinciperna manuellt efter behov.

## <a name="disk-deduplication-support"></a>Stöd för diskdeduplicering

Stöd för diskdeduplicering är följande:

- Diskdeduplicering stöds lokalt när du använder DPM eller MABS för att krypa upp virtuella Hyper-V-datorer som kör Windows. Windows Server utför datadeduplicering (på värdnivå) på virtuella hårddiskar (VHD) som är anslutna till den virtuella datorn som lagringsenheter för säkerhetskopior.
- Deduplicering stöds inte i Azure för säkerhetskopieringskomponenter. När DPM och MABS distribueras i Azure kan inte de lagringsdiskar som är anslutna till den virtuella datorn dedupliceras.

## <a name="security-and-encryption-support"></a>Stöd för säkerhet och kryptering

Azure Backup har stöd för kryptering för data under överföring och i vila.

### <a name="network-traffic-to-azure"></a>Nätverkstrafik till Azure

- Säkerhetskopieringstrafik från servrar till Recovery Services-valvet krypteras med hjälp av Advanced Encryption Standard 256.
- Säkerhetskopierade data skickas via en säker HTTPS-anslutning.

### <a name="data-security"></a>Datasäkerhet

- Säkerhetskopierade data lagras i Recovery Services-valvet i krypterad form.
- När data säkerhetskopieras från lokala servrar med MARS-agenten krypteras data med en lösenfras innan de överförs till Azure Backup och dekrypteras först när de har hämtats från Azure Backup.
- När du backar upp virtuella Azure-datorer måste du konfigurera kryptering *på den* virtuella datorn.
- Azure Backup stöder Azure Disk Encryption, som använder BitLocker på virtuella Windows-datorer och **dm-crypt** på virtuella Linux-datorer.
- På backend-Azure Backup använder [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md), som skyddar vilodata.

**Dator** | **Under överföring** | **I vila**
--- | --- | ---
**Lokala Windows-datorer utan DPM/MABS** | ![Ja][green] | ![Ja][green]
**Virtuella Azure-datorer** | ![Ja][green] | ![Ja][green]
**Lokala Windows-datorer eller virtuella Azure-datorer med DPM** | ![Ja][green] | ![Ja][green]
**Lokala Windows-datorer eller virtuella Azure-datorer med MABS** | ![Ja][green] | ![Ja][green]

## <a name="compression-support"></a>Stöd för komprimering

Säkerhetskopiering stöder komprimering av säkerhetskopieringstrafik, enligt sammanfattningen i följande tabell.

- För virtuella Azure-datorer läser VM-tillägget data direkt från Azure Storage-kontot via lagringsnätverket, så det är inte nödvändigt att komprimera trafiken.
- Om du använder DPM eller MABS kan du spara bandbredd genom att komprimera data innan de säkerhetskopieras.

**Dator** | **Komprimera till MABS/DPM (TCP)** | **Komprimera till valv (HTTPS)**
--- | --- | ---
**Direkt säkerhetskopiering av lokala Windows-datorer** | Ej tillämpligt | ![Ja][green]
**Säkerhetskopiera virtuella Azure-datorer med hjälp av VM-tillägg** | NA | NA
**Säkerhetskopiera lokalt/Azure-datorer med hjälp av MABS/DPM** | ![Ja][green] | ![Ja][green]

## <a name="retention-limits"></a>Gräns för kvarhållning

**Inställning** | **Gränser**
--- | ---
**Maximalt antal återställningspunkter per skyddad instans (dator eller arbetsbelastning)** | 9,999
**Maximal förfallotid för en återställningspunkt** | Obegränsad
**Högsta säkerhetskopieringsfrekvens till DPM/MABS** | Varje kvart för SQL Server<br/><br/> En gång i timmen för andra arbetsbelastningar
**Högsta säkerhetskopieringsfrekvens till valv** | **Lokala Windows-datorer eller virtuella Azure-datorer som kör MARS:** Tre per dag<br/><br/> **DPM/MABS:** Två per dag<br/><br/> **Säkerhetskopiering av virtuell Azure-dator:** En per dag
**Kvarhållning av återställningspunkt** | Varje dag, varje vecka, varje månad, varje år
**Högsta kvarhållningsperiod** | Beror på säkerhetskopieringsfrekvensen
**Återställningspunkter på DPM-/MABS-disk** | 64 för filservrar; 448 för appservrar <br/><br/>Obegränsade bandåterställningspunkter för lokal DPM

## <a name="cross-region-restore"></a>Återställning mellan regioner

Azure Backup har lagt till funktionen Återställning mellan regioner för att förbättra datatillgängligheten och återhämtningsförmågan, vilket ger dig fullständig kontroll över återställningen av data till en sekundär region. Om du vill konfigurera den här funktionen [går du till artikeln Ange återställning mellan regioner.](backup-create-rs-vault.md#set-cross-region-restore). Den här funktionen stöds för följande hanteringstyper:

| Typ av säkerhetskopieringshantering | Stöds                                                    | Regioner som stöds |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | Stöds för virtuella Azure-datorer (inklusive krypterade virtuella Azure-datorer) med både hanterade och ohanterade diskar. Stöds inte för klassiska virtuella datorer. | Tillgängligt i alla offentliga Azure-regioner och suveräna regioner förutom Frankrike, centrala, Australien, centrala, Sydafrika, norra, Förenade Arabemiraten, norra, Schweiz, norra, Tyskland, västra centrala, Nederländerna, östra, UG IOWA och UG Virginia. <br>Om du vill ha information om användning i dessa regioner kan du kontakta [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| SQL /SAP HANA | I förhandsversion                                                      | Tillgängligt i alla offentliga Azure-regioner och suveräna regioner förutom Frankrike, centrala, Australien, centrala, Sydafrika, norra, Förenade Arabemiraten, norra, Schweiz, norra, Tyskland, västra centrala, Nederländerna, östra, UG IOWA och UG Virginia. <br>Om du vill ha information om användning i dessa regioner kan du kontakta [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) |
| MARS-agent/lokal  | No                                                           | Ej tillämpligt               |
| AFS (Azure-filresurser)                 | No                                                           | Ej tillämpligt               |

## <a name="next-steps"></a>Nästa steg

- [Granska supportmatrisen för](backup-support-matrix-iaas.md) säkerhetskopiering av virtuella Azure-datorer.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png