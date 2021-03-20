---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8fba8aa577bcb3b5ef44d57c388a1f1de7494782
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995631"
---
Om du vill att användarna ska uppmanas att ange en andra faktor innan de beviljas åtkomst, kan du konfigurera Azure AD Multi-Factor Authentication (MFA). Du kan konfigurera MFA per användare eller använda MFA via [villkorlig åtkomst](../articles/active-directory/conditional-access/overview.md).

* MFA per användare kan aktive ras utan ytterligare kostnad. När du aktiverar MFA per användare uppmanas användaren att ange en andra Factor Authentication mot alla program som är kopplade till Azure AD-klienten. Se [alternativ 1](#peruser) för steg.
* Villkorlig åtkomst ger en detaljerad kontroll över hur en andra faktor ska höjas. Den kan tillåta tilldelning av MFA till endast VPN och utesluta andra program som är kopplade till Azure AD-klienten. Se [Alternativ 2](#conditional) för steg.