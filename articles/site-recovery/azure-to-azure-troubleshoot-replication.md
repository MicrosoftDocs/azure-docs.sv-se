---
title: Felsöka replikering av virtuella Azure-datorer med Azure Site Recovery
description: Felsök replikering i haveri beredskap för Azure VM med Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: dc14334668b76ee8cbb81e48abfe1eecf17fa138
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96007366"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Felsöka replikering i haveri beredskap för virtuella Azure-datorer

I den här artikeln beskrivs vanliga problem i Azure Site Recovery när du replikerar och återställer Azure Virtual Machines (VM) från en region till en annan region. Det beskriver också hur du felsöker vanliga problem. Mer information om konfigurationer som stöds finns i [support mat ris för replikering av virtuella Azure-datorer](./azure-to-azure-support-matrix.md).

Azure Site Recovery replikerar konsekvent data från käll regionen till Disaster Recovery-regionen. Det skapar också en kraschad återställnings punkt var femte minut. Om Site Recovery inte kan skapa återställnings punkter i 60 minuter, aviseras du med den här informationen:

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

I följande avsnitt beskrivs orsaker och lösningar.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>Hög dataändringstakt på den virtuella källdatorn

Azure Site Recovery skapar en händelse om data ändrings hastigheten på den virtuella käll datorn är högre än de gränser som stöds. Om du vill se om problemet beror på hög omsättning går du till **replikerade objekt**  >  **VM**  >  **-händelser-senaste 72 timmarna**.
Du bör se **ändrings takten för händelse data utöver de gränser som stöds**:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="Azure Site Recovery sida som visar en hög data ändrings hastighet som är för hög.":::

Om du väljer händelsen bör du se exakt disk information:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="Sidan som visar händelse informationen för data ändrings frekvensen.":::

### <a name="azure-site-recovery-limits"></a>Gränser för Azure Site Recovery

Följande tabell innehåller gränserna för Azure Site Recovery. Dessa gränser är baserade på våra tester, men de kan inte användas för alla möjliga kombinationer av program I indata-output (I/O). De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program.

Det finns två gränser att tänka på: data omsättning per disk och data omsättning per virtuell dator. Nu ska vi titta på Premium P20-disken i följande tabell för ett exempel. För en enskild virtuell dator kan Site Recovery hantera 5 MB/s omsättning per disk med högst fem sådana diskar. Site Recovery har en gräns på 54 MB/s av den totala omsättningen per virtuell dator.

**Lagrings mål för replikering** | **Genomsnittlig I/O-storlek för käll disk** |**Genomsnittlig data omsättning för käll disk** | **Total data omsättning per dag för käll data disk**
---|---|---|---
Standard Storage | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB    | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |    336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |20 MB/s | 1684 GB per disk

### <a name="solution"></a>Lösning

Azure Site Recovery har gränser för data ändrings hastigheter, beroende på typ av disk. Om du vill se om det här problemet är återkommande eller tillfälligt kan du hitta data ändrings takten för den berörda virtuella datorn. Gå till den virtuella käll datorn, hitta måtten under **övervakning** och Lägg till måtten som visas på den här skärm bilden:

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="Sidan som visar den tre stegs processen för att hitta data ändrings takten.":::

1. Välj **Lägg till mått** och Lägg till **OS disk-skrivna byte/s** och **data disk-skrivna byte/s**.
1. Övervaka insamling som visas i skärm bilden.
1. Visa de totala Skriv åtgärder som sker mellan OS-diskar och alla data diskar tillsammans. Dessa mått kanske inte ger dig information på nivån per disk, men de visar det totala mönstret för data omsättning.

En insamling i data ändrings takt kan komma från en tillfällig data burst. Om data ändrings takten är större än 10 MB/s (för Premium) eller 2 MB/s (för standard) och är nere, kommer replikeringen att fångas upp. Överväg något av följande alternativ om omsättningen är konsekvent väl efter den gräns som stöds:

- Undanta disken som orsakar en hög data ändrings hastighet: först inaktiverar du replikeringen. Sedan kan du undanta disken med hjälp av [PowerShell](azure-to-azure-exclude-disks.md).
- Ändra nivån på lagrings disken för haveri beredskap: det här alternativet är endast möjligt om disk data omsättningen är mindre än 20 MB/s. Till exempel har en virtuell dator med en P10-disk en data omsättning på mer än 8 MB/s men mindre än 10 MB/s. Om kunden kan använda en P30-disk för mål lagring under skyddet kan problemet lösas. Den här lösningen är bara möjlig för datorer som använder Premium-Managed diskar. Följ de här stegen:

  1. Gå till **diskarna** på den berörda replikerade datorn och kopiera replik diskens namn.
  1. Gå till den här repliken av den hanterade disken.
  1. Du kan se en banderoll i **översikten** om att en SAS-webbadress har skapats. Välj den här banderollen och Avbryt exporten. Ignorera det här steget om du inte ser banderollen.
  1. Så snart SAS-URL: en har återkallats, går du till **konfiguration** för den hanterade disken. Öka storleken så att Site Recovery stöder den observerade omsättnings takten på käll disken.

## <a name="network-connectivity-problems"></a>Problem med nätverks anslutningen

### <a name="network-latency-to-a-cache-storage-account"></a>Nätverks fördröjning till ett cache Storage-konto

Site Recovery skickar replikerade data till cache Storage-kontot. Nätverks fördröjningen kan uppstå om överföringen av data från en virtuell dator till cache Storage-kontot är långsammare än 4 MB i 3 sekunder.

