---
title: Så här fungerar Personanpassare – Personanpassare
description: Inlärnings _slingan_ använder Machine Learning för att bygga modellen som förutsäger den främsta åtgärden för ditt innehåll. Modellen har bara tränats på dina data som du har skickat till den med rang-och belönings samtal.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: cfbe5cf8c19bfafb38f6149391e09350785ebf9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91303615"
---
# <a name="how-personalizer-works"></a>Så här fungerar Personanpassning

Den personanpassa resursen, din _inlärnings slinga_, använder Machine Learning för att bygga modellen som förutsäger den främsta åtgärden för ditt innehåll. Modellen har bara tränats på dina data som du har skickat till den med **rang** -och **belönings** samtal. Varje slinga är helt oberoende av varandra.

## <a name="rank-and-reward-apis-impact-the-model"></a>API: er för rang och belöning påverkar modellen

Du skickar _åtgärder med funktioner_ och _kontext funktioner_ till ranknings-API: et. **Ranknings** -API: et bestämmer att du vill använda antingen:

* _Sårbarhet_: den aktuella modellen för att avgöra den bästa åtgärden baserat på tidigare data.
* _Utforska_: Välj en annan åtgärd i stället för den översta åtgärden. Du [konfigurerar den här procent andelen](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) för din personanpassa resurs i Azure Portal.

Du fastställer belönings poängen och skickar poängen till belönings-API: et. **Belönings** -API: et:

* Samlar in data för att träna modellen genom att registrera funktionerna och belönings poängen för varje rang samtal.
* Använder dessa data för att uppdatera modellen baserat på konfigurationen som anges i _inlärnings principen_.

## <a name="your-system-calling-personalizer"></a>Systemet som ringer upp personanpassa

Följande bild visar det arkitektoniska flödet för anrop av rang-och belönings samtal:

![alternativ text](./media/how-personalizer-works/personalization-how-it-works.png "Så här fungerar anpassning")

1. Du skickar _åtgärder med funktioner_ och _kontext funktioner_ till ranknings-API: et.

    * Personanpassa bestämmer om du vill utnyttja den aktuella modellen eller utforska nya val för modellen.
    * Ranknings resultatet skickas till EventHub.
1. Den främsta rangordningen returneras till ditt system som _belönings åtgärds-ID_.
    Ditt system presenterar innehållet och avgör en belönings poäng baserat på dina egna affärs regler.
1. Systemet returnerar belönings poängen till inlärnings slingan.
    * När Personanpassaren tar emot belöningen skickas belöningen till EventHub.
    * Rankningen och belöningen korreleras.
    * AI-modellen uppdateras utifrån korrelations resultatet.
    * Härlednings motorn uppdateras med den nya modellen.

## <a name="personalizer-retrains-your-model"></a>Personanpassaren tränar om din modell

Personanpassaren tränar om din modell baserat på din **modell frekvens uppdaterings** inställning på din personanpassa resurs i Azure Portal.

I personanpassaren används alla data som för närvarande finns kvar, baserat på inställningen för **datakvarhållning** i antal dagar på din personanpassa resurs i Azure Portal.

## <a name="research-behind-personalizer"></a>Forskning bakom personanpassa

Personanpassan är baserad på vetenskaps-och forsknings verksamhet i området för [förstärknings undervisning](concepts-reinforcement-learning.md) , inklusive arbeten, forsknings aktiviteter och fort löp ande forsknings områden i Microsoft Research.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [de viktigaste scenarierna](where-can-you-use-personalizer.md) för personanpassare