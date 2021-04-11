---
title: Stöd för Arkiv nivå (för hands version)
description: Lär dig mer om Arkiv nivå stöd för Azure Backup
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 322bc9d7e2160cc9156c793859b9fda833b3df09
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563981"
---
# <a name="archive-tier-support-preview"></a>Stöd för Arkiv nivå (för hands version)

Kunder förlitar sig på Azure Backup för lagring av säkerhets kopierings data, inklusive Long-Term kvarhållning av säkerhets kopierings data med kvarhållning som definieras av organisationens regler för efterlevnad. I de flesta fall används tidigare säkerhetskopierade data sällan och lagras bara för kompatibla behov.

Azure Backup stöder säkerhets kopiering av långsiktiga kvarhållning punkter på Arkiv nivån, förutom ögonblicks bilder och standard nivån.

## <a name="scope-for-preview"></a>Omfång för för hands version

Arbets belastningar som stöds:

- Virtuella Azure-datorer
  - Endast månads Visa och årliga återställnings punkter. Dagliga och veckovis återställnings punkter stöds inte.
  - Ålder >= 3 månader i Vault-Standards nivå
  - Kvarhållen kvar >= 6 månader
  - Inga aktiva dagliga och vecko Visa beroenden
- SQL Server på virtuella Azure-datorer
  - Endast fullständiga återställnings punkter. Loggar och differentiella stöds inte.
  - Ålder >= 45 dagar i Vault-Standard nivån
  - Kvarhållen kvar >= 6 månader
  - Inga beroenden

Klienter som stöds:

- Funktionen tillhandahålls med PowerShell