Om du vill söka efter ett problem som rör fördröjningen använder du [AzCopy](../storage/common/storage-use-azcopy-v10.md). Du kan använda det här kommando rads verktyget för att överföra data från den virtuella datorn till cache Storage-kontot. Om svars tiden är hög kontrollerar du om du använder en virtuell nätverks installation (NVA) för att styra utgående nätverks trafik från virtuella datorer. Installationen kan bli begränsad om all replikeringstrafik passerar genom NVA.

Vi rekommenderar att du skapar en nätverks tjänst slut punkt i ditt virtuella nätverk för "lagring" så att replikeringstrafiken inte går till NVA. Mer information finns i [konfiguration av virtuell nätverks](azure-to-azure-about-networking.md#network-virtual-appliance-configuration)installation.

### <a name="network-connectivity"></a>Nätverksanslutningar

För att Site Recovery replikering ska fungera måste den virtuella datorn tillhandahålla utgående anslutning till vissa URL: er eller IP-intervall. Du kan ha en virtuell dator bakom en brand vägg eller använda regler för nätverks säkerhets grupper (NSG) för att kontrol lera utgående anslutningar. I så fall kan det uppstå problem. För att se till att alla webb adresser är anslutna, se [utgående anslutningar för URL: er](azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>Fel-ID 153006 – ingen programkonsekvent återställnings punkt är tillgänglig för den virtuella datorn under de senaste X minuterna

Nedan följer några av de vanligaste problemen.

### <a name="known-issue-in-sql-server-20082008-r2"></a>Känt problem i SQL Server 2008/2008 R2

**Så här åtgärdar du:** Det finns ett känt problem med SQL Server 2008/2008 R2. Läs artikeln [Azure Site Recovery agent eller andra VSS-säkerhetskopieringar som inte kommer från en komponent Miss lyckas för en server som är värd för SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2).

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Azure Site Recovery-jobb fungerar inte på servrar som är värdar för någon version av SQL Server-instanser med AUTO_CLOSE databaser

**Så här åtgärdar du:** Se artikel [VSS-säkerhetskopieringar som inte är Component, till exempel Azure Site Recovery jobb inte kan köras på servrar som är värdar för SQL Server instanser med AUTO_CLOSE databaser](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser).

### <a name="known-issue-in-sql-server-2016-and-2017"></a>Kända problem i SQL Server 2016 och 2017

**Så här åtgärdar** du: Läs artikeln [kumulativ uppdatering 16 för SQL Server 2017](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017).

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Du använder konfiguration för Azure Storage dirigering

**Så här åtgärdar du**: Azure Site Recovery kan inte skapa en konsekvent återställnings punkt för Lagringsdirigering konfiguration. [Konfigurera replikeringsprincipen](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="app-consistency-not-enabled-on-linux-servers"></a>Program-konsekvens är inte aktiverat på Linux-servrar

**Så här åtgärdar du** : Azure Site Recovery för Linux-åtgärds system har stöd för anpassade skript för program-konsekvens. Det anpassade skriptet med för-och-post-alternativen används av Azure Site Recovery Mobility-agenten för program konsekvens. Så [här](./site-recovery-faq.md#replication) aktiverar du det.

### <a name="more-causes-because-of-vss-related-issues"></a>Fler orsaker på grund av VSS-relaterade problem:

Om du vill felsöka ytterligare kan du kontrol lera filerna på käll datorn för att få en exakt felkod för felet:

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

Du hittar felen genom att öppna filen _vacp. log_ i en text redigerings sökning för strängen **vacpError**.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

I föregående exempel är **2147754994** den felkod som visar om felet som följer efter den här meningen.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS-skrivaren är inte installerad-fel 2147221164

**Så här åtgärdar** du: om du vill skapa en tillämpnings konsekvens tag Azure Site Recovery använder tjänsten Volume Shadow Copy (VSS). Site Recovery installerar en VSS-Provider för åtgärden för att ta ögonblicks bilder av program konsekvens. Azure Site Recovery installerar VSS-providern som en tjänst. Om VSS-providern inte är installerad går det inte att skapa en ögonblicks bild av program. Den visar **fel-ID 0x80040154-klassen har inte registrerats**. Läs artikeln om [fel sökning av VSS Writer-installation](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS-skrivaren är inaktive rad-fel 2147943458

**Så här åtgärdar** du: om du vill generera en applikations konsekvens kod använder Azure Site Recovery VSS. Site Recovery installerar en VSS-Provider för åtgärden för att ta ögonblicks bilder av program konsekvens. Den här VSS-providern installeras som en tjänst. Om tjänsten VSS Provider inte är aktive rad går det inte att skapa en ögonblicks bild av program konsekvens. Det visar felet: **den angivna tjänsten är inaktive rad och kan inte startas (0x80070422)**.

Om VSS är inaktiverat:

- Kontrol lera att start typen för tjänsten VSS Provider är inställd på **Automatisk**.
- Starta om följande tjänster:
  - VSS-tjänst.
  - Azure Site Recovery VSS-providern.
  - VDS-tjänsten.

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS-PROVIDER NOT_REGISTERED-fel 2147754756

**Så här åtgärdar** du: om du vill generera en applikations konsekvens kod använder Azure Site Recovery VSS. Kontrol lera om tjänsten Azure Site Recovery VSS Provider är installerad.

Använd följande kommandon för att installera om VSS-providern:

1. Avinstallera befintlig provider:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. Installera om VSS-providern:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Kontrol lera att start typen för tjänsten VSS Provider är inställd på **Automatisk**.

Starta om följande tjänster:

- VSS-tjänst.
- Azure Site Recovery VSS-providern.
- VDS-tjänsten.
