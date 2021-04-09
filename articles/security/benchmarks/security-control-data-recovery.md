---
title: Azure säkerhets kontroll – Data återställning
description: Återställning av Azure Security Control-data
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 5006a592b410685ad808ff01112234bc48f5d267
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728104"
---
# <a name="security-control-data-recovery"></a>Säkerhets kontroll: Data återställning

Se till att alla system data, konfigurationer och hemligheter säkerhets kopie ras automatiskt regelbundet.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 9,1 | 10.1 | Kund |

Aktivera Azure Backup och konfigurera säkerhets kopierings källan (virtuella Azure-datorer, SQL Server eller fil resurser) samt önskad frekvens och kvarhållningsperiod.

- [Så här aktiverar du Azure Backup](../../backup/index.yml)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 9,2 | 10,2 | Kund |

Aktivera Azure Backup och mål-VM: er samt önskade frekvens-och kvarhållningsperiod. Säkerhetskopiera kund hanterade nycklar inom Azure Key Vault.

- [Så här aktiverar du Azure Backup](../../backup/index.yml)

- [Så här säkerhetskopierar du nyckel valv nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 9,3 | 10,3 | Kund |

Se till att regelbundet utföra Data återställning av innehåll inom Azure Backup. Testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

- [Återställa filer från säkerhets kopiering av virtuella Azure-datorer](../../backup/backup-azure-restore-files-from-vm.md)

- [Återställa Key Vault-nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 9,4 | 10,4 | Kund |

Vid lokal säkerhetskopiering tillhandahålls kryptering i vila med hjälp av den lösenfras som du anger när du säkerhetskopierar till Azure. För virtuella Azure-datorer krypteras data i vila med hjälp av kryptering för lagringstjänst (SSE). Använd rollbaserad åtkomst kontroll i Azure för att skydda säkerhets kopior och kund hanterade nycklar.  

Aktivera Soft-Delete och rensa skydd i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.  Om Azure Storage används för att lagra säkerhets kopior aktiverar du mjuk borttagning för att spara och återställa data när blobbar eller BLOB-ögonblicksbilder tas bort. 

- [Förstå Azure RBAC](../../role-based-access-control/overview.md)

- [Aktivera Soft-Delete och rensa skydd i Key Vault](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Mjuk borttagning för Azure Storage-blobar](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhets kontroll:  [incident svar](security-control-incident-response.md)