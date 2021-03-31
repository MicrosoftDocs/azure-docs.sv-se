---
title: Azure Security Center FAQ – frågor om behörigheter
description: Detta vanliga frågor och svar om behörigheter i Azure Security Center, en produkt som hjälper dig att förhindra, identifiera och svara på hot.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 88d6207bf44d1ec04fde4f74ac9295412ea88fbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87824621"
---
# <a name="permissions"></a>Behörigheter

## <a name="how-do-permissions-work-in-azure-security-center"></a>Hur fungerar behörigheter i Azure Security Center?

Azure Security Center använder [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), vilket ger [inbyggda roller](../role-based-access-control/built-in-roles.md) som kan tilldelas till användare, grupper och tjänster i Azure.

Security Center bedömer konfigurationen av dina resurser för att identifiera säkerhets problem och sårbarheter. I Security Center ser du bara information som rör en resurs när du tilldelas rollen som ägare, deltagare eller läsare för den prenumeration eller resurs grupp som en resurs tillhör.

Se [behörigheter i Azure Security Center](security-center-permissions.md) om du vill veta mer om roller och tillåtna åtgärder i Security Center.



## <a name="who-can-modify-a-security-policy"></a>Vem kan ändra en säkerhets princip?

Om du vill ändra en säkerhets princip måste du vara säkerhets administratör eller ägare eller deltagare i den prenumerationen.

Information om hur du konfigurerar en säkerhets princip finns [i ställa in säkerhets principer i Azure Security Center](tutorial-security-policy.md).