---
title: ta med fil
description: ta med fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028041"
---
 Azure Machine Learning beräknings kluster stöder även [hanterade identiteter](../articles/active-directory/managed-identities-azure-resources/overview.md) för att autentisera åtkomsten till Azure-resurser utan att inkludera autentiseringsuppgifter i din kod. Det finns två typer av hanterade identiteter:

* En **systemtilldelad hanterad identitet** aktive ras direkt i Azure Machine Learning beräknings kluster. Livs cykeln för en tilldelad identitet är direkt knuten till beräknings klustret. Om beräknings klustret tas bort rensar Azure automatiskt autentiseringsuppgifterna och identiteten i Azure AD.
* En **användare som tilldelats en hanterad identitet** är en fristående Azure-resurs som tillhandahålls via Azure Managed Identity service. Du kan tilldela en användardefinierad hanterad identitet till flera resurser och den finns kvar så länge du vill.