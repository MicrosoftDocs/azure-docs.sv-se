---
title: Översikt över säkerhetskopieringsvalv
description: En översikt över Säkerhetskopieringsvalv.
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: e2d720da9474a35870de01559201d22c9e5b567f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739088"
---
# <a name="backup-vaults-overview"></a>Översikt över säkerhetskopieringsvalv

I den här artikeln beskrivs funktionerna i ett Backup-valv. Ett Backup-valv är en lagringsenhet i Azure som rymmer säkerhetskopierade data för vissa nyare arbetsbelastningar som Azure Backup stöder. Du kan använda Säkerhetskopieringsvalv för att lagra säkerhetskopierade data för olika Azure-tjänster, till exempel Azure Database for PostgreSQL-servrar och nyare arbetsbelastningar som Azure Backup stöder. Säkerhetskopieringsvalv gör det enkelt att organisera dina säkerhetskopierade data, samtidigt som hanteringskostnader minimeras. Säkerhetskopieringsvalv baseras på Azure Resource Manager av Azure, som innehåller funktioner som:

- **Förbättrade funktioner för att skydda säkerhetskopierade data:** Med säkerhetskopieringsvalv Azure Backup säkerhetsfunktioner för att skydda molnsäkerhetskopior. Säkerhetsfunktionerna ser till att du kan skydda dina säkerhetskopior och återställa data på ett säkert sätt, även om produktions- och säkerhetskopieringsservrar komprometteras. [Läs mer](backup-azure-security-feature.md)

