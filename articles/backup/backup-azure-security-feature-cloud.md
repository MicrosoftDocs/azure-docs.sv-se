---
title: Mjuk borttagning för Azure Backup
description: Lär dig hur du använder säkerhetsfunktioner i Azure Backup för att göra säkerhets kopieringar säkrare.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: da473b1d886ec2fe95a7baae76b09aff38fb3cd7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95254057"
---
# <a name="soft-delete-for-azure-backup"></a>Mjuk borttagning för Azure Backup

Problem med säkerhets problem, t. ex. skadlig kod, utpressnings program vara och intrång, ökar. De här säkerhets problemen kan vara dyra, i termer av både Money och data. För att skydda mot sådana angrepp tillhandahåller Azure Backup nu säkerhetsfunktioner som hjälper dig att skydda säkerhets kopierings data även efter borttagning.

En sådan funktion är mjuk borttagning. Med mjuk borttagning, även om en obehörig aktör tar bort en säkerhets kopia (eller säkerhetskopierade data tas bort av misstag), bevaras säkerhets kopierings data i ytterligare 14 dagar, vilket gör att det inte går att återställa säkerhets kopierings objekt utan data förlust. De ytterligare 14 dagarna för kvarhållning av säkerhets kopierings data i läget "mjuk borttagning" kostar inga kostnader för dig.

Mjuk borttagnings skydd är tillgängligt för dessa tjänster:

- [Mjuk borttagning för virtuella Azure-datorer](soft-delete-virtual-machines.md)
- [Mjuk borttagning för SQL Server i Azure VM och mjuk borttagning för SAP HANA i virtuella Azure-arbetsbelastningar](soft-delete-sql-saphana-in-azure-vm.md)

Det här Flow-diagrammet visar de olika stegen och tillstånden för ett säkerhets kopierings objekt när mjuk borttagning är aktiverat:

![Livs cykel för mjuk borttagning av säkerhets kopierings objekt](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>Aktivera och inaktivera mjuk borttagning

Mjuk borttagning är aktiverat som standard på nyligen skapade valv för att skydda säkerhets kopierings data från oavsiktliga eller skadliga borttagningar.  Du bör inte inaktivera den här funktionen. Den enda omständigheterna där du bör inaktivera mjuk borttagning är om du planerar att flytta dina skyddade objekt till ett nytt valv, och det kan inte vänta 14 dagar innan du tar bort och återskyddar (till exempel i en test miljö). Endast valvets ägare kan inaktivera den här funktionen. Om du inaktiverar den här funktionen kommer alla framtida borttagningar av skyddade objekt att leda till omedelbar borttagning, utan möjlighet att återställa. Säkerhets kopierings data som finns i läget Soft Deleted, innan funktionen inaktive ras, förblir i tyst borttaget läge under perioden 14 dagar. Om du vill ta bort dessa omedelbart permanent måste du ångra borttagningen och ta bort dem igen för att ta bort dem permanent.

Det är viktigt att komma ihåg att när mjuk borttagning har inaktiverats är funktionen inaktive rad för alla typer av arbets belastningar. Det går till exempel inte att inaktivera mjuk borttagning enbart för SQL Server eller SAP HANA databaser samtidigt som den är aktive rad för virtuella datorer i samma valv. Du kan skapa separata valv för detaljerad kontroll.

### <a name="disabling-soft-delete-using-azure-portal"></a>Inaktivera mjuk borttagning med Azure Portal

Följ dessa steg om du vill inaktivera mjuk borttagning:

1. Gå till ditt valv i Azure Portal och gå sedan till **Inställningar**  ->  **Egenskaper**.
2. I fönstret Egenskaper väljer du **säkerhets inställningar**  ->  **Uppdatera**.  
3. I rutan säkerhets inställningar under **mjuk borttagning** väljer du **inaktivera**.

![Inaktivera mjuk borttagning](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Inaktivera mjuk borttagning med Azure PowerShell

> [!IMPORTANT]
> AZ. RecoveryServices-versionen som krävs för att använda mjuk borttagning med Azure PowerShell är minimal 2.2.0. Använd ```Install-Module -Name Az.RecoveryServices -Force``` för att hämta den senaste versionen.

Om du vill inaktivera använder du PowerShell-cmdleten [set-AzRecoveryServicesVaultBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) .

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>Inaktivera mjuk borttagning med REST API

Om du vill inaktivera funktionen för mjuk borttagning med REST API, se de steg som beskrivs [här](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Ta bort mjuka borttagna säkerhets kopierings objekt permanent

Säkerhets kopierings data i läget Soft Deleted tidigare inaktive ring av den här funktionen kommer att vara i läget Soft Deleted Om du vill ta bort dessa omedelbart tar du bort dem igen och tar bort dem permanent.

### <a name="using-azure-portal"></a>Använda Azure Portal

Följ de här stegen:

1. Följ stegen för att [inaktivera mjuk borttagning](#enabling-and-disabling-soft-delete).

2. Gå till ditt valv i Azure Portal, gå till **säkerhets kopierings objekt** och välj det Soft borttagna objektet.

   ![Välj Soft borttaget objekt](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. Välj alternativet **ångra borttagning**.

   ![Välj ångra borttagning](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Ett fönster visas. Välj **ångra borttagning**.

   ![Välj ångra borttagning](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Välj **ta bort säkerhetskopierade data** om du vill ta bort säkerhetskopierade data permanent.

   ![Välj Ta bort säkerhets kopierings data](./media/backup-azure-manage-vms/delete-backup-button.png)

6. Ange namnet på det säkerhetskopierade objektet för att bekräfta att du vill ta bort återställnings punkterna.

   ![Ange namnet på det säkerhetskopierade objektet](./media/backup-azure-manage-vms/delete-backup-data.png)

7. Om du vill ta bort säkerhetskopierade data för objektet väljer du **ta bort**. Ett meddelande visas där du vet att säkerhets kopierings data har tagits bort.

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Om objekten togs bort innan mjuk borttagning har inaktiverats, kommer de att vara i ett mjukt borttaget tillstånd. Om du vill ta bort dem omedelbart måste borttagnings åtgärden ångras och sedan utföras igen.

Identifiera objekt som är i läget Soft-Deleted.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Ångra sedan borttagnings åtgärden som utfördes när mjuk borttagning har Aktiver ATS.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Eftersom den mjuka borttagningen nu är inaktive rad kommer borttagnings åtgärden att leda till omedelbar borttagning av säkerhets kopierings data.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>Använda REST-API:et

Om objekten togs bort innan mjuk borttagning har inaktiverats, kommer de att vara i ett mjukt borttaget tillstånd. Om du vill ta bort dem omedelbart måste borttagnings åtgärden ångras och sedan utföras igen.

1. Börja med att ångra borttagnings åtgärderna med stegen som anges [här](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion).
2. Inaktivera sedan funktionerna för mjuk borttagning med REST API med hjälp av stegen som beskrivs [här](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api).
3. Ta sedan bort säkerhets kopiorna med REST API som anges [här](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Måste jag aktivera funktionen för mjuk borttagning i varje valv?

Nej, det är inbyggt och aktiverat som standard för alla Recovery Services-valv.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>Kan jag konfigurera antalet dagar för vilka mina data ska bevaras i läget "Soft-Deleted" när borttagningen är slutförd?

Nej, det har åtgärd ATS till 14 dagar efter ytterligare kvarhållning efter borttagnings åtgärden.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Behöver jag betala kostnaden för ytterligare 14-dagars kvarhållning?

Nej, den här 14-dagars ytterligare kvarhållning är kostnads fri, som en del av funktionen för mjuk borttagning.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Kan jag utföra en återställnings åtgärd när mina data är i läget för tyst Borttagning?

Nej, du måste ta bort den mjuka borttagna resursen för att kunna återställa. Åtgärden ta bort tar tillbaka resursen i **stopp skyddet med Behåll data tillstånd** där du kan återställa till en viss tidpunkt. Skräp insamlaren förblir pausad i det här läget.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Kommer mina ögonblicks bilder att följa samma livs cykel som mina återställnings punkter i valvet?

Ja.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Hur kan jag aktivera schemalagda säkerhets kopieringar igen för en mjuk borttagen resurs?

Undelete följt av en återuppta-åtgärd kommer att skydda resursen igen. Åtgärden återuppta associerar en säkerhets kopierings princip för att utlösa de schemalagda säkerhets kopieringarna med den valda kvarhållningsperioden. Dessutom körs skräp insamlaren så fort återställnings åtgärden har slutförts. Om du vill utföra en återställning från en återställnings punkt som har passerat förfallo datumet, uppmanas du att göra det innan du utlöser återställnings åtgärden.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Kan jag ta bort mitt valv om det finns mjuka borttagna objekt i valvet?

Det går inte att ta bort Recovery Services valvet om det finns säkerhets kopierings objekt i läget Soft-Deleted i valvet. De mjuka borttagna objekten tas bort permanent 14 dagar efter borttagnings åtgärden. Om du inte kan vänta i 14 dagar [inaktiverar du mjuk borttagning](#enabling-and-disabling-soft-delete), tar bort de mjuka borttagna objekten och tar bort dem igen för att ta bort dem permanent. När du har kontrollerat att det inte finns några skyddade objekt och inga mjuka borttagna objekt, kan valvet tas bort.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Kan jag ta bort data som är tidigare än de 14 dagarna mjuk borttagnings perioden efter borttagningen?

Nej. Du kan inte tvinga bort borttagna objekt. De tas bort automatiskt efter 14 dagar. Den här säkerhetsfunktionen är aktive rad för att skydda säkerhetskopierade data från oavsiktliga eller skadliga borttagningar.  Du bör vänta i 14 dagar innan du utför någon annan åtgärd på objektet.  Ej permanent borttagna objekt debiteras inte.  Kontakta Microsoft-supporten om du behöver skydda objekten som har marker ATS för mjuk borttagning inom 14 dagar i ett nytt valv.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>Kan mjuka borttagnings åtgärder utföras i PowerShell eller CLI?

Mjuk borttagnings åtgärder kan utföras med hjälp av PowerShell. CLI stöds för närvarande inte.

## <a name="next-steps"></a>Nästa steg

- [Översikt över säkerhetsfunktioner i Azure Backup](security-overview.md)
