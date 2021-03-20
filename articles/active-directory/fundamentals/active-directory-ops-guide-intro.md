---
title: Referens guide för Azure Active Directory åtgärder
description: Den här åtgärds guiden beskriver de kontroller och åtgärder som du bör vidta för att skydda och upprätthålla identitets-och åtkomst hantering, autentisering, styrning och åtgärder
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74535319"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Referens guide för Azure Active Directory åtgärder

Den här åtgärds hand boken beskriver de kontroller och åtgärder som du bör vidta för att skydda och underhålla följande områden:

- **[Identitets-och åtkomst hantering](active-directory-ops-guide-iam.md)** – möjlighet att hantera livs cykeln för identiteter och deras rättigheter.
- **[Autentiserings hantering](active-directory-ops-guide-auth.md)** – möjlighet att hantera autentiseringsuppgifter, definiera autentisering, delegera tilldelning, mäta användning och definiera åtkomst principer baserat på företags säkerhet position.
- **[Styrning](active-directory-ops-guide-govern.md)** – möjlighet att utvärdera och intyga åtkomsten beviljade icke privilegierade och privilegierade identiteter, gransknings-och kontroll ändringar i miljön.
- **[Åtgärder](active-directory-ops-guide-ops.md)** – optimera åtgärder Azure Active Directory (Azure AD).

Vissa rekommendationer här kanske inte kan tillämpas på alla kunders miljöer, till exempel, AD FS bästa praxis kanske inte gäller om din organisation använder synkronisering av lösen ords-hash.

> [!NOTE]
> Dessa rekommendationer är aktuella vid publicerings datumet, men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sina identitets metoder när Microsofts produkter och tjänster utvecklas med tiden. Rekommendationer kan ändras när organisationer prenumererar på en annan Azure AD Premium-licens. Azure AD Premium P2 kommer till exempel att innehålla fler styrnings rekommendationer.

## <a name="stakeholders"></a>Fått

Varje avsnitt i den här referens hand boken rekommenderar att du tilldelar intressenter för att planera och implementera viktiga uppgifter. I följande tabell visas en lista över alla intressenter i den här guiden:

| Intressent | Beskrivning |
| :- | :- |
| IAM-åtgärds team | Det här teamet hanterar hantering av dag till dags drift för identitets-och åtkomst hanterings systemet |
| Produktivitets team | Det här teamet äger och hanterar produktivitets programmen, till exempel e-post, fildelning och samarbete, snabb meddelanden och konferenser. |
| Program ägare | Det här teamet äger det specifika programmet från ett företag och vanligt vis ett tekniskt perspektiv i en organisation. |
| Arkitektur team för informations säkerhet | Teamet planerar och utformar en organisations informations säkerhets rutiner. |
| Åtgärds team för informations säkerhet | Det här teamet kör och övervakar den implementerade informations säkerhets praxisen i arkitektur teamet för informations säkerhet. |

## <a name="next-steps"></a>Nästa steg

Kom igång med [identitets-och åtkomst hanterings kontrollerna och åtgärderna](active-directory-ops-guide-iam.md).
