---
title: Om återställnings processen för den virtuella Azure-datorn
description: Lär dig hur tjänsten Azure Backup återställer virtuella Azure-datorer
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 436859bfb3cee37501e0605465d8e19856a846d6
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806831"
---
# <a name="about-azure-vm-restore"></a>Återställning av virtuell Azure-dator

Den här artikeln beskriver hur [Azure Backup tjänsten](./backup-overview.md) återställer virtuella Azure-datorer (VM). Det finns ett antal återställnings alternativ. Vi diskuterar de olika scenarier som de stöder.

## <a name="concepts"></a>Begrepp

- **Återställnings punkt** (kallas även **återställnings punkt**): en återställnings punkt är en kopia av de ursprungliga data som säkerhets kopie ras.

- **Nivå (ögonblicks bild eller valv)**: Azure VM-säkerhetskopiering sker i två faser:

  - I fas 1 lagras den ögonblicks bild som tagits, tillsammans med disken. Detta kallas för **ögonblicks bild nivå**. Återställningar av ögonblicks bild nivåer är snabbare (än återställning från valvet) eftersom de eliminerar vänte tiden för ögonblicks bilder som ska kopieras till valvet innan återställningen utlöses. Återställning från ögonblicks bilds nivån kallas även för [omedelbar återställning](./backup-instant-restore-capability.md).
  - I fas 2 överförs ögonblicks bilden och lagras i valvet som hanteras av Azure Backups tjänsten. Detta kallas för **valv nivå**.

- **Återställning av ursprunglig plats (OLR)**: en återställning som gjorts från återställnings punkten till den virtuella Azure-datorns virtuella Azure-dator från vilken säkerhets kopieringen gjordes, ersätter den med det tillstånd som lagrades i återställnings punkten. Detta ersätter OS-disken och data diskarna för den virtuella käll datorn.

- **Återställning av alternativ plats (återställning till)**: en återställning utfördes från återställnings punkten till en annan server än den ursprungliga servern där säkerhets kopiorna gjordes.

- **Återställning på objekt nivå (ILR):** Återställa enskilda filer eller mappar inuti den virtuella datorn från återställnings punkten

- **Tillgänglighet (typer av replikering)**: Azure Backup erbjuder två typer av replikering för att hålla lagringen/data hög tillgängliga:
  - [Lokalt redundant lagring (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replikerar dina data tre gånger (det skapas tre kopior av dina data) i en lagringsskalningsenhet i ett datacenter. Alla datakopior finns i samma region. LRS är ett billigt alternativ för att skydda dina data mot fel i den lokala maskinvaran.
  - [Geo-redundant lagring (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) är standardalternativet och det som rekommenderas vid replikering. GRS replikerar dina data till en sekundär region (hundratals mil bort från den primära platsen för datakällan). GRS kostar mer än LRS, men GRS ger en högre nivå av hållbarhet för dina data, även om det finns ett regionalt avbrott.
  - [Zone-redundant lagring (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replikerar dina data i [tillgänglighets zoner](../availability-zones/az-overview.md#availability-zones), vilket garanterar data placering och återhämtning i samma region. ZRS har ingen stillestånds tid. Så dina kritiska arbets belastningar som kräver [data placering](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)och får inte ha någon avbrotts tid kan säkerhets kopie ras i ZRS.

- **Återställning mellan regioner (CRR)**: som ett av [återställnings alternativen](./backup-azure-arm-restore-vms.md#restore-options)kan du med hjälp av återställnings alternativen för flera regioner (CRR) återställa virtuella Azure-datorer i en sekundär region, som är en [Azure-kopplad region](../best-practices-availability-paired-regions.md#what-are-paired-regions) . du kan återställa dina data i den sekundära regionen när som helst, under delar av eller hela drifts avbrott, eller någon annan gång som du väljer. 

## <a name="restore-scenarios"></a>Återställningsscenarier

![Återställningsscenarier ](./media/about-azure-vm-restore/recovery-scenarios.png)

| **Scenario**                                                 | **Vad är det som gör**                                             | **När du ska använda detta**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Återställa för att skapa en ny virtuell dator](./backup-azure-arm-restore-vms.md) | Återställer hela den virtuella datorn till OLR (om den virtuella käll datorn fortfarande finns) eller återställning till | <li> Om den virtuella käll datorn tappas bort eller skadas kan du återställa hela den virtuella datorn  <li> Du kan skapa en kopia av den virtuella datorn  <li> Du kan utföra en Restore-granskning för granskning eller efterlevnad  <li> Det här alternativet fungerar inte för virtuella Azure-datorer som skapats från Marketplace-avbildningar (det vill säga om de inte är tillgängliga eftersom licensen har upphört att gälla) |
| [Återställa diskar för den virtuella datorn](./backup-azure-arm-restore-vms.md#restore-disks) | Återställa diskar som är kopplade till den virtuella datorn                             |  Alla diskar: det här alternativet skapar mallen och återställer disken. Du kan redigera den här mallen med särskilda konfigurationer (till exempel tillgänglighets uppsättningar) för att uppfylla dina krav och sedan använda båda mallarna och återställa disken för att återskapa den virtuella datorn. |
| [Återställa specifika filer på den virtuella datorn](./backup-azure-restore-files-from-vm.md) | Välj återställnings punkt, bläddra, Välj filer och Återställ dem till samma (eller kompatibla) OS som den säkerhetskopierade virtuella datorn. |  Om du vet vilka filer som ska återställas använder du det här alternativet i stället för att återställa hela den virtuella datorn. |
| [Återställa en krypterad virtuell dator](./backup-azure-vms-encryption.md) | Från portalen återställer du diskarna och använder sedan PowerShell för att skapa den virtuella datorn | <li> [Krypterad virtuell dator med Azure Active Directory](../virtual-machines/windows/disk-encryption-windows-aad.md)  <li> [Krypterad virtuell dator utan Azure AD](../virtual-machines/windows/disk-encryption-windows.md) <li> [Krypterad virtuell dator *med Azure AD* migrerad till *utan Azure AD*](../virtual-machines/windows/disk-encryption-faq.md#can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app) |
| [Återställning mellan regioner](./backup-azure-arm-restore-vms.md#cross-region-restore) | Skapa en ny virtuell dator eller Återställ diskar till en sekundär region (Azure-kopplad region) | <li> **Fullständigt avbrott**: med funktionen för återställning av kors region finns det ingen vänte tid för att återställa data i den sekundära regionen. Du kan initiera återställningar i den sekundära regionen även innan Azure deklarerar ett avbrott. <li> **Partiellt avbrott**: nedtid kan uppstå i vissa lagrings kluster där Azure Backup lagrar dina säkerhetskopierade data eller till och med i nätverket, ansluter Azure Backup och lagrings kluster som är kopplade till dina säkerhetskopierade data. Med återställning mellan regioner kan du utföra en återställning i den sekundära regionen med hjälp av en replik av säkerhetskopierade data i den sekundära regionen. <li> **Inget avbrott**: du kan utföra granskningar av verksamhets kontinuitet och haveri beredskap (BCDR) för granskning eller efterlevnad med data för den sekundära regionen. På så sätt kan du utföra en återställning av säkerhetskopierade data i den sekundära regionen även om det inte finns ett fullständigt eller delvis avbrott i den primära regionen för verksamhets kontinuitet och haveri beredskap.  |

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar om återställning av virtuella datorer](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#restore)
- [Återställnings metoder som stöds](./backup-support-matrix-iaas.md#supported-restore-methods)
- [Felsök återställnings problem](./backup-azure-vms-troubleshoot.md#restore)