>[!NOTE]
>Arkiv nivå stöd för virtuella Azure-datorer och SQL Server på virtuella Azure-datorer är i begränsad offentlig för hands version med begränsade registreringar. Om du vill registrera dig för Archive support använder du den här [länken](https://aka.ms/ArchivePreviewInterestForm).

## <a name="get-started-with-powershell"></a>Kom igång med PowerShell

1. Kör följande kommando i PowerShell:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.0.0-preview -AllowPrerelease -force
    ```

1. Anslut till Azure med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
1. Logga in på din prenumeration:

   `Set-AzContext -Subscription "SubscriptionName"`

1. Hämta valvet:

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. Hämta listan med säkerhets kopierings objekt:

    `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM/AzureWorkload" -WorkloadType "AzureVM/MSSQL"`

1. Hämta säkerhets kopierings objekt.

    - För virtuella Azure-datorer:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - För SQL Server på virtuella Azure-datorer:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>Använd PowerShell

### <a name="check-archivable-recovery-points"></a>Kontrol lera återställnings bara återställnings punkter

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

Detta visar en lista över alla återställnings punkter som är associerade med ett visst säkerhets kopierings objekt som är redo att flyttas till arkivet.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>Kontrol lera varför en återställnings punkt inte kan flyttas till arkivet

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Var `$rp[0]` finns den återställnings punkt som du vill kontrol lera varför den inte kan arkiveras.

Exempel på utdata:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Kontrol lera Rekommenderad uppsättning av arkiverade punkter (endast för virtuella Azure-datorer)

Återställnings punkterna som är associerade med en virtuell dator är stegvisa. När en viss återställnings punkt flyttas till arkivet, konverteras den till en fullständig säkerhets kopia och flyttas sedan till arkivet. Det kostnads besparingar som är kopplat till att flytta till arkivet beror på data källans omsättning.

Azure Backup har en rekommenderad uppsättning återställnings punkter som kan resultera i kostnads besparingar om de flyttas tillsammans.

>[!NOTE]
>Kostnads besparingarna beror på olika orsaker och kanske inte är samma för två instanser.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>Flytta till Arkiv

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Detta kommando flyttar en återställnings punkt som kan arkiveras. Det returnerar ett jobb som kan användas för att spåra flyttnings åtgärden både från portalen och med PowerShell.

### <a name="view-archived-recovery-points"></a>Visa arkiverade återställnings punkter

Det här kommandot returnerar alla arkiverade återställnings punkter.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>Återställa med PowerShell

Azure Backup tillhandahåller en integrerad Restore-metod för återställnings punkter i arkivet.

Den integrerade återställningen är en två stegs process. I det första steget ingår återuppväcks de återställnings punkter som lagras i arkivet och som tillfälligt lagrar dem på nivån valv, standard under en varaktighet (kallas även ÅTERUPPVÄCKNING varaktighet) från en period av 10 till 30 dagar. Standardvärdet är 15 dagar. Det finns två olika prioriteter för ÅTERUPPVÄCKNING – standard och hög prioritet. Läs mer om [ÅTERUPPVÄCKNING-prioritet](../storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier).

>[!NOTE]
>
>- ÅTERUPPVÄCKNING varaktigheten kan inte ändras och de återtorkade återställnings punkterna stannar kvar på standard nivån för ÅTERUPPVÄCKNING varaktighet.
>- Det ytterligare steget i ÅTERUPPVÄCKNING ådrar sig kostnaderna.

Mer information om olika återställnings metoder för Azure Virtual Machines finns i [återställa en virtuell Azure-dator med PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Följ [dessa steg](backup-azure-sql-automation.md#restore-sql-dbs)om du vill återställa SQL Server. De ytterligare parametrar som krävs är **RehydrationPriority** och **RehydrationDuration**.

### <a name="view-jobs-from-powershell"></a>Visa jobb från PowerShell

Om du vill visa flytt-och återställnings jobben använder du följande PowerShell-cmdlet:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>Använda portalen

### <a name="check-archived-recovery-point"></a>Kontrol lera Arkiverad återställnings punkt

Nu kan du Visa alla återställnings punkter som har flyttats till arkivet.

![Alla återställnings punkter.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>Återställa i portalen

För återställnings punkter som har flyttats till arkivet kräver återställningen att du lägger till parametrarna för ÅTERUPPVÄCKNING varaktighet och ÅTERUPPVÄCKNING prioritet.

![Återställ i portalen.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>Visa jobb i portalen

![Visa jobb i portalen.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>Ändra skydd

Det finns två sätt att ändra skyddet för en data Källa:

- Ändra en befintlig princip
- Skydda data källan med en ny princip

I båda fallen tillämpas den nya principen på alla äldre återställnings punkter, som finns på standard nivån och på Arkiv nivå. Så äldre återställnings punkter kan tas bort om principen ändras.

När återställnings punkterna flyttas till arkivet, omfattas de av en tidig borttagnings period på 180 dagar. Avgifterna beräknas proportionellt. Om en återställnings punkt som inte finns i arkivet för 180 dagar tas bort, kostar det en kostnad som motsvarar 180 minus antalet dagar som den har spenderat på standard nivån.

Återställnings punkter som inte har begått i arkivet i minst sex månader kommer att ådra sig en tidig borttagnings kostnad vid radering.

## <a name="stop-protection-and-delete-data"></a>Stoppa skyddet och ta bort data

Stoppa skyddet och ta bort data tar bort alla återställnings punkter. Borttagning av återställnings punkter leder till tidig borttagnings kostnad för återställnings punkter i arkivet som inte finns under en varaktighet på 180 dagar på Arkiv nivå.

## <a name="error-codes-and-troubleshooting-steps"></a>Felkoder och fel söknings steg

Det finns flera felkoder som kan uppstå när en återställnings punkt inte kan flyttas till arkivet.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Fel meddelande** -Recovery-Point typen är inte giltig för Arkiv flyttning

**Beskrivning** – den här felkoden visas när den valda återställnings punkt typen inte är berättigad att flyttas till arkivet.

**Rekommenderad åtgärd** – kontrol lera berättigande för återställnings punkten [här](#scope-for-preview)

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Fel meddelande** -Recovery-Point som har aktiva beroenden för återställning är inte berättigade för Arkiv flyttning

**Beskrivning –** Den valda återställnings punkten har aktiva beroenden och kan därför inte flyttas till arkivet.

**Rekommenderad åtgärd** – kontrol lera berättigande för återställnings punkten [här](#scope-for-preview)

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Fel meddelande** -Recovery-Point inte är kvalificerat för att arkivera flytta som livs längd som används i valv-standard-nivån är mindre än minimi kravet

**Beskrivning** – återställnings punkten måste vara i standard-nivån i minst tre månader för Azure Virtual Machines och 45 dagar för SQL Server på virtuella Azure-datorer

**Rekommenderad åtgärd** – kontrol lera berättigande för återställnings punkten [här](#scope-for-preview)

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Fel meddelande** -Recovery-Point återstående livs längd är mindre än det minimum som krävs.

**Beskrivning** – den minsta livs längd som krävs för en återställnings punkt för berättigande av Arkiv flyttning är sex månader.

**Rekommenderad åtgärd** – kontrol lera berättigande för återställnings punkten [här](#scope-for-preview)

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Fel meddelande** -Recovery-Point inte är kvalificerat för Arkiv flyttning eftersom det redan har flyttats till Arkiv nivån

**Beskrivning** – den valda återställnings punkten finns redan i arkivet. Det är därför inte behörigt att flyttas till arkivet.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Fel meddelande** -data källans typ är inte kvalificerad för rekommendations-API.

**Beskrivning** – rekommendations-API: t gäller endast för virtuella Azure-datorer. Det gäller inte för den valda data källans typ.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Fel meddelande** : återställnings punkten har redan rehydratiserats. ÅTERUPPVÄCKNING tillåts inte för denna RP.

**Beskrivning** – den valda återställnings punkten har redan rehydratiserats.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Fel meddelande** -återställnings punkt är inte kvalificerad för arkivering av arkiv.

**Beskrivning** – den valda återställnings punkten är inte tillgänglig för Arkiv flyttning.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**Fel** **meddelande** -Arkiv återställnings punkt har inte rehydratiseras. Försök att återställa igen när ÅTERUPPVÄCKNING har slutförts på Arkiv RP.

**Beskrivning** – återställnings punkten är inte rehydratiserad. Försök att återställa efter återuppväcks av återställnings punkten.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**Fel** **meddelande**-ÅTERUPPVÄCKNING stöds bara för Arkiv återställnings punkter-ÅTERUPPVÄCKNING stöds bara för Arkiv återställnings punkter

**Beskrivning** – ÅTERUPPVÄCKNING tillåts inte för den valda återställnings punkten.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Fel meddelande** – ÅTERUPPVÄCKNING är redan In-Progress för Arkiv återställnings punkt.

**Beskrivning** – ÅTERUPPVÄCKNING för den valda återställnings punkten pågår redan.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Fel meddelandet** -återställnings punkten kan inte flyttas till Arkiv nivån på grund av otillräcklig kvarhållningsperiod som angetts i principen

**Rekommenderad åtgärd** – uppdatera princip på det skyddade objektet med lämplig kvarhållning och försök igen.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Fel meddelande** -vi avgör fortfarande om den här återställnings punkten kan flyttas.

**Beskrivning** – den flyttade återställnings punktens flyttnings punkt har ännu inte fastställts.

**Rekommenderad åtgärd** – kontrol lera igen efter en stund.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>Vad händer med att arkivera återställnings punkter om jag slutar skydda och behåller data?

Återställnings punkten finns kvar i arkivet alltid. Mer information finns i [effekten av stopp skydd på återställnings punkter](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

## <a name="next-steps"></a>Nästa steg

- [Priser för Azure Backup](azure-backup-pricing.md)