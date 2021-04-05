---
title: Belönings Poäng – Personanpassare
description: Belönings poängen anger hur bra det RewardActionID som har gjort det, för användaren. Värdet för belönings poängen bestäms av affärs logiken, baserat på observationer av användar beteende. Personanpassa tågens maskin inlärnings modeller genom att utvärdera belöningarna.
ms.service: cognitive-services
ms.subservice: personalizer
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: f3249ba2089c3d9650aa46f665353ad392d0e773
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94365575"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Belönings Poäng visar att anpassningen lyckades

Belönings poängen anger hur bra det [RewardActionID](/rest/api/cognitiveservices/personalizer/rank/rank#response)som har gjort det, för användaren. Värdet för belönings poängen bestäms av affärs logiken, baserat på observationer av användar beteende.

Personanpassa tågens maskin inlärnings modeller genom att utvärdera belöningarna.

Lär dig [hur du](how-to-settings.md#configure-rewards-for-the-feedback-loop) konfigurerar standard belönings poängen i Azure Portal för din personanpassa resurs.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Använd belönings-API: et för att skicka belönings poängen till Personanpassaren

Förmåner skickas till Personanpassare av [belönings-API: et](/rest/api/cognitiveservices/personalizer/events/reward). En belöning är vanligt vis en siffra mellan 0 och 1. En negativ belöning, med värdet-1, är möjlig i vissa scenarier och bör endast användas om du har erfarenhet av förstärknings inlärning (HUVUDWEBBADRESS). Personanpassare tågen modellen för att uppnå högsta möjliga summa av förmåner över tid.

Förmåner skickas när användar beteendet har inträffat, vilket kan vara dagar senare. Den maximala tid som Personanpassaren väntar tills en händelse anses vara ingen belöning eller en standard belöning har kon figurer ATS med försvars [tid](#reward-wait-time) för betalning i Azure Portal.

Om belönings poängen för en händelse inte har tagits emot inom svars **tiden för belöningen**, kommer **standard belöningen** att tillämpas. Normalt är **[standard belöningen](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** inställd på noll.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Beteenden och data som ska övervägas för förmåner

Ta hänsyn till dessa signaler och beteenden för belönings poängen:

* Direkt indata från användaren för förslag när alternativ är inblandade ("vill du betyda X?").
* Sessionens längd.
* Tid mellan sessioner.
* Sentiment analys av användarens interaktioner.
* Direkta frågor och mini-undersökningar där roboten ber användaren om feedback om användbarhet, exakthet.
* Svar på aviseringar eller fördröjning för att svara på aviseringar.

## <a name="composing-reward-scores"></a>Skriva belönings resultat

En belönings Poäng måste beräknas i affärs logiken. Poängen kan representeras som:

* Ett enda nummer skickas en gång
* En poäng som skickas omedelbart (till exempel 0,8) och ytterligare poäng som skickas senare (vanligt vis 0,2).

## <a name="default-rewards"></a>Standard förmåner

Om ingen belöning tas emot inom svarets [vänte tid](#reward-wait-time), varaktigheten sedan ranknings anropet, tillämpar den här klassificeringen implicit **standard belöningen** för denna rang-händelse.

## <a name="building-up-rewards-with-multiple-factors"></a>Skapa fördelar med flera faktorer

För effektiv anpassning kan du skapa belönings poängen baserat på flera faktorer.

Du kan till exempel använda dessa regler för att anpassa en lista med video innehåll:

|Användar beteende|Partiellt Poäng värde|
|--|--|
|Användaren har klickat på det översta objektet.|+ 0,5-belöning|
|Användaren öppnade det faktiska innehållet i objektet.|+ 0,3-belöning|
|Användaren bevakade 5 minuter med innehållet eller 30%, beroende på vilket som är längre.|+ 0,2-belöning|
|||

Du kan sedan skicka den totala belöningen till API: et.

## <a name="calling-the-reward-api-multiple-times"></a>Anrop av belönings-API: n flera gånger

Du kan också anropa belönings-API: et med samma händelse-ID och skicka olika belönings poäng. När en Personanpassare får till gång till dessa förmåner fastställer den den slutliga belöningen för händelsen genom att aggregera dem enligt vad som anges i personanpassa konfigurationen.

Sammansättnings värden:

*  **Först**: tar de första belönings poängen emot för evenemanget och tar bort resten.
* **Sum**: tar alla belönings resultat som samlas in för eventId och lägger till dem tillsammans.

Alla förmåner för en händelse, som tas emot efter den **väntande tiden för belöningen**, tas bort och påverkar inte inlärningen av modeller.

Genom att lägga till belönings resultat kan din slutliga belöning vara utanför det förväntade Poäng intervallet. Detta gör inte att tjänsten fungerar.

## <a name="best-practices-for-calculating-reward-score"></a>Metod tips för att beräkna belönings Poäng

* **Överväg de sanna anpassnings indikatorerna**: det är enkelt att tänka på när det gäller klickningar, men en bra belöning baseras på vad du vill att användarna ska *uppnå* i stället för vad du vill att användarna ska *göra*.  Till exempel kan belöningar i klick leda till att du väljer innehåll som är clickbaitt känsligt.

* **Använd en belönings Poäng för hur stor anpassningen fungerade: att** anpassa ett film förslag skulle förhoppnings vis leda till att användaren tittar på filmen och ger den en hög klassificering. Eftersom film klassificeringen förmodligen beror på många saker (kvaliteten på det som fungerar, stämningen av användaren), är det inte en bra belönings signal för hur väl *anpassningen* fungerade. Användaren tittar på de första minuterna i filmen, men kan vara en bättre signal för anpassnings effektivitet och att skicka en belöning på 1 efter 5 minuter är en bättre signal.

* **Förmåner gäller endast för RewardActionID**: med personanpassare tillämpas belöningarna för att förstå effektiviteten i den åtgärd som anges i RewardActionID. Om du väljer att visa andra åtgärder och användaren klickar på dem ska belöningen vara noll.

* **Ta hänsyn till oönskade konsekvenser**: skapa belönings funktioner som leder till ansvariga resultat med [etik och ansvarig användning](ethics-responsible-use.md).

* **Använd ökande belöningar**: genom att lägga till del förmåner för mindre användar beteende kan du göra det lättare för användare att uppnå bättre fördelar. Den här stegvisa belöningen gör det möjligt för algoritmen att veta att den kommer närmare att engagera användaren i det slutliga önskade beteendet.
    * Om du visar en lista över filmer, om användaren hovrar över den första för ett tag för att se mer information, kan du fastställa att vissa användar engagemang har inträffat. Beteendet kan räknas med en belönings poäng på 0,1.
    * Om användaren öppnade sidan och sedan avslutas, kan belönings poängen vara 0,2.

## <a name="reward-wait-time"></a>Vänte tid för belöning

En personanpassare korrelerar informationen om ett rang samtal med de fördelar som skickas i belönings anrop för att träna modellen. De kan komma att uppstå vid olika tidpunkter. Personanpassaren väntar en begränsad tid och startar när rang anropet skedde, även om rang anropet gjordes som en inaktiv händelse och aktive ras senare.

Om **belöningens vänte tid** upphör att gälla, och det inte finns någon belönings information, tillämpas en standard belöning för utbildning. Maximal vänte tid är 6 dagar.

## <a name="best-practices-for-reward-wait-time"></a>Metod tips för fördröjning av belöning

Följ dessa rekommendationer för bättre resultat.

* Gör belönings vänte tiden så kort som möjligt, samtidigt som du lämnar tillräckligt med tid för att få feedback från användaren.

* Välj inte en varaktighet som är kortare än den tid som krävs för att få feedback. Om några av dina förmåner till exempel har tittat på 1 minut i en video bör experiment längden vara minst dubbel.

## <a name="next-steps"></a>Nästa steg

* [Kunskapsförmedling](concepts-reinforcement-learning.md)
* [Testa rang-API: et](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Testa belönings-API: et](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)