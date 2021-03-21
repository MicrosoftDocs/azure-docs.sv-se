---
title: Azure Backup ord lista
description: Den här artikeln definierar termer som är användbara för användning med Azure Backup.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: fb46415c8bdb463556d57004e37d741c1b9a9b57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502033"
---
# <a name="azure-backup-glossary"></a>Azure Backup ord lista

Den här ord listan kan vara till hjälp när du använder Azure Backup.

>[!NOTE]
>
> - Villkor som är markerade med prefixet "(arbets belastnings villkor)" avser de villkor som endast är relevanta i kontexten för en viss del av arbets belastningar som Azure Backup stöder.
> - För villkor som används vanligt vis i Azure Backup dokumentation men som hänvisar till andra Azure-tjänster, finns en direkt länk till dokumentationen för den relevanta Azure-tjänsten.

## <a name="afs-azure-file-shares"></a>AFS (Azure-filresurser)

Läs [Azure Files-dokumentationen](../storage/files/storage-files-introduction.md).

## <a name="alternate-location-recovery"></a>Återställning av alternativ plats

En återställning utfördes från återställnings punkten till en annan plats än den ursprungliga platsen där säkerhets kopiorna gjordes. När du använder Azure VM backup innebär detta att återställa den virtuella datorn till en annan server än den ursprungliga servern där säkerhets kopiorna gjordes. När du använder Azure-filresurs-säkerhetskopiering kan detta innebära att återställa data till en fil resurs som skiljer sig från den säkerhetskopierade fil resursen.

## <a name="application-consistent-backup"></a>Programkonsekvent säkerhets kopiering

(Arbets belastnings bestämd term)

