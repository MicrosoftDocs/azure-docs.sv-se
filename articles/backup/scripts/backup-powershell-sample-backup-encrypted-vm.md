---
title: Exempel på PowerShell-skript – säkerhetskopiera en virtuell Azure-dator
description: I den här artikeln får du lära dig hur du säkerhetskopierar en virtuell Azure-dator med hjälp av ett Azure PowerShell skript exempel.
ms.topic: sample
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 73dc119e8db34aed04ce8926bfa85f557027c8e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97967335"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Säkerhetskopiera en krypterad virtuell Azure-dator med PowerShell

Det här skriptet skapar ett Recovery Services-valv med Geo-redundant lagring (GRS) för en krypterad virtuell Azure-dator. Standardskyddsprincipen tillämpas på valvet. Principen genererar en daglig säkerhets kopiering för den virtuella datorn och behåller varje säkerhets kopia i 365 dagar. Skriptet utlöser även den första återställnings punkten för den virtuella datorn och behåller återställnings punkten i 30 dagar.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Skapar ett Recovery Services valv för att lagra säkerhets kopior. |
| [Set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Anger egenskaper för lagring av säkerhetskopior i Recovery Services-valvet. |
| [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Skapar skyddsprincip med hjälp av schemaläggningsprincip och bevarandeprincip i Recovery Services-valvet. |
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Anger behörigheter för Key Vault så att tjänstens huvudnamn får åtkomst till krypteringsnycklarna. |
| [Aktivera – AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Aktiverar säkerhetskopiering för ett objekt med en angiven skyddsprincip för säkerhetskopiering. |
| [Set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Ändrar en befintlig skyddsprincip för säkerhetskopiering. |
| [Säkerhetskopiera – AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Startar en säkerhets kopiering för ett skyddat Azure Backup-objekt som inte är kopplat till schema för säkerhets kopiering. |
| [Vänta-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Väntar på att ett Azure Backup-jobb ska slutföras. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/new-azureps-module-az).
