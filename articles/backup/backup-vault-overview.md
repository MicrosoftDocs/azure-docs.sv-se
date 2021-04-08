---
title: Översikt över säkerhets kopierings valv
description: En översikt över säkerhets kopierings valv.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: c189997ecc4814917182246b35003649d317ac77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92091295"
---
# <a name="backup-vaults-overview"></a>Översikt över säkerhets kopierings valv

I den här artikeln beskrivs funktionerna i ett säkerhets kopierings valv. Ett säkerhets kopierings valv är en lagrings enhet i Azure som delar säkerhetskopierade data för vissa nyare arbets belastningar som Azure Backup stöder. Du kan använda säkerhets kopierings valv för att lagra säkerhets kopierings data för olika Azure-tjänster, till exempel Azure Database for PostgreSQL-servrar och nyare arbets belastningar som Azure Backup stöder. Säkerhets kopierings valv gör det enkelt att organisera dina säkerhets kopierings data samtidigt som du minimerar hanterings kostnaderna. Säkerhets kopierings valv baseras på Azure Resource Manager modell för Azure, som innehåller funktioner som:

- **Förbättrade funktioner för att skydda säkerhets kopierings data**: med säkerhets kopierings valv ger Azure Backup säkerhetsfunktioner för säkerhets kopiering av molnet. Säkerhetsfunktionerna garanterar att du kan skydda dina säkerhets kopior och återställa data på ett säkert sätt, även om produktions-och säkerhets kopierings servrar komprometteras. [Läs mer](backup-azure-security-feature.md)

