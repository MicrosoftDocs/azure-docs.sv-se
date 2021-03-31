---
title: Mjuk borttagning för virtuella datorer
description: Lär dig hur mjuk borttagning för virtuella datorer gör säkerhets kopieringar säkrare.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: a8b70d4c8240d096c19e5a8d7449921557b8896c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89022249"
---
# <a name="soft-delete-for-virtual-machines"></a>Mjuk borttagning för virtuella datorer

Mjuk borttagning för virtuella datorer skyddar de virtuella datorerna från oavsiktlig borttagning. Även efter att säkerhets kopiorna har tagits bort bevaras de i läget för mjuk borttagning under ytterligare 14 dagar.

> [!NOTE]
> Mjuk borttagning skyddar bara borttagna säkerhets kopierings data. Om en virtuell dator tas bort utan säkerhets kopiering bevaras inte data av funktionen för mjuk borttagning. Alla resurser bör skyddas med Azure Backup för att garantera fullständig återhämtning.
>

## <a name="supported-regions"></a>Regioner som stöds

Mjuk borttagning stöds för närvarande i USA, västra centrala, Asien, östra, centrala Kanada, Östra Kanada, centrala Frankrike, södra Korea, centrala Korea, södra Korea, Storbritannien, södra, Storbritannien, västra, östra Australien, sydöstra Australien, norra Europa, västra USA, västra 2; USA, centrala USA, Asien, sydöstra, norra centrala USA, södra centrala USA, Östra Japan, västra Japan, södra Indien, västra Indien, östra USA 2 , Schweiz, norra, Schweiz, västra, västra Norge, östra Norge och alla nationella regioner.

## <a name="soft-delete-for-vms-using-azure-portal"></a>Mjuk borttagning för virtuella datorer som använder Azure Portal

