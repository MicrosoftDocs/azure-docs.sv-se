---
title: 'Självstudie: flera virtuella Azure-säkerhetskopieringar med PowerShell'
description: Den här självstudien beskriver hur du säkerhetskopierar flera virtuella Azure-datorer till ett Recovery Services valv med Azure PowerShell.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 53a5df430a3ea57201ecb19bac68ef9f073beb14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980971"
---
# <a name="back-up-azure-vms-with-powershell"></a>Säkerhetskopiera virtuella Azure-datorer med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I den här självstudien beskrivs hur du distribuerar ett [Azure Backup](backup-overview.md) Recovery Services-valv för att säkerhetskopiera flera virtuella Azure-datorer med hjälp av PowerShell.  

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
>
> * Skapa ett Recovery Services valv och ange valv kontexten.
> * Definierar en säkerhetskopieringspolicy
> * applicerar säkerhetskopieringspolicyn för att skydda flera virtuella datorer
> * Utlös ett säkerhets kopierings jobb på begäran för de skyddade virtuella datorerna innan du kan säkerhetskopiera (eller skydda) en virtuell dator måste du slutföra [kraven](backup-azure-arm-vms-prepare.md) för att förbereda din miljö för att skydda dina virtuella datorer.

> [!IMPORTANT]
> Den här självstudien förutsätter att du redan har skapat en resurs grupp och en virtuell Azure-dator.

## <a name="sign-in-and-register"></a>Logga in och registrera

1. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

    ```powershell
    Connect-AzAccount
    ```

2. Första gången du använder Azure Backup måste du registrera Azure Recovery Service-providern i din prenumeration med [register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider). Om du redan har registrerat dig hoppar du över det här steget.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett [Recovery Services valv](backup-azure-recovery-services-vault-overview.md) är en logisk behållare som lagrar säkerhetskopierade data för skyddade resurser, till exempel virtuella Azure-datorer. När ett säkerhets kopierings jobb körs skapas en återställnings punkt i Recovery Services valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

* I den här självstudien skapar du valvet i samma resurs grupp och plats som den virtuella dator som du vill säkerhetskopiera.
* Azure Backup hanterar automatiskt lagring för säkerhetskopierade data. Som standard använder valvet [Geo-redundant lagring (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). GEO-redundans garanterar att säkerhetskopierade data replikeras till en sekundär Azure-region, hundratals mil bort från den primära regionen.

Skapa valvet på följande sätt:

1. Använd  [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)för att skapa valvet. Ange resurs gruppens namn och plats för den virtuella dator som du vill säkerhetskopiera.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det lämpligt att lagra säkerhets kopierings Recovery Services valvet i en variabel.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. Ange valv kontexten med [set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext).

   * Valvets sammanhang är typen av data som skyddas i valvet.
   * När kontexten har angetts gäller den för alla efterföljande cmdletar

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Säkerhetskopiera virtuella Azure-datorer

Säkerhets kopieringar körs enligt det schema som anges i säkerhets kopierings principen. När du skapar ett Recovery Services-valv medföljer standardskydd och principer för kvarhållning.

* Standard skydds principen utlöser ett säkerhets kopierings jobb en gång om dagen vid en viss tidpunkt.
* Principen för standardskydd håller kvar den dagliga återställningspunkten i 30 dagar.

Vi gör följande för att aktivera och säkerhetskopiera den virtuella Azure-datorn i den här självstudien:

1. Ange en behållare i valvet som innehåller dina säkerhets kopierings data med [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Varje virtuell dator för säkerhets kopiering är ett objekt. Om du vill starta ett säkerhets kopierings jobb får du information om den virtuella datorn med [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Kör en säkerhets kopiering på begäran med[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).
    * Det första första säkerhets kopierings jobbet skapar en fullständig återställnings punkt.
    * Efter den första säkerhets kopieringen skapar varje säkerhets kopierings jobb stegvisa återställnings punkter.
    * Inkrementella återställningspunkter är lagrings- och tidseffektiva eftersom de bara överför de ändringar som gjorts sedan den senaste säkerhetskopieringen.

Aktivera och kör säkerhets kopian på följande sätt:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Felsökning

Om du stöter på problem när du säkerhetskopierar den virtuella datorn kan du läsa [artikeln om fel sökning](backup-azure-vms-troubleshoot.md).

### <a name="deleting-a-recovery-services-vault"></a>Ta bort ett Recovery Services-valv

Om du behöver ta bort ett valv tar du först bort återställnings punkterna i valvet och avregistrerar valvet på följande sätt:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Nästa steg

* [Granska](backup-azure-vms-automation.md) en mer detaljerad genom gång av hur du säkerhetskopierar och återställer virtuella Azure-datorer med PowerShell.
* [Hantera och övervaka virtuella Azure-datorer](backup-azure-manage-vms.md)
* [Återställa virtuella Azure-datorer](backup-azure-arm-restore-vms.md)