- **Rollbaserad åtkomst kontroll i Azure (Azure RBAC)**: Azure RBAC tillhandahåller detaljerade åtkomst hanterings kontroller i Azure. [Azure tillhandahåller olika inbyggda roller](../role-based-access-control/built-in-roles.md)och Azure Backup har tre [inbyggda roller för att hantera återställnings punkter](backup-rbac-rs-vault.md). Säkerhets kopierings valv är kompatibla med Azure RBAC, vilket begränsar säkerhets kopierings-och återställnings åtkomsten till den definierade uppsättningen användar roller. [Läs mer](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Lagrings inställningar i säkerhets kopierings valvet

Ett säkerhets kopierings valv är en entitet som lagrar säkerhets kopior och återställnings punkter som skapats med tiden. Säkerhets kopierings valvet innehåller också de säkerhets kopierings principer som är associerade med de skyddade virtuella datorerna.

- Azure Backup hanterar automatiskt lagring för valvet. Välj den redundans för lagring som matchar dina affärs behov när du skapar säkerhets kopierings valvet.

- Mer information om redundans finns i de här artiklarna om [geo](../storage/common/storage-redundancy.md#geo-redundant-storage) -och [lokal](../storage/common/storage-redundancy.md#locally-redundant-storage) redundans.

## <a name="encryption-settings-in-the-backup-vault"></a>Krypterings inställningar i säkerhets kopierings valvet

I det här avsnittet beskrivs de alternativ som är tillgängliga för kryptering av säkerhets kopierings data som lagras i säkerhets kopierings valvet.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Kryptering av säkerhets kopierings data med hjälp av plattforms hanterade nycklar

Som standard krypteras alla dina data med hjälp av plattforms hanterade nycklar. Du behöver inte vidta någon uttrycklig åtgärd från din sida för att aktivera den här krypteringen. Den gäller för alla arbets belastningar som säkerhets kopie ras till säkerhets kopierings valvet.

## <a name="create-a-backup-vault"></a>Skapa ett säkerhets kopierings valv

Ett säkerhets kopierings valv är en hanterings enhet som lagrar återställnings punkter som skapats med tiden och som tillhandahåller ett gränssnitt för att utföra säkerhets kopierings åtgärder. Dessa inkluderar säkerhetskopieringar på begäran, återställningar och att skapa säkerhetskopieringsprinciper.

Följ dessa steg om du vill skapa ett säkerhets kopierings valv.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Skapa säkerhets kopierings valv

1. Skriv **säkerhets kopierings valv** i sökrutan.
1. Under **tjänster** väljer du **säkerhets kopierings valv**.
1. På sidan **säkerhets kopierings valv** väljer du **Lägg till**.
1. På **fliken grundläggande**, under **projekt information**, se till att rätt prenumeration är markerad och välj sedan **Skapa ny** resurs grupp. Skriv *myResourceGroup* som namn.

  ![Skapa ny resurs grupp](./media/backup-vault-overview/new-resource-group.png)

1. Under **instans information** skriver du ett *valv* för **namnet på säkerhets kopierings valvet** och väljer din region som du väljer, i det här fallet, *östra USA* för din **region**.
1. Nu ska du välja **lagrings redundans**. Det går inte att ändra lagrings utrymmet när objekt har skyddats i valvet.
1. Vi rekommenderar att om du använder Azure som primär slut punkt för lagring av säkerhets kopior fortsätter du att använda standardvärdet **Geo-redundant** .
1. Om du inte använder Azure som primär slutpunkt för lagring av säkerhetskopior väljer du **Lokalt redundant**, vilket minskar kostnaderna för Azure-lagring.
1. Lär dig mer om [geo](../storage/common/storage-redundancy.md#geo-redundant-storage) och [lokal](../storage/common/storage-redundancy.md#locally-redundant-storage) redundans.

  ![Välj lagrings redundans](./media/backup-vault-overview/storage-redundancy.png)

1. Välj knappen granska + skapa längst ned på sidan.

    ![Välj granska + skapa](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Ta bort ett säkerhets kopierings valv

I det här avsnittet beskrivs hur du tar bort ett säkerhets kopierings valv. Den innehåller instruktioner för att ta bort beroenden och sedan ta bort ett valv.

### <a name="before-you-start"></a>Innan du börjar

Du kan inte ta bort ett säkerhets kopierings valv med något av följande beroenden:

- Du kan inte ta bort ett valv som innehåller skyddade data källor (till exempel Azure Database för PostgreSQL-servrar).
- Du kan inte ta bort ett valv som innehåller säkerhets kopierings data.

Om du försöker ta bort valvet utan att ta bort beroenden uppstår följande fel meddelanden:

>Det går inte att ta bort säkerhets kopierings valvet eftersom det finns befintliga säkerhets kopierings instanser eller säkerhets principer i valvet. Ta bort alla säkerhets kopierings instanser och säkerhets kopierings principer som finns i valvet och försök sedan att ta bort valvet.

### <a name="proper-way-to-delete-a-vault"></a>Korrekt sätt att ta bort ett valv

>[!WARNING]
Följande åtgärd är förstörande och kan inte återställas. Alla säkerhets kopierings data och säkerhets kopierings objekt som är associerade med den skyddade servern tas bort permanent. Tänk dig för innan du fortsätter.

Om du vill ta bort ett valv korrekt måste du följa stegen i den här ordningen:

- Du måste kontrol lera om det finns några skyddade objekt:
  - Gå till **säkerhets kopierings instanser** i det vänstra navigerings fältet. Alla objekt som anges här måste tas bort först.

När du har slutfört de här stegen kan du fortsätta att ta bort valvet.

### <a name="delete-the-backup-vault"></a>Ta bort säkerhets kopierings valvet

När det inte finns några fler objekt i valvet väljer du **ta bort** på instrument panelen för valvet. En bekräftelse text visas där du tillfrågas om du vill ta bort valvet.

![Ta bort valv](./media/backup-vault-overview/delete-vault.png)

1. Välj **Ja** för att kontrol lera att du vill ta bort valvet. Valvet har tagits bort. Portalen återgår till den **nya** tjänst menyn.

## <a name="monitor-and-manage-the-backup-vault"></a>Övervaka och hantera säkerhets kopierings valvet

I det här avsnittet beskrivs hur du använder **översikts** instrument panelen för säkerhets kopierings valv för att övervaka och hantera dina säkerhets kopierings valv. Översikts fönstret innehåller två paneler: **jobb** och **instanser**.

![Översikts instrument panel](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Hantera säkerhets kopierings instanser

På panelen **jobb** får du en sammanfattande vy över alla säkerhets kopierings-och återställnings jobb i säkerhets kopierings valvet. Genom att välja något av talen på den här panelen kan du Visa mer information om jobb för en viss typ av data källa, åtgärds typ och status.

![Säkerhets kopierings instanser](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Hantera säkerhetskopieringsjobb

På panelen **säkerhets kopierings instanser** får du en sammanfattande vy över alla säkerhets kopierings instanser i säkerhets kopierings valvet. Genom att välja något av talen på den här panelen kan du Visa mer information om säkerhets kopierings instanser för en viss typ av data källa och skydds status.

![Säkerhets kopierings jobb](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera säkerhets kopiering på Azure PostgreSQL-databaser](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
