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
ms.date: 11/11/2020
ms.author: trbye
ms.custom: contperf-fy21q2
ms.openlocfilehash: 51f9ad4fea1c139cee5974ecd81645c9a904feb4
ms.sourcegitcommit: c538b6e4cf27b992500c079ad9c914c05d55eb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854901"
---
# <a name="what-is-custom-speech"></a>Vad är Custom Speech?

[Custom Speech](https://aka.ms/customspeech) är en uppsättning UI-baserade verktyg som gör att du kan utvärdera och förbättra Microsofts tal-till-text-precision för dina program och produkter. Allt det behöver för att komma igång är en fåtal av test-ljudfiler. Följ länkarna i den här artikeln för att börja skapa en anpassad tal-till-text-upplevelse.

## <a name="whats-in-custom-speech"></a>Vad är i Custom Speech?

Innan du kan göra något med Custom Speech behöver du ett Azure-konto och en röst tjänst prenumeration. När du har ett konto kan du Förbered dina data, träna och testa dina modeller, granska igenkännings kvalitet, utvärdera precision och slutligen distribuera och använda den anpassade tal-till-text-modellen.

Det här diagrammet visar de delar som utgör den [Custom Speech portalen](https://aka.ms/customspeech). Använd länkarna nedan för att lära dig mer om varje steg.

![Diagram som visar de komponenter som utgör Custom Speechs portalen.](./media/custom-speech/custom-speech-overview.png)

1. [Prenumerera och skapa ett projekt](#set-up-your-azure-account). Skapa ett Azure-konto och prenumerera på tal tjänsten. Den här enhetliga prenumerationen ger dig åtkomst till tal översättning av tal till text, text till tal, tal översättning och [Custom Speech-portalen](https://speech.microsoft.com/customspeech). Sedan kan du använda prenumerationen för din röst tjänst för att skapa ditt första Custom Speech-projekt.

1. [Ladda upp test data](./how-to-custom-speech-test-and-train.md). Ladda upp test data (ljudfiler) för att utvärdera Microsoft tal-till-text-erbjudandet för dina program, verktyg och produkter.

1. [Inspektera igenkännings kvaliteten](how-to-custom-speech-inspect-data.md). Använd [Custom Speech-portalen](https://speech.microsoft.com/customspeech) för att spela upp ljud som laddats upp och granska kvaliteten på test data. Information om kvantitativa mått finns i [Granska data](how-to-custom-speech-inspect-data.md).

1. [Utvärdera och förbättra noggrannheten](how-to-custom-speech-evaluate-data.md). Utvärdera och förbättra noggrannheten hos tal-till-text-modellen. [Custom Speech Portal](https://speech.microsoft.com/customspeech) ger en *fel frekvens för Word*, som du kan använda för att avgöra om det krävs ytterligare utbildning. Om du är nöjd med noggrannheten kan du använda API: erna för Speech service direkt. Om du vill förbättra noggrannheten med ett relativt genomsnitt på 5% till 20% använder du fliken **utbildning** i portalen för att ladda upp ytterligare tränings data, t. ex. person etiketter och relaterad text.

1. [Träna och distribuera en modell](how-to-custom-speech-train-model.md). Förbättra noggrannheten i tal-till-text-modellen genom att tillhandahålla skrivna avskrifter (10 till 1 000 timmar) och relaterad text (<200 MB) tillsammans med dina ljud test data. Dessa data hjälper till att träna tal-till-text-modellen. Efter inlärningen testar du om. Om du är nöjd med resultatet kan du distribuera din modell till en anpassad slut punkt.

## <a name="set-up-your-azure-account"></a>Konfigurera ditt Azure-konto

Du måste ha ett Azure-konto och en röst tjänst prenumeration innan du kan använda [Custom Speech Portal](https://speech.microsoft.com/customspeech) för att skapa en anpassad modell. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](overview.md#try-the-speech-service-for-free).

Om du planerar att träna en anpassad modell med ljuddata väljer du någon av följande regioner som har dedikerad maskin vara tillgänglig för utbildning. Detta minskar den tid det tar att träna en modell.

* Australien, östra
* Kanada, centrala
* Indien, centrala
* East US
* USA, östra 2
* USA, norra centrala
* Norra Europa
* USA, södra centrala
* Sydostasien
* Storbritannien, södra
* US Gov, Arizona
* US Gov, Virginia
* Europa, västra
* USA, västra 2

> [!NOTE]
> Kom ihåg att skapa en standard prenumeration (S0). Kostnads fria (F0) prenumerationer stöds inte.

När du har skapat ett Azure-konto och en röst tjänst prenumeration måste du logga in på [Custom Speech Portal](https://speech.microsoft.com/customspeech) och ansluta din prenumeration.

1. Logga in på [Custom Speech Portal](https://aka.ms/custom-speech).
1. Välj den prenumeration du behöver för att arbeta i och skapa ett tal projekt.
1. Om du vill ändra din prenumeration väljer du knappen kugg hjuls på den översta menyn.

## <a name="how-to-create-a-project"></a>Så här skapar du ett projekt

Innehåll som data, modeller, tester och slut punkter organiseras i *projekt* i [Custom Speech portalen](https://speech.microsoft.com/customspeech). Varje projekt är specifika för en domän och ett land/språk. Du kan till exempel skapa ett projekt för Call Center som använder engelska i USA.

Skapa ditt första projekt genom att välja **tal-till-text/anpassad tal** och välj sedan **nytt projekt**. Följ anvisningarna i guiden för att skapa projektet. När du har skapat ett projekt bör du se fyra flikar: **data**, **testning**, **utbildning** och **distribution**. Använd länkarna som beskrivs i [Nästa steg](#next-steps) för att lära dig hur du använder varje flik.

> [!IMPORTANT]
> [Custom Speech portalen](https://aka.ms/custom-speech) uppdaterades nyligen! Om du har skapat tidigare data, modeller, tester och publicerade slut punkter i CRIS.ai-portalen eller med API: er, måste du skapa ett nytt projekt i den nya portalen för att ansluta till dessa gamla entiteter.

## <a name="model-lifecycle"></a>Modell livs cykel

Custom Speech använder både *bas modeller* och *anpassade modeller*. Varje språk har en eller flera bas modeller. När en ny tal modell släpps till tjänsten vanliga tal, importeras den också till Custom Speech tjänsten som en ny bas modell. De uppdateras varje 3 till 6 månader. Äldre modeller blir vanligt vis mindre användbara över tid eftersom den senaste modellen vanligt vis har högre noggrannhet.

Anpassade modeller skapas däremot genom att anpassa en vald bas modell till ett visst kund scenario. Du kan fortsätta att använda en viss anpassad modell under en längre tid efter att du har en som motsvarar dina behov. Men vi rekommenderar att du regelbundet uppdaterar till den senaste bas modellen och tränar om den med tiden med ytterligare data. 

Andra viktiga villkor som rör modell livs cykeln är:

* **Anpassning**: ta en bas modell och anpassa den till domänen/scenariot med hjälp av text data och/eller ljuddata.
* **Avkodning**: använda en modell och utföra tal igenkänning (avkodning av ljud i text).
* **Slut punkt**: en användarspecifik distribution av antingen en bas modell eller en anpassad modell som *endast* är tillgänglig för en specifik användare.

### <a name="expiration-timeline"></a>Tids linje för förfallo datum

När nya modeller och nya funktioner blir tillgängliga och äldre, tas mindre exakta modeller bort, se följande tids linjer för modell-och slut punkts utgång:

**Bas modeller** 

* Anpassning: tillgängligt i ett år. När modellen har importer ATS är den tillgänglig i ett år för att skapa anpassade modeller. Efter ett år måste nya anpassade modeller skapas från en senare bas modell version.  
* Avkodning: tillgänglig i två år efter importen. Så du kan skapa en slut punkt och använda batch-avskrift för två år med den här modellen. 
* Slut punkter: tillgängliga på samma tids linje som avkodning.

**Anpassade modeller**

* Avkodning: tillgänglig i två år efter att modellen har skapats. Så du kan använda den anpassade modellen i två år (batch/Real/test) när den har skapats. Efter två år *bör du träna modellen* på grund av att bas modellen normalt sett är föråldrad för anpassning.  
* Slut punkter: tillgängliga på samma tids linje som avkodning.

När antingen en bas modell eller anpassad modell upphör att gälla, kommer den alltid att återgå till den *senaste bas modell versionen*. Därför kommer din implementering aldrig att brytas, men det kan bli mindre exakt för *dina data* om anpassade modeller upphör att gälla. Du kan se förfallo datum för en modell på följande platser i Custom Speech-portalen:

* Sammanfattning av modell utbildning
* Information om modell utbildning
* Distributionssammanfattning
* Distributions information

Du kan också kontrol lera förfallo datumen via [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) och [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) anpassade tal-API: er under `deprecationDates` egenskapen i JSON-svaret.

Observera att du kan uppgradera modellen på en anpassad tal slut punkt utan drift stopp genom att ändra modellen som används av slut punkten i distributions avsnittet i den anpassade tal portalen, eller via det anpassade tal-API: et.

## <a name="next-steps"></a>Nästa steg

* [Förbered och testa dina data](./how-to-custom-speech-test-and-train.md)
* [Inspektera dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera och förbättra modell precisionen](how-to-custom-speech-evaluate-data.md)
* [Träna och distribuera en modell](how-to-custom-speech-train-model.md)