- **Rollbaserad åtkomstkontroll i Azure (Azure RBAC):** Azure RBAC ger en mer fullständig åtkomsthanteringskontroll i Azure. [Azure tillhandahåller olika inbyggda roller, och](../role-based-access-control/built-in-roles.md)Azure Backup har tre [inbyggda roller för att hantera återställningspunkter.](backup-rbac-rs-vault.md) Säkerhetskopieringsvalv är kompatibla med Azure RBAC, vilket begränsar åtkomsten till säkerhetskopiering och återställning till den definierade uppsättningen användarroller. [Läs mer](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Lagringsinställningar i Säkerhetskopieringsvalvet

Ett säkerhetskopieringsvalv är en entitet som lagrar säkerhetskopior och återställningspunkter som skapats över tid. Säkerhetskopieringsvalvet innehåller också de säkerhetskopieringsprinciper som är associerade med de skyddade virtuella datorerna.

- Azure Backup hanterar automatiskt lagringen för valvet. Välj den lagringsredundans som passar dina affärsbehov när du skapar säkerhetskopieringsvalvet.

- Mer information om lagringsredundans finns i de här artiklarna om [geo-](../storage/common/storage-redundancy.md#geo-redundant-storage) och [lokal redundans.](../storage/common/storage-redundancy.md#locally-redundant-storage)

## <a name="encryption-settings-in-the-backup-vault"></a>Krypteringsinställningar i Säkerhetskopieringsvalvet

I det här avsnittet beskrivs de alternativ som är tillgängliga för kryptering av dina säkerhetskopierade data som lagras i Säkerhetskopieringsvalvet. Azure Backup service använder **Backup Management Service-appen** för att komma åt Azure Key Vault, men inte den hanterade identiteten för Backup-valvet.


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Kryptering av säkerhetskopierade data med plattformsbaserade nycklar

Som standard krypteras alla dina data med plattformsbaserade nycklar. Du behöver inte vidta några explicita åtgärder från din slutpunkt för att aktivera den här krypteringen. Det gäller för alla arbetsbelastningar som säkerhetskopieras till säkerhetskopieringsvalvet.

## <a name="create-a-backup-vault"></a>Skapa ett Säkerhetskopieringsvalv

Ett Säkerhetskopieringsvalv är en hanteringsenhet som lagrar återställningspunkter som skapats över tid och som tillhandahåller ett gränssnitt för att utföra säkerhetskopieringsrelaterade åtgärder. Dessa inkluderar säkerhetskopieringar på begäran, återställningar och att skapa säkerhetskopieringsprinciper.

Följ dessa steg om du vill skapa ett säkerhetskopieringsvalv.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Skapa Säkerhetskopieringsvalv

1. Skriv **Säkerhetskopieringsvalv** i sökrutan.
1. Under **Tjänster** väljer du **Säkerhetskopieringsvalv.**
1. På sidan **Säkerhetskopieringsvalv** väljer du Lägg **till**.
1. Kontrollera att **rätt prenumeration har** valts under **Projektinformation** på fliken Grundläggande inställningar och välj sedan **Skapa ny** resursgrupp. Skriv *myResourceGroup* som namn.

  ![Skapa ny resursgrupp](./media/backup-vault-overview/new-resource-group.png)

1. Under **Instansinformation** skriver du *myVault* som namn på säkerhetskopieringsvalvet och väljer din region, i det här fallet *USA,* östra för din **region.** 
1. Välj nu **lagringsredundans.** Lagringsredundans kan inte ändras efter att objekt har skyddats i valvet.
1. Vi rekommenderar att du fortsätter att använda standardinställningen **geo-redundant** om du använder Azure som primär lagringsslutpunkt för säkerhetskopiering.
1. Om du inte använder Azure som primär slutpunkt för lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring.
1. Läs mer om [geo-](../storage/common/storage-redundancy.md#geo-redundant-storage) och [lokal redundans.](../storage/common/storage-redundancy.md#locally-redundant-storage)

  ![Välj lagringsredundans](./media/backup-vault-overview/storage-redundancy.png)

1. Välj knappen Granska + skapa längst ned på sidan.

    ![Välj Granska + skapa](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Ta bort ett Säkerhetskopieringsvalv

I det här avsnittet beskrivs hur du tar bort ett Säkerhetskopieringsvalv. Den innehåller instruktioner för att ta bort beroenden och sedan ta bort ett valv.

### <a name="before-you-start"></a>Innan du börjar

Du kan inte ta bort ett Säkerhetskopieringsvalv med något av följande beroenden:

- Du kan inte ta bort ett valv som innehåller skyddade datakällor (till exempel Azure-databas för PostgreSQL-servrar).
- Du kan inte ta bort ett valv som innehåller säkerhetskopierade data.

Om du försöker ta bort valvet utan att ta bort beroendena får du följande felmeddelanden:

>Det går inte att ta bort säkerhetskopieringsvalvet eftersom det finns befintliga säkerhetskopieringsinstanser eller säkerhetskopieringsprinciper i valvet. Ta bort alla säkerhetskopieringsinstanser och säkerhetskopieringsprinciper som finns i valvet och prova sedan att ta bort valvet.

### <a name="proper-way-to-delete-a-vault"></a>Rätt sätt att ta bort ett valv

>[!WARNING]
Följande åtgärd är destruktiv och kan inte ångras. Alla säkerhetskopierade data och säkerhetskopieringsobjekt som är associerade med den skyddade servern tas bort permanent. Tänk dig för innan du fortsätter.

Om du vill ta bort ett valv korrekt måste du följa stegen i den här ordningen:

- Kontrollera om det finns några skyddade objekt:
  - Gå till **Säkerhetskopieringsinstanser** i det vänstra navigeringsfältet. Alla objekt som anges här måste tas bort först.

När du har slutfört de här stegen kan du fortsätta att ta bort valvet.

### <a name="delete-the-backup-vault"></a>Ta bort säkerhetskopieringsvalvet

När det inte finns några fler objekt i valvet väljer du Ta **bort på** valvets instrumentpanel. Du ser en bekräftelsetext som frågar om du vill ta bort valvet.

![Ta bort valv](./media/backup-vault-overview/delete-vault.png)

1. Välj **Ja** för att kontrollera att du vill ta bort valvet. Valvet tas bort. Portalen återgår till **menyn Ny** tjänst.

## <a name="monitor-and-manage-the-backup-vault"></a>Övervaka och hantera Säkerhetskopieringsvalvet

I det här avsnittet beskrivs hur du använder instrumentpanelen Översikt över **Säkerhetskopieringsvalv** för att övervaka och hantera dina säkerhetskopieringsvalv. Översiktsfönstret innehåller två paneler: **Jobb** **och instanser**.

![Instrumentpanel för översikt](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Hantera Säkerhetskopieringsinstanser

På panelen **Jobb** får du en sammanfattad vy över alla säkerhetskopierings- och återställningsrelaterade jobb i säkerhetskopieringsvalvet. Om du väljer något av siffrorna i den här panelen kan du visa mer information om jobb för en viss typ av datakälla, åtgärdstyp och status.

![Säkerhetskopieringsinstanser](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Hantera säkerhetskopieringsjobb

På panelen **Säkerhetskopieringsinstanser** får du en sammanfattad vy över alla säkerhetskopieringsinstanser i säkerhetskopieringsvalvet. Om du väljer något av siffrorna i den här panelen kan du visa mer information om säkerhetskopieringsinstanser för en viss typ av datakälla och skyddsstatus.

![Säkerhetskopieringsjobb](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera säkerhetskopiering på Azure PostgreSQL-databaser](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
