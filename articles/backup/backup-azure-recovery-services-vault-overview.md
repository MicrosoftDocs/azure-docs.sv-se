---
title: Översikt över Recovery Services-valv
description: En översikt och jämförelse mellan Recovery Services valv och Azure Backup valv.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 5334bc2aea5ddbf734c3fd3ef314ff4da609d61d
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587760"
---
# <a name="recovery-services-vaults-overview"></a>Översikt över Recovery Services-valv

I den här artikeln beskrivs funktionerna i ett Recovery Services-valv. Ett Recovery Services-valv är en lagrings enhet i Azure som huserar data. Data är vanligt vis kopior av data eller konfigurations information för virtuella datorer (VM), arbets belastningar, servrar eller arbets stationer. Du kan använda Recovery Services valv för att lagra säkerhets kopierings data för olika Azure-tjänster, till exempel virtuella IaaS-datorer (Linux eller Windows) och Azure SQL-databaser. Recovery Services valv stöder System Center DPM, Windows Server, Azure Backup Server med mera. Med Recovery Services-valv är det enkelt att organisera dina säkerhetskopierade data samtidigt som du minimerar hanteringskostnaden. Recovery Services-valv baseras på Azure Resource Manager modell för Azure, som innehåller funktioner som:

- **Förbättrade funktioner för att skydda säkerhets kopierings data**: med Recovery Services-valv tillhandahåller Azure Backup säkerhetsfunktioner för säkerhets kopiering av molnet. Säkerhetsfunktionerna garanterar att du kan skydda dina säkerhets kopior och återställa data på ett säkert sätt, även om produktions-och säkerhets kopierings servrar komprometteras. [Läs mer](backup-azure-security-feature.md)

- **Central övervakning för din hybrid IT-miljö**: med Recovery Services valv kan du bara övervaka dina [virtuella Azure IaaS-datorer](backup-azure-manage-vms.md) utan även dina [lokala till gångar](backup-azure-manage-windows-server.md#manage-backup-items) från en central Portal. [Läs mer](backup-azure-monitoring-built-in-monitor.md)

- **Rollbaserad Access Control (RBAC)**: RBAC ger detaljerad åtkomst hanterings kontroll i Azure. [Azure tillhandahåller olika inbyggda roller](../role-based-access-control/built-in-roles.md)och Azure Backup har tre [inbyggda roller för att hantera återställnings punkter](backup-rbac-rs-vault.md). Recovery Services-valv är kompatibla med RBAC, vilket begränsar säkerhets kopiering och återställning av åtkomst till den definierade uppsättningen användar roller. [Läs mer](backup-rbac-rs-vault.md)

- **Mjuk borttagning**: med mjuk borttagning, även om en obehörig aktör tar bort en säkerhets kopia (eller säkerhetskopierade data tas bort av misstag), bevaras säkerhets kopierings data i ytterligare 14 dagar, vilket gör att det inte går att återställa säkerhets kopierings objekt utan data förlust. De ytterligare 14 dagarna för kvarhållning av säkerhets kopierings data i läget "mjuk borttagning" kostar ingen kostnad för kunden. [Läs mer](backup-azure-security-feature-cloud.md).

- **Återställning mellan regioner**: kors regions återställning (CRR) gör att du kan återställa virtuella Azure-datorer i en sekundär region, som är en Azure-kopplad region. Om Azure deklarerar en katastrof i den primära regionen, är de data som replikeras i den sekundära regionen tillgänglig för återställning i den sekundära regionen för att minimera den verkliga stillestånds tiden i den primära regionen för deras miljö. [Läs mer](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Lagrings inställningar i Recovery Services valvet

Ett Recovery Services-valv är en entitet som lagrar säkerhets kopior och återställnings punkter som skapats med tiden. Recovery Services valvet innehåller också de säkerhets kopierings principer som är associerade med de skyddade virtuella datorerna.

- Azure Backup hanterar automatiskt lagring för valvet. Se hur [lagrings inställningarna kan ändras](./backup-create-rs-vault.md#set-storage-redundancy).

- Mer information om redundans finns i de här artiklarna om [geo](../storage/common/storage-redundancy.md) -och [lokal](../storage/common/storage-redundancy.md) redundans.

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Krypterings inställningar i Recovery Services valvet

I det här avsnittet beskrivs de alternativ som är tillgängliga för kryptering av säkerhets kopierings data som lagras i Recovery Services valvet.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Kryptering av säkerhets kopierings data med hjälp av plattforms hanterade nycklar

Som standard krypteras alla dina data med hjälp av plattforms hanterade nycklar. Du behöver inte vidta någon uttrycklig åtgärd från din sida för att aktivera den här krypteringen. Den gäller för alla arbets belastningar som säkerhets kopie ras till Recovery Services-valvet.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Kryptering av säkerhets kopierings data med Kundhanterade nycklar

Du kan välja att kryptera dina data med hjälp av krypterings nycklar som ägs och hanteras av dig. Med Azure Backup kan du använda dina RSA-nycklar som lagras i Azure Key Vault för kryptering av dina säkerhets kopior. Krypterings nyckeln som används för kryptering av säkerhets kopior kan skilja sig från den som används för källan. Data skyddas med en AES 256-baserad data krypterings nyckel (DEK), som i sin tur skyddas med hjälp av dina nycklar. Detta ger dig fullständig kontroll över data och nycklar. För att tillåta kryptering måste Recovery Servicess valvet beviljas åtkomst till krypterings nyckeln i Azure Key Vault. Du kan inaktivera nyckeln eller återkalla åtkomst när det behövs. Du måste dock aktivera kryptering med dina nycklar innan du försöker skydda några objekt i valvet.

Läs mer om hur du krypterar dina säkerhetskopierade data [med Kundhanterade nycklar](encryption-at-rest-with-cmk.md).

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) är en anpassad moln konsult som hjälper dig att optimera användningen av Azure. Den analyserar din Azure-användning och ger rekommendationer som hjälper dig att optimera och skydda dina distributioner. Det ger rekommendationer i fyra kategorier: hög tillgänglighet, säkerhet, prestanda och kostnad.

Azure Advisor ger [rekommendationer](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) för virtuella datorer som inte har säkerhetskopierats, så att du aldrig har säkerhetskopierat viktiga virtuella datorer. Du kan också styra rekommendationerna genom att försätta dem i vilo läge.  Du kan välja rekommendationen och aktivera säkerhets kopiering på virtuella datorer på en rad genom att ange valvet (där säkerhets kopior ska lagras) och säkerhets kopierings policyn (schema för säkerhets kopior och kvarhållning av säkerhets kopior).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Scenarier som stöds och scenarier som inte stöds](backup-support-matrix.md#vault-support)
- [Vanliga frågor och svar om valvet](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Nästa steg

Använd följande artiklar för att:

- [Säkerhetskopiera en virtuell IaaS-dator](backup-azure-arm-vms-prepare.md)
- [Säkerhetskopiera en Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Säkerhetskopiera en Windows Server](backup-windows-with-mars-agent.md)
