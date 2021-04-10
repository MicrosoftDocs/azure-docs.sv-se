---
title: Förbättra syntesen med anpassade röst tal tjänster
titleSuffix: Azure Cognitive Services
description: Anpassad röst är en uppsättning online-verktyg som du kan använda för att skapa en igenkännings bara röst för ditt varumärke. Allt det krävs för att komma igång är en fåtal av ljudfiler och tillhör ande avskrifter. Följ länkarna nedan för att börja skapa en anpassad tal-till-text-upplevelse.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 99af0ee46e2827ac8a5749d58bb19cf85f96aa46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577469"
---
# <a name="get-started-with-custom-voice"></a>Komma igång med Custom Voice

[Anpassad röst](https://aka.ms/customvoice) är en uppsättning online-verktyg som du kan använda för att skapa en igenkännings bara röst för ditt varumärke. Allt det krävs för att komma igång är en fåtal av ljudfiler och tillhör ande avskrifter. Följ länkarna nedan för att börja skapa en anpassad text till tal-upplevelse.

## <a name="whats-in-custom-voice"></a>Vad är en anpassad röst?

Innan du börjar med anpassad röst behöver du ett Azure-konto och en röst tjänst prenumeration. När du har skapat ett konto kan du förbereda dina data, träna och testa dina modeller, utvärdera röst kvalitet och slutligen distribuera din anpassade röst modell.

Diagrammet nedan visar stegen för att skapa en anpassad röst modell med hjälp av den [anpassade röst portalen](https://aka.ms/customvoice). Använd länkarna för att lära dig mer.

![Diagram över anpassad röst arkitektur](media/custom-voice/custom-voice-diagram.png)

1. [Prenumerera och skapa ett projekt](#set-up-your-azure-account) – skapa ett Azure-konto och skapa en prenumeration på tal tjänsten. Den här enhetliga prenumerationen ger dig åtkomst till tal översättning av tal till text, text till tal, tal översättning och den anpassade röst portalen. Sedan kan du skapa ditt första anpassade röst projekt med hjälp av din röst tjänst prenumeration.

2. [Ladda upp data](how-to-custom-voice-create-voice.md#upload-your-datasets) överförings data (ljud och text) med hjälp av den anpassade röst portalen eller det anpassade röst-API: et. Från portalen kan du undersöka och utvärdera uttal-resultat och signal-till-brus-förhållande. Mer information finns i [så här förbereder du data för anpassad röst](how-to-custom-voice-prepare-data.md).

3. [Träna din modell](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – Använd dina data för att skapa en anpassad röst modell för text till tal. Du kan träna en modell på olika språk. Efter utbildning, testa din modell och om du är nöjd med resultatet kan du distribuera modellen.

4. [Distribuera din modell](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) – skapa en anpassad slut punkt för din text till tal-röst modell och Använd den för tal syntes i dina produkter, verktyg och program.

## <a name="custom-neural-voices"></a>Anpassade neurala-röster

Anpassad röst stöder för närvarande både standard-och neurala-nivåer. Anpassad neurala-röst ger användarna möjlighet att bygga bättre kvalitets röst modeller samtidigt som de kräver mindre data och tillhandahåller mått som hjälper dig att distribuera AI-ett ansvarsfullt sätt. Vi rekommenderar att du använder anpassad neurala-röst för att utveckla mer realistiska röster för mer naturliga konversations gränssnitt och göra det möjligt för dina kunder och slutanvändare att dra nytta av den senaste text till tal-tekniken på ett ansvarigt sätt. [Läs mer om den anpassade neurala rösten](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

> [!NOTE]
> Som en del av Microsofts engagemang för att utforma ansvarig AI har vi begränsat användningen av anpassad neurala röst. Du får bara till gång till tekniken när dina program har granskats och du har åtagit dig att använda det i justering med våra ansvariga AI-principer. Läs mer om vår [policy om att begränsa åtkomsten](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) och [tillämpa den här](https://aka.ms/customneural). De [språk](language-support.md#customization) och [regioner](regions.md#custom-voices) som stöds för standard-och neurala-versionen av den anpassade rösten är olika. Kontrol lera informationen innan du börjar.  

## <a name="set-up-your-azure-account"></a>Konfigurera ditt Azure-konto

En röst tjänst prenumeration krävs innan du kan använda Custom Speech Portal för att skapa en anpassad modell. Följ de här anvisningarna för att skapa en prenumeration på en röst tjänst i Azure. Om du inte har ett Azure-konto kan du registrera dig för ett nytt.  

När du har skapat ett Azure-konto och en prenumeration på en röst tjänst måste du logga in på den anpassade röst portalen och ansluta din prenumeration.

1. Hämta din prenumerations nyckel för röst tjänsten från Azure Portal.
2. Logga in på den [anpassade röst portalen](https://aka.ms/custom-voice).
3. Välj din prenumeration och skapa ett tal projekt.
4. Om du vill växla till en annan tal prenumeration använder du kugg hjuls-ikonen som finns i det övre navigerings fönstret.

> [!NOTE]
> Du måste ha en F0-eller S0 Speech service-nyckel som skapats i Azure innan du kan använda tjänsten. Anpassad neurala-röst stöder endast S0-nivån. 

## <a name="how-to-create-a-project"></a>Så här skapar du ett projekt

Innehåll som data, modeller, tester och slut punkter organiseras i **projekt** i den anpassade röst portalen. Varje projekt är specifika för ett land/språk och köns kön för den röst du vill skapa. Du kan till exempel skapa ett projekt för en hona röst för samtals centrets chatt-robotar som använder engelska i USA ("en-US").

Skapa ditt första projekt genom att välja fliken **text-till-tal/anpassad röst** och klicka på **nytt projekt**. Följ anvisningarna i guiden för att skapa projektet. När du har skapat ett projekt visas fyra flikar: **data**, **utbildning**, **testning** och **distribution**. Använd länkarna som beskrivs i [Nästa steg](#next-steps) för att lära dig hur du använder varje flik.

> [!IMPORTANT]
> Den [anpassade röst portalen](https://aka.ms/custom-voice) har nyligen uppdaterats! Om du har skapat tidigare data, modeller, tester och publicerade slut punkter i CRIS.ai-portalen eller med API: er, måste du skapa ett nytt projekt i den nya portalen för att ansluta till dessa gamla entiteter.

## <a name="how-to-migrate-to-custom-neural-voice"></a>Så här migrerar du till den anpassade neurala rösten

Standard-/icke-neurala utbildnings nivå (adaptiv, statistisk parameter, concacenative) för anpassad röst är föråldrad. Meddelandet har skickats ut till alla befintliga tal prenumerationer före 2/28/2021. Under utfasnings perioden (3/1/2021-2/29/2024) kan befintliga standard nivå användare fortsätta att använda sina icke-neurala modeller. Alla nya användare/nya tal resurser bör gå över till neurala-nivån/den anpassade neurala rösten. Efter 2/29/2024 kommer alla standard-/icke-neurala anpassade röster inte längre att stödjas. 

Om du använder icke-neurala/standard anpassad röst, migrera till anpassad neurala röst direkt genom att följa stegen nedan. Genom att flytta till den anpassade neurala rösten kan du utveckla mer realistiska röster för ännu mer naturliga konversations gränssnitt och göra det möjligt för dina kunder och slutanvändare att dra nytta av den senaste text till tal-tekniken på ett ansvarigt sätt. 

1. Läs mer om vår [policy om att begränsa åtkomsten](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) och [tillämpa den här](https://aka.ms/customneural). Observera att åtkomsten till den anpassade röst tjänsten för neurala är föremål för Microsofts enda val utifrån våra Berättiganderegler. Kunder kan få till gång till tekniken först efter det att programmet har granskats och de har åtagit sig att använda det i sin egen anpassning med våra [ansvariga AI-principer](https://microsoft.com/ai/responsible-ai) och [uppförandekod](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 
2. När ditt program har godkänts får du till gång till inlärnings funktionen "neurala". Se till att du loggar in på den [anpassade röst portalen](https://speech.microsoft.com/customvoice) med samma Azure-prenumeration som du anger i ditt program. 
    > [!IMPORTANT]
    > För att skydda röst-personal och förhindra utbildning av röst modeller med otillåten inspelning eller utan bekräftelse från röst-personal, kräver vi att kunden laddar upp en inspelad översikt över röst personal som ger sitt medgivande. När du förbereder ditt inspelnings skript ska du se till att ta med den här meningen. "I [tillstånd ditt för-och efter namn] är du medveten om att inspelningar av min röst kommer att användas av [stat namnet på företaget] för att skapa och använda en syntetisk version av rösten."
    > Den här meningen måste överföras till fliken **röst personal** som en muntlig medgivande fil. Det kommer att användas för att kontrol lera om inspelningarna i dina utbildnings data uppsättningar utförs av samma person som gör ditt medgivande.
3. När den anpassade röst modellen för neurala har skapats distribuerar du röst modellen till en ny slut punkt. Om du vill skapa en ny anpassad röst slut punkt med neurala röst modell går du till **text-till-tal > anpassad röst > distribution**. Välj **distribuera modell** och ange ett **namn** och en **Beskrivning** för din anpassade slut punkt. Välj sedan den anpassade neurala röst modell som du vill koppla till den här slut punkten och bekräfta distributionen.  
4. Uppdatera koden i dina appar om du har skapat en ny slut punkt med en ny modell. 

## <a name="next-steps"></a>Nästa steg

- [Förbered anpassade röst data](how-to-custom-voice-prepare-data.md)
- [Skapa en anpassad röst](how-to-custom-voice-create-voice.md)
- [Guide: spela in dina röst exempel](record-custom-voice-samples.md)
