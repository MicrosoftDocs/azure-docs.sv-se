---
title: Snabb start – säkerhetskopiera en virtuell dator med PowerShell
description: I den här snabb starten lär du dig hur du säkerhetskopierar dina virtuella Azure-datorer med Azure PowerShell-modulen.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 81005a3032355f566aef2a6794cf2ec5038b0dd8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737124"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Säkerhetskopiera en virtuell dator med PowerShell

Modulen [Azure POWERSHELL AZ](/powershell/azure/new-azureps-module-az) används för att skapa och hantera Azure-resurser från kommando raden eller i skript.

[Azure Backup](backup-overview.md) säkerhetskopierar lokala datorer och appar och virtuella Azure-datorer. Den här artikeln visar hur du säkerhetskopierar en virtuell Azure-dator med AZ-modulen. Alternativt kan du säkerhetskopiera en virtuell dator med hjälp av [Azure CLI](quick-backup-vm-cli.md)eller i [Azure Portal](quick-backup-vm-portal.md).

I den här snabbstarten sker säkerhetskopieringen på en befintlig virtuell Azure-dator. Om du behöver skapa en virtuell dator kan du [skapa en virtuell dator med Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fpowershell%2fmodule%2ftoc.json).

Den här snabb starten kräver Azure PowerShell AZ-modul version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Logga in och registrera

1. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

    ```powershell
    Connect-AzAccount
    ```

2. Första gången du använder Azure Backup måste du registrera Azure Recovery Service-providern i din prenumeration med [register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider), enligt följande:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett [Recovery Services valv](backup-azure-recovery-services-vault-overview.md) är en logisk behållare som lagrar säkerhetskopierade data för skyddade resurser, till exempel virtuella Azure-datorer. När ett säkerhets kopierings jobb körs skapas en återställnings punkt i Recovery Services valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

När du skapar valvet:

- För resurs gruppen och platsen anger du resurs grupp och plats för den virtuella dator som du vill säkerhetskopiera.
- Om du använde det här [exempel skriptet](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm?toc=%2fpowershell%2fmodule%2ftoc.json) för att skapa den virtuella datorn är resurs gruppen **myResourceGroup**, den virtuella datorn är **_MyVM_* och resurserna finns i **WestEurope** -regionen.
- Azure Backup hanterar automatiskt lagring för säkerhetskopierade data. Som standard använder valvet [Geo-redundant lagring (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). GEO-redundans garanterar att säkerhetskopierade data replikeras till en sekundär Azure-region, hundratals mil bort från den primära regionen.

Skapa ett valv nu:

1. Använd  [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) för att skapa valvet:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. Ange valv kontexten med [set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)enligt följande:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. Ändra LRS/GRS (Storage redundans Configuration) för valvet med [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty)enligt följande:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > Lagrings redundans kan bara ändras om det inte finns några säkerhets kopierings objekt skyddade i valvet.

## <a name="enable-backup-for-an-azure-vm"></a>Aktivera säkerhetskopiering för en virtuell Azure-dator

Du aktiverar säkerhets kopiering för en virtuell Azure-dator och anger en säkerhets kopierings princip.

- Principen definierar när säkerhets kopieringar körs och hur länge återställnings punkter som skapas av säkerhets kopiorna ska behållas.
- Standard skydds principen kör en säkerhets kopiering en gång per dag för den virtuella datorn och behåller de skapade återställnings punkterna i 30 dagar. Du kan använda den här standard principen för att snabbt skydda den virtuella datorn.

Aktivera säkerhets kopiering på följande sätt:

1. Ange först standard principen med [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy):

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. Aktivera säkerhets kopiering av virtuella datorer med [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection). Ange principen, resurs gruppen och namnet på den virtuella datorn.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Starta ett säkerhetskopieringsjobb

Säkerhets kopieringar körs enligt det schema som anges i säkerhets kopierings principen. Du kan också köra en säkerhets kopiering på begäran:

- Det första första säkerhets kopierings jobbet skapar en fullständig återställnings punkt.
- Efter den första säkerhets kopieringen skapar varje säkerhets kopierings jobb stegvisa återställnings punkter.
- Inkrementella återställningspunkter är lagrings- och tidseffektiva eftersom de bara överför de ändringar som gjorts sedan den senaste säkerhetskopieringen.

Om du vill köra en säkerhets kopiering på begäran använder du [säkerhets kopierings-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

- Du anger en behållare i valvet som innehåller dina säkerhets kopierings data med [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer).
- Varje virtuell dator som säkerhetskopieras hanteras som ett objekt. Om du vill starta ett säkerhets kopierings jobb får du information om den virtuella datorn med [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem).

Kör ett säkerhets kopierings jobb på begäran på följande sätt:

1. Ange behållaren, hämta information om virtuella datorer och kör säkerhets kopieringen.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. Du kan behöva vänta upp till 20 minuter eftersom det första säkerhets kopierings jobbet skapar en fullständig återställnings punkt. Övervaka jobbet enligt beskrivningen i nästa procedur.

## <a name="monitor-the-backup-job"></a>Övervaka säkerhetskopieringen

1. Kör [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) för att övervaka jobb statusen.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    Utdata liknar följande exempel som visar jobbet som **pågående**:

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. När jobbets status är **slutförd**, skyddas den virtuella datorn och har en fullständig återställnings punkt.

## <a name="clean-up-the-deployment"></a>Rensa distributionen

Om du inte längre behöver säkerhetskopiera den virtuella datorn kan du rensa den.

- Om du vill prova att återställa den virtuella datorn hoppar du över rensningen.
- Om du använde en befintlig virtuell dator kan du hoppa över den slutliga cmdleten [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) så att du lämnar resurs gruppen och den virtuella datorn på plats.

Inaktivera skydd, ta bort återställnings punkterna och valvet. Ta sedan bort resurs gruppen och tillhör ande virtuella dator resurser enligt följande:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett Recovery Services-valv, aktiverat skydd på en virtuell dator och skapat den första återställningspunkten.

- [Lär dig hur](tutorial-backup-vm-at-scale.md) du säkerhetskopierar virtuella datorer i Azure Portal.
- [Lär dig hur](tutorial-restore-disk.md) du snabbt återställer en virtuell dator