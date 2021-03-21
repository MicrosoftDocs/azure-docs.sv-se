---
title: ta med fil
description: ta med fil
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96026615"
---
En aktivitet som körs i Azure Batch kan producera utdata när den körs. Uppgifter om utdata för uppgifter behöver ofta lagras för hämtning av andra uppgifter i jobbet, klient programmet som körde jobbet eller båda. Aktiviteter skriver utdata till fil systemet i en batch-datornod, men alla data på noden förloras när de är avbildade eller när noden lämnar poolen. Aktiviteter kan också ha en kvarhållning av filer, efter vilken filer som skapats av uppgiften tas bort. Av dessa skäl är det viktigt att spara Uppgiftsutdata som du behöver senare till ett data lager, till exempel [Azure Storage](../articles/storage/index.yml).

För lagrings konto alternativ i batch, se [batch-konton och Azure Storage konton](../articles/batch/accounts.md#azure-storage-accounts).