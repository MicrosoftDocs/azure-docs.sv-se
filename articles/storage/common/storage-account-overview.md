---
title: Översikt över lagringskonto
titleSuffix: Azure Storage
description: Lär dig mer om de olika typerna av lagringskonton i Azure Storage. Granska kontonamn, prestandanivåer, åtkomstnivåer, redundans, kryptering, slutpunkter med mera.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d4874ad6688fa85f0c511632498938817bb218f7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714208"
---
# <a name="storage-account-overview"></a>Översikt över lagringskonto

Ett Azure Storage-konto innehåller alla Azure Storage dataobjekt: blobar, filer, köer, tabeller och diskar. Lagringskontot tillhandahåller ett unikt namnområde för dina Azure Storage data som kan nås var som helst i världen via HTTP eller HTTPS. Data i ditt Azure-lagringskonto är hållbara och har hög tillgänglig, säker och mycket skalbar.

Information om hur du skapar ett Azure-lagringskonto finns i [Skapa ett lagringskonto](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typer av lagringskonton

Azure Storage erbjuder flera typer av lagringskonton. Varje typ stöder olika funktioner och har en egen prismodell. Tänk på dessa skillnader innan du skapar ett lagringskonto för att avgöra vilken typ av konto som passar bäst för dina program.

I följande tabell beskrivs de typer av lagringskonton som rekommenderas av Microsoft för de flesta scenarier:

| Typ av lagringskonto | Tjänster som stöds | Redundansalternativ | Distributionsmodell | Användning |
|--|--|--|--|--|
| Standard generell användning v2 | Blob, fil, kö, tabell och Data Lake Storage<sup>1</sup> | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Resource Manager<sup>3</sup> | Grundläggande lagringskontotyp för blobar, filer, köer och tabeller. Rekommenderas för de flesta scenarier med Azure Storage. |
| Premium-blockblobar<sup>4</sup> | Endast blockblobar | LRS<br /><br />ZRS<sup>2</sup> | Resource Manager<sup>3</sup> | Lagringskonton med premiumprestandaegenskaper för blockblobar och tilläggsblobar. Rekommenderas för scenarier med höga transaktionsfrekvenser eller scenarier som använder mindre objekt eller som kräver konsekvent låg lagringsfördröjning.<br />[Lära sig mer...](../blobs/storage-blob-performance-tiers.md) |
| Premium-filresurser<sup>4</sup> | Endast filresurser | LRS<br /><br />ZRS<sup>2</sup> | Resource Manager<sup>3</sup> | Lagringskonton med endast filer med premiumprestandaegenskaper. Rekommenderas för skalningsprogram i företags- eller högprestandaskala.<br />[Lära sig mer...](../files/storage-files-planning.md#management-concepts) |
| Premium-sidblobar<sup>4</sup> | Endast sidblobar | LRS | Resource Manager<sup>3</sup> | Premium Storage-kontotyp endast för sidblobar.<br />[Lära sig mer...](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Azure Data Lake Storage är en uppsättning funktioner för analys av stordata som bygger på Azure Blob Storage. Data Lake Storage stöds endast på V2-lagringskonton för generell användning med en hierarkisk namnrymd aktiverad. Mer information om Data Lake Storage Gen2 finns i [Introduktion till Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>2</sup> Zonredundant lagring (ZRS) och geo-zonredundant lagring (GZRS/RA-GZRS) är endast tillgängliga för konton för generell användning v2, premium-blockblob och premiumfilresurs i vissa regioner. Mer information om Azure Storage finns i Azure Storage [redundans](storage-redundancy.md).

<sup>3</sup> Azure Resource Manager är den rekommenderade distributionsmodellen för Azure-resurser, inklusive lagringskonton. Mer information finns i [Resource Manager översikt.](../../azure-resource-manager/management/overview.md)

<sup>4</sup> Lagringskonton på premiumprestandanivå använder SSD-diskar för korta svarstider och högt dataflöde.

Äldre lagringskonton stöds också. Mer information finns i Äldre [lagringskontotyper.](#legacy-storage-account-types)

## <a name="storage-account-endpoints"></a>Slutpunkter för lagringskonto

På ett lagringskonto finns ett unikt namnområde i Azure för dina data. Alla objekt som du lagrar i Azure Storage har en adress som innehåller ditt unika kontonamn. Kombinationen av kontonamnet och Azure Storage-tjänstens slutpunkt bildar slutpunkterna för ditt lagringskonto.

Tänk på dessa regler när du namnger lagringskontot:

- Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
- Namnet på ditt lagringskonto måste vara unikt i Azure. Det får inte finnas två lagringskonton med samma namn.

I följande tabell visas formatet på slutpunkten för var och en av de Azure Storage tjänsterna.

| Lagringstjänst | Slutpunkt |
|--|--|
| Blob Storage | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

Skapa URL:en för åtkomst till ett objekt i ett lagringskonto genom att lägga till objektets plats i lagringskontot till slutpunkten. URL:en för en blob ser till exempel ut ungefär så här:

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

Du kan också konfigurera ditt lagringskonto så att det använder en anpassad domän för blobar. Mer information finns i [Konfigurera ett anpassat domännamn för ditt Azure Storage konto.](../blobs/storage-custom-domain-name.md)  

## <a name="migrating-a-storage-account"></a>Migrera ett lagringskonto

I följande tabell sammanfattas och pekar på vägledning om hur du flyttar, uppgraderar eller migrerar ett lagringskonto:

| Migreringsscenario | Information |
|--|--|
| Flytta ett lagringskonto till en annan prenumeration | Azure Resource Manager alternativ för att flytta en resurs till en annan prenumeration. Mer information finns i Flytta [resurser till en ny resursgrupp eller prenumeration.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) |
| Flytta ett lagringskonto till en annan resursgrupp | Azure Resource Manager alternativ för att flytta en resurs till en annan resursgrupp. Mer information finns i Flytta [resurser till en ny resursgrupp eller prenumeration.](../../azure-resource-manager/management/move-resource-group-and-subscription.md) |
| Flytta ett lagringskonto till en annan region | Om du vill flytta ett lagringskonto skapar du en kopia av lagringskontot i en annan region. Flytta sedan dina data till det kontot med hjälp av AzCopy eller något annat verktyg. Mer information finns i Flytta [ett Azure Storage till en annan region.](storage-account-move.md) |
| Uppgradera till ett v2-lagringskonto för generell användning | Du kan uppgradera ett v1-lagringskonto för generell användning eller ett Blob Storage-konto till ett v2-konto för generell användning. Observera att den här åtgärden inte kan ångras. Mer information finns i [Uppgradera till ett v2-lagringskonto för generell användning.](storage-account-upgrade.md) |
| Migrera ett klassiskt lagringskonto till Azure Resource Manager | Distributionsmodellen Azure Resource Manager är bättre än den klassiska distributionsmodellen vad gäller funktionalitet, skalbarhet och säkerhet. Mer information om hur du migrerar ett klassiskt [](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) lagringskonto till Azure Resource Manager finns i Migrering av lagringskonton i Plattformsstödd migrering av **IaaS-resurser** från klassisk till Azure Resource Manager . |

## <a name="transferring-data-into-a-storage-account"></a>Överföra data till ett lagringskonto

Microsoft tillhandahåller tjänster och verktyg för att importera data från lokala lagringsenheter eller molnlagringsleverantörer från tredje part. Vilken lösning du använder beror på hur många data du överför. Mer information finns i [översikten Azure Storage migrering.](storage-migration-overview.md)

## <a name="storage-account-encryption"></a>Kryptering för lagringskonto

Alla data i ditt lagringskonto krypteras automatiskt på tjänstsidan. Mer information om kryptering och nyckelhantering finns i [Azure Storage för vilodata.](storage-service-encryption.md)

## <a name="storage-account-billing"></a>Fakturering för lagringskonto

Azure Storage fakturor baserat på användningen av ditt lagringskonto. Alla objekt i ett lagringskonto faktureras tillsammans som en grupp. Lagringskostnaderna beräknas enligt följande faktorer:

- **Region** avser den geografiska region där ditt konto är baserat.
- **Kontotyp** avser den typ av lagringskonto som du använder.
- **Åtkomstnivå** avser det dataanvändningsmönster som du har angett för ditt allmänna v2- eller Blob Storage-konto.
- **Kapacitet** avser hur mycket av ditt lagringskonto som du använder för att lagra data.
- **Redundans** avgör hur många kopior av dina data som underhålls samtidigt och på vilka platser.
- **Transaktioner avser** alla läs- och skrivåtgärder till Azure Storage.
- **Utgående data avser** alla data som överförs från en Azure-region. När data i ditt lagringskonto används av ett program som inte körs i samma region debiteras du för utgående data. Information om hur du använder resursgrupper för att gruppera data och tjänster i samma region för att begränsa utgående avgifter finns i [Vad är en Azure-resursgrupp?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

Sidan [Azure Storage innehåller](https://azure.microsoft.com/pricing/details/storage/) detaljerad prisinformation baserat på kontotyp, lagringskapacitet, replikering och transaktioner. Prisinformationen [för dataöverföringar](https://azure.microsoft.com/pricing/details/data-transfers/) innehåller detaljerad prisinformation för utgående data. Du kan använda [priskalkylatorn Azure Storage för](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) att beräkna dina kostnader.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>Äldre lagringskontotyper

I följande tabell beskrivs de äldre lagringskontotyperna. Dessa kontotyper rekommenderas inte av Microsoft, men kan användas i vissa scenarier:

| Typ av äldre lagringskonto | Tjänster som stöds | Redundansalternativ | Distributionsmodell | Användning |
|--|--|--|--|--|
| Standard generell användning v1 | Blob, fil, kö, tabell och Data Lake Storage | LRS/GRS/RA-GRS | Resource Manager, klassisk | General-purpose v1-konton kanske inte har de senaste funktionerna eller det lägsta priset per gigabyte. Överväg att använda för följande scenarier:<br /><ul><li>Dina program kräver den klassiska Azure-distributionsmodellen.</li><li>Dina program är transaktionsintensiva eller använder betydande bandbredd för geo-replikering, men kräver inte stor kapacitet. I det här fallet kan generell användning v1 vara det mest ekonomiska valet.</li><li>Du använder en tidigare version av Azure Storage REST API än 2014-02-14 eller ett klientbibliotek med en tidigare version än 4.x och du kan inte uppgradera programmet.</li></ul> |
| Standard Blob Storage | Blob (endast blockblobar och tilläggsblobar) | LRS/GRS/RA-GRS | Resource Manager | Microsoft rekommenderar att du använder standardkonton för generell användning v2 i stället när det är möjligt. |

## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagringskonto](storage-account-create.md)
- [Uppgradera till ett v2-lagringskonto för generell användning](storage-account-upgrade.md)
- [Återställa ett borttaget lagringskonto](storage-account-recover.md)