---
title: Återställa virtuella datorer med hjälp av Azure Portal
description: Återställa en virtuell Azure-dator från en återställningspunkt med hjälp Azure Portal, inklusive funktionen Återställning mellan regioner.
ms.reviewer: geg
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: 0f3a715f4fef85b90fd8f06558a8cfdab1ca8900
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739052"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Återställa data för virtuella Azure-datorer i Azure Portal

I den här artikeln beskrivs hur du återställer data från virtuella Azure-datorer från återställningspunkter som [lagras Azure Backup](backup-overview.md) Recovery Services-valv.

## <a name="restore-options"></a>Återställningsalternativ

Azure Backup finns flera sätt att återställa en virtuell dator.

**Återställningsalternativ** | **Information**
--- | ---
**Skapa en ny virtuell dator** | Skapar och kör snabbt igång en grundläggande virtuell dator från en återställningspunkt.<br/><br/> Du kan ange ett namn för den virtuella datorn, välja den resursgrupp och det virtuella nätverk (VNet) som den ska placeras i och ange ett lagringskonto för den återställda virtuella datorn. Den nya virtuella datorn måste skapas i samma region som den virtuella källdatorn.<br><br>Om en återställning av en virtuell dator misslyckas på grund av att en SKU för en virtuell Azure-dator inte var tillgänglig i den angivna regionen i Azure, eller på grund av andra problem, återställer Azure Backup fortfarande diskarna i den angivna resursgruppen.
**Återställ disk** | Återställer en virtuell datordisk, som sedan kan användas för att skapa en ny virtuell dator.<br/><br/> Azure Backup tillhandahåller en mall som hjälper dig att anpassa och skapa en virtuell dator. <br/><br> Återställningsjobbet genererar en mall som du kan ladda ned och använda för att ange anpassade inställningar för virtuella datorer och skapa en virtuell dator.<br/><br/> Diskarna kopieras till den resursgrupp som du anger.<br/><br/> Du kan också ansluta disken till en befintlig virtuell dator eller skapa en ny virtuell dator med hjälp av PowerShell.<br/><br/> Det här alternativet är användbart om du vill anpassa den virtuella datorn, lägga till konfigurationsinställningar som inte fanns vid tidpunkten för säkerhetskopieringen eller lägga till inställningar som måste konfigureras med hjälp av mallen eller PowerShell.
**Ersätt befintlig** | Du kan återställa en disk och använda den för att ersätta en disk på den befintliga virtuella datorn.<br/><br/> Den aktuella virtuella datorn måste finnas. Om den har tagits bort kan det här alternativet inte användas.<br/><br/> Azure Backup tar en ögonblicksbild av den befintliga virtuella datorn innan du ersätter disken och lagrar den på den mellanlagringsplats som du anger. Befintliga diskar som är anslutna till den virtuella datorn ersätts med den valda återställningspunkten.<br/><br/> Ögonblicksbilden kopieras till valvet och bevaras i enlighet med bevarandeprincipen. <br/><br/> Efter diskåtgärden behålls den ursprungliga disken i resursgruppen. Du kan välja att manuellt ta bort de ursprungliga diskarna om de inte behövs. <br/><br/>Ersätt befintlig stöds för okrypterade hanterade virtuella datorer, inklusive virtuella datorer som skapats [med anpassade avbildningar.](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) Det stöds inte för klassiska virtuella datorer, ohanterade virtuella datorer och [generaliserade virtuella datorer.](../virtual-machines/windows/upload-generalized-managed.md)<br/><br/> Om återställningspunkten har fler eller färre diskar än den aktuella virtuella datorn återspeglar antalet diskar i återställningspunkten endast VM-konfigurationen.<br><br> Ersätt befintlig stöds också för virtuella datorer med länkade resurser, till exempel [användar tilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) eller [Key Vault](../key-vault/general/overview.md).
**Mellan regioner (sekundär region)** | Återställning mellan regioner kan användas för att återställa virtuella Azure-datorer i den sekundära regionen, som är en [Länkad Azure-region.](../best-practices-availability-paired-regions.md#what-are-paired-regions)<br><br> Du kan återställa alla virtuella Azure-datorer för den valda återställningspunkten om säkerhetskopieringen görs i den sekundära regionen.<br><br> Under säkerhetskopieringen replikeras inte ögonblicksbilder till den sekundära regionen. Endast de data som lagras i valvet replikeras. Sekundära regionåterställningar är alltså bara [återställningar på](about-azure-vm-restore.md#concepts) valvnivå. Återställningstiden för den sekundära regionen är nästan samma som återställningstiden för valvnivån för den primära regionen.  <br><br> Den här funktionen är tillgänglig för alternativen nedan:<br> <li> [Skapa en virtuell dator](#create-a-vm) <br> <li> [Återställ diskar](#restore-disks) <br><br> Vi stöder för närvarande inte [alternativet Ersätt befintliga](#replace-existing-disks) diskar.<br><br> Behörigheter<br> Återställningsåtgärden i den sekundära regionen kan utföras av säkerhetskopieringsadministratörer och appadministratörer.

> [!NOTE]
> Du kan också återställa specifika filer och mappar på en virtuell Azure-dator. [Läs mer](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Lagringskonton

Lite information om lagringskonton:

- **Skapa virtuell** dator: När du skapar en ny virtuell dator placeras den virtuella datorn i det lagringskonto som du anger.
- **Återställ disk:** När du återställer en disk kopieras disken till det lagringskonto som du anger. Återställningsjobbet genererar en mall som du kan ladda ned och använda för att ange anpassade inställningar för virtuella datorer. Den här mallen placeras i det angivna lagringskontot.
- **Ersätt disk:** När du ersätter en disk på en befintlig virtuell Azure Backup tar en ögonblicksbild av den befintliga virtuella datorn innan du ersätter disken. Ögonblicksbilden kopieras också till Recovery Services-valvet via dataöverföring som en bakgrundsprocess. När ögonblicksbildsfasen har slutförts utlöses dock åtgärden för att ersätta diskar. Efter bytet av disk lämnas diskarna för den virtuella Azure-källdatorn kvar i den angivna resursgruppen för åtgärden och de virtuella hårddiskarna lagras på det angivna lagringskontot. Du kan välja att ta bort eller behålla dessa virtuella hårddiskar och diskar.
- **Lagringskontoplats:** Lagringskontot måste finnas i samma region som valvet. Endast dessa konton visas. Om det inte finns några lagringskonton på platsen måste du skapa ett.
- **Lagringstyp:** Blob Storage stöds inte.
- **Lagringsredundans:** Zonredundant lagring (ZRS) stöds inte. Replikerings- och redundansinformationen för kontot visas inom parentes efter kontonamnet.
- **Premium Storage:**
  - När du återställer virtuella datorer som inte är Premium stöds inte Premium Storage-konton.
  - När du återställer hanterade virtuella datorer stöds inte premiumlagringskonton som konfigurerats med nätverksregler.

## <a name="before-you-start"></a>Innan du börjar

Om du vill återställa en virtuell dator (skapa en ny virtuell [dator)](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) kontrollerar du att du har rätt behörigheter för rollbaserad åtkomstkontroll i Azure (Azure RBAC) för åtgärden Återställ virtuell dator.

Om du inte har behörighet kan du återställa en [disk](#restore-disks)och när disken [](#use-templates-to-customize-a-restored-vm) har återställts kan du använda mallen som genererades som en del av återställningsåtgärden för att skapa en ny virtuell dator.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="select-a-restore-point"></a>Välj en återställningspunkt

1. I valvet som är associerat med den virtuella dator som du vill återställa väljer du **Säkerhetskopieringsobjekt**  >  **på den virtuella Azure-datorn**.
1. Välj en virtuell dator. Som standard visas återställningspunkter från de senaste 30 dagarna på den virtuella datorns instrumentpanel. Du kan visa återställningspunkter som är äldre än 30 dagar eller filtrera för att hitta återställningspunkter baserat på datum, tidsintervall och olika typer av ögonblicksbildkonsekvens.
1. Om du vill återställa den virtuella datorn väljer du **Återställ virtuell dator.**

    ![Återställningspunkt](./media/backup-azure-arm-restore-vms/restore-point.png)

1. Välj en återställningspunkt som ska användas för återställningen.

## <a name="choose-a-vm-restore-configuration"></a>Välja en konfiguration för återställning av virtuell dator

1. I **Återställ virtuell dator** väljer du ett återställningsalternativ:
    - **Skapa ny:** Använd det här alternativet om du vill skapa en ny virtuell dator. Du kan skapa en virtuell dator med enkla inställningar eller återställa en disk och skapa en anpassad virtuell dator.
    - **Ersätt befintlig:** Använd det här alternativet om du vill ersätta diskar på en befintlig virtuell dator.

        ![Guiden Återställ konfiguration av virtuell dator](./media/backup-azure-arm-restore-vms/restore-configuration.png)

1. Ange inställningar för det valda återställningsalternativet.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Som ett av [återställningsalternativen](#restore-options)kan du snabbt skapa en virtuell dator med grundläggande inställningar från en återställningspunkt.

1. I **Återställ virtuell dator** Skapa ny  >    >  **återställningstyp** väljer du **Skapa en virtuell dator.**
1. I **Namn på virtuell dator** anger du en virtuell dator som inte finns i prenumerationen.
1. I **Resursgrupp** väljer du en befintlig resursgrupp för den nya virtuella datorn eller skapar en ny med ett globalt unikt namn. Om du tilldelar ett namn som redan finns tilldelar Azure gruppen samma namn som den virtuella datorn.
1. I **Virtuellt** nätverk väljer du det VNet där den virtuella datorn ska placeras. Alla virtuella nätverk som är associerade med prenumerationen visas. Välj undernätet. Det första undernätet är valt som standard.
1. I **Mellanlagringsplats** anger du lagringskontot för den virtuella datorn. [Läs mer](#storage-accounts).

    ![Guiden Återställ konfiguration – välj återställningsalternativ](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

1. Välj **Återställ för** att utlösa återställningsåtgärden.

## <a name="restore-disks"></a>Återställa diskar

Som ett av [återställningsalternativen](#restore-options)kan du skapa en disk från en återställningspunkt. Sedan kan du utföra någon av följande åtgärder med disken:

- Använd mallen som genereras under återställningen för att anpassa inställningar och utlösa distribution av virtuella datorer. Du redigerar standardmallinställningarna och skickar mallen för vm-distribution.
- [Koppla återställda diskar till](../virtual-machines/windows/attach-managed-disk-portal.md) en befintlig virtuell dator.
- [Skapa en ny virtuell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) dator från de återställda diskarna med PowerShell.

1. I **Återställ konfiguration** Skapa  >  **ny**  >  **återställningstyp** väljer du Återställ **diskar.**
1. I **Resursgrupp** väljer du en befintlig resursgrupp för de återställda diskarna eller skapar en ny med ett globalt unikt namn.
1. I **Mellanlagringsplats** anger du det lagringskonto som de virtuella hårddiskarna ska kopieras till. [Läs mer](#storage-accounts).

    ![Välj Resursgrupp och Mellanlagringsplats](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Välj **Återställ för** att utlösa återställningsåtgärden.

När den virtuella datorn använder hanterade  diskar och du väljer alternativet Skapa virtuell dator Azure Backup inte det angivna lagringskontot. När det gäller **Restore disks (Återställ** **diskar)** och Instant Restore (Omedelbar återställning) används lagringskontot endast för att lagra mallen. Hanterade diskar skapas i den angivna resursgruppen.
När den virtuella datorn använder ohanterade diskar återställs de som blobar till lagringskontot.

### <a name="use-templates-to-customize-a-restored-vm"></a>Använda mallar för att anpassa en återställd virtuell dator

När disken har återställts använder du mallen som genererades som en del av återställningsåtgärden för att anpassa och skapa en ny virtuell dator:

1. I **Säkerhetskopieringsjobb** väljer du det relevanta återställningsjobbet.

1. I **Återställ väljer** du Distribuera mall för **att** initiera malldistributionen.

    ![Öka detaljgranskningen för återställningsjobb](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

1. Om du vill anpassa VM-inställningen som anges i mallen väljer du **Redigera mall.** Om du vill lägga till fler anpassningar väljer du **Redigera parametrar.**
    - [Läs mer](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) om att distribuera resurser från en anpassad mall.
    - [Läs mer](../azure-resource-manager/templates/template-syntax.md) om redigeringsmallar.

   ![Läs in malldistribution](./media/backup-azure-arm-restore-vms/edit-template1.png)

1. Ange anpassade värden för den virtuella datorn, godkänn **villkoren och** välj **Köp.**

   ![Skicka malldistribution](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Ersätta befintliga diskar

Som ett av [återställningsalternativen](#restore-options)kan du ersätta en befintlig virtuell datordisk med den valda återställningspunkten. [Granska](#restore-options) alla återställningsalternativ.

1. I **Återställ konfiguration** väljer du Ersätt **befintlig**.
1. I **Återställningstyp** väljer du **Ersätt disk/s**. Det här är återställningspunkten som ska användas för att ersätta befintliga virtuella datordiskar.
1. I **Mellanlagringsplats** anger du var ögonblicksbilder av de aktuella hanterade diskarna ska sparas under återställningsprocessen. [Läs mer](#storage-accounts).

   ![Guiden Återställ konfiguration ersätt befintlig](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Återställning mellan regioner

Som ett av [återställningsalternativen](#restore-options)gör CRR (Cross Region Restore) att du kan återställa virtuella Azure-datorer i en sekundär region, som är en länkad Azure-region.

Om du vill börja använda funktionen läser du [avsnittet Innan du börjar.](./backup-create-rs-vault.md#set-cross-region-restore)

Om du vill se om CRR är aktiverat följer du anvisningarna i [Konfigurera återställning mellan regioner.](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Visa säkerhetskopieringsobjekt i en sekundär region

Om CRR är aktiverat kan du visa säkerhetskopieringsobjekten i den sekundära regionen.

1. Från portalen går du till Recovery **Services-valvSäkerhetsobjekt**  >  .
1. Välj **Sekundär region** för att visa objekten i den sekundära regionen.

>[!NOTE]
>Endast Säkerhetskopieringshanteringstyper som stöder CRR-funktionen visas i listan. För närvarande tillåts endast stöd för att återställa sekundära regiondata till en sekundär region.<br></br>CRR för virtuella Azure-datorer stöds för azure-hanterade virtuella datorer (inklusive krypterade virtuella Azure-datorer).

![Virtuella datorer i sekundär region](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Välj sekundär region](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Återställa i sekundär region

Användarupplevelsen för återställning av sekundär region liknar användarupplevelsen för återställning i den primära regionen. När du konfigurerar information i fönstret Återställ konfiguration för att konfigurera återställningen uppmanas du att endast ange parametrar för sekundär region.

För närvarande är [RPO](azure-backup-glossary.md#rpo-recovery-point-objective) för sekundär region upp till 12 timmar från den primära regionen, även om [RA-GRS-replikeringen (Read-Access Geo Redundant Storage)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) är 15 minuter.

![Välj den virtuella dator som ska återställas](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Välj återställningspunkt](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Återställ konfiguration](./media/backup-azure-arm-restore-vms/rest-config.png)

![Meddelande om pågående återställning av utlösare](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Information om hur du återställer och skapar en virtuell dator finns i [Skapa en virtuell dator.](#create-a-vm)
- Information om hur du återställer som en disk finns [i Återställa diskar](#restore-disks).

>[!NOTE]
>
>- När återställningen har utlösts och i dataöverföringsfasen kan inte återställningsjobbet avbrytas.
>- Funktionen Återställning mellan regioner återställer CMK (kund hanterade nycklar) aktiverade virtuella Azure-datorer, som inte säkerhetskopieras i ett CMK-aktiverat Recovery Services-valv som icke-CMK-aktiverade virtuella datorer i den sekundära regionen.
>- De Azure-roller som behövs för att återställa i den sekundära regionen är desamma som de i den primära regionen.
>- När du återställer en virtuell Azure-Azure Backup konfigurerar automatiskt inställningarna för virtuella nätverk i den sekundära regionen. Om du återställer [diskar när du](#restore-disks) distribuerar mallen måste du ange de virtuella nätverksinställningar som motsvarar den sekundära regionen.

[Fästa virtuella datorer i Azure-zonen](../virtual-machines/windows/create-portal-availability-zone.md) kan återställas i alla [tillgänglighetszoner](../availability-zones/az-overview.md) i samma region.

Under återställningsprocessen visas alternativet **Tillgänglighetszon.** Du ser din standardzon först. Om du vill välja en annan zon väljer du den zonnummer du vill använda. Om den fästa zonen inte är tillgänglig kan du inte återställa data till en annan zon eftersom säkerhetskopierade data inte replikeras zonindelade. Återställningen i tillgänglighetszoner är endast möjlig från återställningspunkter på valvnivå.

![Välj tillgänglighetszon](./media/backup-azure-arm-restore-vms/cross-zonal-restore.png)

### <a name="monitoring-secondary-region-restore-jobs"></a>Övervaka återställningsjobb för sekundär region

1. Från portalen går du till **Säkerhetskopieringsjobb för Recovery**  >  **Services-valv**
1. Välj **Sekundär region** för att visa objekten i den sekundära regionen.

    ![Säkerhetskopieringsjobb filtrerade](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restoring-unmanaged-vms-and-disks-as-managed"></a>Återställa ohanterade virtuella datorer och diskar som hanterade

Du får ett alternativ för att återställa [ohanterade diskar som](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks) [hanterade diskar under](../virtual-machines/managed-disks-overview.md) återställningen. Som standard återställs ohanterade virtuella datorer/diskar som ohanterade virtuella datorer/diskar. Men om du väljer att återställa som hanterade virtuella datorer/diskar är det nu möjligt att göra det. Dessa återställningar utlöses inte från ögonblicksbildfasen utan endast från valvfasen. Den här funktionen är inte tillgänglig för ohanterade krypterade virtuella datorer.

![Återställa som hanterade diskar](./media/backup-azure-arm-restore-vms/restore-as-managed-disks.png)

## <a name="restore-vms-with-special-configurations"></a>Återställa virtuella datorer med särskilda konfigurationer

Det finns många vanliga scenarier där du kan behöva återställa virtuella datorer.

**Scenario** | **Vägledning**
--- | ---
**Återställa virtuella datorer med Hybrid Use Benefit** | Om en virtuell Windows-dator använder licensiering för [Hybrid Use Benefit (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md)återställer du  diskarna och skapar en ny virtuell dator med hjälp av den angivna mallen (med licenstypen inställd **på Windows_Server**), eller PowerShell.  Den här inställningen kan också tillämpas när du har skapat den virtuella datorn.
**Återställa virtuella datorer vid ett haveri i Ett Azure-datacenter** | Om valvet använder GRS och det primära datacentret för den virtuella datorn går Azure Backup stöd för återställning av säkerhetskopierade virtuella datorer till det parkopplade datacentret. Du väljer ett lagringskonto i det parkopplade datacentret och återställer som vanligt. Azure Backup använder beräkningstjänsten i den parkopplade regionen för att skapa den återställda virtuella datorn. [Läs mer](/azure/architecture/resiliency/recovery-loss-azure-region) om datacenteråter återhämtning.<br><br> Om valvet använder GRS kan du välja den nya funktionen Återställning [mellan regioner.](#cross-region-restore) På så sätt kan du återställa till en andra region i antingen fullständiga eller partiella avbrottsscenarier, eller även om det inte finns något avbrott alls.
**Bare Metal-återställning** | Den största skillnaden mellan virtuella Azure-datorer och lokala hypervisorer är att det inte finns någon VM-konsol tillgänglig i Azure. En konsol krävs för vissa scenarier, till exempel återställning med hjälp av en BMR-typ (Bare Metal Recovery) säkerhetskopiering. Återställning av virtuella datorer från valvet är dock en fullständig ersättning för BMR.
**Återställa virtuella datorer med särskilda nätverkskonfigurationer** | Särskilda nätverkskonfigurationer omfattar virtuella datorer som använder intern eller extern belastningsutjämning, använder flera nätverkskort eller flera reserverade IP-adresser. Du återställer de här virtuella datorerna med hjälp av [återställningsdiskalternativet](#restore-disks). Det här alternativet gör en kopia av de virtuella hårddiskarna till det [](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) angivna [](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) lagringskontot och du kan sedan skapa en virtuell dator med en intern eller extern lastbalanserare, [](../virtual-machines/windows/multiple-nics.md)flera nätverkskort eller flera reserverade [IP-adresser](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)i enlighet med din konfiguration.
**Nätverkssäkerhetsgrupp (NSG) på nätverkskort/undernät** | Säkerhetskopiering av virtuella Azure-datorer stöder säkerhetskopiering och återställning av NSG-information på vnet-, undernäts- och NIC-nivå.
**Zon fästa virtuella datorer** | Om du har erat en virtuell Azure-dator som är fäst på en zon (med Azure Backup) kan du återställa den i samma zon där den har fästs. [Läs mer](../availability-zones/az-overview.md)
**Återställa en virtuell dator i en tillgänglighetsuppsättning** | När du återställer en virtuell dator från portalen finns det inget alternativ för att välja en tillgänglighetsuppsättning. En återställd virtuell dator har ingen tillgänglighetsuppsättning. Om du använder alternativet för återställning [](../virtual-machines/windows/tutorial-availability-sets.md) av disk kan du ange en tillgänglighetsuppsättning när du skapar en virtuell dator från disken med hjälp av den angivna mallen eller PowerShell.
**Återställa särskilda virtuella datorer, till exempel virtuella SQL-datorer** | Om du säkerhetskopierar en virtuell SQL-dator med hjälp av säkerhetskopiering av virtuell Azure-dator och sedan använder alternativet för att återställa en virtuell dator eller skapar en virtuell dator efter återställning av diskar, måste den nyligen skapade virtuella datorn registreras med SQL-providern enligt det som anges [här.](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%2cbash) Då konverteras den återställda virtuella datorn till en virtuell SQL-dator.

### <a name="restore-domain-controller-vms"></a>Återställa domänkontrollantens virtuella datorer

**Scenario** | **Vägledning**
--- | ---
**Återställa en enskild virtuell dator med domänkontrollant i en enda domän** | Återställ den virtuella datorn på samma sätt som andra virtuella datorer. Tänk på följande:<br/><br/> Ur ett Active Directory-perspektiv är den virtuella Azure-datorn som vilken annan virtuell dator som helst.<br/><br/> Återställningsläge för katalogtjänster (DSRM) är också tillgängligt, så alla Active Directory-återställningsscenarier är genomförbara. [Läs mer om](#post-restore-steps) överväganden för säkerhetskopiering och återställning för virtualiserade domänkontrollanter.
**Återställa flera virtuella domänkontrollanter i en enda domän** | Om andra domänkontrollanter i samma domän kan nås via nätverket kan domänkontrollanten återställas precis som vilken virtuell dator som helst. Om det är den sista återstående domänkontrollanten i domänen, eller om en återställning i ett isolerat nätverk utförs, använder du en [skogsåterställning.](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)
**Återställa en enskild virtuell domänkontrollant i en konfiguration med flera domäner** |  Återställa diskarna och skapa en virtuell dator med [hjälp av PowerShell](backup-azure-vms-automation.md#restore-the-disks)  
**Återställa flera domäner i en skog** | Vi rekommenderar en [skogsåterställning.](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)

Mer information finns i [Domänkontrollanter för domänkontrollanter i Active Directory.](active-directory-backup-restore.md)

## <a name="track-the-restore-operation"></a>Spåra återställningsåtgärden

När du har utlöst återställningsåtgärden skapar säkerhetskopieringstjänsten ett jobb för spårning. Azure Backup visar meddelanden om jobbet i portalen. Om de inte visas väljer du **meddelandesymbolen** och sedan Fler händelser i aktivitetsloggen för **att** se Status för återställningsprocess.

![Återställning utlöst](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Spåra återställning på följande sätt:

1. Om du vill visa åtgärder för jobbet väljer du hyperlänken meddelanden. Du kan också välja Säkerhetskopieringsjobb **i valvet** och sedan den relevanta virtuella datorn.

    ![Lista över virtuella datorer i ett valv](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

1. Om du vill övervaka återställningsförloppet väljer du ett återställningsjobb med **statusen Pågår.** Detta visar förloppsfältet, som visar information om återställningsförloppet:

    - **Uppskattad tid för återställning:** Ger inledningsvis den tid det tar att slutföra återställningsåtgärden. När åtgärden fortskrider minskar den tid det tar och når noll när återställningen är klar.
    - **Procentandel återställning.** Visar procentandelen återställningsåtgärd som är klar.
    - **Antal byte som överförts:** Om du återställer genom att skapa en ny virtuell dator visas de byte som överfördes mot det totala antalet byte som ska överföras.

## <a name="post-restore-steps"></a>Steg efter återställning

Det finns några saker att notera när du har återställt en virtuell dator:

- Tillägg som finns under säkerhetskopieringskonfigurationen installeras, men är inte aktiverade. Om du ser ett problem installerar du om tilläggen.
- Om den säkerhetskopierade virtuella datorn har en statisk IP-adress har den återställda virtuella datorn en dynamisk IP-adress för att undvika konflikter. Du kan lägga [till en statisk IP-adress till den återställda virtuella datorn](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description).
- En återställd virtuell dator har ingen tillgänglighetsuppsättning. Om du använder alternativet för återställning [](../virtual-machines/windows/tutorial-availability-sets.md) av disk kan du ange en tillgänglighetsuppsättning när du skapar en virtuell dator från disken med hjälp av den angivna mallen eller PowerShell.
- Om du använder en molnbaserad Linux-distribution, till exempel Ubuntu, blockeras lösenordet efter återställningen av säkerhetsskäl. Använd VMAccess-tillägget på den återställda virtuella datorn [för att återställa lösenordet](/troubleshoot/azure/virtual-machines/reset-password). Vi rekommenderar att du använder SSH-nycklar på dessa distributioner, så du behöver inte återställa lösenordet efter återställningen.
- Om du inte kan komma åt en virtuell dator när den har återställts på grund av att den virtuella datorn har en bruten relation med domänkontrollanten följer du stegen nedan för att öppna den virtuella datorn:
  - Koppla OS-disken som en datadisk till en återställd virtuell dator.
  - Installera VM-agenten manuellt om Azure Agent inte svarar genom att följa den här [länken.](/troubleshoot/azure/virtual-machines/install-vm-agent-offline)
  - Aktivera åtkomst till seriekonsolen på den virtuella datorn för att tillåta kommandoradsåtkomst till den virtuella datorn

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - När den virtuella datorn återskapas använder Azure Portal för att återställa det lokala administratörskontot och lösenordet
  - Använd Seriell konsol åtkomst och CMD för att åtse virtuella datorer från domänen

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- När den virtuella datorn är från och har startats om, kommer du att kunna RDP till den virtuella datorn med autentiseringsuppgifter för lokal administratör och återansluta den virtuella datorn till domänen.

## <a name="backing-up-restored-vms"></a>Återställning av återställda virtuella datorer

- Om du har återställt en virtuell dator till samma resursgrupp med samma namn som den ursprungliga säkerhetskopierade virtuella datorn fortsätter säkerhetskopieringen på den virtuella datorn efter återställningen.
- Om du har återställt den virtuella datorn till en annan resursgrupp eller om du har angett ett annat namn för den återställda virtuella datorn måste du konfigurera säkerhetskopiering för den återställda virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Om du får problem under återställningsprocessen kan [du granska](backup-azure-vms-troubleshoot.md#restore) vanliga problem och fel.
- När den virtuella datorn har återställts kan du lära dig mer [om att hantera virtuella datorer](backup-azure-manage-vms.md)