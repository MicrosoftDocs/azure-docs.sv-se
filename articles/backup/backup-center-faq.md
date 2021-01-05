---
title: Säkerhets kopierings Center – vanliga frågor och svar
description: I den här artikeln får du svar på vanliga frågor om Backup Center
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 5befa39411c22253bfccc689d8b5c5967a8cd759
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858628"
---
# <a name="backup-center---frequently-asked-questions"></a>Säkerhets kopierings Center – vanliga frågor och svar

## <a name="management"></a>Hantering

### <a name="can-backup-center-be-used-across-tenants"></a>Kan backup Center användas mellan klienter?

Ja, om du använder [Azure-Lighthouse](../lighthouse/overview.md) och har delegerad åtkomst till prenumerationer över olika klienter, kan du använda Backup Center som ett enda fönster i glas för att hantera säkerhets kopieringar mellan klienter.

### <a name="can-backup-center-be-used-to-manage-both-recovery-services-vaults-and-backup-vaults"></a>Kan backup Center användas för att hantera både Recovery Services valv och säkerhets kopierings valv?

Ja, backup Center kan hantera både [Recovery Services valv](./backup-azure-recovery-services-vault-overview.md) och [säkerhets kopierings valv](backup-vault-overview.md).

### <a name="is-there-a-delay-before-data-surfaces-in-backup-center"></a>Finns det en fördröjning innan dataytor i Backup Center?

Backup Center syftar till att tillhandahålla information i real tid. Det kan finnas några sekunders fördröjningar mellan hur lång tid en enhet visas på en enskild valv-skärm och den tid då samma entitet visas i Backup Center.

## <a name="configuration"></a>Konfiguration

### <a name="do-i-need-to-configure-anything-to-see-data-in-backup-center"></a>Måste jag konfigurera något för att se data i Backup Center?

Nej. Säkerhets kopierings Center kommer att vara klart från lådan. Om du vill visa [säkerhets kopierings rapporter](./configure-reports.md) under säkerhets kopierings Center måste du dock konfigurera rapportering för dina valv.

### <a name="do-i-need-to-have-any-special-permissions-to-use-backup-center"></a>Måste jag ha några särskilda behörigheter för att använda Backup Center?

Backup Center eftersom det inte behövs några nya behörigheter. Så länge du har rätt nivå av Azure RBAC-åtkomst för de resurser som du hanterar kan du använda Backup Center för dessa resurser. Om du till exempel vill visa information om dina säkerhets kopior behöver du **läsa in läsare** till dina valv. Om du vill konfigurera säkerhets kopiering och utföra andra säkerhets kopierings åtgärder behöver du rollen **säkerhets kopierings deltagare** eller **ansvariga för säkerhets kopiering** . Lär dig mer om [Azure-roller för Azure Backup](./backup-rbac-rs-vault.md). 

Om du använder [säkerhets kopierings rapporter](./configure-reports.md) under säkerhets kopierings Center behöver du åtkomst till Log Analytics arbets ytor som dina valv skickar data till, för att visa rapporter för dessa valv.

## <a name="pricing"></a>Prissättning

### <a name="do-i-need-to-pay-anything-extra-to-use-backup-explorer"></a>Måste jag betala något extra för att använda Backup Explorer?

För närvarande finns det inga ytterligare kostnader (förutom dina kostnader för säkerhets kopiering) för att använda säkerhets kopierings Center. Men om du använder [säkerhets kopierings rapporter](./configure-reports.md) under säkerhets kopierings Center, finns det en [kostnad](https://azure.microsoft.com/pricing/details/monitor/) för att använda Azure Monitor loggar för säkerhets kopierings rapporter.

## <a name="next-steps"></a>Nästa steg

Läs andra vanliga frågor och svar:

* [Vanliga frågor om Recovery Services valv](./backup-azure-backup-faq.md)
* [Vanliga frågor om säkerhets kopiering av virtuella Azure-datorer](./backup-azure-vm-backup-faq.md)