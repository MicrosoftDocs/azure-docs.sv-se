---
title: Vad är utbildning och modell? – Anpassad översättare
titleSuffix: Azure Cognitive Services
description: En modell är systemet, som tillhandahåller översättning för ett angivet språk par. Resultatet av en lyckad utbildning är en modell. När du tränar en modell, krävs tre ömsesidigt uteslutande data uppsättningar inlärnings data uppsättning, justerings data uppsättning och testning av data uppsättningar.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: swmachan
ms.openlocfilehash: d8713d20503dc788175a408f5ec652505090396e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510835"
---
# <a name="what-are-trainings-and-models"></a>Vad är utbildningar och modeller?

En modell är systemet, som tillhandahåller översättning för ett angivet språk par.
Resultatet av en lyckad utbildning är en modell. När du tränar en modell krävs tre ömsesidigt uteslutande dokument typer: utbildning, justering och testning. Ord listans dokument typ kan också tillhandahållas. Se [menings justering](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences).

Om det bara finns tränings uppgifter när du har en utbildning, kommer anpassad översättare automatiskt att sätta samman justering och testning av data. Den kommer att använda en slumpmässig del av meningar från utbildnings dokumenten och undanta dessa meningar från själva tränings data.

## <a name="training-document-type-for-custom-translator"></a>Utbildnings dokument typ för anpassad översättare

Dokument som ingår i inlärnings uppsättningen används av den anpassade översättaren som grund för att skapa din modell. Under övnings körningen är meningar som förekommer i dessa dokument justerade (eller paras ihop). Du kan ta Liberties i att skriva in dina utbildnings dokument. Du kan inkludera dokument som du tror är av Tangential relevans i en modell. Uteslut dem igen i en annan för att se effekten i [Bleu (tvåspråkig Evaluation understudie) Poäng](what-is-bleu-score.md). Så länge du behåller justerings uppsättningen och konstanten för test uppsättning, kan du experimentera med sammansättningen i inlärnings uppsättningen. Den här metoden är ett effektivt sätt att ändra kvaliteten på ditt översättnings system.

Du kan köra flera utbildningar i ett projekt och jämföra [Bleu-poängen](what-is-bleu-score.md) i alla utbildnings körningar. När du kör flera utbildningar för jämförelse bör du se till att samma justerings-/test data anges varje gång. Se även till att även granska resultaten manuellt på fliken ["testning"](how-to-view-system-test-results.md) .

## <a name="tuning-document-type-for-custom-translator"></a>Justera dokument typ för anpassad översättare

Parallella dokument som ingår i den här uppsättningen används av den anpassade översättare för att justera översättnings systemet för optimala resultat.

Justerings data används under träningen för att justera alla parametrar och vikter för översättnings systemet till de optimala värdena. Välj justerings data noggrant: justerings data bör vara representativa för innehållet i dokumenten som du tänker översätta i framtiden. Justerings data har stor påverkan på kvaliteten på de översättningar som skapas. Med justering kan översättnings systemet tillhandahålla översättningar som är närmast de exempel som du anger i justerings data. Du behöver inte fler än 2500 meningar i dina justerings data. För optimal översättnings kvalitet rekommenderar vi att du väljer inställnings uppsättningen manuellt genom att välja det mest representativa valet av meningar.

När du skapar en justerings uppsättning väljer du meningar som är en meningsfull och representativ längd på de kommande meningarna som du förväntar dig att översätta. Du bör också välja meningar som innehåller ord och fraser som du tänker översätta i den ungefärliga distribution som du förväntar dig i dina framtida översättningar. I praktiken kommer en mening med 7 till 10 ord att producera det bästa resultatet, eftersom dessa meningar innehåller tillräckligt med kontext för att Visa Bryt och ger en fras längd som är signifikant, utan att vara alltför komplex.

En lämplig Beskrivning av vilken typ av meningar som ska användas i justerings uppsättningen är Prose: faktiska Fluent-meningar. Inte tabell celler, inte poems, inte en lista med saker, inte bara interpunktion eller siffror i en mening – vanligt språk.

Om du väljer att justera data manuellt bör den inte ha någon av samma meningar som din utbildning och testning av data. Justerings data har en betydande inverkan på översättningens kvalitet – Välj de meningarna noggrant.

Om du inte är säker på vad du ska välja för dina justerings data väljer du tränings data och låter anpassad översättare välja justerings data åt dig. När du låter den anpassade översättare välja att justera data automatiskt, kommer den att använda en slumpmässig del av meningar från dina dokument med tvåspråkig utbildning och utesluta dessa meningar från själva utbildnings materialet.

## <a name="testing-dataset-for-custom-translator"></a>Testar data uppsättning för anpassad översättare

Parallella dokument som ingår i test uppsättningen används för att beräkna BLEU (understudie av tvåspråkiga utvärderings resultat). Den här poängen indikerar översättnings systemets kvalitet. Den här poängen visar i själva verket hur nära översättnings systemet som är resultatet av den här utbildningen som matchar referens meningarna i test data uppsättningen.

BLEU poängen är en mätning av delta mellan automatisk översättning och referens översättning. Värdet sträcker sig från 0 till 100. Poängen 0 anger att inte ett enskilt ord i referensen visas i översättningen. Poängen på 100 anger att den automatiska översättningen exakt matchar referensen: samma ord är i exakt samma position. Poängen du får är BLEU Poäng genomsnitt för alla meningar om test data.

Test data bör innehålla parallella dokument där meningarna med mål språk är de mest önskvärda översättningarna av motsvarande käll språks meningar i käll mål paret. Du kanske vill använda samma kriterier som du använde för att skapa justerings data. Test data påverkar dock inte översättnings systemets kvalitet. Den används uteslutande för att generera BLEU Poäng för dig.

Du behöver inte fler än 2 500 meningar som test data. När du låter systemet välja testnings uppsättning automatiskt, kommer det att använda en slumpmässig del av meningar från dina dokument med tvåspråkig utbildning och utesluta dessa meningar från själva utbildnings materialet.

Du kan visa de anpassade översättningarna av test uppsättningen och jämföra dem med översättningarna som finns i din test uppsättning genom att gå till fliken test i en modell.
