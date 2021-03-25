---
title: Vanliga frågor och svar om säkerhets kopiering av Azure-diskar
description: Få svar på vanliga frågor om säkerhets kopiering av Azure-diskar
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 7729bc1120fc0e2f4361739a8e05f3a82ccb4268
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107320"
---
# <a name="frequently-asked-questions-about-azure-disk-backup"></a>Vanliga frågor och svar om säkerhets kopiering av Azure-diskar

I den här artikeln får du svar på vanliga frågor om säkerhets kopiering av Azure-diskar. Mer information om tillgänglighet för [Azure disk backup](disk-backup-overview.md) , stödda scenarier och begränsningar finns i [support mat ris](disk-backup-support-matrix.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>Kan jag säkerhetskopiera disken med hjälp av Azure disk backup-lösningen om samma disk säkerhets kopie ras med säkerhets kopiering av virtuella Azure-datorer?

Azure Backup erbjuder stöd sida vid sida för säkerhets kopiering av hanterade diskar med disk säkerhets kopiering och lösningar för [säkerhets kopiering av virtuella Azure-datorer](backup-azure-vms-introduction.md) . Detta är användbart när du behöver en gång – en-dagars program konsekvent säkerhets kopiering av virtuella datorer och även oftare säkerhets kopiering av OS-disken eller en specifik data disk, som är kraschat utan att påverka produktions programmets prestanda.

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>Hur gör jag för att hittar du den resurs grupp för ögonblicks bilder som jag använde för att konfigurera säkerhets kopiering för en disk?

På skärmen **säkerhets kopierings instans** hittar du fältet resurs grupp för ögonblicks bild i avsnittet **Essentials** . Du kan söka efter och välja din säkerhets kopierings instans av motsvarande disk från backup Center eller säkerhets kopierings valvet.

![Fält för resurs grupp för ögonblicks bild](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>Vad är en resurs grupp för ögonblicks bilder?

Säkerhets kopiering av Azure-disk erbjuder säkerhets kopiering på verksamhets nivå för hanterad disk. Det vill säga att ögonblicks bilderna som skapas under schemalagda säkerhets kopierings-och säkerhets kopierings åtgärder lagras i en resurs grupp i din prenumeration. Azure Backup erbjuder omedelbar återställning eftersom de stegvisa ögonblicks bilderna lagras i din prenumeration. Den här resurs gruppen kallas för resurs gruppen för ögonblicks bilder. Mer information finns i [Konfigurera säkerhets kopiering](backup-managed-disks.md#configure-backup).

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>Varför måste resurs gruppen för ögonblicks bilder finnas i samma prenumeration som den disk som säkerhets kopie ras?

Du kan inte skapa en stegvis ögonblicks bild för en viss disk utanför diskens prenumeration. Välj därför resurs gruppen i samma prenumeration som den disk som ska säkerhets kopie ras. Läs mer om [stegvis ögonblicks bild](../virtual-machines/disks-incremental-snapshots.md#restrictions) för hanterade diskar.

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>Varför måste jag ange roll tilldelningar för att kunna konfigurera säkerhets kopieringar, utföra schemalagda säkerhets kopieringar och återställnings åtgärder på begäran?

Azure disk backup använder den minsta behörighets metoden för att identifiera, skydda och återställa de hanterade diskarna i dina prenumerationer. För att uppnå detta använder Azure Backup den hanterade identiteten för [säkerhets kopierings valvet](backup-vault-overview.md) för att få åtkomst till andra Azure-resurser. En systemtilldelad hanterad identitet är begränsad till en per resurs och är knuten till den här resursens livs cykel. Du kan bevilja behörighet till den hanterade identiteten med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Hanterad identitet är ett tjänst objekt av en särskild typ som bara kan användas med Azure-resurser. Läs mer om [hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md). Som standard har säkerhets kopierings valvet inte behörighet att komma åt disken som ska säkerhets kopie ras, skapa regelbundna ögonblicks bilder, ta bort ögonblicks bilder efter kvarhållningsperioden och återställa en disk från en säkerhets kopia. Genom att uttryckligen bevilja roll tilldelningar till säkerhets kopierings valvets hanterade identitet har du kontroll över att hantera behörigheter till resurserna i prenumerationerna.

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>Varför begränsar säkerhets kopierings principen lagrings tiden?

Azure disk backup använder stegvisa ögonblicks bilder, som är begränsade till 200 ögonblicks bilder per disk. För att du ska kunna ta säkerhets kopior på begäran från schemalagda säkerhets kopieringar begränsar säkerhets kopierings principen den totala säkerhets kopian till 180. Läs mer om [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md#restrictions) för hanterade diskar.

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>Hur fungerar frekvensen för varje timme och daglig säkerhets kopiering i säkerhets kopierings principen?

Azure disk Backup erbjuder flera säkerhets kopior per dag. Om du behöver mer frekventa säkerhets kopieringar väljer du säkerhets kopierings frekvens **per timme** . Säkerhets kopiorna schemaläggs utifrån det **tidsintervall** som valts. Om du till exempel väljer **var fjärde timme** så tas säkerhets kopiorna ungefär var fjärde timme, så att säkerhets kopiorna distribueras jämnt över dagen. Om det räcker tillräckligt med säkerhets kopiering av en dag väljer du den **dagliga** säkerhets kopierings frekvensen. I frekvensen för daglig säkerhets kopiering kan du ange efter hur lång tid säkerhets kopieringen ska ske. Det är viktigt att notera att tiden på dagen anger start tiden för säkerhets kopieringen och inte tidpunkten då säkerhets kopieringen slutförts. Tiden som krävs för att slutföra säkerhets kopierings åtgärden beror på olika faktorer, inklusive omsättnings takten mellan på varandra följande säkerhets kopieringar. Säkerhets kopiering av Azure-diskar är dock en agent utan agent som använder [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md) som inte påverkar produktions programmets prestanda.

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>Varför gäller inte säkerhets kopierings valvets redundans för säkerhets kopiorna som lagras i drifts nivån (resurs gruppen för ögonblicks bilder)?

Azure Backup använder [stegvisa ögonblicks bilder](../virtual-machines/disks-incremental-snapshots.md#restrictions) av hanterade diskar som endast lagrar förändrings ändringar till diskar sedan den senaste ögonblicks bilden på standard HDD lagring, oavsett den överordnade diskens lagrings typ. För mer tillförlitlighet lagras stegvisa ögonblicks bilder i zoner redundant lagring (ZRS) som standard i regioner som stöder ZRS. För närvarande stöder Azure disk backup drift säkerhets kopior av hanterade diskar som inte kopierar säkerhets kopior till lagring med säkerhets kopierings valv. Därför gäller inte inställningen för säkerhets kopierings lagringen för säkerhets kopierings valvet för återställnings punkterna.

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>Kan jag använda Backup Center för att konfigurera säkerhets kopiering och hantera säkerhets kopierings instanser för Azure-diskar?

Ja, Azure disk backup integreras i [Backup Center](backup-center-overview.md), som ger en **enhetlig hanterings upplevelse** i Azure för företag som styr, övervakar, hanterar och analyserar säkerhets kopior i stor skala. Du kan också använda säkerhets kopierings valvet för att säkerhetskopiera, återställa och hantera de säkerhets kopierings instanser som skyddas i valvet.

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>Varför måste jag skapa ett säkerhets kopierings valv och inte använda ett Recovery Services valv?

Ett säkerhets kopierings valv är en lagrings enhet i Azure som delar säkerhetskopierade data för vissa nyare arbets belastningar som Azure Backup stöder. Du kan använda säkerhets kopierings valv för att lagra säkerhets kopierings data för olika Azure-tjänster, t. ex. Azure Database for PostgreSQL servrar, Azure-diskar och nyare arbets belastningar som Azure Backup kommer att stödja. Säkerhets kopierings valv gör det enkelt att organisera dina säkerhets kopierings data samtidigt som du minimerar hanterings kostnaderna. Se [säkerhets kopierings valv](./backup-vault-overview.md) för mer information.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>Kan disken säkerhets kopie ras och säkerhets kopierings valvet finnas i olika prenumerationer?

Ja, den hanterade disken som ska säkerhets kopie ras och säkerhets kopierings valvet kan finnas i olika prenumerationer.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>Kan disken säkerhets kopie ras och säkerhets kopierings valvet finnas i olika regioner?

Nej, den källa-hanterade disken som ska säkerhets kopie ras och säkerhets kopierings valvet måste finnas i samma region.

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>Kan jag återställa en disk till en annan prenumeration?

Ja, du kan återställa disken till en annan prenumeration än den som den käll-hanterade disk som säkerhets kopian görs från.

### <a name="can-i-back-up-multiple-disks-together"></a>Kan jag säkerhetskopiera flera diskar tillsammans?

Nej, ögonblicks bilder av flera diskar som är kopplade till en virtuell dator stöds inte på tidpunkt. Mer information finns i [Konfigurera säkerhets kopiering](backup-managed-disks.md#configure-backup) och mer information om begränsningar finns i [support mat ris](disk-backup-support-matrix.md).

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>Vilka alternativ kan jag använda för att säkerhetskopiera diskar i flera prenumerationer?

För närvarande kan du använda Azure Portal för att konfigurera säkerhets kopiering av diskar till högst 20 diskar från samma prenumeration.

### <a name="what-is-a-target-resource-group"></a>Vad är en mål resurs grupp?

Under en återställnings åtgärd kan du välja den prenumeration och en resurs grupp där du vill återställa disken till. Azure Backup skapar nya diskar från återställnings punkten i den valda resurs gruppen. Detta kallas för en mål resurs grupp. Observera att säkerhets kopierings valvets hanterade identitet kräver att roll tilldelningen i mål resurs gruppen kan utföra återställnings åtgärden. Mer information finns i dokumentationen för [återställning](restore-managed-disks.md).

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>Vilka behörigheter används av Azure Backup under säkerhets kopierings-och återställnings åtgärden?

Följande är de åtgärder som används i rollen **disk säkerhets kopierings läsare** som tilldelats den **disk** som ska säkerhets kopie ras:

"Microsoft. Compute/disks/Read"

"Microsoft. Compute/disks/beginGetAccess/Action"

Följande är de åtgärder som används i rollen **disk Snapshot Contributor** som tilldelats **resurs gruppen för ögonblicks bilder**:

"Microsoft. Compute/ögonblicks bilder/Delete"

"Microsoft. Compute/snaps hots/Write"

"Microsoft. Compute/snaps hots/Read"

"Microsoft. Storage/storageAccounts/Write"

"Microsoft. Storage/storageAccounts/Read"

"Microsoft. Storage/storageAccounts/Delete"

"Microsoft. Resources/Subscriptions/resourceGroups/Read"

"Microsoft. Storage/storageAccounts/listnycklar/Action"

"Microsoft. Compute/snaps hots/beginGetAccess/Action"

"Microsoft. Compute/snaps hots/endGetAccess/Action"

"Microsoft. Compute/disks/beginGetAccess/Action"

Följande är de åtgärder som används i rollen för **disk återställnings operatorn** som tilldelats **mål resurs gruppen**:

"Microsoft. Compute/disks/Write"

"Microsoft. Compute/disks/Read"

"Microsoft. Resources/Subscriptions/resourceGroups/Read"

>[!NOTE]
>Behörigheterna för dessa roller kan ändras i framtiden, baserat på de funktioner som har lagts till av tjänsten Azure Backup.

## <a name="next-steps"></a>Nästa steg

- [Stödmatris för säkerhetskopiering av Azure-disk](disk-backup-support-matrix.md)