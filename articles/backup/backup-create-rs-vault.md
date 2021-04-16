---
title: Skapa och konfigurera Recovery Services-valv
description: I den här artikeln får du lära dig hur du skapar och konfigurerar Recovery Services-valv som lagrar säkerhetskopior och återställningspunkter. Lär dig hur du använder återställning mellan regioner för att återställa i en sekundär region.
ms.topic: conceptual
ms.date: 04/14/2021
ms.custom: references_regions
ms.openlocfilehash: 5e2983e473fac72d02f0fdbc8c307e96326ac0a6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518583"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Skapa och konfigurera ett Recovery Services-valv

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Ange lagringsredundans

Azure Backup hanterar automatiskt lagringen för valvet. Du måste ange hur lagringen replikeras.

> [!NOTE]
> Du **måste ändra lagringsreplikeringstyp** (lokalt redundant/geo-redundant) för ett Recovery Services-valv innan du konfigurerar säkerhetskopieringar i valvet. När du har konfigurerat säkerhetskopiering inaktiveras alternativet att ändra.
>
>- Om du inte har konfigurerat säkerhetskopieringen än följer du [dessa steg för](#set-storage-redundancy) att granska och ändra inställningarna.
>- Om du redan har konfigurerat säkerhetskopieringen och måste gå från GRS till LRS granskar [du dessa lösningar.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

1. Välj det **nya valvet i** fönstret Recovery Services-valv. Under avsnittet **Inställningar** väljer du **Egenskaper.**
1. I **Egenskaper** går du till **Konfiguration av säkerhetskopiering** och väljer **Uppdatera**.

1. Välj typ av lagringsreplikering och välj **Spara.**

     ![Ange lagringskonfigurationen för det nya valvet](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Vi rekommenderar att du fortsätter att använda standardinställningen **geo-redundant** om du använder Azure som primär lagringsslutpunkt för säkerhetskopiering.
   - Om du inte använder Azure som primär slutpunkt för lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring.
   - Läs mer om [geo-](../storage/common/storage-redundancy.md#geo-redundant-storage) och [lokal redundans.](../storage/common/storage-redundancy.md#locally-redundant-storage)
   - Om du behöver datatillgänglighet utan avbrottstid i en region, garanterar datahemlighet, väljer du [zonredundant lagring.](../storage/common/storage-redundancy.md#zone-redundant-storage)

>[!NOTE]
>Inställningarna för lagringsreplikering för valvet är inte relevanta för säkerhetskopiering av Azure-filresurs eftersom den aktuella lösningen är ögonblicksbildbaserad och inga data överförs till valvet. Ögonblicksbilder lagras i samma lagringskonto som den säkerhetskopierade filresursen.

## <a name="set-cross-region-restore"></a>Ange återställning mellan regioner

Med **återställningsalternativet Återställning mellan regioner (CRR)** kan du återställa data i en sekundär, [länkad Azure-region.](../best-practices-availability-paired-regions.md)

Den stöder följande datakällor:

- Virtuella Azure-datorer (allmän tillgänglighet)
- SQL-databaser som finns på virtuella Azure-datorer (förhandsversion)
- SAP HANA databaser som finns på virtuella Azure-datorer (förhandsversion)

Med hjälp av återställning mellan regioner kan du:

- utföra granskningar när det finns ett gransknings- eller efterlevnadskrav
- återställa data vid ett haveri i den primära regionen

När du återställer en virtuell dator kan du återställa den virtuella datorn eller dess disk. Om du återställer från SQL/SAP HANA som finns på virtuella Azure-datorer kan du återställa databaser eller deras filer.

Välj den här funktionen genom att **välja Aktivera återställning mellan regioner** i fönstret Konfiguration av **säkerhetskopiering.**

Eftersom den här processen är på lagringsnivå [påverkas prissättningen av](https://azure.microsoft.com/pricing/details/backup/).

>[!NOTE]
>Innan du börjar:
>
>- Granska [supportmatrisen](backup-support-matrix.md#cross-region-restore) för en lista över hanterade typer och regioner som stöds.
>- Crr-funktionen (Cross Region Restore) för virtuella Azure-datorer är nu allmänt tillgänglig i alla offentliga Azure-regioner.
>- Återställning mellan regioner för SQL- och SAP HANA databaser finns i förhandsversion i alla offentliga Azure-regioner.
>- CRR är en funktion för val av val av GRS-valv på valvnivå (inaktiverad som standard).
>- När du har valt att göra det kan det ta upp till 48 timmar innan säkerhetskopieringsobjekten är tillgängliga i sekundära regioner.
>- CRR för virtuella Azure-datorer stöds för närvarande för Azure Resource Manager virtuella Azure-datorer och krypterade virtuella Azure-datorer. Klassiska virtuella Azure-datorer stöds inte. När ytterligare hanteringstyper stöder CRR registreras **de** automatiskt.
>- Återställning mellan **regioner kan för närvarande inte återställas till** GRS eller LRS när skyddet har initierats för första gången.
>- För närvarande är [RPO](azure-backup-glossary.md#rpo-recovery-point-objective) för sekundär region upp till 12 timmar från den primära regionen, även om [RA-GRS-replikeringen (Read-Access Geo Redundant Storage)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) är 15 minuter.

### <a name="configure-cross-region-restore"></a>Konfigurera återställning mellan regioner

Ett valv som skapats med GRS-redundans innehåller alternativet för att konfigurera återställningsfunktionen mellan regioner. Varje GRS-valv har en banderoll som länkar till dokumentationen. Om du vill konfigurera CRR för valvet går du till fönstret Konfiguration av säkerhetskopiering som innehåller alternativet för att aktivera den här funktionen.

 ![Banderoll för konfiguration av säkerhetskopiering](./media/backup-azure-arm-restore-vms/banner.png)

1. Från portalen går du till Ditt Recovery Services-valv > **Egenskaper** (under **Inställningar**).
1. Under **Konfiguration av säkerhetskopiering** väljer du **Uppdatera**.
1. Välj **Aktivera återställning mellan regioner i det här valvet** för att aktivera funktionen.

   ![Aktivera återställning mellan regioner](./media/backup-azure-arm-restore-vms/backup-configuration.png)

I de här artiklarna finns mer information om säkerhetskopiering och återställning med CRR:

- [Återställning mellan regioner för virtuella Azure-datorer](backup-azure-arm-restore-vms.md#cross-region-restore)
- [Återställning mellan regioner för SQL-databaser](restore-sql-database-azure-vm.md#cross-region-restore)
- [Återställning mellan regioner för SAP HANA databaser](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Ange krypteringsinställningar

Som standard krypteras data i Recovery Services-valvet med plattformsbaserade nycklar. Inga explicita åtgärder krävs från din slutpunkt för att aktivera den här krypteringen, och det gäller för alla arbetsbelastningar som säkerhetskopieras till Recovery Services-valvet.  Du kan välja att använda din egen nyckel för att kryptera säkerhetskopierade data i det här valvet. Detta kallas kund hanterade nycklar. Om du vill kryptera säkerhetskopierade data med din egen nyckel måste krypteringsnyckeln anges innan något objekt skyddas i det här valvet. När du aktiverar kryptering med din nyckel kan den inte ångras.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Konfigurera ett valv för kryptering med kund hanterade nycklar

Om du vill konfigurera valvet så att det krypteras med kund hanterade nycklar måste du följa dessa steg i den här ordningen:

1. Aktivera hanterad identitet för Recovery Services-valvet

1. Tilldela behörigheter till valvet för åtkomst till krypteringsnyckeln i Azure Key Vault

1. Aktivera mjuk borttagning och rensningsskydd på Azure Key Vault

1. Tilldela krypteringsnyckeln till Recovery Services-valvet

Anvisningar för vart och ett av dessa steg finns [i den här artikeln.](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys)

## <a name="modifying-default-settings"></a>Ändra standardinställningar

Vi rekommenderar starkt att du granskar standardinställningarna i **Lagringsreplikeringstyp** och **Säkerhetsinställningar** innan du konfigurerar säkerhetskopior i valvet.

- **Lagringsreplikeringstypen** är som standard **inställd på Geo-redundant** (GRS). När du har konfigurerat säkerhetskopieringen inaktiveras alternativet att ändra.
  - Om du inte har konfigurerat säkerhetskopieringen än följer du [dessa steg för](#set-storage-redundancy) att granska och ändra inställningarna.
  - Om du redan har konfigurerat säkerhetskopieringen och måste flytta från GRS till LRS granskar du [dessa lösningar.](#how-to-change-from-grs-to-lrs-after-configuring-backup)

- **Mjuk borttagning** är som standard **Aktiverat för** nyligen skapade valv för att skydda säkerhetskopierade data från oavsiktliga eller skadliga borttagningar. [Följ dessa steg](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) för att granska och ändra inställningarna.

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Så här byter du från GRS till LRS när du har konfigurerat säkerhetskopiering

Innan du bestämmer dig för att flytta från GRS till lokalt redundant lagring (LRS) bör du granska avvägningarna mellan lägre kostnad och högre datatillförlitlighet som passar ditt scenario. Om du måste flytta från GRS till LRS har du två alternativ. De beror på dina affärskrav för att behålla säkerhetskopierade data:

- [Du behöver inte bevara tidigare säkerhetskopierade data](#dont-need-to-preserve-previous-backed-up-data)
- [Tidigare säkerhetskopierade data måste bevaras](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Du behöver inte bevara tidigare säkerhetskopierade data

För att skydda arbetsbelastningar i ett nytt LRS-valv måste det aktuella skyddet och data tas bort i GRS-valvet och säkerhetskopior konfigureras igen.

>[!WARNING]
>Följande åtgärd är destruktiv och kan inte ångras. Alla säkerhetskopierade data och säkerhetskopieringsobjekt som är associerade med den skyddade servern tas bort permanent. Tänk dig för innan du fortsätter.

Stoppa och ta bort aktuellt skydd på GRS-valvet:

1. Inaktivera mjuk borttagning i egenskaperna för GRS-valvet. Följ [dessa steg om](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) du vill inaktivera mjuk borttagning.

1. Stoppa skyddet och ta bort säkerhetskopior från det befintliga GRS-valvet. På menyn Valvinstrumentpanel väljer du **Säkerhetskopieringsobjekt.** Objekt som anges här som måste flyttas till LRS-valvet måste tas bort tillsammans med sina säkerhetskopierade data. Se hur du [tar bort skyddade objekt i molnet](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) och tar bort skyddade objekt [lokalt.](backup-azure-delete-vault.md#delete-protected-items-on-premises)

1. Om du planerar att flytta AFS (Azure-filresurser), SQL-servrar eller SAP HANA-servrar måste du också avregistrera dem. I menyn på instrumentpanelen för valvet väljer du **Infrastruktur för säkerhetskopiering.** Se hur du [avregistrerar SQL-servern,](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance) [avregistrerar](manage-afs-backup.md#unregister-a-storage-account)ett lagringskonto som är associerat med Azure-filresurser och [avregistrerar en SAP HANA instans](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. När de har tagits bort från GRS-valvet fortsätter du att konfigurera säkerhetskopieringarna för din arbetsbelastning i det nya LRS-valvet.

#### <a name="must-preserve-previous-backed-up-data"></a>Tidigare säkerhetskopierade data måste bevaras

Om du behöver behålla aktuella skyddade data i GRS-valvet och fortsätta skyddet i ett nytt LRS-valv finns det begränsade alternativ för vissa arbetsbelastningar:

- För MARS kan du stoppa [skyddet med kvarhållna data och](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) registrera agenten i det nya LRS-valvet.

  - Azure Backup fortsätter att behålla alla befintliga återställningspunkter för GRS-valvet.
  - Du måste betala för att behålla återställningspunkterna i GRS-valvet.
  - Du kommer endast att kunna återställa säkerhetskopierade data för återställningspunkter som inte har visats i GRS-valvet.
  - En ny inledande replik av data måste skapas på LRS-valvet.

- För en virtuell Azure-dator kan du stoppa skyddet med kvarhållna [data](backup-azure-manage-vms.md#stop-protecting-a-vm) för den virtuella datorn i GRS-valvet, flytta den virtuella datorn till en annan resursgrupp och sedan skydda den virtuella datorn i LRS-valvet. Se [vägledning och begränsningar för att](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) flytta en virtuell dator till en annan resursgrupp.

  En virtuell dator kan endast skyddas i ett valv i taget. Den virtuella datorn i den nya resursgruppen kan dock skyddas på LRS-valvet eftersom den betraktas som en annan virtuell dator.

  - Azure Backup tjänsten behåller återställningspunkterna som har säkerhetskopierats i GRS-valvet.
  - Du måste betala för att behålla återställningspunkterna i GRS-valvet (se Azure Backup [för](azure-backup-pricing.md) mer information).
  - Du kommer att kunna återställa den virtuella datorn, om det behövs, från GRS-valvet.
  - Den första säkerhetskopieringen av LRS-valvet för den virtuella datorn i den nya resursen är en inledande replik.

## <a name="next-steps"></a>Nästa steg

[Läs mer om](backup-azure-recovery-services-vault-overview.md) Recovery Services-valv.
[Läs mer om](backup-azure-delete-vault.md) Ta bort Recovery Services-valv.