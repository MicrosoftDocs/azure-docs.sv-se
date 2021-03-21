---
title: Behörigheter i Azure Security Center | Microsoft Docs
description: I den här artikeln förklaras hur Azure Security Center använder rollbaserad åtkomst kontroll för att tilldela behörigheter till användare och för att identifiera tillåtna åtgärder för varje roll.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 01/03/2021
ms.author: memildin
ms.openlocfilehash: dcbb4977e1bfd17f0cbed61abf9ba335615b7799
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443768"
---
# <a name="permissions-in-azure-security-center"></a>Behörigheter i Azure Security Center

Azure Security Center använder [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), vilket ger [inbyggda roller](../role-based-access-control/built-in-roles.md) som kan tilldelas till användare, grupper och tjänster i Azure.

Security Center bedömer konfigurationen av dina resurser för att identifiera säkerhets problem och sårbarheter. I Security Center ser du bara information som rör en resurs när du tilldelas rollen som ägare, deltagare eller läsare för den prenumeration eller resurs grupp som en resurs tillhör.

Förutom dessa roller finns två specifika roller i Security Center:

* **Säkerhets läsare**: en användare som tillhör den här rollen har visnings rättigheter att Security Center. Användaren kan visa rekommendationer, aviseringar, säkerhets principer och säkerhets tillstånd, men kan inte göra ändringar.
* **Säkerhets administratör**: en användare som tillhör den här rollen har samma rättigheter som säkerhets läsaren och kan också uppdatera säkerhets principen och ignorera aviseringar och rekommendationer.

> [!NOTE]
> Säkerhets rollerna, säkerhets läsaren och säkerhets administratören har endast åtkomst i Security Center. Säkerhets rollerna har inte åtkomst till andra tjänst områden i Azure, till exempel lagring, webb & mobil eller Sakernas Internet.
>

## <a name="roles-and-allowed-actions"></a>Roller och tillåtna åtgärder

I följande tabell visas roller och tillåtna åtgärder i Security Center.

| Action                                                                                                                                        | Säkerhets läsare/ <br> Läsare | Säkerhetsadministratör | Resurs grupps deltagare/ <br> Resurs grupp ägare | Prenumerations deltagare | Prenumerations ägare |
|:----------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
| Redigera säkerhets princip                                                                                                                          | -                             | ✔             | -                                                      | -                        | ✔                 |
| Lägg till/tilldela initiativ (inklusive) regler för regelefterlevnad)                                                                           | -                             | -              | -                                                      | -                        | ✔                 |
| Aktivera/inaktivera Azure Defender                                                                                                               | -                             | ✔             | -                                                      | -                        | ✔                 |
| Aktivera/inaktivera automatisk etablering                                                                                                            | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Tillämpa säkerhets rekommendationer för en resurs</br> (och Använd [snabb korrigering!](security-center-remediate-recommendations.md#quick-fix-remediation)) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| Ignorera aviseringar                                                                                                                                | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Visa aviseringar och rekommendationer                                                                                                               | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |

> [!NOTE]
> Vi rekommenderar att du ger användarna den roll som precis ger dem den behörighet de behöver för att kunna utföra sina arbetsuppgifter. Du kan till exempel tilldela rollen läsare till användare som bara behöver visa information om säkerhets hälso tillståndet för en resurs men inte vidta åtgärder, till exempel att tillämpa rekommendationer eller redigera principer.
>
>

## <a name="next-steps"></a>Nästa steg
I den här artikeln förklaras hur Security Center använder Azure RBAC för att tilldela behörigheter till användare och identifierat tillåtna åtgärder för varje roll. Nu när du är bekant med roll tilldelningarna som krävs för att övervaka säkerhets status för din prenumeration, redigera säkerhets principer och använda rekommendationer, lär du dig att:

- [Ange säkerhetsprinciper i Security Center](tutorial-security-policy.md)
- [Hantera säkerhets rekommendationer i Security Center](security-center-recommendations.md)
- [Hantera och besvara säkerhetsaviseringar i Security Center](security-center-managing-and-responding-alerts.md)
- [Övervaka säkerhets lösningar för partner](./security-center-partner-integration.md)