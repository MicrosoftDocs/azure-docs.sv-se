---
title: Azure Resource Manager-mallar
description: Azure Resource Manager mallar för att använda Azure Site Recovery-funktioner.
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720536"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Azure Resource Manager mallar för Azure Site Recovery

Följande tabell innehåller länkar till Azure Resource Manager mallar för att använda Azure Site Recovery-funktioner.

| Mall | Beskrivning |
|---|---|
|**Azure till Azure** | |
| [Skapa ett Recovery Services-valv](./quickstart-create-vault-template.md)| Skapa ett Recovery Services-valv. Du kan använda valvet för Azure Backup och Azure Site Recovery. |
| [Aktivera replikering för virtuella Azure-datorer](https://aka.ms/asr-arm-enable-replication) | Aktivera replikering för virtuella Azure-datorer med hjälp av det befintliga valvet och anpassade mål inställningarna.|
| [Utlös redundans och återaktivera skydd](https://aka.ms/asr-arm-failover-reprotect) | Utlös en redundansväxling och skydds åtgärd för en uppsättning virtuella Azure-datorer. |
| [Kör ett DR-flöde från slut punkt till slut punkt för virtuella Azure-datorer](https://aka.ms/asr-arm-e2e-flow) | Starta ett fullständigt haveri återställnings flöde (aktivera replikering + redundans och skydda igen + återställning efter fel och återskydd) för virtuella Azure-datorer, även kallat 540-flöde.|
|   |   |