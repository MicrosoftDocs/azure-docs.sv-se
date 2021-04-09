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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99525440"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Säkerhetskopiering online och dataåterställning på begäran i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB gör automatiskt en säkerhetskopia av dina data med jämna mellanrum. Automatiska säkerhetskopieringar görs utan att det påverkar databasernas prestanda eller tillgänglighet. Alla säkerhetskopior lagras separat i en lagringstjänst. Automatiska säkerhetskopieringar är användbara om du oavsiktligt skulle råka ta bort eller uppdatera ditt Azure Cosmos-konto, din databas eller container och senare behöver återställa data. Det finns två säkerhets kopierings lägen:

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
