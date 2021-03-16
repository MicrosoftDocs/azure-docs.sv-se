---
title: Custom Speech översikt – tal tjänst
titleSuffix: Azure Cognitive Services
description: Custom Speech är en uppsättning online-verktyg som gör att du kan utvärdera och förbättra Microsofts tal-till-text-precision för dina program, verktyg och produkter.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.custom: contperf-fy21q2; references_regions
ms.openlocfilehash: 13bf0f2430e0d58dd9ef28061aad897acf94ac3f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493058"
---
# <a name="what-is-custom-speech"></a>Vad är Custom Speech?

[Custom Speech](https://aka.ms/customspeech) är en uppsättning UI-baserade verktyg som gör att du kan utvärdera och förbättra Microsofts tal-till-text-precision för dina program och produkter. Allt det behöver för att komma igång är en fåtal av test-ljudfiler. Följ länkarna i den här artikeln för att börja skapa en anpassad tal-till-text-upplevelse.

## <a name="whats-in-custom-speech"></a>Vad är i Custom Speech?

Innan du kan göra något med Custom Speech behöver du ett Azure-konto och en röst tjänst prenumeration. När du har ett konto kan du Förbered dina data, träna och testa dina modeller, granska igenkännings kvalitet, utvärdera precision och slutligen distribuera och använda den anpassade tal-till-text-modellen.

Det här diagrammet visar de delar som utgör [Custom speechs delen av tal Studio](https://aka.ms/customspeech). Använd länkarna nedan för att lära dig mer om varje steg.

![Diagram som visar de komponenter som utgör Custom Speechs delen av tal Studio.](./media/custom-speech/custom-speech-overview.png)

1. [Prenumerera och skapa ett projekt](#set-up-your-azure-account). Skapa ett Azure-konto och prenumerera på tal tjänsten. Den här enhetliga prenumerationen ger dig åtkomst till tal översättning av tal till text, text till tal, tal översättning och [tal Studio](https://speech.microsoft.com/customspeech). Sedan kan du använda prenumerationen för din röst tjänst för att skapa ditt första Custom Speech-projekt.

1. [Ladda upp test data](./how-to-custom-speech-test-and-train.md). Ladda upp test data (ljudfiler) för att utvärdera Microsoft tal-till-text-erbjudandet för dina program, verktyg och produkter.

1. [Inspektera igenkännings kvaliteten](how-to-custom-speech-inspect-data.md). Använd [tal Studio](https://speech.microsoft.com/customspeech) för att spela upp ljud som laddats upp och granska kvaliteten på test data. Information om kvantitativa mått finns i [Granska data](how-to-custom-speech-inspect-data.md).

1. [Utvärdera och förbättra noggrannheten](how-to-custom-speech-evaluate-data.md). Utvärdera och förbättra noggrannheten hos tal-till-text-modellen. [Talet Studio](https://speech.microsoft.com/customspeech) visar en *fel frekvens för Word*, som du kan använda för att avgöra om det krävs ytterligare utbildning. Om du är nöjd med noggrannheten kan du använda API: erna för Speech service direkt. Om du vill förbättra noggrannheten med ett relativt genomsnitt på 5% till 20% använder du fliken **utbildning** i portalen för att ladda upp ytterligare tränings data, t. ex. person etiketter och relaterad text.

1. [Träna och distribuera en modell](how-to-custom-speech-train-model.md). Förbättra noggrannheten i tal-till-text-modellen genom att tillhandahålla skrivna avskrifter (10 till 1 000 timmar) och relaterad text (<200 MB) tillsammans med dina ljud test data. Dessa data hjälper till att träna tal-till-text-modellen. Efter inlärningen testar du om. Om du är nöjd med resultatet kan du distribuera din modell till en anpassad slut punkt.

## <a name="set-up-your-azure-account"></a>Konfigurera ditt Azure-konto

Du måste ha ett Azure-konto och en röst tjänst prenumeration innan du kan använda [tal Studio](https://speech.microsoft.com/customspeech) för att skapa en anpassad modell. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Kom ihåg att skapa en standard prenumeration (S0). Kostnads fria (F0) prenumerationer stöds inte.

Om du planerar att träna en anpassad modell med **ljuddata** väljer du någon av följande regioner som har dedikerad maskin vara tillgänglig för utbildning. Detta minskar den tid det tar att träna en modell och låter dig använda mer ljud för utbildning. I dessa regioner kommer tal tjänsten att använda upp till 20 timmars ljud för utbildning. i andra regioner är det bara att använda upp till åtta timmar.

* Australien, östra
* Kanada, centrala
* Indien, centrala
* East US
* USA, östra 2
* USA, norra centrala
* Europa, norra
* USA, södra centrala
* Sydostasien
* Storbritannien, södra
* US Gov, Arizona
* US Gov, Virginia
* Europa, västra
* USA, västra 2

När du har skapat ett Azure-konto och en röst tjänst prenumeration måste du logga in på [tal Studio](https://speech.microsoft.com/customspeech) och ansluta din prenumeration.

1. Logga in på [tal Studio](https://aka.ms/custom-speech).
1. Välj den prenumeration du behöver för att arbeta i och skapa ett tal projekt.
1. Om du vill ändra din prenumeration väljer du knappen kugg hjuls på den översta menyn.

## <a name="how-to-create-a-project"></a>Så här skapar du ett projekt

Innehåll som data, modeller, tester och slut punkter organiseras i *Projects* i [tal Studio](https://speech.microsoft.com/customspeech). Varje projekt är specifika för en domän och ett land/språk. Du kan till exempel skapa ett projekt för Call Center som använder engelska i USA.

Skapa ditt första projekt genom att välja **tal-till-text/anpassad tal** och välj sedan **nytt projekt**. Följ anvisningarna i guiden för att skapa projektet. När du har skapat ett projekt bör du se fyra flikar: **data**, **testning**, **utbildning** och **distribution**. Använd länkarna som beskrivs i [Nästa steg](#next-steps) för att lära dig hur du använder varje flik.

> [!IMPORTANT]
> [Tal Studio](https://aka.ms/custom-speech) tidigare kallat "Custom Speech portal" uppdaterades nyligen! Om du har skapat tidigare data, modeller, tester och publicerade slut punkter i CRIS.ai-portalen eller med API: er, måste du skapa ett nytt projekt i den nya portalen för att ansluta till dessa gamla entiteter.

## <a name="model-and-endpoint-lifecycle"></a>Livs cykel för modell och slut punkt

Äldre modeller blir vanligt vis mindre användbara över tid eftersom den senaste modellen vanligt vis har högre noggrannhet. Därför kan bas modeller och anpassade modeller och slut punkter som skapats via portalen förfalla efter 1 år för anpassning och 2 år för avkodning. Se en detaljerad beskrivning i artikeln [modell och livs cykel livs cykel](./how-to-custom-speech-model-and-endpoint-lifecycle.md) .

## <a name="next-steps"></a>Nästa steg

* [Förbered och testa dina data](./how-to-custom-speech-test-and-train.md)
* [Inspektera dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera och förbättra modell precisionen](how-to-custom-speech-evaluate-data.md)
* [Träna och distribuera en modell](how-to-custom-speech-train-model.md)
