---
title: Mjuk borttagning för SQL Server i Azure VM och SAP HANA i Azure VM-arbetsbelastningar
description: Lär dig hur mjuk borttagning för SQL Server i Azure VM och SAP HANA i Azure VM-arbetsbelastningar gör säkerhets kopieringar säkrare.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2a442997d426ff0bf4c74b0b45f7657cc0593b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254303"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Mjuk borttagning för SQL Server i Azure VM och SAP HANA i Azure VM-arbetsbelastningar

Azure Backup tillhandahåller nu mjuk borttagning för SQL Server i virtuella Azure-datorer och SAP HANA i Azure VM-arbetsbelastningar. Detta är utöver det tidigare stödda [scenariot för mjuk borttagning av virtuella Azure-datorer](soft-delete-virtual-machines.md).

[Mjuk borttagning](backup-azure-security-feature-cloud.md) är en säkerhetsfunktion som hjälper dig att skydda säkerhets kopierings data även efter borttagning. Med mjuk borttagning, även om en obehörig aktör tar bort säkerhets kopian av en databas (eller om säkerhets kopierings data tas bort av misstag), bevaras säkerhets kopierings data i ytterligare 14 dagar. Detta gör det möjligt att återställa säkerhets kopierings objekt utan data förlust. Den ytterligare kvarhållning av 14 dagar av säkerhets kopierings data i läget "mjuk borttagning" kostar ingen kostnad för kunden.

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Mjuk borttagning för SQL Server i virtuell Azure-dator med Azure Portal

>[!NOTE]
>Dessa anvisningar gäller även för SAP HANA i Azure VM.

1. Om du vill ta bort säkerhets kopierings data för en databas i en SQL-Server måste säkerhets kopian stoppas. Gå till Recovery Services-valvet i Azure Portal, gå till objektet säkerhets kopia och välj **Avbryt säkerhets kopiering**.

   ![Stoppa säkerhets kopiering](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. I följande fönster kan du välja att ta bort eller behålla säkerhetskopierade data. Om du väljer **ta bort säkerhets kopierings data** tas inte säkerhets kopian av databasen bort permanent. I stället behålls säkerhets kopierings data i 14 dagar i läget Soft Deleted. Borttagningen skjuts upp fram till den 15: e dagen med regelbundna aviseringar via e-post på den första, tolfte och 15: e dagen som informerar om säkerhets kopierings status för databasen till användaren.

   ![Ta bort säkerhetskopieringsdata](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. Under dessa 14 dagar visas det mjuka borttagna objektet i Recovery Services-valvet med en röd "mjuk borttagning"-ikon bredvid.

   ![Ej permanent borttagna objekt](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. Om du vill återställa den mjuk-borttagna databasen måste den först tas bort. Om du vill ångra borttagningen väljer du den mjuk-borttagna databasen och väljer sedan alternativet **ta bort**.

   ![Ta bort databas](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   Ett fönster visas där du får ett varnings meddelande om att ångra borttagning har valts och att alla återställnings punkter för databasen tas bort och är tillgängliga för att utföra en återställnings åtgärd. Backup-objektet kommer att behållas i status "stoppa skydd med kvarhållning av data" med säkerhets kopior som pausas och säkerhets kopierings data kvarhålls för alltid med ingen säkerhets kopierings policy gällande.

   ![Ta bort varning](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. I det här läget kan du också återställa data genom att välja **Återställ** för det valda objektet för mjuk borttagning av säkerhets kopia.

   ![Återställa virtuell dator](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. När du har slutfört borttagnings processen återgår statusen till "stoppa säkerhets kopiering med kvar data" och sedan kan du välja **återuppta säkerhets kopiering**. Åtgärden **återuppta säkerhets kopiering** kommer tillbaka till säkerhets kopierings objektet i det aktiva läget, associerat med en säkerhets kopierings princip som valts av användaren som definierar säkerhets kopierings-och bevarande scheman.

   ![Återuppta säkerhets kopiering](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Mjuk borttagning för SQL Server i virtuell dator med hjälp av Azure PowerShell

>[!NOTE]
>AZ. RecoveryServices-versionen som krävs för att använda mjuk borttagning med Azure PowerShell är minimal 2.2.0. Använd `Install-Module -Name Az.RecoveryServices -Force` för att hämta den senaste versionen.

Sekvensen av steg för att använda Azure PowerShell är samma som i Azure Portal, som beskrivs ovan.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Ta bort säkerhets kopierings objekt med Azure PowerShell

Ta bort säkerhets kopierings objekt med PowerShell-cmdleten [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) .

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

**DeleteState** för säkerhets kopierings objekt ändras från **NotDeleted** till **ToBeDeleted**. Säkerhetskopierade data sparas i 14 dagar. Om du vill återställa borttagnings åtgärden ska du utföra Undo-Delete.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Ångra borttagnings åtgärden med Azure PowerShell

Börja med att hämta det relevanta säkerhets kopierings objekt som är i läget för mjuk borttagning (det vill säga tas bort).

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

Utför sedan åtgärden ångra borttagning med hjälp av cmdleten [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

**DeleteState** för säkerhets kopierings objekt återställs till **NotDeleted**. Men skyddet har fortfarande stoppats. Återuppta säkerhets kopieringen för att återaktivera skyddet.

## <a name="how-to-disable-soft-delete"></a>Så här inaktiverar du mjuk borttagning

Du bör inte inaktivera den här funktionen. Den enda omständigheterna där du bör inaktivera mjuk borttagning är om du planerar att flytta dina skyddade objekt till ett nytt valv, och det kan inte vänta 14 dagar innan du tar bort och återskyddar (till exempel i en test miljö). Instruktioner för hur du inaktiverar mjuk borttagning finns i [Aktivera och inaktivera mjuk borttagning](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Nästa steg

- Läs [vanliga frågor](backup-azure-security-feature-cloud.md#frequently-asked-questions) om mjuk borttagning
- Läs om alla [säkerhetsfunktioner i Azure Backup](security-overview.md)
