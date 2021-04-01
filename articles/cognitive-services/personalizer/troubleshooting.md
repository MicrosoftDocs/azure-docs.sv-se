---
title: Fel sökning – Personanpassare
description: Den här artikeln innehåller svar på vanliga fel söknings frågor om Personanpassare.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.openlocfilehash: b5f7ed1f2ded8f6ec0320d417b59bab016d75028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91777283"
---
# <a name="personalizer-troubleshooting"></a>Fel sökning av personanpassa

Den här artikeln innehåller svar på vanliga fel söknings frågor om Personanpassare.

## <a name="configuration-issues"></a>Konfigurations problem

### <a name="i-changed-a-configuration-setting-and-now-my-loop-isnt-performing-at-the-same-learning-level-what-happened"></a>Jag har ändrat en konfigurations inställning och nu fungerar inte min loop på samma inlärnings nivå. Vad hände?

Vissa konfigurations inställningar [återställer din modell](how-to-settings.md#settings-that-include-resetting-the-model). Konfigurations ändringar bör planeras noggrant.

### <a name="when-configuring-personalizer-with-the-api-i-received-an-error-what-happened"></a>När jag konfigurerar Personanpassaren med API: et, fick jag ett fel meddelande. Vad hände?

Om du använder en enda API-begäran för att konfigurera tjänsten och ändra inlärnings beteendet får du ett fel meddelande. Du måste göra två separata API-anrop: först, för att konfigurera tjänsten och sedan för att växla inlärnings beteende.

## <a name="transaction-errors"></a>Transaktions fel

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>Jag får ett svar för HTTP 429 (för många begär Anden) från tjänsten. Vad kan jag göra?

Om du har valt en kostnads fri pris nivå när du skapade personanpassa instansen, finns det en kvot gräns för antalet ranknings begär Anden som tillåts. Granska API-anrops frekvensen för rang-API: n (i fönstret mått i Azure Portal för din personanpassa resurs) och justera pris nivån (i fönstret pris nivå) om din anrops volym förväntas öka efter tröskelvärdet för den valda pris nivån.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>Jag får ett 5xx-fel vid rang-eller belönings-API: er. Vad ska jag göra?

Dessa problem bör vara transparenta. Om de fortsätter kan du kontakta supporten genom att välja **ny supportbegäran** i avsnittet **support och fel sökning** i Azure Portal för din personanpassa resurs.

## <a name="learning-loop"></a>Inlärnings slinga

### <a name="the-learning-loop-doesnt-attain-a-100-match-to-the-system-without-personalizer-how-do-i-fix-this"></a>Inlärnings slingan inte uppnår en 100%-matchning till systemet utan någon personligt tillverkare. Hur gör jag för att åtgärda detta?

Anledningen till att du inte uppnår ditt mål med inlärnings slingan:
* Inte tillräckligt med funktioner som skickas med rang-API-anrop
* Buggar i de funktioner som skickas – till exempel skicka icke-aggregerade funktions data, till exempel tidsstämplar till Range-API
* Buggar med loop-bearbetning – till exempel att inte skicka belönings data till belönings-API för händelser

För att åtgärda detta måste du ändra bearbetningen genom att antingen ändra de funktioner som skickats till slingan, eller se till att belöningen är en korrekt utvärdering av kvaliteten på rangens svar.

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Inlärnings slingan verkar inte lära sig. Hur gör jag för att åtgärda detta?

Inlärnings slingan behöver några tusen belönings samtal innan ranknings anropen prioriteras effektivt.

Om du är osäker på hur din inlärnings slinga för närvarande fungerar, kör du en [offline-utvärdering](concepts-offline-evaluation.md)och tillämpar den korrigerade inlärnings principen.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>Jag vill få ranknings resultat med samma sannolikhet för alla objekt. Hur gör jag för att vet du att personanpassa är inlärning?

En personanpassare returnerar samma sannolikhet i ett rang-API-resultat när den precis har startat och har en _Tom_ modell, eller när du återställer en personanpassa-loop och din modell fortfarande ligger inom **modell uppdateringens frekvens** period.

När den nya uppdaterings perioden börjar används den uppdaterade modellen och du ser sannolikheten för ändringen.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>Inlärnings-slingan var inlärning men verkar inte längre, och kvaliteten på ranknings resultatet är inte så bra. Vad ska jag göra?

* Se till att du har slutfört och tillämpat en utvärdering i Azure Portal för denna personanpassa resurs (inlärnings slinga).
* Se till att alla förmåner skickas, via belönings-API: et och bearbetas.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>Hur gör jag för att vet du att inlärnings slingan uppdateras regelbundet och används för att räkna data?

Du hittar tiden då modellen senast uppdaterades på sidan **modell-och inlärnings inställningar** i Azure Portal. Om du ser en gammal tidsstämpel är det troligt att du inte skickar rang-och belönings samtal. Om tjänsten inte har några inkommande data uppdateras inte inlärningen. Om inlärnings-slingan inte uppdateras tillräckligt ofta kan du redigera **uppdaterings frekvensen** för loopen.

## <a name="offline-evaluations"></a>Offlineutvärderingar

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>En offline-utvärderings funktions prioritet returnerar en lång lista med hundratals eller tusentals objekt. Vad hände?

Detta beror vanligt vis på tidsstämpel, användar-ID: n eller andra detaljerade funktioner som skickas i.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>Jag skapade en offline-utvärdering och den lyckades nästan omedelbart. Vad beror det på? Jag ser inga resultat?

Offline-utvärderingen använder de tränade modell data från händelserna under den tids perioden. Om du inte skickade några data under tids perioden mellan start-och slut tiden för utvärderingen, slutförs det utan några resultat. Skicka en ny offline-utvärdering genom att välja ett tidsintervall med händelser som du vet har skickats till Personanpassaren.

## <a name="learning-policy"></a>Utbildningsprincip

### <a name="how-do-i-import-a-learning-policy"></a>Hur gör jag för att importera en utbildnings princip?

Lär dig mer om [begrepp för inlärnings principer](concept-active-learning.md#understand-learning-policy-settings) och [hur du använder](how-to-manage-model.md) en ny utbildnings princip. Om du inte vill välja en utbildnings princip kan du använda [offline-utvärderingen](how-to-offline-evaluation.md) för att föreslå en utbildnings princip baserat på dina aktuella händelser.


## <a name="security"></a>Säkerhet

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>API-nyckeln för min slinga har komprometterats. Vad kan jag göra?

Du kan återskapa en nyckel när du har bytt ut klienterna så att de använder den andra nyckeln. Med två nycklar kan du sprida nyckeln på ett Lazy sätt utan att behöva ha några drift avbrott. Vi rekommenderar att du gör detta på ett regelbundet sätt som ett säkerhets mått.


## <a name="next-steps"></a>Nästa steg

[Konfigurera modell uppdaterings frekvensen](how-to-settings.md#model-update-frequency)