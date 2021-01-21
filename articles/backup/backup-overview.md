---
title: Vad är Azure Backup?
description: Ger en översikt över den Azure Backup tjänsten och hur den bidrar till din strategi för affärs kontinuitet och haveri beredskap (BCDR).
ms.topic: overview
ms.date: 04/24/2019
ms.custom: mvc
ms.openlocfilehash: 6a30e31dd1462e427faf64966a38c94f9fa56df6
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624480"
---
# <a name="what-is-the-azure-backup-service"></a>Vad är Azure Backup-tjänsten?

Azure Backup tillhandahåller enkla, säkra och kostnadseffektiva lösningar för att säkerhetskopiera dina data och återställa dem från Microsoft Azure-molnet.

> [!VIDEO https://www.youtube.com/embed/elODShatt-c]

## <a name="what-can-i-back-up"></a>Vad kan jag säkerhetskopiera?

- **Lokala** säkerhets kopierings filer, mappar, system tillstånd med hjälp av [Microsoft Azure Recovery Services (mars) Agent](backup-support-matrix-mars-agent.md). Eller Använd DPM-eller Azure Backup Server-agenten (MABS) för att skydda lokala virtuella datorer ([Hyper-V](back-up-hyper-v-virtual-machines-mabs.md) och [VMware](backup-azure-backup-server-vmware.md)) och andra [lokala arbets belastningar](backup-mabs-protection-matrix.md)
- **Virtuella Azure-datorer**  -  [Säkerhetskopiera hela virtuella Windows-eller Linux-datorer](backup-azure-vms-introduction.md) (med hjälp av säkerhets kopierings tillägg) eller säkerhetskopiera filer, mappar och system tillstånd med hjälp av [mars-agenten](backup-azure-manage-mars.md).
- **Azure-Managed disks**  -  [Säkerhetskopiera Azure Managed disks (för hands version)](backup-managed-disks.md)
- **Azure Files resurser**  -  [Säkerhetskopiera Azure-filresurser till ett lagrings konto](backup-afs.md)
- **SQL Server på virtuella Azure-datorer**  -   [Säkerhetskopiera SQL Server databaser som körs på virtuella Azure-datorer](backup-azure-sql-database.md)
- **SAP HANA-databaser på virtuella Azure-datorer**  -  [Säkerhetskopiera SAP HANA databaser som körs på virtuella Azure-datorer](backup-azure-sap-hana-database.md)
- **Azure Database for PostgreSQL servrar (förhands granskning)**  -   [Säkerhetskopiera Azure postgresql-databaser och behåll säkerhets kopiorna i upp till 10 år](backup-azure-database-postgresql.md)

![Översikt över Azure Backup](./media/backup-overview/azure-backup-overview.png)

## <a name="why-use-azure-backup"></a>Varför bör jag använda Azure Backup?

Azure Backup ger följande viktiga fördelar:

- **Avlastning av lokal säkerhets kopiering**: Azure Backup erbjuder en enkel lösning för att säkerhetskopiera lokala resurser till molnet. Du får både kort- och långsiktig kvarhållning av säkerhetskopior utan att behöva distribuera komplexa lokala lösningar.
- **Säkerhetskopiera virtuella Azure IaaS-datorer**: Azure Backup tillhandahåller oberoende och isolerade säkerhets kopior för att skydda mot oavsiktlig destruktion av ursprungliga data. Säkerhetskopior lagras i ett Recovery Services-valv med inbyggd hantering av återställningspunkter. Konfiguration och skalbarhet är enkla, säkerhets kopieringar optimeras och du kan enkelt återställa efter behov.
- **Enkel skalning** – Azure Backup använder Azure-molnets underliggande kraft och obegränsade storlek för att tillhandahålla hög tillgänglighet – utan underhåll och omkostnad för övervakning.
- **Få obegränsad data överföring**: Azure Backup begränsar inte mängden inkommande eller utgående data som du överför eller debiteras för de data som överförs.
  - Utgående data syftar på data som överförs från ett Recovery Services-valv under en återställningsåtgärd.
  - Om du utför en inledande säkerhets kopiering offline med Azure import/export-tjänsten för att importera stora mängder data, finns det en kostnad för inkommande data.  [Läs mer](backup-azure-backup-import-export.md).
- Skydda **data**: Azure Backup tillhandahåller lösningar för att skydda data [under överföring](backup-azure-security-feature.md) och [i vila](backup-azure-security-feature-cloud.md).
- **Centraliserad övervakning och hantering**: Azure Backup tillhandahåller [inbyggda övervaknings-och aviserings funktioner](backup-azure-monitoring-built-in-monitor.md) i ett Recovery Services-valv. Dessa funktioner är tillgängliga utan ytterligare hanteringsinfrastruktur. Du kan också öka skalan för övervakning och rapportering med [hjälp av Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).
- **Hämta programkonsekventa säkerhets kopieringar**: en programkonsekvent säkerhets kopiering innebär att en återställnings punkt har alla data som krävs för att återställa säkerhets kopian. Azure Backup ger programkonsekventa säkerhets kopieringar som säkerställer att ytterligare korrigeringar inte krävs för att återställa data. Återställning av konsekventa programdata minskar tiden för återställning, så att du snabbt kan återgå till körläge.
- **Behåll korta och långsiktiga data**: du kan använda [Recovery Services valv](backup-azure-recovery-services-vault-overview.md) för kortsiktig och långsiktig data kvarhållning.
- **Automatisk lagringshantering** – hybridmiljöer kräver ofta heterogen lagring – vissa lokalt och vissa i molnet. Med Azure Backup kostar det inget att använda lokala lagrings enheter. Azure Backup allokerar och hanterar lagringen av säkerhetskopiorna automatiskt och tillämpar en modell där du betalar baserat på din användning. Så du betalar bara för den lagring som du använder. [Läs mer](https://azure.microsoft.com/pricing/details/backup) om prissättning.
- **Flera lagrings alternativ** – Azure Backup erbjuder tre typer av replikering för att hålla lagringen/data hög tillgängliga.
  - [Lokalt redundant lagring (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replikerar dina data tre gånger (det skapas tre kopior av dina data) i en lagringsskalningsenhet i ett datacenter. Alla datakopior finns i samma region. LRS är ett billigt alternativ för att skydda dina data mot fel i den lokala maskinvaran.
  - [Geo-redundant lagring (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) är standardalternativet och det som rekommenderas vid replikering. GRS replikerar dina data till en sekundär region (hundratals mil bort från den primära platsen för datakällan). GRS kostar mer än LRS, men GRS ger en högre nivå av hållbarhet för dina data, även om det finns ett regionalt avbrott.
  - [Zone-redundant lagring (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replikerar dina data i [tillgänglighets zoner](../availability-zones/az-overview.md#availability-zones), vilket garanterar data placering och återhämtning i samma region. ZRS har ingen stillestånds tid. Så dina kritiska arbets belastningar som kräver [data placering](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)och får inte ha någon avbrotts tid kan säkerhets kopie ras i ZRS.

## <a name="next-steps"></a>Nästa steg

- [Gå igenom](backup-architecture.md) arkitekturen och komponenterna i olika scenarier för säkerhetskopiering.
- [Kontrol lera](backup-support-matrix.md) support kraven och begränsningarna för säkerhets kopiering och för [Azure VM-säkerhetskopiering](backup-support-matrix-iaas.md).
