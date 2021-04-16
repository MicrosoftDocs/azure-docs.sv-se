---
title: Översikt över Recovery Services-valv
description: En översikt över Recovery Services-valv.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 2f2018f0f3d3135d632418c2e591e6ad938d62d2
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517461"
---
# <a name="recovery-services-vaults-overview"></a>Översikt över Recovery Services-valv

I den här artikeln beskrivs funktionerna i ett Recovery Services-valv. Ett Recovery Services-valv är en lagringsentitet i Azure som innehåller data. Data är vanligtvis kopior av data eller konfigurationsinformation för virtuella datorer ,arbetsbelastningar, servrar eller arbetsstationer. Du kan använda Recovery Services-valv för att lagra säkerhetskopierade data för olika Azure-tjänster, till exempel virtuella IaaS-datorer (Linux eller Windows) och Azure SQL databaser. Recovery Services-valv stöder System Center DPM, Windows Server, Azure Backup Server med mera. Med Recovery Services-valv är det enkelt att organisera dina säkerhetskopierade data samtidigt som du minimerar hanteringskostnaden. Recovery Services-valv baseras på Azure Resource Manager av Azure, som innehåller funktioner som:

- **Förbättrade funktioner för att skydda säkerhetskopierade data:** Med Recovery Services-valv Azure Backup säkerhetsfunktioner för att skydda molnsäkerhetskopior. Säkerhetsfunktionerna ser till att du kan skydda dina säkerhetskopior och återställa data på ett säkert sätt, även om produktions- och säkerhetskopieringsservrar komprometteras. [Läs mer](backup-azure-security-feature.md)

- **Central övervakning för din hybrid-IT-miljö:** Med Recovery Services-valv kan du övervaka inte bara dina virtuella [Azure IaaS-datorer](backup-azure-manage-vms.md) utan även dina lokala tillgångar från en central portal. [](backup-azure-manage-windows-server.md#manage-backup-items) [Läs mer](backup-azure-monitoring-built-in-monitor.md)

- **Rollbaserad åtkomstkontroll i Azure (Azure RBAC):** Azure RBAC ger en mer fullständig åtkomsthanteringskontroll i Azure. [Azure tillhandahåller olika inbyggda roller, och](../role-based-access-control/built-in-roles.md)Azure Backup har tre [inbyggda roller för att hantera återställningspunkter.](backup-rbac-rs-vault.md) Recovery Services-valv är kompatibla med Azure RBAC, vilket begränsar åtkomsten till säkerhetskopiering och återställning till den definierade uppsättningen användarroller. [Läs mer](backup-rbac-rs-vault.md)

- **Mjuk borttagning:** Med mjuk borttagning, även om en illvillig aktör tar bort en säkerhetskopia (eller om säkerhetskopierade data tas bort av misstag), bevaras säkerhetskopierade data i ytterligare 14 dagar, vilket gör att säkerhetskopieringsobjektet kan återställas utan dataförlust. De ytterligare 14 dagarnas kvarhållning för säkerhetskopierade data i läget "mjuk borttagning" medför inte någon kostnad för dig. [Läs mer](backup-azure-security-feature-cloud.md).

- **Återställning mellan regioner:** Med återställning mellan regioner (CRR) kan du återställa virtuella Azure-datorer i en sekundär region, som är en länkad Azure-region. Genom att aktivera den här funktionen på [valvnivå](backup-create-rs-vault.md#set-cross-region-restore)kan du återställa replikerade data i den sekundära regionen när du vill. På så sätt kan du återställa data för sekundära regioner för granskningsefterlevnad och under avbrottsscenarier, utan att vänta på att Azure ska deklarera en katastrof (till skillnad från GRS-inställningarna för valvet). [Läs mer](backup-azure-arm-restore-vms.md#cross-region-restore).

## <a name="storage-settings-in-the-recovery-services-vault"></a>Lagringsinställningar i Recovery Services-valvet

Ett Recovery Services-valv är en entitet som lagrar säkerhetskopior och återställningspunkter som skapats över tid. Recovery Services-valvet innehåller också de säkerhetskopieringsprinciper som är associerade med de skyddade virtuella datorerna.

- Azure Backup hanterar automatiskt lagringen för valvet. Se hur [lagringsinställningar kan ändras.](./backup-create-rs-vault.md#set-storage-redundancy)

- Mer information om lagringsredundans finns i dessa artiklar om [geo,](../storage/common/storage-redundancy.md#geo-zone-redundant-storage) [lokal och](../storage/common/storage-redundancy.md#locally-redundant-storage) [zonindelade redundans.](../storage/common/storage-redundancy.md#zone-redundant-storage)

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Krypteringsinställningar i Recovery Services-valvet

I det här avsnittet beskrivs de alternativ som är tillgängliga för kryptering av dina säkerhetskopierade data som lagras i Recovery Services-valvet.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Kryptering av säkerhetskopierade data med plattformsbaserade nycklar

Som standard krypteras alla dina data med plattformsbaserade nycklar. Du behöver inte vidta några explicita åtgärder från din slutpunkt för att aktivera den här krypteringen. Det gäller för alla arbetsbelastningar som säkerhetskopieras till Recovery Services-valvet.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Kryptering av säkerhetskopieringsdata med kundhanterade nycklar

Du kan välja att kryptera dina data med hjälp av krypteringsnycklar som ägs och hanteras av dig. Azure Backup kan du använda dina RSA-nycklar som lagras i Azure Key Vault för att kryptera dina säkerhetskopior. Krypteringsnyckeln som används för att kryptera säkerhetskopior kan vara en annan än den som används för källan. Data skyddas med en AES 256-baserad datakrypteringsnyckel (DEK), som i sin tur skyddas med hjälp av dina nycklar. Detta ger dig fullständig kontroll över data och nycklar. För att tillåta kryptering måste Recovery Services-valvet beviljas åtkomst till krypteringsnyckeln i Azure Key Vault. Du kan inaktivera nyckeln eller återkalla åtkomsten när det behövs. Du måste dock aktivera kryptering med hjälp av dina nycklar innan du försöker skydda objekt i valvet.

Läs mer om hur du krypterar dina säkerhetskopierade data [med kund hanterade nycklar.](encryption-at-rest-with-cmk.md)

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) är en anpassad molnkonsult som hjälper dig att optimera användningen av Azure. Den analyserar din Azure-användning och ger rekommendationer i rätt tid för att optimera och skydda dina distributioner. Den ger rekommendationer i fyra kategorier: hög tillgänglighet, säkerhet, prestanda och kostnad.

Azure Advisor rekommendationer [per](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) timme för virtuella datorer som inte säkerhetskopieras, så att du aldrig missar säkerhetskopiering av viktiga virtuella datorer. Du kan också styra rekommendationerna genom att snooza dem.  Du kan välja rekommendationen och aktivera säkerhetskopiering på virtuella datorer på rad genom att ange valvet (där säkerhetskopior ska lagras) och säkerhetskopieringsprincipen (schema för säkerhetskopiering och kvarhållning av säkerhetskopior).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Valvscenarier som stöds och inte stöds](backup-support-matrix.md#vault-support)
- [Vanliga frågor och svar om Vault](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Nästa steg

Använd följande artiklar för att:

- [Backa upp en virtuell IaaS-dator](backup-azure-arm-vms-prepare.md)
- [Back up an Azure Backup Server](backup-azure-microsoft-azure-backup.md)
- [Back up a Windows Server](backup-windows-with-mars-agent.md)
