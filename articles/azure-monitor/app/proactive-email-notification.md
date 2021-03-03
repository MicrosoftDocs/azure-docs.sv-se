---
title: Ändra avisering om Smart identifiering – Azure Application insikter
description: Ändra till standard mottagare för meddelanden från Smart identifiering. Med Smart identifiering kan du övervaka program spår med Azure Application insikter om ovanliga mönster i trace telemetri.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: fa14f0dd40a30a4750d9bb102d8e67608f958135
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734510"
---
# <a name="smart-detection-e-mail-notification-change"></a>Ändra e-postavisering om Smart identifiering

Baserat på kundfeedback, den 1 april 2019, ändrar vi standard rollerna som tar emot e-postaviseringar från Smart identifiering.

## <a name="what-is-changing"></a>Vad ändras?

För närvarande skickas e-postaviseringar för smart identifiering som standard till rollerna _prenumerations ägare_, _prenumerations deltagare_ och _prenumerations läsare_ . Dessa roller omfattar ofta användare som inte är aktivt inblandad i övervakning, vilket gör att många av dessa användare kan ta emot meddelanden i onödan. För att förbättra den här upplevelsen gör vi en ändring så att e-postaviseringar endast går till rollerna [övervaknings läsare](../../role-based-access-control/built-in-roles.md#monitoring-reader) och [övervakning deltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor) som standard.

## <a name="scope-of-this-change"></a>Omfånget för den här ändringen

Den här ändringen kommer att påverka alla regler för smart identifiering, förutom följande:

* Regler för smart identifiering markerad som för hands version. Dessa regler för smart identifiering stöder inte e-postaviseringar idag.

* Regel för fel avvikelser.

## <a name="how-to-prepare-for-this-change"></a>Hur förbereder du för den här ändringen?

För att säkerställa att e-postmeddelanden från Smart identifiering skickas till relevanta användare måste användarna tilldelas [övervaknings läsaren](../../role-based-access-control/built-in-roles.md#monitoring-reader) eller [övervaknings deltagar](../../role-based-access-control/built-in-roles.md#monitoring-contributor) rollerna för prenumerationen.

Om du vill tilldela användare till övervaknings läsaren eller övervaknings deltagar roller via Azure Portal följer du stegen som beskrivs i artikeln [tilldela Azure-roller](../../role-based-access-control/role-assignments-portal.md) . Se till att välja _övervaknings läsare_ eller _övervaknings deltagare_ som den roll som användare tilldelas till.

> [!NOTE]
> Vissa mottagare av meddelanden om Smart identifiering, som kon figurer ATS med alternativet _ytterligare e-postmottagare_ i regel inställningarna, påverkas inte av den här ändringen. Dessa mottagare fortsätter att ta emot e-postaviseringar.

Om du har några frågor eller problem med den här ändringen kan du inte ovilliga [kontakta oss](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>Nästa steg

Läs mer om Smart identifiering:

- [Felavvikelser](./proactive-failure-diagnostics.md)
- [Minnes läckor](./proactive-potential-memory-leak.md)
- [Prestandaavvikelser](./proactive-performance-diagnostics.md)

