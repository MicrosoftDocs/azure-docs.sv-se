---
title: Prissättning för Azure Functions
description: Lär dig hur faktureringen fungerar för Azure Functions.
author: craigshoemaker
ms.author: cshoe
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: cde1c5ee45edb4472bed992ee9ac4075f8ad05ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95002184"
---
# <a name="azure-functions-pricing"></a>Prissättning för Azure Functions

Azure Functions har tre typer av pris planer. Välj den som passar dina behov bäst:

- **Förbruknings plan**: Azure tillhandahåller alla nödvändiga beräknings resurser. Du behöver inte oroa dig för resurs hantering och bara betala för den tid som koden körs.

- **Premium plan**: du anger ett antal förvärmade instanser som alltid är online och som är redo att omedelbart svara. När din funktion körs tillhandahåller Azure eventuella ytterligare beräknings resurser som behövs. Du betalar för de förvärmade instanserna som körs kontinuerligt och eventuella ytterligare instanser som du använder som Azure skalar din app i och ut.

- **App Service plan**: Kör funktioner precis som dina webb program. Om du använder App Service för andra program kan funktionerna köras i samma plan utan extra kostnad.

Mer information om värdplaner finns i [Azure Functions hosting plan comparison](functions-scale.md) (Jämförelse av värdplaner för Azure Functions). Fullständig prisinformation är tillgänglig på sidan [Prissättning för Functions](https://azure.microsoft.com/pricing/details/functions/).