1. Om du vill ta bort säkerhets kopierings data för en virtuell dator måste säkerhets kopian stoppas. I Azure Portal går du till Recovery Services-valvet, högerklickar på säkerhets kopierings objekt och väljer **stoppa säkerhets kopiering**.

   ![Skärm bild av Azure Portal säkerhets kopierings objekt](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. I följande fönster kan du välja att ta bort eller behålla säkerhetskopierade data. Om du väljer **ta bort säkerhetskopierade data** och sedan **stoppar säkerhets kopieringen** tas inte den virtuella datorns säkerhets kopia bort permanent. I stället behålls säkerhets kopierings data i 14 dagar i läget Soft Deleted. Om du väljer **ta bort säkerhets kopierings data** skickas en e-postavisering till det konfigurerade e-postmeddelandet som informerar användaren om att 14 dagar fortfarande är av utökad kvarhållning för säkerhetskopierade data. Dessutom skickas en e-postavisering den 12: e dagen på att det finns två dagar kvar för att Resurrect borttagna data. Borttagningen skjuts upp fram till den femtonde dagen, när permanent borttagning kommer att ske och en slutgiltig e-postavisering skickas för att informera om permanent borttagning av data.

   ![Skärm bild av Azure Portal, stoppa säkerhets kopierings skärmen](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. Under dessa 14 dagar visas den mjuka borttagna virtuella datorn i Recovery Services-valvet med en röd "mjuk borttagning"-ikon bredvid.

   ![Skärm bild av Azure Portal, VM i läget för mjuk borttagning](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > Om det finns mjuka, borttagna säkerhets kopierings objekt i valvet kan valvet inte tas bort vid denna tidpunkt. Försök att ta bort valvet när säkerhets kopierings objekten har tagits bort permanent och det finns inga objekt i läget Soft Deleted kvar i valvet.

4. Om du vill återställa den mjuk-borttagna virtuella datorn måste den först tas bort. Om du vill ångra borttagningen väljer du den mjuk-borttagna virtuella datorn och väljer sedan alternativet **ta bort**.

   ![Skärm bild av Azure Portal, ta bort virtuell dator](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   Ett fönster visas där du får ett varnings meddelande om att ångra borttagning har valts och att alla återställnings punkter för den virtuella datorn tas bort och är tillgängliga för att utföra en återställnings åtgärd. Den virtuella datorn kommer att behållas i status "stoppa skydd med kvarhållning" med säkerhets kopior som pausats och säkerhets kopierings data kvarhålls för alltid med ingen säkerhets kopierings princip gällande.

   ![Skärm bild av Azure Portal, bekräfta borttagning av virtuell dator](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   I det här läget kan du också återställa den virtuella datorn genom att välja **Återställ virtuell dator** från den valda återställnings punkten.  

   ![Skärm bild av Azure Portal, Återställ VM-alternativ](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > Skräp insamlaren kommer att köra och rensa återställnings punkter som har upphört att gälla först när användaren utför **säkerhets kopierings åtgärden återuppta** .

5. När du har slutfört borttagnings processen återgår statusen till "stoppa säkerhets kopiering med kvar data" och sedan kan du välja **återuppta säkerhets kopiering**. Åtgärden **återuppta säkerhets kopiering** kommer tillbaka till säkerhets kopierings objektet i det aktiva läget, associerat med en säkerhets kopierings princip som valts av användaren som definierar säkerhets kopierings-och bevarande scheman.

   ![Skärm bild av Azure Portal, återuppta säkerhets kopierings alternativ](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Mjuk borttagning för virtuella datorer som använder Azure PowerShell

> [!IMPORTANT]
> AZ. RecoveryServices-versionen som krävs för att använda mjuk borttagning med Azure PowerShell är minimal 2.2.0. Använd ```Install-Module -Name Az.RecoveryServices -Force``` för att hämta den senaste versionen.

Som beskrivs ovan för Azure Portal, är sekvensen av steg densamma när du använder Azure PowerShell även.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Ta bort säkerhets kopierings objekt med Azure PowerShell

Ta bort säkerhets kopierings objekt med PowerShell-cmdleten [disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) .

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

DeleteState för säkerhets kopierings objekt ändras från ' NotDeleted ' till ' ToBeDeleted '. Säkerhetskopierade data sparas i 14 dagar. Om du vill återställa borttagnings åtgärden ska du utföra Undo-Delete.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Ångra borttagnings åtgärden med Azure PowerShell

Börja med att hämta det relevanta säkerhets kopierings objekt som är i läget för mjuk borttagning (det vill säga tas bort).

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Utför sedan åtgärden ångra borttagning med hjälp av cmdleten [Undo-AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

DeleteState för säkerhets kopierings objekt återgår till ' NotDeleted '. Men skyddet har fortfarande stoppats. [Återuppta säkerhets kopieringen](./backup-azure-vms-automation.md#change-policy-for-backup-items) för att återaktivera skyddet.

## <a name="soft-delete-for-vms-using-rest-api"></a>Mjuk borttagning för virtuella datorer som använder REST API

- Ta bort säkerhets kopiorna med REST API som anges [här](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).
- Om du vill ångra dessa borttagnings åtgärder, se de steg som beskrivs [här](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion).

## <a name="how-to-disable-soft-delete"></a>Så här inaktiverar du mjuk borttagning

Du bör inte inaktivera den här funktionen. Den enda omständigheterna där du bör inaktivera mjuk borttagning är om du planerar att flytta dina skyddade objekt till ett nytt valv, och det kan inte vänta 14 dagar innan du tar bort och återskyddar (till exempel i en test miljö). Instruktioner för hur du inaktiverar mjuk borttagning finns i [Aktivera och inaktivera mjuk borttagning](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Nästa steg

- Läs [vanliga frågor](backup-azure-security-feature-cloud.md#frequently-asked-questions) om mjuk borttagning
- Läs om alla [säkerhetsfunktioner i Azure Backup](security-overview.md)