Programkonsekventa säkerhets kopieringar fångar upp minnes innehåll och väntande I/O-åtgärder. I programkonsekventa ögonblicks bilder används VSS-skrivare för [VSS](#vss-windows-volume-shadow-copy-service) (eller för in-eller publicerings skript för Linux) för att säkerställa konsekvensen av AppData innan en säkerhets kopiering sker. [Läs mer](backup-azure-vms-introduction.md).

## <a name="azure-resource-manager-arm-templates"></a>Azure Resource Manager-mallar (ARM)

Se [dokumentationen för ARM-mallar](../azure-resource-manager/templates/overview.md).

## <a name="autoprotection-for-databases"></a>Autoskydd (för databaser)

(Arbets belastnings bestämd term)

Autoskydd är en funktion som gör att du automatiskt kan skydda alla databaser i en fristående SQL Server instans eller en SQL Server Always on-tillgänglighetsgruppen. Den aktiverar inte bara säkerhets kopieringar för befintliga databaser, men skyddar också alla databaser som du kan lägga till i framtiden.

## <a name="availability-storage-replication-types"></a>Tillgänglighet (typer av lagrings replikering)

Azure Backup erbjuder tre typer av replikering för att hålla lagring och data hög tillgängliga:

### <a name="lrs"></a>LRS

[Lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replikerar dina säkerhets kopierings data tre gånger (det skapas tre kopior av dina säkerhets kopierings data) i en lagrings skalnings enhet i ett Data Center. Alla kopior av säkerhetskopierade data finns inom samma region. LRS är ett billigt alternativ för att skydda dina säkerhets kopierings data från lokala maskin varu haverier.

### <a name="grs"></a>GRS

[Geo-redundant lagring (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) är standardalternativet och det som rekommenderas vid replikering. GRS replikerar dina säkerhetskopierade data till en sekundär region, hundratals mil bort från den primära platsen för data källan. GRS kostar mer än LRS, men GRS ger en högre hållbarhets nivå för dina säkerhets kopierings data, även om det finns ett regionalt avbrott.

>[!NOTE]
>För GRS-valv som har återställnings funktionen över flera regioner, uppgraderas säkerhets kopieringen från GRS till RA-GRS (Läs åtkomst Geo-Redundant lagring).

### <a name="zrs"></a>ZRS

[Zone-redundant lagring (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replikerar dina säkerhets kopierings data i [tillgänglighets zoner](../availability-zones/az-overview.md#availability-zones), vilket garanterar säkerhets kopierings data placering och återhämtnings förmåga i samma region. Så dina kritiska arbets belastningar som kräver [data placering](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) kan säkerhets kopie ras i ZRS.

## <a name="azure-command-line-interface-cli"></a>Kommandoradsgränssnitt för Azure (CLI)

Se [Azure CLI-dokumentationen](/cli/azure/what-is-azure-cli).

## <a name="azure-policy"></a>Azure Policy

Läs [Azure policy-dokumentationen](../governance/policy/overview.md).

## <a name="azure-powershell"></a>Azure PowerShell

Läs [Azure PowerShell-dokumentationen](/powershell/azure/).

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM)

Läs [Azure Resource Manager-dokumentationen](../azure-resource-manager/management/overview.md).

## <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Läs [Azure Disk Encryption-dokumentationen](../security/fundamentals/azure-disk-encryption-vms-vmss.md).

## <a name="backend-storage--cloud-storage--backup-storage"></a>Server dels lagring/moln lagring/lagring för säkerhets kopiering

Den faktiska lagrings plats som används av en säkerhets kopierings instans. Innehåller storleken på alla kvarhållning-platser som finns för säkerhets kopierings instansen (enligt definitionen i säkerhets kopierings-och bevarande principen).

## <a name="bare-metal-backup"></a>Bare Metal-säkerhetskopiering

Säkerhets kopiering av operativsystemfiler och alla data på kritiska volymer, förutom användar data. Efter definition innehåller en Bare Metal-säkerhetskopiering en säkerhets kopia av system tillstånd. Den ger skydd när en dator inte startar och du måste återställa allt. [Läs mer](backup-mabs-system-state-and-bmr.md).

## <a name="backup-extensions--vm-extensions"></a>Säkerhets kopierings tillägg/VM-tillägg

(Gäller för arbets belastning för virtuell Azure-dator)

Azure-tillägg för virtuella datorer är små program som ger konfigurations- och automationsuppgifter på virtuella Azure-datorer efter distribution. Azure Backup säkerhetskopierar virtuella Azure-datorer genom att installera ett tillägg till Azure VM-agenten som körs på datorn. Azure Backup hanterar dessa tillägg automatiskt och användarna behöver inte uppdatera tilläggen manuellt.

## <a name="backup-instance--backup-item"></a>Säkerhets kopierings instans/säkerhets kopierings objekt

En data källa som har säkerhetskopierats till ett valv med en viss säkerhets kopierings-och bevarande princip utgör en säkerhets kopierings instans eller ett säkerhets kopierings objekt.

## <a name="backup-rule--backup-policy"></a>Säkerhets kopierings regel/säkerhets kopierings princip

En säkerhets kopierings regel är en användardefinierad regel som anger när och hur ofta säkerhets kopieringar ska utföras på en data källa. För vissa arbets belastnings typer ger säkerhets kopierings principen också ett sätt att ange vilken ögonblicks bild metod som ska användas på data källan (fullständig, stegvis, differentiell). Säkerhets kopierings principer skapas ofta som en kombination av regler för säkerhets kopiering och kvarhållning.

## <a name="backup-vault"></a>Backup-valv

En Azure Resource Manager resurs av typen *Microsoft. DataProtection/BackupVaults*. För närvarande används säkerhets kopierings valv för att säkerhetskopiera Azure-databaser för PostgreSQL-servern. [Läs mer om säkerhets kopierings valv](backup-azure-recovery-services-vault-overview.md).

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (verksamhets kontinuitet och haveri beredskap)

BCDR omfattar en uppsättning processer som en organisation måste vidta för att säkerställa att appar och arbets belastningar är igång under planerade och oplanerade drift-eller Azure-avbrott, med minimal affärs störning. [Lär dig mer](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) om de olika tjänsterna som Azure erbjuder för att hjälpa dig att skapa en BCDR strategi.

## <a name="churn"></a>Omsättning

Procent andelen av ändringar i data som säkerhets kopie ras mellan två på varandra följande säkerhets kopieringar. Detta kan bero på att nya data har lagts till eller att befintliga data har ändrats eller tagits bort.

## <a name="crash-consistent-backup"></a>Krasch-konsekvent säkerhets kopiering

(Arbets belastnings bestämd term)

Krasch-konsekventa ögonblicks bilder inträffar vanligt vis om en virtuell Azure-dator stängs av vid säkerhets kopieringen. Endast de data som redan finns på disken vid tidpunkten för säkerhets kopieringen samlas in och säkerhets kopie ras. [Läs mer](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="cross-region-restore-crr"></a>Återställning mellan regioner (CRR)

Som en av [återställnings alternativen](backup-azure-arm-restore-vms.md#restore-options)kan du med återställningen mellan regioner (CRR) återställa säkerhets kopierings objekt i en sekundär region, som är en [Azure-kopplad region](../best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="data-box"></a>Data Box

Läs mer i [data Box-dokumentationen](../databox/data-box-overview.md).

## <a name="datasource"></a>Datakälla

En resurs (Azure-resurs, proxy-resurs eller lokal resurs) som är en kandidat som ska säkerhets kopie ras. Till exempel en virtuell Azure-dator eller en Azure-filresurs.

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager)

(Arbets belastnings bestämd term)

Se DPM- [dokumentationen](/system-center/dpm/dpm-overview).

## <a name="expressroute"></a>ExpressRoute

Se ExpressRoute- [dokumentationen](../expressroute/expressroute-introduction.md).

## <a name="file-system-consistent-backup"></a>Konsekvent säkerhets kopiering av fil system

(Arbets belastnings bestämd term)

Konsekventa säkerhets kopieringar i fil systemet ger konsekvens genom att ta en ögonblicks bild av alla filer på samma gång. [Läs mer](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="frontend-storage--source-size"></a>Lagring/käll storlek för klient del

Storleken på de data som ska säkerhets kopie ras för en data källa. Klient delens storlek för en data källa avgör antalet [skyddade instanser](#protected-instance) .

## <a name="full-backup"></a>Fullständig säkerhetskopia

I fullständiga säkerhets kopieringar lagras en kopia av hela data källan för varje säkerhets kopiering.

## <a name="gfs-backup-policy"></a>GFS säkerhets kopierings princip

En säkerhets kopierings princip för GFS (farfar-fader-son) är en som gör att du kan definiera vecko Visa, månatliga och årliga säkerhets kopierings scheman utöver det dagliga säkerhets kopierings schemat. De veckovis säkerhets kopieringarna är kända "Sons", de månatliga säkerhets kopiorna kallas "far" och de årliga säkerhets kopiorna kallas "grandfathers". Var och en av de här uppsättningarna med säkerhets kopior kan konfigureras för att behållas under olika tids perioder, vilket ger en mer anpassning av alternativ för kvarhållning av säkerhets kopior. GFS-principer är användbara för att bevara säkerhets kopior under en lång tids period på ett mer lagrings effektivt sätt.

## <a name="iaas-vms--azure-vms"></a>Virtuella IaaS-datorer/virtuella Azure-datorer

Läs dokumentationen om [Azure VM](../virtual-machines/index.yml).

## <a name="incremental-backup"></a>Inkrementell säkerhetskopiering

Stegvisa säkerhets kopior lagrar bara de block som har ändrats sedan den tidigare säkerhets kopieringen.

## <a name="instant-restore"></a>Omedelbar återställning

(Villkor för arbets belastning) Omedelbar återställning innebär att återställa en dator direkt från säkerhets kopian i stället för från kopian av ögonblicks bilden i valvet. Direkt återställning är snabbare än att återställas från ett valv. Antalet tillgängliga återställnings punkter beror på Retentions tiden som kon figurer ATS för ögonblicks bilder. Gäller för närvarande endast för virtuell Azure-säkerhetskopiering.

## <a name="iops"></a>IOPS

In-/utdata-åtgärder per sekund.

## <a name="item-level-restore"></a>Återställning på objekt nivå

(Arbets belastnings bestämd term)

Återställa enskilda filer eller mappar inuti datorn från återställnings punkten.

## <a name="job"></a>Jobb

En säkerhets kopierings åtgärd som skapats av en användare eller tjänsten Azure Backup. Jobb kan antingen vara schemalagda eller på begäran (ad hoc). Det finns olika typer av jobb – säkerhets kopiering, återställning, konfigurations skydd och så vidare. [Läs mer om jobb](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

## <a name="mabs--azure-backup-server"></a>MABS/Azure Backup Server

(Arbets belastnings bestämd term)

Med Azure Backup Server kan du skydda program arbets belastningar, till exempel virtuella Hyper-V-datorer, Microsoft SQL Server, SharePoint Server, Microsoft Exchange och Windows-klienter från en enda konsol. Den ärver mycket av funktionerna för säkerhets kopiering av arbets belastningar från DPM, men med några få skillnader. [Läs mer](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>Hanterade diskar

Läs mer i [dokumentationen för Managed disks](../virtual-machines/managed-disks-overview.md).

## <a name="mars-agent"></a>MARS-agent

(Arbets belastnings bestämd term)

Som även kallas **Azure Backup Agent** eller **Recovery Services agent** används mars-agenten av Azure Backup för att säkerhetskopiera data från lokala datorer och virtuella Azure-datorer till ett säkerhets kopierings Recovery Services valv i Azure. [Läs mer](backup-support-matrix-mars-agent.md).

## <a name="nsg-network-security-group"></a>NSG (nätverks säkerhets grupp)

Se NSG- [dokumentationen](../virtual-network/network-security-groups-overview.md).

## <a name="offline-seeding"></a>Seeding offline

Offline-seeding avser processen att överföra den inledande (fullständig) säkerhets kopieringen offline, utan att nätverks bandbredden används. Den innehåller en mekanism för att kopiera säkerhets kopierings data till fysiska lagrings enheter som sedan levereras till ett närliggande Azure-datacenter och laddas upp till ett Recovery Services-valv. [Läs mer](offline-backup-overview.md).

## <a name="on-demand-backup--ad-hoc-backup"></a>Säkerhets kopiering på begäran/ad hoc-säkerhetskopiering

Ett säkerhets kopierings jobb som utlöses av en användare vid ett engångs behov och som inte baseras på schemat för säkerhets kopiering (princip) som har kon figurer ATS för resursen.

## <a name="original-location-recovery-olr"></a>Återställning av ursprunglig plats (OLR)

En återställning görs från återställnings punkten till käll platsen varifrån säkerhets kopieringarna gjordes, vilket ersätter det med det tillstånd som lagras i återställnings punkten. När du använder Azure VM backup innebär detta att återställa den virtuella datorn till den ursprungliga servern där säkerhets kopiorna gjordes. När du använder Azure-filresurs-säkerhetskopiering kan detta innebära att återställa data till den säkerhetskopierade fil resursen.

## <a name="passphrase"></a>Fraser

(Arbets belastnings bestämd term)

En lösen fras används för att kryptera och dekryptera data vid säkerhets kopiering eller återställning av den lokala eller lokala datorn med hjälp av MARS-agenten till eller från Azure.

## <a name="private-endpoint"></a>Privat slutpunkt

Läs dokumentationen om den [privata slut punkten](../private-link/private-endpoint-overview.md).

## <a name="protected-instance"></a>Skyddad instans

En skyddad instans avser den dator, fysiska eller virtuella server som du använder för att konfigurera säkerhets kopieringen till Azure.  Från en **fakturerings synpunkt** är antalet skyddade instanser för en dator en funktion i dess klient del storlek. Därför kan en enda säkerhets kopierings instans (till exempel en virtuell dator som säkerhets kopie ras till Azure) motsvara flera skyddade instanser, beroende på dess klient storlek. [Läs mer](https://azure.microsoft.com/pricing/details/backup/).

## <a name="rbac-role-based-access-control"></a>RBAC (rollbaserad åtkomst kontroll)

Se RBAC- [dokumentationen](../role-based-access-control/overview.md).

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>Återställnings punkt/återställnings punkt/kvarhållning/punkt-i-tid (PIT)

En kopia av de ursprungliga data som säkerhets kopie ras. En kvarhållningsperiod är associerad med en tidsstämpel så att du kan använda den för att återställa ett objekt till en viss tidpunkt.

## <a name="recovery-services-vault"></a>Recovery Services-valv

En Azure Resource Manager resurs av typen *Microsoft. RecoveryServices/valv*. Recovery Services valv används för närvarande för att säkerhetskopiera följande arbets belastningar: virtuella Azure-datorer, SQL i virtuella Azure-datorer, SAP HANA i virtuella Azure-datorer och Azure-filresurser. Den används också för att säkerhetskopiera lokala arbets belastningar med MARS, Azure Backup Server (MABS) och System Center DPM. [Läs mer om Recovery Services-valv](backup-azure-recovery-services-vault-overview.md).

## <a name="resource-group"></a>Resursgrupp

Läs Azure Resource Manager- [dokumentationen](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

## <a name="rest-api"></a>REST-API

Läs mer i [Azure REST API-dokumentationen](/rest/api/azure/).

## <a name="retention-rule"></a>Bevarande regel

En användardefinierad regel som anger hur länge säkerhets kopior ska behållas.

## <a name="rpo-recovery-point-objective"></a>Återställnings punkt mål

Återställa anger den maximala data förlust som är möjlig i ett data förlust scenario. Detta avgörs av säkerhets kopierings frekvensen.

## <a name="rto-recovery-time-objective"></a>RTO (mål för återställnings tid)

RTO anger den maximala tid i vilken data kan återställas till den senaste tillgängliga tidpunkten efter ett data förlust scenario.

## <a name="scheduled-backup"></a>Schemalagd säkerhets kopiering

Ett säkerhets kopierings jobb som aktive ras automatiskt av den säkerhets kopierings princip som kon figurer ATS för det angivna objektet.

## <a name="secondary-region--paired-region"></a>Sekundär region/kopplad region

Ett regionalt par består av två regioner i samma geografi. En är den primära regionen och den andra är den sekundära regionen. Kopplade regioner används av vissa Azure-tjänster (inklusive Azure Backup med GRS-inställningar) för att säkerställa affärs kontinuitet och skydda mot data förlust. [Läs mer](../best-practices-availability-paired-regions.md).

## <a name="soft-delete"></a>Mjuk borttagning

Mjuk borttagning är en funktion som hjälper till att skydda mot oavsiktlig borttagning av säkerhets kopierings data. Med mjuk borttagning, även om en skadlig aktör tar bort en säkerhets kopia (eller säkerhets kopierings data tas bort av misstag), behålls säkerhets kopierings data under ytterligare en tids period, vilket innebär att det inte går att återställa säkerhets kopierings objekt utan data förlust. [Läs mer](backup-azure-security-feature-cloud.md).

## <a name="snapshot"></a>Ögonblicksbild

En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk (VHD) eller en Azure-filresurs. Läs mer om [ögonblicks bilder av diskar](../virtual-machines/windows/snapshot-copy-managed-disk.md) och [ögonblicks bilder av filer](../storage/files/storage-snapshots-files.md).

## <a name="storage-account"></a>Lagringskonto

Läs dokumentationen för [lagrings kontot](../storage/common/storage-account-overview.md).

## <a name="subscription"></a>Prenumeration

En Azure-prenumeration är en logisk container som används för att etablera resurser i Azure. Den innehåller information om alla resurser, t. ex. virtuella datorer (VM), databaser med mera.

## <a name="system-state-backup"></a>Säkerhets kopiering av system tillstånd

(Arbets belastnings bestämd term)

Säkerhetskopierar filer för operativ systemet. Med den här säkerhets kopian kan du återställa när en dator startar, men systemfiler och registret går förlorade. [Läs mer](backup-mabs-system-state-and-bmr.md).

## <a name="tenant"></a>Klientorganisation

En klientorganisation är en representation av en organisation. Det är en dedikerad instans av Azure AD som en organisation eller apputvecklare får när organisationen eller apputvecklaren skapar en relation med Microsoft, t.ex. genom att registrera sig för Azure, Microsoft Intune eller Microsoft 365.

## <a name="tier"></a>Nivå

Azure Backup stöder för närvarande följande lagrings nivåer för säkerhets kopiering:

### <a name="snapshot-tier"></a>Ögonblicks bilds nivå

(Villkor för arbets belastning) I den första fasen av VM-säkerhetskopieringen lagras den ögonblicks bild som togs tillsammans med disken. Den här typen av lagring kallas ögonblicks bild nivå. Återställningar av ögonblicks bilders nivåer är snabbare (än att återställa från ett valv) eftersom de eliminerar vänte tiden för ögonblicks bilder som ska kopieras till från valvet innan återställnings åtgärden utlöses.

### <a name="vault-standard-tier"></a>Vault-Standard nivå

Säkerhets kopierings data för alla arbets belastningar som stöds av Azure Backup lagras i valv som innehåller säkerhets kopierings lagring, en uppsättning med automatisk skalning av lagrings konton som hanteras av Azure Backup. Vault-Standard nivån är en lagrings nivå online som gör att du kan lagra en isolerad kopia av säkerhets kopierings data i en Microsoft-hanterad klient, vilket skapar ett extra skydds lager. För arbets belastningar där ögonblicks bilds nivån stöds finns det en kopia av säkerhets kopierings data både på ögonblicks bilds nivån och på nivån valvet standard. Valvet – standard nivån säkerställer att säkerhets kopierings data är tillgängliga även om data källan som säkerhets kopie ras tas bort eller komprometteras.

## <a name="unmanaged-disk"></a>Ohanterad disk

Läs mer i [dokumentationen för ohanterade diskar](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks).

## <a name="vault"></a>Valv

En lagrings enhet i Azure som lagrar säkerhetskopierade data. Det är också en enhet av RBAC och fakturering. Det finns för närvarande två typer av valv – Recovery Services valv och säkerhets kopierings valv.

## <a name="vault-credentials"></a>Autentiseringsuppgifter för valv

Valv filen med autentiseringsuppgifter är ett certifikat som genereras av portalen för varje valv. Detta används när du registrerar en lokal server på valvet. [Läs mer](backup-azure-dpm-introduction.md).

## <a name="vnet-virtual-network"></a>VNET (Virtual Network)

Se VNet- [dokumentationen](../virtual-network/virtual-networks-overview.md).

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Windows tjänsten Volume Shadow Copy)

Läs dokumentationen för [VSS](/windows-server/storage/file-server/volume-shadow-copy-service).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Backup](backup-overview.md)
- [Azure Backup arkitektur och komponenter](backup-architecture.md)