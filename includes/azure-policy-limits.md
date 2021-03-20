---
title: ta med fil
description: ta med fil
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91605913"
---
Det finns ett maximalt antal för varje objekt typ för Azure Policy. För definitioner innebär en post för _scope_ [hanterings gruppen](../articles/governance/management-groups/overview.md) eller prenumerationen.
För tilldelningar och undantag innebär en post för _omfattning_ [hanterings gruppen](../articles/governance/management-groups/overview.md), prenumerationen, resurs gruppen eller enskilda resurser.

| Var | Vad | Maximalt antal |
|---|---|---|
| Omfång | Principdefinitioner | 500 |
| Omfång | Initiativdefinitioner | 200 |
| Klientorganisation | Initiativdefinitioner | 2 500 |
| Omfång | Princip-eller initiativ tilldelningar | 200 |
| Omfång | Undantag | 1000 |
| Definition av princip | Parametrar | 20 |
| Initiativdefinition | Principer | 1000 |
| Initiativdefinition | Parametrar | 100 |
| Princip-eller initiativ tilldelningar | Undantag (notScopes) | 400 |
| Principregel | Kapslade villkor | 512 |
| Reparations uppgift | Resurser | 500 |
