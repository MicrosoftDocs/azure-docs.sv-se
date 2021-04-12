---
title: Så här uppfyller du placering-kraven för data i Azure Cosmos DB
description: Lär dig hur du uppfyller placering-kraven för data i Azure Cosmos DB för dina data och säkerhets kopior så att de blir kvar i en enda region.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c5f8a4361774368e3934d1e2b16c8311876f5caa
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491873"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Så här uppfyller du placering-kraven för data i Azure Cosmos DB

I Azure Cosmos DB kan du konfigurera dina data och säkerhets kopior så att de finns kvar i en enda region för att uppfylla[ placering-kraven.](https://azure.microsoft.com/en-us/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>Placering krav för data

I Azure Cosmos DB måste du uttryckligen konfigurera replikeringen mellan regioner. Lär dig hur du konfigurerar geo-replikering med [Azure Portal](how-to-manage-database-account.md#addremove-regions-from-your-database-account), [Azure CLI](scripts/cli/common/regions.md). För att uppfylla kraven på data placering kan du skapa en Azure-princip som tillåter att vissa regioner förhindrar datareplikering till oönskade regioner.

## <a name="residency-requirements-for-backups"></a>Placering krav för säkerhets kopieringar

**Säkerhets kopior med kontinuerliga lägen**: de här säkerhets kopiorna är residenta som standard när de lagras i antingen lokalt redundant eller zon för redundant lagring. Mer information finns i artikeln om [kontinuerlig säkerhets kopiering](continuous-backup-restore-portal.md) .

**Säkerhets kopiering i periodiskt läge**: som standard lagras säkerhets kopior i periodiskt läge i Geo-redundant lagring. För periodiska säkerhets kopierings lägen kan du konfigurera dataredundans på konto nivå. Det finns tre alternativ för lagring av säkerhets kopior. De är lokala redundans, zon redundans eller GEO-redundans. Mer information finns i så här [konfigurerar du redundans för säkerhets kopiering](configure-periodic-backup-restore.md#configure-backup-interval-retention) med hjälp av portalen.

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Använd Azure Policy för att genomdriva placering-kraven

Om du har data placering krav som kräver att du behåller alla dina data i en enda Azure-region, kan du framtvinga zoner-redundanta eller lokalt redundanta säkerhets kopieringar för ditt konto genom att använda en Azure Policy.  Du kan också genomdriva en princip om att Azure Cosmos DB-kontona inte är geo-replikerade till andra regioner.

Azure Policy är en tjänst som du kan använda för att skapa, tilldela och hantera principer som tillämpar regler på Azure-resurser. Azure Policy hjälper dig att hålla resurserna kompatibla med företagets standarder och service nivå avtal. Mer information finns i använda [Azure policy](policy.md) för att implementera styrning och kontroller för Azure Cosmos DB resurser

## <a name="next-steps"></a>Nästa steg

* Konfigurera och hantera regelbunden säkerhets kopiering med [Azure Portal](configure-periodic-backup-restore.md)
* Konfigurera och hantera kontinuerlig säkerhets kopiering med [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)eller [Azure Resource Manager](continuous-backup-restore-template.md).
