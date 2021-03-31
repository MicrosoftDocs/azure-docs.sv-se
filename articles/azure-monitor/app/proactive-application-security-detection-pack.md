---
title: Säkerhets identifierings paket med Azure Application insikter
description: Övervaka program med Azure Application insikter och smart identifiering för potentiella säkerhets problem.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 1ed304e903bb50591e61e294b6701f8268f9d8e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87081635"
---
# <a name="application-security-detection-pack-preview"></a>Identifierings paket för program säkerhet (för hands version)

Application Insights analyserar automatiskt den telemetri som genereras av ditt program och identifierar potentiella säkerhets problem. Med den här funktionen kan du identifiera potentiella säkerhets problem och hantera dem genom att åtgärda programmet eller genom att vidta nödvändiga säkerhets åtgärder.

Den här funktionen kräver ingen särskild konfiguration, förutom [att konfigurera din app för att skicka telemetri](./usage-overview.md).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När får jag den här typen av meddelande om Smart identifiering?
Det finns tre typer av säkerhets problem som identifieras:
1. Osäker URL-åtkomst: en URL i programmet nås via både HTTP och HTTPS. Normalt bör en URL som accepterar HTTPS-begäranden inte acceptera HTTP-begäranden. Detta kan tyda på ett fel eller säkerhets problem i ditt program.
2. Osäkert formulär: ett formulär (eller en annan "POST"-begäran) i programmet använder HTTP i stället för HTTPS. Att använda HTTP kan kompromettera användar data som skickas av formuläret.
3. Misstänkt användar aktivitet: programmet nås från flera länder/regioner av samma användare på ungefär samma gång. Till exempel är samma användare som har åtkomst till programmet från Spanien och USA inom samma timme. Den här identifieringen visar ett potentiellt skadligt åtkomst försök till ditt program.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Har min app definitivt ett säkerhets problem?
Nej, en avisering innebär inte att din app definitivt har ett säkerhets problem. En identifiering av något av scenarierna ovan kan i många fall tyda på ett säkerhets problem. Identifieringen kan dock ha en naturlig affärs justering och kan ignoreras.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Hur gör jag för att korrigera identifieringen "osäker URL-åtkomst"?
1. **Prioritering.** Meddelandet innehåller antalet användare som har åtkomst till osäkra URL: er och den URL som har påverkats av osäker åtkomst. Detta kan hjälpa dig att tilldela en prioritet för problemet.
2. **Utrymme.** Hur många procent av användarna har åtkomst till osäkra URL: er? Hur många webb adresser påverkades? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller en lista över osäkra begär Anden och listor med URL: er och användare som påverkades för att hjälpa dig att ytterligare diagnostisera problemet.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Hur gör jag för att korrigera identifieringen av "osäkert format"?
1. **Prioritering.** Meddelandet innehåller antalet oskyddade formulär och antalet användare vars data möjligen komprometterats. Detta kan hjälpa dig att tilldela en prioritet för problemet.
2. **Utrymme.** Vilket formulär har varit involverade i det största antalet oskyddade sändningar och vad är distributionen av oskyddade sändningar över tid? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller en lista över osäkra formulär och en analys av antalet oskyddade sändningar för varje formulär för att hjälpa dig att ytterligare diagnostisera problemet.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Hur gör jag för att korrigera identifieringen "misstänkt användar aktivitet"?
1. **Prioritering.** Meddelandet innehåller antalet olika användare som har uppvisat det misstänkta beteendet. Detta kan hjälpa dig att tilldela en prioritet för problemet.
2. **Utrymme.** Från vilka länder/regioner har de misstänkta förfrågningarna sitt ursprung? Vilken användare var mest misstänkt? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller en lista över misstänkta användare och listan över länder/regioner för varje användare för att hjälpa dig att ytterligare diagnostisera problemet.
