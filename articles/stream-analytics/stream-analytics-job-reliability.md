---
title: Undvik avbrott i tjänsten i Azure Stream Analytics jobb
description: I den här artikeln beskrivs hur du gör dina Stream Analyticss jobb uppgradering elastiska.
author: jseb225
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: f4eda779b1bc719706f9eb42cf805a7d5ce864aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020340"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantera Stream Analytics jobbets tillförlitlighet under tjänst uppdateringar

En del av att vara en fullständigt hanterad tjänst är möjligheten att införa nya tjänst funktioner och förbättringar i en snabb takt. Det innebär att Stream Analytics kan ha en tjänst uppdaterings distribution per vecka (eller mer frekvent). Oavsett hur mycket testning som görs är det fortfarande en risk att ett befintligt, pågående jobb kan brytas på grund av införandet av ett fel. Om du kör uppdrags kritiska jobb måste dessa risker undvikas. Du kan minska den här risken genom att följa den **[kopplade region](../best-practices-availability-paired-regions.md)** modellen i Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hur tar Azure-kopplade regioner itu med detta?

Stream Analytics garanterar att jobb i kopplade regioner uppdateras i separata batchar. Det finns därför en tillräckligt lång lucka mellan uppdateringarna för att identifiera potentiella problem och åtgärda dem.

_Med undantag för central Indien_ (vars kopplade region, södra Indien, inte har Stream Analytics närvaro), sker distributionen av en uppdatering till Stream Analytics inte samtidigt i en uppsättning kopplade regioner. Distributioner i flera regioner **i samma grupp** kan förekomma **samtidigt**.

I artikeln om **[tillgänglighet och kopplade regioner](../best-practices-availability-paired-regions.md)** får du den senaste informationen om vilka regioner som paras ihop.

Vi rekommenderar att du distribuerar identiska jobb till båda kopplade regioner. Du bör sedan [övervaka dessa jobb](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) för att få meddelanden när något oväntade inträffar. Om ett av dessa jobb upphör i [fel tillstånd](./job-states.md) efter en Stream Analytics tjänst uppdatering kan du kontakta kund supporten för att identifiera rotor saken. Du bör också redundansväxla eventuella efterföljande användare till den felfria utskriften.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Språk referens för Stream Analytics-fråga](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Stream Analytics hanterings REST API](/rest/api/streamanalytics/)