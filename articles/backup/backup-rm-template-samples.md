---
title: Azure Resource Manager-mallar
description: Azure Resource Manager mallar för användning med Recovery Services-valv och Azure Backup funktioner
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: a4c2f444cb821f7979571b9d777895a59450e7c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96309587"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Resource Manager-mallar för Azure Backup

I följande tabell finns länkar till Azure Resource Manager-mallar du kan använda för Recovery Services-valv och funktioner i Azure Backup. Mer information om JSON-syntaxen och JSON-egenskaperna finns i [Microsoft.RecoveryServices-resurstyper](/azure/templates/microsoft.recoveryservices/allversions).

| Mall | Beskrivning |
|---|---|
|**Recovery Services-valv** | |
| [Skapa ett Recovery Services-valv](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Skapa ett Recovery Services-valv. Du kan använda valvet för Azure Backup och Azure Site Recovery. |
|**Säkerhetskopiera virtuella datorer**| |
| [Säkerhetskopiera virtuella Resource Manager-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Använd det befintliga Recovery Services-valvet och Backup-principen för säkerhetskopiering av virtuella Resource Manager-datorer i samma resursgrupp.|
| [Säkerhetskopiera virtuella IaaS-datorer till ett Recovery Services-valv](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Mall för säkerhetskopiering av klassiska virtuella datorer och virtuella Resource Manager-datorer. |
| [Skapa en princip för veckovis säkerhetskopiering av virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Mallen skapar Recovery Services valv och en princip för veckovis säkerhets kopiering, som används för att säkerhetskopiera klassiska och Resource Manager-virtuella datorer.|
| [Skapa en princip för daglig säkerhetskopiering av virtuella IaaS-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Mallen skapar Recovery Services valv och en princip för daglig säkerhets kopiering som används för att säkerhetskopiera klassiska virtuella datorer och virtuella Resource Manager-datorer.|
| [Distribuera virtuella Windows Server-datorer med säkerhetskopiering aktiverad](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Mallen skapar en virtuell Windows Server-dator och ett Recovery Services-valv med standardprincipen för säkerhetskopiering aktiverad.|
|**Övervaka säkerhets kopierings jobb** |  |
| [Använda Azure Monitor-loggar med Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Mallen distribuerar Azure Monitor loggar med Azure Backup, vilket gör att du kan övervaka säkerhets kopierings-och återställnings jobb, säkerhets kopierings aviseringar och moln lagring som används i dina Recovery Services-valv.|  
|**Säkerhetskopiera SQL Server i Azure VM** |  |
| [Säkerhetskopiera SQL Server i Azure VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | Mall skapar ett Recovery Services valv och en princip för säkerhets kopiering av arbets belastning. Den registrerar den virtuella datorn med Azure Backup tjänsten och konfigurerar skyddet på den virtuella datorn. För närvarande fungerar det bara för SQL Gallery-avbildningar. |
|**Säkerhetskopiera Azure-filresurser** |  |
| [Säkerhetskopiera Azure-filresurser](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-file-share) | Den här mallen konfigurerar skydd för en befintlig Azure-filresurs genom att ange lämplig information för Recovery Services valvet och säkerhets kopierings principen. Du kan också skapa ett nytt Recovery Services valv och en säkerhets kopierings princip och registrera lagrings kontot som innehåller fil resursen i Recovery Services-valvet. |
|   |   |
