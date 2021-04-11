---
title: Kvoter i för hands versionen av Azure statisk Web Apps
description: Läs mer om kvoter som är kopplade till för hands versionen av Azure static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e3538e90a6dea69c703f56871fde86a18557a022
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095177"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Kvoter i för hands versionen av Azure statisk Web Apps

Följande kvoter finns för för hands versionen av Azure static Web Apps.

> [!IMPORTANT]
> Den statiska Azure-Web Apps är i offentlig för hands version och är inte avsedd för användning i produktion.

| Funktion                     | Kostnads fri plan        |
|-----------------------------|------------------|
| Inkluderad bandbredd          | 100 GB per månad |
| Överanvändning av bandbredd           | Inte tillgänglig      |
| Appar per Azure-prenumeration | 10               |
| App-storlek                    | 250 MB           |
| Förproduktionsmiljöer | 3                |
| Anpassade domäner              | 1                |
| Auktorisering (med anpassade roller och regler för routning) | Högst 25 slutanvändare som kan tillhöra anpassade roller |
| Azure Functions             | Tillgänglig        |
| SLA                         | Inget             |

## <a name="github-storage"></a>GitHub-lagring

GitHub-åtgärder och-paket använder GitHub-lagring, som har en egen uppsättning kvoter. När du skapar en Azure-statisk Web Apps webbplats lagrar GitHub artefakter för platsen även efter distributionen.

Se följande resurser för mer information:

- [Hantera åtgärder lagrings utrymme](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [Om fakturering för GitHub-åtgärder](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Hantera din utgifts gräns för GitHub-åtgärder](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Nästa steg

- [Översikt](overview.md)
