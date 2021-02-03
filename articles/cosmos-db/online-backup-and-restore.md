---
title: Säkerhets kopiering online och data återställning på begäran i Azure Cosmos DB.
description: Den här artikeln beskriver hur automatisk säkerhets kopiering, data återställning på begäran fungerar. Det förklarar också skillnaden mellan kontinuerliga och periodiska säkerhets kopierings lägen.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525440"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Säkerhets kopiering online och data återställning på begäran i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB automatiskt tar säkerhets kopior av dina data med jämna mellanrum. Automatiska säkerhets kopieringar tas utan att påverka databas åtgärdernas prestanda eller tillgänglighet. Alla säkerhets kopior lagras separat i en lagrings tjänst. Automatiska säkerhets kopieringar är användbara i scenarier när du oavsiktligt tar bort eller uppdaterar ditt Azure Cosmos-konto, din databas eller behållare och senare behöver data återställningen. Det finns två säkerhets kopierings lägen:

* **Periodiskt säkerhets kopierings läge** – det här läget är standard läget för säkerhets kopiering för alla befintliga konton. I det här läget tas säkerhets kopieringen med ett periodiskt intervall och data återställs genom att en begäran skapas med support teamet. I det här läget konfigurerar du ett intervall för säkerhets kopiering och kvarhållning för ditt konto. Den maximala kvarhållningsperioden sträcker sig till en månad. Det lägsta intervallet för säkerhets kopiering kan vara en timme.  Mer information finns i artikeln om [Periodisk säkerhets kopierings läge](configure-periodic-backup-restore.md) .

* **Kontinuerligt säkerhets kopierings läge** (för närvarande i offentlig för hands version) – du väljer det här läget när du skapar Azure Cosmos DB kontot. Med det här läget kan du återställa till en viss tidpunkt inom de senaste 30 dagarna. Mer information finns i [Introduktion till kontinuerligt säkerhets kopierings läge](continuous-backup-restore-introduction.md), konfigurera kontinuerlig säkerhets kopiering med [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)och [Resource Manager](continuous-backup-restore-template.md) -artiklar.

  > [!NOTE]
  > Om du konfigurerar ett nytt konto med kontinuerlig säkerhets kopiering kan du utföra återställning via självbetjäning via Azure Portal, PowerShell eller CLI. Om ditt konto har kon figurer ATS i kontinuerligt läge kan du inte växla tillbaka till periodiskt läge. För närvarande går det inte att ändra befintliga konton med periodiskt säkerhets kopierings läge till kontinuerligt läge.  

## <a name="next-steps"></a>Nästa steg

Härnäst kan du lära dig hur du konfigurerar och hanterar periodiska och kontinuerliga säkerhets kopierings lägen för ditt konto:

* [Konfigurera och hantera princip för periodisk säkerhets kopiering](configure-periodic-backup-restore.md) .
* Vad är [kontinuerligt säkerhets kopierings](continuous-backup-restore-introduction.md) läge?
* Konfigurera och hantera kontinuerlig säkerhets kopiering med [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)eller [Azure Resource Manager](continuous-backup-restore-template.md).
* [Hantera behörigheter](continuous-backup-restore-permissions.md) som krävs för att återställa data med kontinuerligt säkerhets kopierings läge.
