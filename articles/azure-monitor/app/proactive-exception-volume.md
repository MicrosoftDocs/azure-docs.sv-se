---
title: Onormal ökning av undantags volym – Azure Application insikter
description: Övervaka program undantag med Smart identifiering i Azure Application insikter om ovanliga mönster i undantags volym.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 00b7a28a51f91c969b41d2ab85b611f6dde51396
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86499434"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Onormal ökning av undantags volym (för hands version)

Application Insights analyserar automatiskt undantagen som har utlösts i ditt program och kan varna dig om ovanliga mönster i din undantags telemetri.

Den här funktionen kräver ingen särskild konfiguration, förutom att [Konfigurera undantags rapportering](./asp-net-exceptions.md#set-up-exception-reporting) för din app. Den är aktiv när din app genererar tillräckligt med telemetri för undantag.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När får jag den här typen av meddelande om Smart identifiering?
Du kan få den här typen av meddelande om din app visar en onormal ökning av antalet undantag av en speciell typ under en dag, jämfört med en bas linje som beräknas under de föregående sju dagarna.
Machine Learning-algoritmer används för att upptäcka ökningen av antalet undantag, samtidigt som du tar hänsyn till en naturlig tillväxt i din program användning.

## <a name="does-my-app-definitely-have-a-problem"></a>Har min app definitivt ett problem?
Nej, en avisering innebär inte att din app definitivt har ett problem. Även om ett alltför stort antal undantag ofta indikerar ett program problem kan dessa undantag vara ofarliga och hanteras korrekt av ditt program.

## <a name="how-do-i-fix-it"></a>Vad kan jag göra?
Aviseringarna innehåller diagnostikinformation som ska stödjas i diagnostikprogrammet:
1. **Prioritering.** Meddelandet visar hur många användare eller hur många begär Anden som påverkas. Detta kan hjälpa dig att tilldela en prioritet för problemet.
2. **Utrymme.** Påverkar problemet all trafik eller bara en åtgärd? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller information om den metod som undantaget utlöstes från, samt undantags typen. Du kan också använda relaterade objekt och rapporter som länkar till kompletterande information för att hjälpa dig att diagnostisera problemet ytterligare.
